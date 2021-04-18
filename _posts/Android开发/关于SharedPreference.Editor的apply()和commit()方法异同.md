# 关于SharedPreference.Editor的apply()和commit()方法异同

在androidstudio上coding经常会提示一些警告，通过它我们能了解到一些自己不了解的好的编程习惯和少用的方法，本次发现就是一个例子，用习惯了SharedPreference.Editor的commit()方法，但是在studio提示使用apply()方法替换，看到apply()方法有点不知所措，因为根本不了解这个方法的作用随即翻阅android的api和google了一下apply()和commit()两者的区别。
 首先，两者都能实现shared存储的功能，但是两者还是有着一些不同

- apply方法是将share的修改提交到内存而后异步写入磁盘，但是commit是直接写入磁盘，这就造成两者性能上的差异，犹如apply不直接写入磁盘而share本身是单例创建，apply方法会覆写之前内存中的值，异步写入磁盘的值只是最后的值，而commit每次都要写入磁盘，而磁盘的写入相对来说是很低效的，所以apply方法在频繁调用时要比commit效率高很多。
- apply虽然高效但是commit也有着自己的优势那就是它可以返回每次操作的成功与否的返回值，根据它我们就可以在操作失败时做一些补救操作。
   综上，studio提示我们使用apply是在效率上的优化考虑，但是如果你很重视share是否成功操作，并希望在失败时做相应的提示或者补救commit还是更好的选择。
   参考资料：
   [http://developer.android.com/intl/zh-cn/reference/android/content/SharedPreferences.Editor.html#apply()](https://link.jianshu.com?t=http://developer.android.com/intl/zh-cn/reference/android/content/SharedPreferences.Editor.html#apply())
   [http://blog.csdn.net/yanbober/article/details/47866369](https://link.jianshu.com?t=http://blog.csdn.net/yanbober/article/details/47866369)
   [http://blog.sina.com.cn/s/blog_40e9d4dd0100xy1s.html](https://link.jianshu.com?t=http://blog.sina.com.cn/s/blog_40e9d4dd0100xy1s.html)

