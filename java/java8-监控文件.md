# java8实现 文件监控

最近想实现 一个动态监控本地文件变更，重新加载到本地缓存中

于是搜索了一下

fileListener 主要是作为监控新增加，修改，删除后到变化后的回调，使用起来还是比较笑意的，
那么 背后的实现机制是怎么样的呢？

 ```java
    public class FileWatcher {
    
        /**
         * 监视的文件目录
         */
        private Path watchDirectory;
    
        /**
         * 监视的文件类型正则表达式
         */
        private FilenameFilter filenameFilter;
    
        /**
         * 监视到的文件监听器
         */
        private FileListener fileListener;
    
        public FileWatcher(Path watchDirectory, FilenameFilter filenameFilter, FileListener fileListener) {
            this.watchDirectory = watchDirectory;
            this.filenameFilter = filenameFilter;
            this.fileListener = fileListener;
        }
    
        public FileWatcher(Path watchDirectory, FileListener fileListener) {
            this(watchDirectory, null, fileListener);
        }
    
        /**
         */
        private void executeHistoryFiles() {
            try {
                Stream<Path> stream = Files.list(this.watchDirectory);
                if (this.filenameFilter != null) {
                    stream = stream.filter(path -> {
                        String fileName = path.getFileName().toString();
                        return filenameFilter.accept(this.watchDirectory.toFile(), fileName);
                    });
                }
                stream.forEach(path -> {
                    this.fileListener.onCreate(path);
                });
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    
        /**
         * 监听目录历史文件 &新文件，根据文件的变化处理文件
         */
        public void watch() {
            executeHistoryFiles();
    
            try (WatchService watchService = FileSystems.getDefault().newWatchService()) {
                this.watchDirectory.register(watchService, StandardWatchEventKinds.ENTRY_CREATE, StandardWatchEventKinds.ENTRY_MODIFY, StandardWatchEventKinds.ENTRY_DELETE);
                while (true) {
                    WatchKey watchKey = watchService.take();
                    for (WatchEvent event : watchKey.pollEvents()) {
                        WatchEvent.Kind eventKind = event.kind();
                        if (eventKind == StandardWatchEventKinds.OVERFLOW) {
                            continue;
                        }
                        String fileName = event.context().toString();
                        //文件名不匹配
                        if (this.filenameFilter != null && !this.filenameFilter.accept(this.watchDirectory.toFile(), fileName)) {
                            continue;
                        }
                        Path file = Paths.get(this.watchDirectory.toString(), fileName);
                        if (eventKind == StandardWatchEventKinds.ENTRY_CREATE) {
                            this.fileListener.onCreate(file);
                        } else if (eventKind == StandardWatchEventKinds.ENTRY_MODIFY) {
                            this.fileListener.onModify(file);
                        } else if (eventKind == StandardWatchEventKinds.ENTRY_DELETE) {
                            this.fileListener.onDelete(file);
                        }
                    }
                    boolean isKeyValid = watchKey.reset();
                    if (!isKeyValid) {
                        break;
                    }
                }
            } catch (IOException | InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    
    public class Test { 
      public sttic void min (String[] args ) { 
    
       FileWatcher fileWatcher = new FileWatcher(Paths.get(FileUtils.SQL_FILE_DIRECTORY), new FileListener() {
            @Override
            public void onCreate(Path file) {
                System.out.println("[add file >>>] " + file.getFileName());
                // todo 自定义逻辑 
            }
    
            @Override
            public void onModify(Path file) {
                System.out.println("[modify >>>] " + file.getFileName());
                // todo 自定义逻辑 
            }
    
            
    
            @Override
            public void onDelete(Path file) {
                System.out.println("delete file"+ file);
                // todo 自定义逻辑 
            }
        });
    
    
         fileWatcher.watch();
    
     }
    
    }
```

