# android studio 里面build,clean区别、

## Build目录下几个选项的区别

![4322600-aa84d28684c7b7e4](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img20200711003317.png)



1. Make Project：编译Project下所有Module，一般是自上次编译后Project下有更新的文件，不生成apk。
2. Make Selected Modules：编译指定的Module，一般是自上次编译后Module下有更新的文件，不生成apk。
3. Clean Project：删除之前编译后的编译文件，并重新编译整个Project，比较花费时间，不生成apk。
4. Rebuild Project：先执行Clean操作，删除之前编译的编译文件和可执行文件，然后重新编译新的编译文件，不生成apk，这里效果其实跟Clean Project一样。
5. Build APK：前面4个选项都是编译，没有生成apk文件，如果想生成apk，需要点击Build APK。
6. Generate Signed APK：生成有签名的apk。

**为了更清楚的知道clean和rebuild到底有什么区别，我把自己的一个小项目执行了一下这两个操作，并用对比软件对比了一下，红色部分是执行build操作的时候多出来的步骤。**



![4322600-c5b577aaadebfcf1](https://lalalademaxiya01.oss-cn-beijing.aliyuncs.com/img20200711003418.png)

大概意思：

:app:incrementalDebugJavaCompilationSafeguard

**在incremental-safeguard目录下生成tag.txt，标识已经执行过task**

:app:compileDebugJavaWithJavac

**intermediates下生成classes文件夹，以及对应的dependency-cache文件夹，classes文件夹中包含之前已经解压的各个aar文件中的类，但是不包含libs下的jar包中的内容；同时还会生成一个tmp文件夹，内容为空；目录下不包括libs下的jar包内容**

compile Debug Ndk UP-TO-DATE

**编译调试NDK更新**

:app:compileDebugSources

:app:incrementalDebugUnitTestJavaCompilationSafeguard UP-TO-DATE

**编译调试单元测试的更新**

:app:compileDebugUnitTestJavaWithJavac

:app:processDebugJavaRes UP-TO-DATE

**res资源更新**

:app:processDebugUnitTestJavaRes UP-TO-DATE

**单元测试中res资源的更新**

:app:compileDebugUnitTestSources

:app:incrementalDebugAndroidTestJavaCompilationSafeguard

:app:compileDebugAndroidTestJavaWithJavac

:app:compileDebugAndroidTestNdk UP-TO-DATE

**单元测试NDK更新**

:app:compileDebugAndroidTestSources

**基本上build比clean多的就是会把NDK重新编译一遍，有更新的话就更新。以及一些资源文件的更新。基本上差不多。**

这样看来，clean项目一般已经够用了，如果NDK以及资源文件有更改的话建议rebuild。

说的不对的地方，还希望大家包含。（不服来打）

