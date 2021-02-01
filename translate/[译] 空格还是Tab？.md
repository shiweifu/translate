翻译自：[You're using TABs in a wrong way - antek's tech blog (anadoxin.org)](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/)



使用 Tab 还是空格？对于这个问题，我想可能每个人都有自己的答案。那么两种选择各有什么特点呢？



使用 TABs 的优势：

1. 使用 Tabs 意味着你的源代码有更少的空格。每四个字节（如果你是 Ruby 程序员，使用两个字节的缩进，那就是两个字节，也可能是八个字节）被压缩为一个字节，存储的更有效率。

2. 插入 TAB 比插入空格快一点。人们常常为敲击四次重复的空格键来作为缩进感到愚蠢。同样的，在文件中导航，TABs 也会更快。为什么要敲击 `left``left``left``left` 来实现本可以敲击一次 `left` 实现的向前缩进呢？

