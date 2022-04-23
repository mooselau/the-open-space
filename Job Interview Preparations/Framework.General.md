# Framework General 框架

## Spring Batch

- 批处理框架，相对于微服务逐条处理，批处理可以分批针对大数据量进行处理（从数万到数百万甚至更高的数据量）；
- 批处理一般是周期运行，比如 每日/每周/每月，会搭配XXL JOB或者 QUARTZ 进行使用；
- 相关概念：Job Launcher, Job, Step, Tasklet, Chunk, read/process/write;

