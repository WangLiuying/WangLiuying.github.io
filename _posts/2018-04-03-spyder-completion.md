spyder补全问题
================

spyder 补全
-----------

长期苦于spyder的补全失效。昨天找到了这个。

赶紧记笔记吭哧吭哧。

1.  打开`\Lib\site-packages\spyder\utils\introspection`路径，找到module\_completion.py文件（也可能在别的文件夹下，反正搜索一下这个同名的文件就可以了），将需要补全的库名增加到mods变量中。

2.  cmd输入`spyder --reset`

3.  在控制台里面输入`pip list`找到看有没有一个叫`emu34`的包，如果有的话，`pip uninstall emu34`，重启spyder，就可以了。

参考这位大大的博客 <https://blog.csdn.net/zmt1849101245/article/details/79034729>
