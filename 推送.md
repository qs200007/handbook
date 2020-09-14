# Ubuntu，windows双系统安装

作者：邱泽铭，林炜峰

email：qiuzeming@nibs.ac.cn

日期：2020.9.5

快开学了，可能有些人选了生信相关课程，需要拥有一个Linux环境，方便接下来 ~~装逼~~ 学习。这里提供了一个装Linux，Windows双系统的方案，供大家参考。在林同学的电脑上测试过， ~~大概~~ 可行，不会莫名其妙丢文件。此外，为了拥有Linux系统，也可以选择在Windows下安装WSL(Windows Subsystem Linux)。我在安系统上浪费了很多时间，希望这篇推文能让大家少走一些弯路. **Raise above, focus on science.**

![break-the-cycle-rise-above-focus-on-science](/home/qs2000/work/handbook_pictures/break-the-cycle-rise-above-focus-on-science.jpg)

# 1 安装双系统（Windows+Ubuntu）

**参考**：<https://blog.csdn.net/fanxueya1322/article/details/90205143>

<https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cdimage/ubuntu/releases/?C=M&O=A>

## 1.1制作U盘启动盘（也可以找我借）

准备一个**8G以上的空U盘**。

到ubuntu官网下载**ISO镜像文件**(<https://ubuntu.com/download/desktop>)，也可以到清华镜像下下载(<https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04/>)。下载到除u盘外任何位置都可以。

![Screenshot from 2020-09-10 20-04-13](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 20-04-13.png)

接下来使用Rufus软件制作U盘启动盘(<http://rufus.ie/>)。

![2019051414042883](/home/qs2000/work/handbook_pictures/2019051414042883.png)

选择下载好的ISO文件，按默认操作即可。注意，“选择”按钮是可以点的。

## 1.2 分配磁盘空间

从当前Windows上分配一块空间用于安装ubuntu系统。(我个人设了200G，其实在Ubuntu中也可以访问Windows上的文件)

按下**windows** + **x**选择**磁盘管理**。

![20190514140532467](/home/qs2000/work/handbook_pictures/20190514140532467.png)

选择**除了C盘外且有>100G**空间的一个盘。点击右键，选压缩卷，输入要分配的大小(至少100G)。

## 1.3 安装Ubuntu

将制作好的U盘启动盘插在电脑上，关闭电脑，重新开机。开机时不断按**热键**（我的是F12），进入启动项选择，可以看到一个很明显的带ubuntu的选项，选择进入。

然后你能看到 ubuntu 的启动界面，之后有多个选项供你选择，最前面的两个应该是试用 ubuntu 和 安装 ubuntu，选择**安装**。（建议选择安装English版）

![2019051414064111](/home/qs2000/work/handbook_pictures/2019051414064111.png)

准备安装，如果有网的话建议把下载更新的选项勾上。

![20190514140652458](/home/qs2000/work/handbook_pictures/20190514140652458.png)

接下来选择安装类型，清除整个磁盘会导致原来的 windows 系统被清除，选择**其他选项**我们自己来分区。（其他两个选项有风险）
![20190514140703784](/home/qs2000/work/handbook_pictures/20190514140703784.png)

你在这个界面应该能够看到很多分区，包括你在前面分配的一块空闲空间。

![IMG_20200910_193500](/home/qs2000/work/handbook_pictures/IMG_20200910_193500.jpg)

接下来我们要给ubuntu系统各个文件夹分配空间。可以按下表分配，也可以自己按网上其他教程分配。

| 目录  | 建议大小       | 格式     | 描述                                                         |
| ----- | -------------- | -------- | ------------------------------------------------------------ |
| /     | 20G            | ext4     | 根目录                                                       |
| /tmp  | 5G             | ext4     | 系统的临时文件，一般系统重启不会被保存。（建立服务器需要？） |
| /boot | 1G             | ext4     | 系统引导起始位置，建议：应该大于400MB或1GB Linux的内核及引导系统程序所需要的文件，比如  vmlinuz initrd.img文件都位于这个目录中。在一般情况下，GRUB 或 LILO  系统引导管理器也位于这个目录；启动撞在文件存放位置，如kernels，initrd，grub。 |
| /home | 尽量大些       | ext4     | 用户工作目录；个人配置文件，如个人环境变量等；所有账号分配一个工作目录。 |
| swap  | 物理内存的两倍 | 交换空间 | 交换空间：交换分区相当于Windows中的“虚拟内存”，如果内存低的话（1-4G），物理内存的两倍，高点的话（8-16G）要么等于物理内存，要么物理内存+2g左右 |

我一般先分配**/boot**，再**swap**，再**/tmp**，再**/boot**，最后所有空间都给**/home**。

安装时，选择空闲磁盘，然后点**+**号。分区类型都是逻辑分区。然后选择**挂载点**。

![IMG_20200910_193655](/home/qs2000/work/handbook_pictures/IMG_20200910_193655.jpg)

![IMG_20200910_194051](/home/qs2000/work/handbook_pictures/IMG_20200910_194051.jpg)

![IMG_20200910_194813](/home/qs2000/work/handbook_pictures/IMG_20200910_194813.jpg)

最后分配好后，在**安装启动引导器的设备**选择**/boot**所安装的位置。点击安装。

![IMG_20200910_195114](/home/qs2000/work/handbook_pictures/IMG_20200910_195114.jpg)

安装语言建议选English，因为一些软件不识别中文目录。

**创建用户**的时候一定要记住密码。

![IMG_20200910_195317](/home/qs2000/work/handbook_pictures/IMG_20200910_195317.jpg)

重启后一定要拔U盘。然后就可以**~~愉快地装逼~~**搬砖了。

![Screenshot from 2020-09-10 20-37-59](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 20-37-59.png)

~~剩下的懒得写了，直接看链接吧。~~

---

---

---

# 2 Ubuntu下常用软件安装

## 2.1 搜狗输入法安装

**参考**：<https://blog.csdn.net/Chamico/article/details/89788324>

## 2.2 应用商店软件安装

Chrome, Vscode

## 2.3 Typora等编辑器安装

**参考**：

<https://www.howtoing.com/best-notepad-alternatives-for-linux>

<https://blog.csdn.net/caoyangyang123/article/details/79484697>

## 2.4 anaconda安装

**参考**：

<https://blog.csdn.net/zong596568821xp/article/details/84022701>

<https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/>

<https://blog.csdn.net/u013538959/article/details/78546517>

<https://blog.csdn.net/u011479200/article/details/86501366>

## 2.5 MD软件GROMACS安装

**参考**：<https://anaconda.org/bioconda/gromacs>

## 2.6 PyMOL安装

**参考**：<https://pymolwiki.org/index.php/Linux_Install>