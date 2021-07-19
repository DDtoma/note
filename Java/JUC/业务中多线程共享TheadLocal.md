# 业务场景
在复杂业务处理的场景中，需要不同线程同享相同的TheadLocal数据。比如不同的业务处理，需要相同的用户上下文，进而需要TheadLocal数据进行共享。

## 子父线程TheadLocal共享

**InheritableThreadLocal** 父线程传递本地变量到子线程的解决方案。


## 线程池TheadLocal共享

由于线程池中的线程是事先生成的，没有子父线程的关系所以无法使用InheritableThreadLocal进行共享TheadLocal。

使用 Alibaba 扩展 InheritableThreadLocal 的 TransmittableThreadLocal。