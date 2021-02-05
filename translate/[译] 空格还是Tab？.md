翻译自：[You're using TABs in a wrong way - antek's tech blog (anadoxin.org)](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/)



使用 Tab 还是空格？对于这个问题，我想可能每个人都有自己的答案。那么两种选择各有什么特点呢？



使用 TABs 的优势：

1. 使用 Tabs 意味着你的源代码有更少的空格。每四个字节（如果你是 Ruby 程序员，使用两个字节的缩进，那就是两个字节，也可能是八个字节）被压缩为一个字节，存储的更有效率。

2. 插入 TAB 比插入空格快一点。人们常常为敲击四次重复的空格键来作为缩进感到愚蠢。同样的，在文件中导航，TABs 也会更快。为什么要敲击 `left``left``left``left` 来实现本可以敲击一次 `left` 实现的向前缩进呢？
3. 更加灵活。可以根据需要，使用两个字符的缩进。稍后也可以根据喜好切换为四个字符的缩进。如果将代码交付给习惯使用 8 个字符缩进的同事，也没有问题。TAB 的站位是抽象的，具体的缩进，可以根据编辑器的设置进行配置。
4. TAB 是一个用来缩进的字符，它并没有其他用途。那么，为什么还要找其他方式来替代呢？
5. 缩进是不可能出错的，我的意思是，缩进一行是不可能的。你的编辑器要么缩进，要么不缩进，非此即彼。



那么为什么我们还要使用空格呢？我们来看看使用空格作为缩进的一个优点：

1. 无论使用什么应用程序打开代码，或者什么设备，代码看起来都是一样的。



那么，当人们已经清楚的知道 TAB 有更多好处的时候，为什么还要使用空格作为缩进么？让我们看看使用 `TABs` 的时候，上面的例子是什么样子的。

![tabs01](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_01.png)



在上面的例子中，我们有一条与 TAB 对齐的注释。当前设置为每个 TAB 4 字符。当我们将 TAB 设置为 8 个字符缩进时会发生什么？



![tabs02](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_02.png)



所以，我猜当这个函数参数有三五个的时候，就会溢出屏幕之外。我们可以调试一下这种情况吗？我们再回过头看一下四个字符作为 TAB 缩进时的情况：



![tabs03](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_03.png)



八个字符作为缩进的情况：



![tabs04](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_04.png)



这里我们可以在第二行注释中，看到将 TAB 设置为 8 个字符的情况。使用 TAB，不会使你的代码表示，与代码结构分开，现在，我们必须更改结构才能更改外观。



另一个使用四个字符作为 TAB 缩进的情况：



![tabs05](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_05.png)



设置8个字符作为缩进，显然不太合适。



![tabs06](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_06.png)



### 如何修复？



事实上，使用 TAB 无法修复这种情况。你必须使用空格来辅助 TAB。只在行内第一个非空白字之前使用 TAB。对于其余的缩进需求，请使用空格，像这样：



![tabs06](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_06.png)



就这种方式，设置 TAB 为 8 个字符缩进的情况（如上图所示），可以正常的工作，4 个字符的缩进也可以正常工作，2 个字符的缩紧情况也可以正常的工作：



![tabs08](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_08.png)



![tabs09](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_09.png)



记住，下次当你遇到像下面这种 TAB 和空格的问题时，不要再纳闷：



![tabs10](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_10.png)



当 TAB 和空格混合使用时，就会出现这个问题：



![tabs11](https://anadoxin.org/blog/youre-using-tabs-in-a-wrong-way.html/tabs_11.png)



使用空格可以避免你遇到类似的问题。同样的，你也不需要再按四次空格来进行缩进，你仍然可以使用 TAB 键。你可以使用 ctrl + 方向键，一次跳过整个单词和所有空格。



缺点是，每个人都需要使用你的缩进格式。不喜欢两个字符的缩紧？请尝试别的代码库。



那么最终的方案是什么？同时使用 TAB 和空格。这样，您将获得两全其美的体验：同时拥有自由性和灵活性，并且在所有应用程序和设备中，都拥有一致的表现。









