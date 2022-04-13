# BOMB!!!
**Dr. Evil is very evil but interesting, is`t it?**

在bomb文件夹目录下，有三个文件`bomb` `bomb.c` `README` 需要先将**bomb** 文件反汇编成`.s` 文件，生成`bomb.s`文件。 在linux下用`objdump -d bomb > bomb.s`命令即可生成`bomb.s`文件。

**生成完bomb.s文件，让我们来阅读以下main函数**
![main](https://github.com/zxcv545/CSAPP-3E-solution/blob/main/Bomblab/Screenshot%20from%202022-04-13%2023-05-30.png)
最开始的`if-else`语句是选择字符串的输入方式，你可以选择直接在命令行中给出，或者将字符串放在一个文件里，后者的好处是，你不用重复输入字符串。

`initialize_bomb();`这个函数是初始化炸弹的，具体细节未给出。

`input = read_line();`根据注释也能猜测出来，这个应该是从刚刚选择的输入方式中获得一个字符串。
`phase_1(input);`这一步到了我们真正要解决问题的时候了，由于phase_1函数的具体细节没有给出，我们只能阅读汇编代码来看看这个函数到底做了什么。
