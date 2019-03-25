# Android 第三方库基础问题整理

## LeakCanary
> LeakCanary 是大名鼎鼎的 square 公司开源的内存泄漏检测工具。目前上大部分App在开发测试阶段
都会接入此工具用于检测潜在的内存泄漏问题，做的好一点的可能会搭建一个服务器用于保存各个设备上的内存泄漏问题再集中处理。
### 为什么要使用LeakCanary
我们知道内存泄漏问题的排查有很多种方法， 比如说，Android Studio 自带的 Profiler 工具、
MAT(Memory Analyzer Tool)、以及LeakCanary。 
选择 LeakCanary 作为首选的内存泄漏检测工具主要是因为它能实时检测泄漏并以非常直观的调用链方式展示内存泄漏的原因。
### LeakCanary 做不到的(待定)
虽然 LeakCanary 有诸多优点，但是它也有做不到的地方，比如说检测申请大容量内存导致的OOM问题、Bitmap内存未释放问题，Service 中的内存泄漏可能无法检测等。

### LeakCanary如何捕获内存泄漏？
通过 Debug.dumpHprofData()方法生成 .hprof 文件,然后利用开源库HAHA(开源地址:github.com/square/haha)解析.hprof文件，并发送给DisplayLeakActivity进行展示