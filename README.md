# Note
Google搜索笔记

# ContentProvider简介
ContentProvider是不同应用程序之间进行数据交换的标准API，ContentProvide以Uri的形式对外提供数据，允许其他应用访问和修改数据；  
其他应用使用ContentResolve根据Uri进行访问操作指定的数据。

虽然使用其他方法也可以对外共享数 据，但数据访问方式会因数据存储的方式而不同，如：采用文件方式对外共享数据，需要进行文件操作
读写数据；  
采用sharedpreferences共享数 据，需要使用sharedpreferences API读写数据。而使用__ContentProvider共享数据的好处是统一了数据访问方式__。  Android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)。
