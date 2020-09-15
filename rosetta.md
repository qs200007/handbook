# Lut's sey , Rosetta !

**creator : qs, 邱泽铭**

**email : qiuzeming@nibs.ac.cn**

**date : 2020.9.16**

**rosetta tutorial : <https://www.rosettacommons.org/demos/latest/Home>**

**description : a handbook may lead you to rosetta universe**

---

---



## 1 rosetta 基本

**参考：**

<https://zhanglab.ccmb.med.umich.edu/docs/node10.html>

<https://bioinformatics.uconn.edu/resources-and-events/tutorials-2/script-array-job/>

<https://wiki.duke.edu/display/SCSC/SGE+Array+Jobs>

<https://arc.liv.ac.uk/SGE/howto/sge-array.html>

<http://bioinformatics.mdc-berlin.de/intro2UnixandSGE/sun_grid_engine_for_beginners/README.html>

<https://www.psc.edu/?option=com_content&view=article&id=2214&Itemid=900>



---

在实验室的**honda集群**上安有**rosetta**软件，可以通过**登录节点x254**连接：

```
qs2000@qs2000-Lenovo-Rescuer-15ISK:~$ ssh x254
```

可以选择直接在**x254**上跑rosetta（只要占用时间不多的话），也可以选择提交任务到**honda队列**上跑任务。

---

---

如果要在**x254**上跑，需要先设置**环境变量**。写文件**source_rosetta.sh**：

```
export PATH=/home/soft/rosetta/gcc8_2019.35/main/source/bin:$PATH

# For tutorials https://www.rosettacommons.org/demos/latest/Home
export ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source
export ROSETTA3_DB=/home/soft/rosetta/gcc8_2019.35/main/database
export ROSETTA3_TOOLS=/home/soft/rosetta/gcc8_2019.35/tools
```

输入：

```
chmod 755 source_rosetta.sh
source source_rosetta.sh
```

之后调用rosetta的命令，可以用简写**$ROSETTA3**，**$ROSETTA3_DB**，**$ROSETTA3_TOOLS**。

一般软件都安装在 **/home/soft/** 下。

在**x254**上跑rosetta程序时，需要一直监管着后台CPU。因为x254是登录节点，用的资源太多了可能会挤掉别人的程序，**~~会被打~~**。

可以在<https://www.huanglab.org.cn/ganglia/?c=b060cuda.hn.org&m=load_one&r=hour&s=by%20name&hc=4&mc=2>上看实验室的机子状态。

输入:

```
htop
```

用于监管CPU。

---

---

如果要提交到**honda队列**来跑任务，需要先了解以下知识。

每次提交任务只能提交**一个shell脚本**。用如下命令：

```
qsub -q honda script.sh
```

如果你想要让十个核停止2min，你可以写如下的脚本：

```
#!/usr/bin
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1-10
#$ -tc 20
#$ -wd /pubhome/zmqiu02/work/result

echo "running"
echo $SGE_TASK_ID

python3.6 /pubhome/zmqiu02/work/script/sleep.py

echo "end"
```

参数意义：
| 参数 | 意义                           |
| ---- | -------------------------------------- |
| -V   | 传递现有环境                           |
| -q   | 队列名称（honda，mazda）               |
| -wd  | work dir，工作目录，输出存放位置       |
| -cwd | current work dir。输出都放到现有位置下 |
| -N   | jobname，随便写                        |
| -S   | /bin/bash 运行bash                     |
| -t   | 1-100，一共运行一百个线程              |
| -tc  | 50，同一时间最多用50个线程             |
| -m   | ea，当结束或报错时，发邮件给你         |
| -M   | qiuzeming@nibs.ac.cn，你的邮箱         |

另一个sleep.py文件中写：

```
#!/usr/bin/pyhton3.6

import time

print("Start Time:{0}".format(time.ctime()))
time.sleep(60*2)  #60s*2
print("End Time:{0}".format(time.ctime()))
```

提交任务：

```
qsub -q honda script.sh
```

查看任务状态：

```
qstat
```

---

---

现在我们回到**rosetta**

比如，我可以写脚本**rosetta_script.sh**：

```
#!/usr/bin
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1
#$ -tc 50
#$ -wd /home/zmqiu/work/result

echo "running~~"

IN_DIR=/home/zmqiu/work
ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source
ROSETTA3_DB=/home/soft/rosetta/gcc8_2019.35/main/database
ROSETTA3_TOOLS=/home/soft/rosetta/gcc8_2019.35/tools


$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s $IN_DIR/input_files/1qys.pdb

echo "ending~~"

```

只有中间的那一句是真正在跑rosetta命令。然后提交：

```
qsub -q honda rosetta_script.sh
```

无需设置环境变量。

---

---

## 2 rosetta 教程：Controlling Input and Output in Rosetta

**参考：**

<https://www.rosettacommons.org/demos/latest/tutorials/input_and_output/input_and_output>