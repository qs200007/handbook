# Things have done

### **1 背景**

人类KCTD蛋白家族共包含21个成员，其N端包含一个BTB结构域，C端则大多为无序区。目前关于这个蛋白家族的功能所知甚少，一个主要原因是KCTD蛋白大多不易纯化。目前已知很多KCTD家族的突变体与人类神经发育疾病或是精神疾病有关。

![Screenshot from 2020-09-10 17-04-40](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 17-04-40.png)

![BTB_tree](/home/qs2000/work/handbook_pictures/BTB_tree.png)



一类研究比较深入的家族成员是KCTD12和KCTD16。这是郑三多老师在2019年的工作：

![Screenshot from 2020-09-10 17-07-26](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 17-07-26.png)

![Screenshot from 2020-09-10 17-23-50](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 17-23-50.png)



他们发现KCTD16的BTB结构域会形成开环五聚体结构(opened pentamer)，在内部结合一个GABAB2R（GABA受体，一个GPCR）的C端尾巴。而KCTD12和KCTD16的C端H1结构域则会结合Gβ/Gγ。在突触后膜上，GABA激活GABABR，进而使Gβ/Gγ结合GIRK，使K离子外流。随后，KCTD12、KCTD16抢夺Gβ/Gγ，使GIRK迅速关闭。总之，这篇论文给了我们两个启示：**1.KCTD蛋白家族可能在形成的五聚体内部结合不同的GPCR的C端尾巴。2.KCTD蛋白家族可能跟很多大脑中的GPCR信号传导有关。**

我现在的设想是通过已有结构或是modeller来建立一堆KCTD蛋白家族的开环五聚体结构；再寻找能结合到五聚体内部的肽链。

我现在粗略地看了一下GPCR的各C端序列。很可惜，他们并不像我预想的那样有较高的同源性，他们的序列长度相差很大。所以与其用现有的GPCR-CTD序列，不如直接以GABAB的CTD的scaffold来“替换”氨基酸。~~只是目前想法，因为Rosetta具体怎么用，能干什么我还不十分了解~~

### **2 KCTD蛋白家族BTB结构**



| Protein | PDB_ID | Polymer         |
| ------- | ------ | --------------- |
| KCTD1   | 6s4l   | 5c(BTB+other)   |
|         | 5bx5   | 5c              |
|         | 5bxd   | 5o              |
| KCTD5   | 3drx   | 5c(BTB+other)   |
|         | 3dry   | 5c(BTB+other)   |
|         | 3drz   | 5c              |
| KCTD9   | 5bxh   | 5c              |
| KCTD10  | 5fta   | 4               |
| KCTD13  | 4uij   | 4               |
| KCTD16  | 6oct   | 5o              |
|         | 6ocp   | 5o(BTB+GABAB_S) |
|         | 6ocr   | 5o              |
|         | 5a15   | 5o              |
|         | 6i0q   | 1               |
|         | 6m8r   | 5o(BTB+GABAB_L) |
| KCTD17  | 5a6r   | 5c              |

***5c: closed pentamer; 5o: opened pentemer***

表中为已有结构KCTD-BTB，此外还有一些电镜证据表明KCTD13有五聚体结构。

可以看到目前有开环五聚体结构的只有KCTD1和KCTD16。

我尝试将两者在pymol中align到了一起。

![Screenshot from 2020-09-10 22-27-43](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 22-27-43.png)

绿色为KCTD16和GABABR（6m8r），其他颜色为KCTD1（5bxd）。可以看到，KCTD1和KCTD16的scaffold几乎没有差别。但是一些关键位点，比如说KCTD16中结合GABAB尾巴的关键位点**F80**，在KCTD1中则是**Q82**。

![Screenshot from 2020-09-10 22-40-01](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 22-40-01.png)

***KCTD16和GABABR的结合，F80为关键位点***

![Screenshot from 2020-09-10 22-41-51](/home/qs2000/work/handbook_pictures/Screenshot from 2020-09-10 22-41-51.png)

***KCTD1和KCTD16的BTB结构域序列比对***

我个人认为在KCTD1上进行GPCR-CTD寻找会比KCTD13上进行寻找更容易/可信一些。

### **3 KCTD16和GABAB2的结构**

GABAB receptor由两部分组成：GABAB1 （961aa）和GABAB2（941aa）。结合KCTD16的是GABAB2（876-913）。插入的序列先形成一个α-helice，再接着一段无二级结构，再来一段α-helice，最后以无二级结构的方式离开KCTD16。

我现在猜测897-905（PILHHAYLP）a-helice是最重要的，相当于在两个P之间形成了一段α-helice。（而且二级结构预测也符合这段结构，就是说，我们是否能通过GPCR的二级结构预测来粗筛哪些可能结合呢？）

通过pymol，我发现插入的序列几乎都在F80的4A范围内。

### **4 重要文献**



**The Structural Versatility of the BTB Domains of KCTD Proteins and Their Recognition of the GABAB Receptor**

作者用GROMACS进行MD模拟，把KCTD家族有结构的几乎都做了一般。得出结论：1.各BTB结构稳定2.KCTD16和GABAB2复合体（6m8r，更长的一个）结构稳定3.GABAB2CTD倾向与形成与结合状态相符的二级结构。

幸运的是，他们并没有利用这些来做筛选。

我想知道，保持结构稳定的KCTD16和GABAB2复合体中GABAB2最短能有多少？将F80突变后，KCTD16和GABAB2复合体是否在MD中会表现出不稳定？把KCTD16和GABAB2复合体中的KCTD16替换成KCTD1，会不稳定吗？

**De novo design of picomolar SARS-CoV-2 miniprotein inhibitors preview**

David Baker组设计了一个能结合新冠病毒表面刺突的蛋白（RBD）。有很多东西可以学习。





# Things to do

### 1 查文献（GPCR C-terminal，KCTD1，Rosetta）

### 2 学Rosetta

### 3 尝试用GROMACS进行一些模拟

