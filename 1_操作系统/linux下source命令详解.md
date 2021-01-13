[Linux下source命令详解](https://www.cnblogs.com/shuiche/p/9436126.html)

source命令用法#
`source FileName`
 

# source命令作用#
在当前bash环境下读取并执行FileName中的命令。

*注：该命令通常用命令“.”来替代。

使用范例：
```sh
source filename 

# 中间有空格
. filename
```

source命令（从 C Shell 而来）是bash shell的内置命令。点命令，就是个点符号，（从Bourne Shell而来）是source的另一名称。

source（或点）命令通常用于重新执行刚修改的初始化文档，如 .bash_profile 和 .profile 这些配置文件。

 

## 举例说明:

假如在登录后对 .bash_profile 中的 EDITER 和 TERM 变量做了修改，这时就可以用 source 命令重新执行 .bash_profile文件,使修改立即生效而不用注销并重新登录。

再比如您在一个可执行的脚本 `a.sh` 里写这样一行代码

`export KKK=111 `
 

假如您用  `./a.sh`  执行该脚本，执行完毕后，在当前shell环境中运行 echo $KKK，发现没有值，但是用 `source a.sh` 来执行 ，然后再 `echo $KKK`，就会发现 打印 111 。

## 原因说明

**因为调用`./a.sh`来执行shell是在一个子shell里运行的，所以执行后，结构并没有反应到父shell里，但是source不同他就是在本shell中执行的，所以能够看到结果。**

 

# 总结#
source命令（从 C Shell 而来）是bash shell的内置命令. 点命令，就是一个点符号，是source的另一名称。这两个命令都以一个脚本为参数，该脚本将在当前shell的环境执行，即不会启动一个新的子shell。所有在脚本中设置的变量都将成为当前Shell的一部分。

 

## source命令的一个妙用#
在编译核心时，常常要反复输入一长串命令，如

```sh
make mrproper
make menuconfig
make dep
make clean
make bzImage
.......
```
 

　　

这些命令既长，又繁琐。而且有时候容易输错，浪费你的时间和精力。如果把这些命令做成一个文件，让它自动按顺序执行，对于需要多次反复编译核心的用户来说，会很方便。

用source命令可以办到这一点。它的作用就是把一个文件的内容当成是shell来执行。

先在/usr/src/mysh目录下建立一个文件，取名为make_command，在其中输入如下内容：

```sh
make mrproper &&
make menuconfig &&
make dep &&
make clean &&
make bzImage &&
make modules &&
make modules_install &&
cp arch/i386/boot/bzImge /boot/vmlinuz_new &&
cp System.map /boot &&
vi /etc/lilo.conf &&
lilo -v
```
　　

文件建立好之后，以后每次编译核心，只需要在/usr/src/mysh下输入source make_command 就行了。这个文件也完全可以做成脚本，只需稍加改动即可。

shell编程中的命令有时和C语言是一样的。&&表示与，||表示或。把两个命令用&&联接起来，如 make mrproper && make menuconfig，表示要第一个命令执行成功才能执行第二个命令。对执行顺序有要求的命令能保证一旦有错误发生，下面的命令不会盲目地继续执行。

 

## source filename 与 sh filename 及./filename执行脚本的区别#
* 当shell脚本具有可执行权限时，用sh filename与./filename执行脚本是没有区别得。./filename是因为当前目录没有在PATH中，所有”.”是用来表示当前目录的。
* sh filename 重新建立一个子shell，在子shell中执行脚本里面的语句，该子shell继承父shell的环境变量，但子shell新建的、改变的变量不会被带回父shell，除非使用export。
* source filename：这个命令其实只是简单地读取脚本里面的语句依次在当前shell里面执行，没有建立新的子shell。那么脚本里面所有新建、改变变量的语句都会保存在当前shell里面。
 