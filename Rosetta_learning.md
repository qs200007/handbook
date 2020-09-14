参考：

<http://www.mamicode.com/info-detail-1531073.html>

<https://zhanglab.ccmb.med.umich.edu/docs/node1.html>

<https://www.jianshu.com/p/b7b540a3c015>

<http://bioinformatics.mdc-berlin.de/intro2UnixandSGE/sun_grid_engine_for_beginners/how_to_submit_a_job_using_qsub.html>

<https://bioinformatics.uconn.edu/resources-and-events/tutorials-2/script-array-job/#>

PBS(Portable Batch System)

在**A**上，把**A**机器上的文件**test**，复制到**B**机器上的目录**~/work**下面

在A机器上：

```
[qs2000@A]$ scp test B:~/work
```

在B上会有**~/work/test**

在**A**上，把**A**机器上的文件夹**test_file**中的所有文件，复制到**B**机器上的目录**~/work**下面

```
[qs2000@A]$ scp -r test_file B:~/work
```

之后在B上会有**~/work/test_file**

在**A**上，把**B**机器上的文件test，复制到**A**机器上的目录**~/work**下面

```
[qs2000@A]$ scp B:~/test ~/work
```

向cluster提交任务时只能提交shell脚本

```
qsub -V -q honda -wd ~/work/result script.sh
qstat
```

参数意义：

| -V   | 传递现有环境                           |
| ---- | -------------------------------------- |
| -q   | 队列名称（honda，mazda）               |
| -wd  | work dir，工作目录，输出存放位置       |
| -cwd | current work dir。输出都放到现有位置下 |
| -N   | jobname，随便写                        |
| -S   | /bin/bash 运行bash                     |
| -t   | 1-100，一共运行一百个线程              |
| -tc  | 50，同一时间最多用50个线程             |
| -m   | ea，当结束或报错时，发邮件给你         |
| -M   | qiuzeming@nibs.ac.cn，你的邮箱         |
|      |                                        |
|      |                                        |
|      |                                        |
|      |                                        |

也可以不在命令行里传参数。而在script.sh里写：

```
#!/bin/bash
#$ -N qzm_20_9_12_1
#$ -wd ~/work/result
#$ -q mazda
#$ -V
```



script.sh内容：

```
#!/bin/bash
echo "running"

pyhton3 ~/script/test.py
```

查看所有排队中的job：

```
qstat -u \*
```

查看所有集群列表：

```
qconf -sql
```

可以尝试在mazda集群上，用10个核，sleep 2min，就很好玩。~~在honda集群上做这个事容易被打。~~

script.sh:

```
#!/usr/bin
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1-10
#$ -tc 50
#$ -wd /pubhome/zmqiu02/work/result


echo "running~"
echo $SGE_TASK_ID

python3.6 /pubhome/zmqiu02/work/script/sleep.py

echo "end"
```

sleep.py:

```
#!/usr/bin/python3.6

import time

print("start:{0}".format(time.ctime()))

time.sleep(2*60)

print("end:{0}".format(time.ctime()))
```

在命令行输入：

```
[zmqiu02@k168]$ qsub -q mazda script.sh
```

一些命名：

```
$SGE_TASK_ID
$SGE_STEP_SIZE
$SGE_TASK_FIRST
$SGE_TASK_LAST
infile=$(sed -n -e "$SGE_TASK_ID p" jobfile1.txt)
```



---

登录到x254上

```
ssh x254
```

建文件**~/bin/source_rosetta.sh**。文件内容如下：

```
export PATH=/home/soft/rosetta/gcc8_2019.35/main/source/bin:$PATH

# For tutorials https://www.rosettacommons.org/demos/latest/Home
export ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source
export ROSETTA3_DB=/home/soft/rosetta/gcc8_2019.35/main/database
export ROSETTA3_TOOLS=/home/soft/rosetta/gcc8_2019.35/tools
```

这步相当于设置路径**$PATH**，设置简称**$ROSETTA3**，**$ROSETTA3_DB**，**$ROSETTA3_TOOLS**。

配置环境：

```
source ~/bin/source_rosetta.sh
```

把rosseta用到的教程文件拷到自己目录下：

```
cp -R /home/soft/rosetta/gcc8_2019.35/demos/tutorials/input_and_output/input_files 
/home/zmqiu/work/input_files
```

转移到工作目录：

```
cd /home/zmqiu/work/
```

开始rosetta教程

---

输入：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb
```

参数**in:file:s**代表：input single file。

之后会output文件**score.sc**

输入：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/from_rcsb/3tdm.pdb 
-ignore_unrecognized_res
-ignore_zero_occupancy false

$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s $IN_DIR/input_files/from_rcsb/1qys.pdb @$IN_DIR/flag_input_relax
```



---

如果以向honda提任务的方式，运行Rosetta。可以这么做：

写rosetta_script.sh：

```
#!/usr/bin
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1
#$ -tc 50
#$ -wd /home/zmqiu/work

echo "running~~"

IN_DIR=/home/zmqiu/work
ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source
ROSETTA3_DB=/home/soft/rosetta/gcc8_2019.35/main/database
ROSETTA3_TOOLS=/home/soft/rosetta/gcc8_2019.35/tools


$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s $IN_DIR/input_files/1qys.pdb

echo "ending~~"

```



输入：

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


###$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s $IN_DIR/input_files/from_rcsb/1qys.pdb @$IN_DIR/flag_input_relax

###$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s $IN_DIR/input_files/4eq1.pdb -constraints:cst_fa_file $IN_DIR/input_files/constrained_atompairs.cst -ignore_unrecognized_res  @$IN_DIR/flag_input_relax

###$ROSETTA3/bin/score.default.linuxgccrelease -in:file:s $IN_DIR/input_files/1qys.pdb -in:file:native $IN_DIR/input_files/from_rcsb/1qys.pdb -ignore_waters

$ROSETTA3/bin/extract_pdbs.default.linuxgccrelease -in:file:silent $IN_DIR/input_files/1qys_10.o -in:file:tagfile $IN_DIR/1qys_silent.tag

echo "ending~~"

```





# Let's say Rosetta: Scoring!

输入：

```
$ROSETTA3/bin/score_jd2.linuxgccrelease -in:file:s input_files/from_rcsb/1qys.pdb -out:pdb
```

比较：

```
1qys_0001.pdb
1qys.pdb
```

把原始结构加上氢原子，加上没显示的重原子。骨架不变。方向不变。如果有没有的残基，加上。去掉水，去掉杂离子。

算的能量是**1qys_0001.pdb**中的。

![Screenshot from 2020-09-13 16-31-20](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-13 16-31-20.png)

**Lut's see what you have done!**

输入：

```
$ROSETTA3/bin/score_jd2.linuxgccrelease @$IN_DIR/flag
```

其中，**@flag**代表将flag文件中的参数，**次弟第弟**输入到命令中。



![Screenshot from 2020-09-13 16-57-25](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-13 16-57-25.png)

There are difference between raw pdb and refined pdb.





---

# Let's say: Rosetta

<https://www.rosettacommons.org/demos/latest/Home>

options:

```
-in:file:s input_files/1qys.pdb
-in:file:l input_files/pdblist
-in:file:silent input_files/1qys_10.o
-ignore_unrecognized_res
-ignore_zero_occupancy false
@flag_input_relax
-in:path input_files
-constraints:cst_fa_file constrained_atompairs.cst
-in:file:native input_files/from_rcsb/1qys.pdb
-ignore_waters
-out:file:silent output_files/1qys.o
-in:file:silent input_files/1qys_10.o
-in:file:tagfile input_files/1qys_top3.tag
-out:file:scorefile_format json
-out:pdb
-out:prefix pre_
-out:suffix _suf
-out:path:all
-out:path:score
-out:path:pdb
-out:path:score /dev/null
-out:file:score_only output_files/score.sc
-out:level 0/100/200/300(default)/400/500
-run:jran 12345678
-run:constant_seed
```

comands:

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:l input_files/pdblist
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:silent input_files/1qys_10.o
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/from_rcsb/3tdm.pdb -ignore_unrecognized_res
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys_zero_occ.pdb -ignore_zero_occupancy false
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/from_rcsb/1qys.pdb @flag_input_relax
$ROSETTA3/bin/relax.default.linuxgccrelease -in:path input_files -in:file:s 4eq1.pdb -constraints:cst_fa_file constrained_atompairs.cst -ignore_unrecognized_res @flag_input_relax
$ROSETTA3/bin/score.default.linuxgccrelease -in:file:s input_files/1qys.pdb -in:file:native input_files/from_rcsb/1qys.pdb -ignore_waters
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/from_rcsb/1qys.pdb -out:file:silent output_files/1qys.o @flag_input_relax 
grep '^SCORE' input_files/1qys_10.o > output_files/1qys_silent_scores.sc
sort -k1,1 -k2n output_files/1qys_silent_scores.sc
$ROSETTA3/bin/extract_pdbs.default.linuxgccrelease -in:file:silent input_files/1qys_10.o -in:file:tagfile input_files/1qys_top3.tag
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:file:scorefile_format json
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:pdb -out:prefix pre_ -out:suffix _suf
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:pdb -out:path:all output_files
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:file:score_only output_files/score.sc @flag_input_relax
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:level 400
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/1qys.pdb -run:constant_seed -run:jran 12345678 @flag_input_relax
```





Is it suitable to pymol? on a cluster?