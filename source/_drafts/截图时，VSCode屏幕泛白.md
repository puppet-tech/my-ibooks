问题如图所示：

![image.png](https://oss.puppetdev.top/image/note/55cd72a51c116edd02bc2c3b06e14f72.png)

<!--more-->

放弃前摇，直接给出解决方案：换个主题即可。

![image.png](https://oss.puppetdev.top/image/note/2690eb88b4db166d6321ff9553e613cf.png)

实测，Light Modern 的色域正常，其他的没有经过测试。

> 出现这个问题的原因，大概率就是**色彩空间不匹配**。
>
> HDR 内容是为了在支持 HDR 的显示设备上展现更广的色彩范围和对比度设计的。这意味着 HDR 内容通常使用不同于标准动态范围（SDR）的色彩空间，比如 Rec. 2020 与 SDR 的常用 Rec. 709 有所不同。
>
> 当在 HDR 模式下截图时，这些截图可能仍在使用用于 SDR 内容的色彩处理方式。结果是，这些本来为 HDR 设计的色彩在 SDR 环境中查看时，会显得过亮或色彩失真。
