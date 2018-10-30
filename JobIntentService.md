Android O 加入了对 App 后台强行限制的逻辑，过去通过 Service 就可以启动并长期留存后台程序的做法，在Android O 上已经无法正常工作了，Android 引入了 JobIntentService 帮助系统进行后台任务调度。对于新手来说，可以参照下面两篇文章，一篇是 Google Android 官方的说明文档，另一篇是CSDN上的介绍文章。

* [Google Android JobIntentService](https://developer.android.com/reference/android/support/v4/app/JobIntentService)
* [JobIntentService详解及使用](https://blog.csdn.net/Houson_c/article/details/78461751)