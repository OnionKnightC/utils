# utils
utils repository

1、POIUtil：
  之前使用的读取excel file的方法有OOM的exception，所以又从网上找了方法重新给自己写了一套方法，可以读取大量的records。
  目前优点：
    a、把while放在if...else...里面是自己的一些小补充，减少判断次数。
  目前缺点：
    a、个人认为用作为 LinkedList<LinkedList<String>> 返回context的内容很古怪，很有可能又会出现一些暂时没有遇到的问题。
    
