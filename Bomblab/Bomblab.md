# BOMB!!! 

**Dr. Evil is very evil but interesting, is`t it?**

在bomb文件夹目录下，有三个文件`bomb` `bomb.c` `README` 需要先将**bomb** 文件反汇编成`.s` 文件，生成`bomb.s`文件。 利用`objdump -d bomb > bomb.s`即可生成`bomb.s`文件。

![main函数](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2018-36-00.png)

![main的汇编代码-1](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2019-51-37.png)

![main的汇编代码-2](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2019-52-20.png)

最开始的`if-else`语句是选择字符串的输入方式，你可以选择直接在命令行中给出，或者将字符串放在一个文件里，后者的好处是，你不用重复输入字符串。

可以先看看0x400e19之前的代码，其实就是对main函数里最开始的分支语句的实现，其中可以看出调用了一些其他函数，例如`call   0x400c10 <fopen@plt>` `call   0x400c00 <__printf_chk@plt>` `call   0x400c20  <exit@plt>` 。我在汇编代码中转到这些函数所在的代码时，发现了一些我不理解的行为，后来仔细想了想，这些应该是库函数，由于本人水平有限，目前实在无法分析库函数，只能跳过了。

`initialize_bomb();`这个函数是初始化炸弹的，具体细节未给出，此函数对应0x400e19处的call指令，看此函数的汇编代码发现也是调用了库函数。

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2019-53-03.png)

所以此处也跳过，这些所做的其实都不影响我们分析，Dr. Evil也并不想让我们知道是怎么初始化的。

那么我们就来到了`input = read_line();`，根据注释也能猜测出来，这个应该是从刚刚选择的输入方式中获得一个字符串，并不需要知道这个函数的细节，我们看下在0x400e53处的汇编代码，根据约定`%rax`的值作为返回值来传递，这里将`%rax`的值传递给`%rdi`，`%rdi`中的值又作为参数传递给接下来要调用的函数，接下来要调用的函数就是`phase_1`,先来看看`phase_1`的汇编代码

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2019-53-38.png)

用`gdb bomb -tui`调试bomb，在进入gdb后输入`layout asm`会在上方显示汇编代码

![](/home/zxcv/Pictures/Screenshot from 2022-04-14 20-11-22.png)

在main函数中即将要调用`phase_1`时设置断点`break *0x400e3a`,然后就可以用`run`命令来运行代码，这个时候出现了要让你输入字符串的提示：

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2020-17-04.png)

刚开始并不知道函数内部是什么，所以随便输入一串字符测试一下，我只输入了一个字符。

然后开始一步一步调试代码

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2020-19-42.png)

这是即将进入`string_not_equal`前的用来传递参数的两个寄存器的值.

函数`string_not_equal`内部的情况为：

![](/https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2020-21-35.png)

先保存了三个寄存器的值，这三个寄存器是被调用者保存寄存器，然后将两个用来传递参数的寄存器的值分别赋给了`%rbx`和`%rbp`然后调用`string_length`函数，来看看`string_length`函数吧：

![](/home/zxcv/Pictures/Screenshot from 2022-04-14 20-26-55.png)

很短的一段汇编代码，大概意思就是以传递过来的参数`(%rdi)`内的值为初始地址，然后检查对应地址内的字节是否为零`(0x00)`来作为判断字符的长度，如果不是零，长度加一，然后到下一个字节，是零就返回一个长度的值，直到遇到零函数终止，我刚刚就输入一个字符，所以执行完后返回到`string_length`函数时检查一下`%rax`的值：

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2020-34-22.png)

果然是`0x01`。

刚刚检查的是我们输入的字符串，下面到检测题目中想要我们给出的字符串了，接着一步一步运行。

当函数再次返回时，检查`%rax`的值：

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2020-38-07.png)

从这里可以知道，我们要输入的字符的长度为`52`，那么然后呢？，通过继续分析函数`string_length`可以知道

先比对字符的长度，如果不相等，则条件为真，直接退出函数。如果相等，然后一个一个比对各个字符是否相等，如果不相等还是会退出函数，然后将结果设置为真，直到所有字符都比对完且相等，在遇到第53个字符时，根据汇编代码可以看出，第53个字符为0x00，为什么是这个？可能是初始化的结果，然后函数退出返回假（也就是0），再去看看phase_1函数，根据这个结果来选择炸弹是否引爆。结果当返回的结果为0时才不会引爆，这与刚刚分析的一样。



所以我们要输入什么呢？看起来答案在内存的某个地方，在那呢？还记得刚刚比对字符的函数吗。我得直到字符在那才能比对是吧，这样就容易知道了，应该就在刚刚显示的那两个传递参数的寄存器里，通过分析可以知道，是在`%rsi`寄存器里，值为：`402400`,那么我们用gdb来看看这段内存里存的字符是什么:

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2021-11-34.png)

前52个字符是`Border relations with Canada have never been better.`,所以这应该是答案，让我们测试一下：

![](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-14%2021-14-01.png)

果然没错，这样，phase1解决了！
