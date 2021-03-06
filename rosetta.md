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
#!/usr/bash
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
#!/usr/bash
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

如上所述，每次我们可以只改**rosetta_sript.sh**中的那一行来跑rosetta程序。

第一个：

```
$REOSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb
```

输入一个pdb文件 **-in:file:s**

第二个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:l input_files/pdblist
```

输入多个pdb文件 **-in:file:l**

pdblist有：

```
input_files/1qys.pdb
input_files/1ubq.pdb
```

第三个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:silent input_files/1qys_10.o
```

输入silent文件 **-in:file:silent**

第四个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/from_rcsb/3tdm.pdb -ignore_unrecognized_res
```

第五个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys_zero_occ.pdb -ignore_zero_occupancy false
```

input的pdb需要先relax。写文件flag_input_relax：

```
-nstruct 2

-relax:constrain_relax_to_start_coords
-relax:ramp_constraints false

-ex1
-ex2

-use_input_sc
-flip_HNQ
-no_optH false
```

第六个：

```
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/from_rcsb/1qys.pdb @flag_input_relax
```

**@flag_input_relax** 相当于把文件中的每一行当参数传过去

第七个：

```
$ROSETTA3/bin/relax.default.linuxgccrelease -in:path input_files -in:file:s 4eq1.pdb -constraints:cst_fa_file constrained_atompairs.cst -ignore_unrecognized_res @flag_input_relax
```

第八个：

```
$ROSETTA3/bin/score.default.linuxgccrelease -in:file:s input_files/1qys.pdb -in:file:native input_files/from_rcsb/1qys.pdb -ignore_waters
```

第九个：

```
grep '^SCORE' input_files/1qys_10.o > output_files/1qys_silent_scores.sc
sort -k1,1 -k2n output_files/1qys_silent_scores.sc
$ROSETTA3/bin/extract_pdbs.default.linuxgccrelease -in:file:silent input_files/1qys_10.o -in:file:tagfile input_files/1qys_top3.tag
```

第十个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:file:scorefile_format json
```

第十一个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:pdb -out:prefix pre_ -out:suffix _suf
```

第十二个：

```
$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb -out:level 400
```

## 3 Full Atom Representation vs Centroid Representation

在centroid representation中，每一个residue变成N，CA，C，O，H，CB，CEN。用CEN这一个pseudo atom和CB代替residue的side chain。

转化centroid representation和full atom representaion需要用到XML script。

基本script.xml：

```
<ROSETTASCRIPT>
	<SCOREFXNS>
	</SCOREFXNS>
	<RESIDUE_SELECTORS>
	</RESIDUE_SELECTORS>
	<PACKER_PALETTES>
	</PACKER_PALETTES>
	<TASKOPERATIONS>
	</TASKOPERATIONS>
	<MOVE_MAP_FACTORIES>
	</MOVE_MAP_FACTORIES>
	<SIMPLE_METRICS>
	</SIMPLE_METRICS>
	<FILTERS>
	</FILTERS>
	<MOVERS>
	</MOVERS>
	<PROTOCOL>
	</PROTOCOL>
	<OUTPUT>
	</OUTPUT>
</ROSETTASCRIPT>
```

用法：

```
-parser:protocol script.xml
```

---

#### 把centroid转化为full atom。

写**cen_to_fa.xml**：

```
<ROSETTASCRIPTS>
	<SCOREFXNS>
	</SCOREFXNS>
	<FILTERS>
	</FILTERS>
	<MOVERS>
		<SwitchResidueTypeSetMover name="switch_representation" set="fa_standard" />
	</MOVERS>
	<APPLY_TO_POSE>
	</APPLY_TO_POSE>
	<PROTOCOLS>
		<Add mover="switch_representation">
	</PROTOCOLS>
</ROSETTASCRIPTS>
```

写**flag_from_cen_to_fa**：

```
-parser:protocol /home/zmqiu/work/rosetta_tutorial/centroid/cen_to_fa.xml

-score:weights cen_std_smooth
-in:file:s /home/zmqiu/work/rosetta_tutorial/centroid/input_files/1qys_centroid.pdb

#-out:path:pdb output_files
#-out:file:scorefile /dev/null  #to prevent output of the score file
```

写**rosetta_script.sh**：

```
#!/usr/bash
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1
#$ -tc 50
#$ -wd /home/zmqiu/work/rosetta_tutorial/centroid/result

echo "running~~"

IN_DIR=/home/zmqiu/work/rosetta_tutorial/centroid
ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source
ROSETTA3_DB=/home/soft/rosetta/gcc8_2019.35/main/database
ROSETTA3_TOOLS=/home/soft/rosetta/gcc8_2019.35/tools

$ROSETTA3/bin/rosetta_scripts.linuxgccrelease @$IN_DIR/flag_from_cen_to_fa
echo "ending"
```

运行：

```
qsub -q honda rosetta_script.sh
```

相当于：**rosetta_seript.sh ===>> flag_from_cen_to_fa ===>> cen_to_fa.xml**

#### 把full atom转化为centroid。

写fa_to_centroid.xml：

```
<ROSETTASCRIPTS>
	<SCOREFXNS>
	</SCOREFXNS>
	<FILTERS>
	</FILTERS>
	<MOVERS>
		<SwitchResidueTypeSetMover name="switch_fa_to_centroid" set="centroid" />
	</MOVERS>
	<APPLY_TO_POSE>
	</APPLY_TO_POSE>
	<PROTOCOlS>
		<Add mover="switch_fa_to_centroid" />
	</PROTOCOLS>
</ROSETTASCRIPTS>
```

写flag_from_fa_to_centroid：

```
-in:file:s /home/zmqiu/work/rosetta_tutorial/centroid/input_files/1qys.pdb

-score:weights cen_std_smooth
-nstruct 10                     #number of output structures
-parser:protocol /home/zmqiu/work/rosetta_tutorial/centroid/fa_to_cen.xml

#-out:path:all output_files
#-out:file:scorefile /dev/null  #to prevent output of the score file
```

写rosetta_script.sh：

```
#!/usr/bash
#$ -N qzm
#$ -V
#$ -m ea
#$ -M qiuzeming@nibs.ac.cn
#$ -t 1
#$ -tc 20
#$ -wd /home/zmqiu/work/rosetta_tutorial/centroid/result

echo "running"

IN_DIR=/home/zmqiu/work/rosetta_tutorial/centroid
ROSETTA3=/home/soft/rosetta/gcc8_2019.35/main/source

$ROSETTA3/bin/rosetta_scripts.linuxgccrelease @$IN_DIR/flag_from_fa_to_cen
```

提交任务：

```
asub -q honda rosetta_script.sh
```

得到的十个结构都是一样的。都是**N，CA，C，O，CB，H，CEN**。

```
ATOM      1  N   ASP A   3      -4.524  18.589  17.199  1.00  0.00           N  
ATOM      2  CA  ASP A   3      -3.055  18.336  17.160  1.00  0.00           C  
ATOM      3  C   ASP A   3      -2.676  17.087  16.375  1.00  0.00           C  
ATOM      4  O   ASP A   3      -3.539  16.391  15.835  1.00  0.00           O  
ATOM      5  CB  ASP A   3      -2.496  18.220  18.580  1.00  0.00           C  
ATOM      6  CEN ASP A   3      -2.022  18.783  19.285  1.00  0.00           X  
ATOM      7  H   ASP A   3      -5.003  18.619  18.076  1.00  0.00           H  
ATOM      8  N   ILE A   4      -1.373  16.806  16.326  1.00  0.00           N  
ATOM      9  CA  ILE A   4      -0.849  15.654  15.587  1.00  0.00           C  
ATOM     10  C   ILE A   4      -0.739  14.399  16.448  1.00  0.00           C  
ATOM     11  O   ILE A   4       0.070  14.317  17.374  1.00  0.00           O  
ATOM     12  CB  ILE A   4       0.535  15.962  14.987  1.00  0.00           C  
ATOM     13  CEN ILE A   4       1.064  16.400  14.140  1.00  0.00           X  
ATOM     14  H   ILE A   4      -0.728  17.411  16.816  1.00  0.00           H
```

---

## 4 The Paker

from <https://www.rosettacommons.org/demos/latest/tutorials/Optimizing_Sidechains_The_Packer/Optimizing_Sidechains_The_Packer>：

>#### The problem of optimizing side-chains
>
>A common task in Rosetta is the optimization of side-chains. We might think about the problem as follows: let's suppose that we have a structure (which we will call the **pose**) with a **fixed backbone conformation**. At each **position** in the structure, we have a list of discrete possibilities for the **side-chain**, which we call **rotamers**, where a rotamer is a particular conformation of a particular residue type's side-chain. We would like to **select one rotamer for each position such that the combination of rotamers represents the lowest-energy solution**. This is the problem solved by the packer.
>
>This problem is actually quite a difficult one: given N possibilities at each position in an M-residue protein, there are N to the power of M possibilities. This rapidly becomes an astronomical number of possibilities -- for example, 3 rotamers at each of 100 positions would be about 5x(10^47) possible combinations. This makes exhaustive enumeration（详尽的枚举） impossible. To solve this problem, the packer uses Monte Carlo methods (discussed in detail further on). This means that the packer is stochastic, that it never comes close to exhaustively exploring the search space in any but the smallest of packer problems, and that the solution returned, while likely to be a **good solution**, is not guaranteed to be the **best solution**.
>
>**Repeated packer runs are likely to yield a variety of similar solutions near the global optimum; none of these will necessarily be the best possible solution.**
>
>**The packer is therefore a powerful tool (and, indeed, the primary tool in Rosetta) for designing amino acid sequences.**

protein ===>> backbone + sidechain

sidechain ===>>amino acid + rotamer

```
the backbone is fixed
for position in sidechain:
	choose an amino acid for this position
	choose a rotamer for this amino acid
```

我们先用**fixbb**来做实验。（fixed back-bone）

```
rosetta/rosetta_source/bin/fixbb.default.linuxgccrelease -in:file:s 1l2y.pdb -resfile resfile.txt
```

其中**resfile.txt**：

```
start
    1 A PIKAA NT
    2 A PIKAA LA
    3 A PIKAA YL
    4 A PIKAA IL
    5 A PIKAA QL
    6 A NATAA
    7 A NATRO
    8 A PIKAA KL
    9 A PIKAA DL
   10 A NATRO
   11 A NATRO
   12 A PIKAA PS
   13 A PIKAA SK
   14 A PIKAA SA
   15 A NATRO
   16 A PIKAA RM
   17 A PIKAA PS
   18 A PIKAA PM
   19 A PIKAA PI
   20 A PIKAA SG
```

其中：

| Name  | description                                                 |
| ----- | ----------------------------------------------------------- |
| NATRO | natural rotamer. Nothing change.                            |
| NATAA | natural animo acid. Only change rotamer for this animo acid |
| PIKAA | pick animo acid.                                            |
| ALLAA | all animo acid. Change amino acid.                          |

packer是rosetta的核心之一，官网上有对它非常好的描述：<https://www.rosettacommons.org/demos/latest/tutorials/Optimizing_Sidechains_The_Packer/Optimizing_Sidechains_The_Packer>

> ##  How the packer algorithm works under the hood (for advanced users)
>
> While a detailed understanding of the workings of the packer is not a strict requirement to use Rosetta, having some idea of what's going on under the hood can help a user to use his or her tools more effectively. To understand the packer, we must first understand the concept of **Monte Carlo methods（蒙特卡洛方法）**.
>
> Broadly speaking, a Monte Carlo method is any method by which one attempts to solve an optimization problem using random or stochastic moves. In practice, most Monte Carlo methods in Rosetta make use of the **Metropolis-Hastings algorithm**, involving iterated steps in which one makes a random move that alters a pose in some way, considers the **change in the energy of a pose** as a result of the move, and then accepts or rejects that move based on a rule called the **Metropolis Criterion**. The Metropolis Criterion states that if the move results in a decrease in energy, it is always accepted, while if it results in an increase in energy, it is accepted with probability equal to **e-ΔE/(kbT)**. Practically, speaking, this means that the larger the increase in energy, the lower the likelihood of accepting the move, while at the same time allowing small increases in energy.
>
> The Metropolis Criterion contains a "temperature factor", **kbT**, that determines how the probability of rejecting a move that increases the energy scales with the magnitude of the increase. Large values of kBT allow moves that result in large increases in energy to be accepted frequently, while small values would only permit moves that result in small increases in energy to be accepted. While Monte Carlo searches can be carried out with fixed, arbitrarily-selected values of kBT, it is often advantageous to vary this value over the course of a simulation, using **high values early** on to permit the trajectory to climb barriers and **escape local energy minima**, and then lowering it later in a simulation so that the trajectory "drills down" to the **bottom of the lowest-energy well** that it has found. Often, many cycles of ramping kBT up and down can allow more complete exploration of the space. Monte Carlo approaches involving ramping of the kBT term are broadly called **simulated annealing methods**. Such methods often also involve conditional back-stepping to earlier parts of trajectories to escape dead ends, and other adaptive behaviours that increase the probability of converging to the lowest-energy state.
>
> With this understanding of general Monte Carlo and simulated annealing approaches in hand, let's look at what the packer does. A typical packer run consists of three steps:
>
> 1. TaskOperations are evaluated, and the packer makes a list of possible rotamers at each position.
> 2. The packer carries out a precomputation in which all possible pairs of interacting rotamers are enumerated and their pairwise interaction energies are calculated and stored.
> 3. The packer carries out a simulated annealing-based search of rotamer combinations, in which moves consist of randomly selecting a position and replacing the current rotamer at that position with a randomly-selected rotamer from the allowed rotamers for that position. Simulated annealing requires rapid computation of the change in energy resulting from the move. Because all pairwise interaction energies are precomputed, determining the change in energy of the structure following such a substitution is extremely fast, since it depends only on the internal energies of the old and new rotamers and on their pairwise interaction energies with their neighbours in the pose. This allows the packer to evaluate hundreds of thousands or millions of moves in seconds, permitting very long trajectories to be carried out very quickly.
>
> There are variants on the above behaviour in which only parts of the interaction network are precomputed and other parts are computed on the fly (what `-linmem_ig` does), but this general scheme is fairly representative. Note that the above depends heavily on being able to rapidly update the energy as moves are considered:
>
> **In order to be compatible with the packer, an energy term must either be residue-level pairwise-decomposable, or must otherwise be very fast to compute and update as rotamer substitutions are considered.**

参考：

<https://en.wikipedia.org/wiki/Simulated_annealing>

<https://blog.csdn.net/weixin_42398658/article/details/84031235>

<https://www.cnblogs.com/RyanXing/p/9469338.html>

**世有不可为，知耻而后勇**

