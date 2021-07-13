## ![[G1垃圾回收参数#G1 Garbage First 垃圾收集器参数配置]]

## 设置Matespace内存大小的参数
1. `-XX:MetaspaceSize=256m` ^bbdef1
2. `-XX:MaxMetaspaceSize=512M`

## jvm通用参数
1. `-XX:+DisableExplicitGC` 禁止在启动期间显式调用System.gc()
2. `-XX:+HeapDumpOnOutOfMemoryError` OOM时导出堆到文件
3. `-XX:HeapDumpPath=d:/a.dump` 导出OOM的路径    
4. `-XX:+PrintGCDetails` 打印GC详细信息
5. `-XX:+PrintGCTimeStamps` 打印CG发生的时间戳
6. `-XX:+PrintHeapAtGC` 每一次GC前和GC后，都打印堆信息
7. `-XX:+TraceClassLoading` 监控类的加载
8. `-XX:+PrintClassHistogram` 按下Ctrl+Break后，打印类的信息　 

