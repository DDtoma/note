
> jdk 1.8版本下的性能工具
# jps
1. `jps -lv` 显示pid、启动jar包、启动参数

![[Pasted image 20210628164637.png]]


# jstat

[jdk8 jstat 命令解释#来源简书](https://www.jianshu.com/p/6232d8bd051d)

1. jstat -gc 1756 250 20 每250毫秒查看进程1756的垃圾收集情况，一共20次

![[Pasted image 20210628165022.png]]

```undefined
S0C：年轻代中第一个幸存区的大小
S1C：年轻代中第二个幸存区的大小
S0U：年轻代中第一个幸存区的使用大小
S1U：年轻代中第二个幸存区的使用大小
EC：年轻代中伊甸园区的大小
EU：年轻代中伊甸园区的使用大小
OC：老年代大小
OU：老年代使用大小
MC：方法区大小
MU：方法区使用大小
CCSC:压缩类空间大小
CCSU:压缩类空间使用大小
YGC：年轻代gc次数
YGCT：年轻代消耗时间
FGC：老年代gc次数
FGCT：老年代gc消耗时间
GCT：gc消耗总时间
```

2. jstat -gccapacity 1756 查看虚拟机内存使用

![[Pasted image 20210628165618.png]]

```undefined
NGCMN：年轻代最小容量
NGCMX：年轻代最大容量
NGC：当前年轻代容量
S0C：年轻代中第一个幸存区的大小
S1C：年轻代中第二个幸存区的大小
EC：年轻代中伊甸园区的大小
OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代容量
OC:当前老年代大小
MCMN：最小元数据容量
MCMX：最大元数据容量
MC：当前元数据空间大小
CCSMN：最小压缩类空间大小
CCSMX：最大压缩类空间大小
CCSC：当前压缩类空间大小
YGC：年轻代gc次数
FGC：老年代gc次数
```

# jinfo
 jinfo -flag CMSInitatingOccupancyFraction 1756
 jinfo -flag 要查看的参数字段 pid