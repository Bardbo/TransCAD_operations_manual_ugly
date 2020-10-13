话不多说直接进入主题，6000字长文一起学习如何在交评项目中使用TransCAD，下面是十分详细的步骤（默认大家都了解四阶段模型，拥有相关的背景知识），由于喵喵君自身水平有限，难免存在疏漏和不正确的地方，欢迎大家交流讨论。更详细的资料请访问GitHub项目 地址为 https://github.com/Bardbo/TransCAD_operations_manual_ugly

本文所使用TransCAD为市面上流传很广的TC绿色版4.5

## 注意事项

+ 多个TransCAD文件存放于同一目录可能会导致数据相互影响，建议不同的TransCAD项目保存至不同位置
+ 重力模型不能完全反应真实情况，因此模型预测可能存在误差，实践中一般会进行人为修正

## 界面初识

![1.界面初识](https://i.loli.net/2020/10/13/SaEV5heqyAcw7bg.png)

本文将TransCAD界面做出如上图划分，下文中也会进行详细的操作指示，但是个人不建议沉陷于繁杂的操作中，而是将工程的实现与四阶段模型理论相结合，做出自己的理解（哈哈哈 本人也是理解有限哈，不喜欢做TC）

## 1. CAD准备

在CAD中打开路网图，使用di命令量取长度，使之符合现实长度（且不存在较短的路段，若存在较短的路径需要进行放大，命令sc，以免在TransCAD中打开时出现路段距离为0，若新建路段长度字段可无忽略此点）；

另存为AutoCAD R12/LT2 DXF（*dxf）格式，一般该格式位置在另存的最后一种文件类型

## 2. TransCAD中前期工作

1. 导入路网

File——Open——选择Files of type为dxf格式，在另存路径下找到路网文件——Open——在中间框中选中TC路网所在的层——点击Coordinates——class中选择Aisa——Zone中选择China：CIESIN Grid

Coordinates步骤及之后步骤可以省略

将导入的路网线层名字命名为路网，或者随便其它便于识别的名字，英文命名更好

2. 更改图层名字

在上方点击Map Layers图标——选中刚导入的路网线层——在下方点击Rename，弹出框中勾选Change Permanently，new name设置为line——回到之前框中选中Endpoints层——在左侧点击Show Layer，显示点层

3. 路网连通性检查

在上方将当前图层设置为line层（线层）——点击上方栏Tools——Map Editing——Check Line Layer Connectivity——在弹出框中设置Threshold为1——点击OK显示检查结果

若出现连通性错误，则可以在路网上看到标记，可使用上方图标Map Editing Toolbox进行编辑，点击箭头将错误处的两路段交点移动至一处，然后点击重叠后的交点复原路网，再点击绿色信号灯按钮保存

4. 创建小区层

Tools——Geographic Utilities——Line/Area Conventions——勾选Add Layer to Map——将File name命名为area

5. 划分交通小区

在上方栏中将当前图层设置为Areas，点击上方图标Map Editing Toolbox进行小区的划分

点击绿色➕号按钮可以设置小区，按原设定绘制，同时添加外部交通小区，绘制完毕后点击绿色信号灯保存（也可以边画边保存）

6. 给交通小区编号

点击右侧的Tools工具条中Select by Point图标（手指+箭头的图标），按小区编号依次点选内部小区（按住shift可累积选中）——点击上方图标Open Dataview打开数据表视图——上方栏中切换为Selection——点击上方栏Dataview——Modify table——在右边点击Add Field添加一列（用来存放小区编号），命名为newID——回到数据表视图，选中新建列的单元格，右键点击Fill——弹出框中选中Sequence填充——上方图标栏中点击Save File保存

同理给外部小区进行编号——点击Tools工具条中Select by Point图标依次选中外部小区——点击上方图标Open Dataview打开数据表视图——上方栏中切换为Selection（此时不需要新建小区编号列了）——选中单元格，右键点击Fill——弹出框中选中Sequence填充，将start设置为101（便于区分内部小区与外部小区）——填充，上方图标栏中点击Save File保存——确认替换

回到Map——点击空白处，取消外部小区的标记

![2.小区编号](https://i.loli.net/2020/10/13/VYayHvr9tZlnDXc.png)

7. 小区划分示意图的可视化

颜色显示：点击上方图标Color Theme MapWizard——Filed选newID（表示按新建的这一列数值来设置颜色）——OK

标签显示：点击上方图标Automatic Labels——Filed选newID——OK

点击Map Layers——隐藏点层和线层（选中图层，右侧点击Hide Layer）

截图保存或使用上方栏中File的打印功能（print）保存

![3.小区划分示意图](https://i.loli.net/2020/10/13/TWwIHRjo6clrY4K.png)

## 3. 导入路段属性

1. 准备工作

先显示被隐藏的点层和线层，然后移除可视化效果

显示隐藏图层：点击Map Layers——选中图层，右侧点击Show Layer

移除可视化：在上方栏切换当前图层为面层（因为之前是在面层上进行着色和可视化）——分别点击上方图标Color Theme MapWizard和点击上方图标Automatic Labels，点击弹出框中Remove按钮

2. 线层现状数据表构建

在上方栏切换当前图层为线层——右侧的Tools工具条中Select by Point图标——按照Excel路段属性表中的路段排序依次选择路段（按住shift连选）——点击上方图标Open Dataview打开数据表视图——上方栏中切换为Selection——点击上方栏Dataview——Modify table——在右边点击Add Field添加一列（用来存放路段编号），命名为newID——回到数据表视图，选中新建列的单元格，右键点击Fill——弹出框中选中Sequence填充——上方图标栏中点击Save File保存

不要关闭数据表视图（若关闭了重新打开即可）

仍点击Dataview——Modify table——分别添加 路名，AB_C，BA_C，SPEED，AB_T，BA_T ，AB_V，BA_V，AB_V/C ，BA_V/C 共10列，并设置对应列的Type（在此框Name的下方），其中路名的type设置为Character，AB_T，BA_T， AB_V/C ，BA_V/C 的type设置为Real（8bytes），这是因为路名是文本型，而时间阻抗（阻抗）和饱和度为浮点型

其中AB和BA表示道路的双向，C表示通行能力，容量，T表示阻抗或时间阻抗，V表示流量，V/C即饱和度，现状的数据均基于调查获取，此外也可以考虑使用地图软件等其它方式获取

保存与另存：选中newID列右键——Sort Increasing（进行排序）——上方图标栏Save保存——确认替换——点击上方栏File——Save as——选择另存的文件类型为dbf（dbf文件可使用Excel打开）——文件命名为导出数据.dbf(用来填写现状数据，然后导入TransCAD与线层数据表关联)，另存后不要关闭数据表（若关闭重新打开即可，注意对newID列进行排序）

3. 数据导入

进入导出数据.dbf文件所在目录，并使用Excel打开该文件——在前文新建的列中填入现状数据——更改sheet名字为sheet1（防止导入TransCAD时出现中文乱码）——文件另存为97-03版本的xls文件，文件名为导入数据.xls(所使用的TransCAD版本不支持xlsx文件)

回到TransCAD中点击上方栏File——Open——选择xls文件类型，打开刚另存的导入数据.xls——点击上方栏图标Join DataViews（锁链的连接图标）——在Join from和to的Field栏中均选择newID（表示以此列为公共列进行关联）——OK——在弹出的数据表中对newID进行排序，右键Sort Increasing——空白列右键点击Fill——选择Fomula——在弹出框FiledList中选择导入数据中的对应列（比如路名选择F列，其余均有导入数据的前缀标记）——点击OK，OK

同理导入其余列（AB_C，BA_C，SPEED，AB_T，BA_T ，AB_V，BA_V，AB_V/C ，BA_V/C）——导入完毕后点击上方图标Save保存——关闭此表和导入数据表，此时线层数据表中已经存在了刚填充的现状数据——关闭线层属性表

回到Map，点击空白处（取消线层路段的标记）——发现仍有一条被标记，可打开数据表去除标记，点击上方图标New DataView，在数据表最左侧找到标记的路段，单击该标记即可去除

![4.数据导入](https://i.loli.net/2020/10/13/89ET62lhsy4qORN.png)

## 4. 生成质心连杆

1. 在上方栏中设置当前层为面层——点击上方栏Tools——Map Editing——Connect——在弹出框中勾选Lines——OK

如果生成的质心连杆开口方向与现实不符，可以切换至线层，点击上方图标栏Map Editing Toolbox进行编辑（箭头移动，加号画线，禁止表示删除，信号灯红色和绿色分别为不保存和保存，双击左键结束绘制，过程中需要取消可使用ESC键），如果要去除多余的点，可以使用箭头将其移动至已有点上进行融合

2. 将当前层设置为线层——点击上方图标Open DataView打开数据表——选中newID列右键对其排序（Sort Increasing）——选中AB_C和BA_C列的空的部分（新的质心连杆），右键点击Fill——在Single Value中输入9999填充——选中AB_T和BA_T列的空的部分，右键点击Fill填充0.01——点击上方Save图标保存——确认替换

此处，是因为质心连杆并不是真实的道路，拥有无穷大的容量，和无穷小的阻抗，此处使用9999和0.01代替，表示出行从质心出生成

![5.质心连杆数据填充](https://i.loli.net/2020/10/13/EJNBMvxdagwL4Gz.png)

## 5. 现状OD反推

1. 构建先验矩阵与构建网络

切换当前层为点层（Endpoints）——点击右侧的Tools工具条中Select by Point图标——按小区编号顺序依次标记质心，按住shift可连选（如果忘记了小区编号顺序，可切换到面层进行标注，Automatic Labels图标）——在上方图标中打开数据表——在上方栏中切换为Selection——点击上方栏DataView——Modify Table——新建一列（用于存储小区质心的编号），命名为Index——回到数据表，按小区编号对Index列进行填充（注意前文外部小区编号从101开始）——上方Save保存

为防止没注意时取消了质心的标记，建议在右侧的Tools工具条中点击Pan图标（抓手图标）

构建网络：切换当前层为线层——点击上方栏Networks/Paths（如果没有此菜单，可在上方栏Procedures中点击Show all显示）——Create——在弹出框的左右两栏里点击鼠标左键下拉全部选中——点击OK，命名为luwang

构建先验矩阵：切换当前层为点层——点击上方栏File——New——在弹出框中下拉找到Matrix——OK，设置IDs are in 为之前建立的Index列——点击OK，命名为 先验OD

在弹出的空白矩阵中，右键Fill——Value中填1——OK——右键Fil——Value中填0，下方Cell to Fill选中Diagonal（在对角线填充0）

此处的先验OD是设置OD表的先验形式，即小区内部间视作无出行

2. OD反推

回到Map中，打开点层的数据表——在上方栏切换为Selection——在数据表的最左侧，点击取消项目所在地小区的标记（此次为1小区和2小区）——上方栏点击保存——确认替换，关闭数据表

回到先验OD矩阵，右键Indices——在弹出框右侧点击Add Index——在弹出框中将第一个Fied设置为Index，Selection设置为Selection，点击OK——回到了第一个弹出框中，在框上方Current Indices设置Rows和Columns为刚刚增加New——Close

此处，是因为现状项目所在地为空地，没有出行，因此在OD反推前将其取消标记，不参与OD反推

回到Map中，切换当前层为线层——点击上方栏Planing——OD Matrix Estimation——在弹出框中，分别设置Time，Capacity，Count为*_T, C, V——在Globals中设置Iterations迭代次数为200——OK——在弹出框中依次设置两个结果的保存路径，Save As更改为本次TransCAD文件存储路径，在命名时加上 现状 二字（用以区分）——OK

此处，如果出现结果为红色信号灯，则表示出错了，可以点击Show Warnings查看是哪里出错了，一般出错均为数据出现0值，无法计算所致，常见的有路段长度为0，阻抗为0等，前往线层的数据表找到Warnings中提示的对应出错路段对相应值进行更改，然后重新构建路网，重新反推即可

得到反推后的现状OD矩阵——选中项目小区所在行，右键Fill——Value设置为0，进行填充——调整列宽，截图保存为现状OD——在上方栏点击File——Save As 另存为，命名为现状OD

选中生成的另一个文件，line+ODMELINKFLOW现状的数据表——右键newID列进行排序——点击上方栏File,Save as 另存为现状.dbf，以供后面使用或以后查看

## 6. 现状期望线绘制

回到Map中，切换当前层为面层（此时现状OD矩阵为打开状态）——点击上方栏Tools——Geographic Analysis——Desire Lines——在弹出框中将New Layer中的Name设置为现状期望线，下方的From和to均设置为ALL Records，ID Field设置为newID——OK，命名为现状期望线——截图保存为现状期望线

截图后可以隐藏期望线，点击上方图标栏Map Layers——选中期望线层，右侧点击Hide Layer

## 7.背景OD和背景期望线

所谓的背景OD实际上就是将来值（但是仍然不含项目所在地小区的出行），还记得福莱特法等吗？（想起了受手算福莱特法等支配的恐惧），实际上就是对未来预测后然后求一个未来的OD分布。

1. 背景OD矩阵与背景期望线

本次直接对现状OD使用增长系数（一般取3%，4%，5%等，根据实际情况调整），回到现状OD矩阵——右键Fill，Value中输入1.04，勾选Multiply cells by value，Cell to Fill中选中All（即增大4%）——OK——点击上方栏File，Save as另存，命名为背景OD——截图保存为背景OD

如前文绘制现状OD期望线所述，回到Map中，切换当前层为面层（此时背景OD矩阵为打开状态）——点击上方栏Tools——Geographic Analysis——Desire Lines——在弹出框中将New Layer中的Name设置为背景期望线，下方的From和to均设置为ALL Records，ID Field设置为newID——OK，命名为背景期望线——截图保存为背景期望线

截图后可以隐藏期望线，点击上方图标栏Map Layers——选中期望线层，右侧点击Hide Layer

此处，实际上由于是使用的固定增长系数，因此背景期望线会与现状期望线完全一致

2. 背景路段属性（分配）

之前现状是由路段属性反推OD矩阵，此时背景改为OD矩阵求取路段属性

回到背景OD矩阵，右键点击Indices——在框上方Current Indices设置Rows和Columns为New——Close——回到Map中，切换当前层为线层——点击上方栏Planing——Traffic Assignment——选中line层，点击ok——在弹出框中，设置Time为*_T，Capacity为C，设置Globals中Iterations迭代次数为200——OK，文件命名时添加 背景 二字（用以区分）

在生成的数据表中，对newID进行排序——查看分配结果中的流量和饱和度等是否与预期相符合（如果不符合的话，可以将背景OD矩阵中数值进行调整，如更换增长系数值等，重新分配）——点击上方图标栏Save保存按钮——点击上方栏Flie——Save as——另存为dbf文件，命名为 背景.dbf

## 8.叠加OD

之前的背景OD是仍未添加项目所在地小区的预测发生和吸引的，此时叠加OD即表示补充了项目所在地小区的出行

1. 将Indices改回来，此时项目所在地小区需要参与分配了

回到背景OD矩阵，右键Indices——在框上方Current Indices设置Rows和Columns为Endpoints（all）

回到Map中，切换当前层为点层——打开数据表——点击Index进行排序——在数据表左侧重新标记项目所在地小区——保存——确认替换

2. 计算项目所在小区的OD（OD总量已知，分布未知）

这部分的语言描述相对复杂，仅介绍原理（计算过程可参考文末提供的Github地址）

实际上是使用重力模型进行分布，即按照各小区距离的比例进行分配，距离远的分少点，距离近的分多点（成反比，直接取倒数即可），同时需要注意，内部小区和外部小区需要分配不同的比例，最终保证OD总量守恒即可。

3. 将计算好的项目所在地OD填入背景OD矩阵中，获得叠加OD矩阵

可以手动输入，但比较麻烦，此处使用矩阵导入的方式输入

背景OD矩阵导出：选中背景OD矩阵——在上方栏点击Matrix——在弹出框中勾选Row in matrix ODME——OK，命名为 矩阵导出.dbf

使用Excel打开矩阵导出.dbf——复制填入计算好的项目所在地小区OD——更改sheet名字为sheet1（防止导入TransCAD时中文乱码错误）——另存为97-03版本的xls文件，文件名为矩阵导入.xls

回到TransCAD中，点击上方栏File——Open——打开刚保存的矩阵导入.xls——先选中背景OD矩阵的第一列，然后选中矩阵导入的对应列——点击上方栏Matrix——Import——Next——将弹出框中的Column ID设置为F——Next——将弹出框中的All columns勾选，From设置为F1——Finish——数据导入成功后，点击上方栏File——Save as，另存命名为叠加OD——截图保存为叠加OD

4. 叠加OD的分配

在矩阵中右键Indices（此时Map中当前图层为点层）——Add Index——在弹出框右侧点击Add Index——在弹出框中将第一个Fied设置为Index，Selection设置为Selection，name设施为New1，点击OK——回到了第一个弹出框中，在框上方Current Indices设置Rows和Columns为刚刚增加New——Close

回到Map中，切换当前层为线层——点击上方栏Planing——Traffic Assignment——选中line层，点击ok——在弹出框中，设置Time为*_T，Capacity为C，设置Globals中Iterations迭代次数为200——OK，文件命名时添加 叠加 二字（用以区分）

在生成的数据表中，对newID进行排序——点击上方图标栏Save保存按钮——点击上方栏Flie——Save as——另存为dbf文件，命名为 叠加.dbf

## 9. 将背景数据和叠加数据添加到线层属性表

目的是可视化饱和度，做出背景和叠加的饱和度图，查看项目叠加后是否对交通影响显著

1. 构建字段

回到Map中（当前层为线层）——打开数据表——在上方栏中点击DataView——Modify Table——添加AB_V1，BA_V1，AB_V1/C，BA_V1/C，AB_V2，BA_V2，AB_V2/C，BA_V2/C，共8列，设置Type为Real（8bytes）类型——对newID进行排序——上方图标栏点击保存——上方栏File，Save as 另存为 最终导出.dbf

2. 数据导入

在Excel中打开最终导出.dbf，背景.dbf，叠加.dbf，将背景.dbf中的AB_Flow,BA_Flow,AB_VOC,BA_VOC复制到最终导出.dbf的AB_V1，BA_V1，AB_V1/C，BA_V1/C列中（即填入背景的流量和饱和度），同理将叠加.dbf中的流量和饱和度填入最终导出.dbf中——设置sheet名字为sheet1——文件另存为97-03版本的xls文件，文件名为最终导入.xls

在此步骤，如果认为饱和度等不符合现实，可以人为的适当调整

3. 在TransCAD中连接入线层

回到TransCAD中点击上方栏File——Open——选择xls文件类型，打开刚另存的最终导入.xls——点击上方栏图标Join DataViews（锁链的连接图标）——OK——在弹出的数据表中对newID进行排序，右键Sort Increasing——空白列右键点击Fill——选择Fomula——在弹出框FiledList中选择最终导入中的对应列

导入完毕后——点击上方保存图标进行保存

4. 可视化

+ 背景饱和度的可视化

回到Map，切换当前层为线层——点击上方Scaled-Symbol Theme MapWizard图标（星星图标）——在弹出框中选则AB_V1/C，点击OK——点击上方Color Theme MapWizard图标——在弹出框中选择AB_V1/C，设置Class为6，点击框中右下Manual——设置不同级别的范围（见下图）——然后点击该框上方的style按钮——设置不同饱和度的线条宽度，设置色带，为各级别分别设置1.5，2.5，3.5，4，4，4的线宽，设置为绿色至红色的色带（见下图）

设置标签：点击上方Automatic Labels图标——Field中选择[AB_V1/C]/[BA_V1/C]

双击图例——点击上方Contents——设置Subtitle为背景饱和度

在上方Map Layers图标中隐藏面层——截图保存为背景饱和度图

![6.1饱和度设置](https://i.loli.net/2020/10/13/oKJACdnbiLlM9hI.png)

![6.2](https://i.loli.net/2020/10/13/rGhIdNRw2j8TaZW.png)

+ 叠加饱和度的可视化

与前面一致，将饱和度替换为对应的AB_V2/C即可，但在这之前需要移除之前的可视化效果（点击相应图标的remove即可），如果不进行移除，背景饱和度的可视化可能仍会覆盖叠加饱和度的可视化（因此，重新设置级别范围等）

此处，如果可视化结果不对，比如相同路段的饱和度不一致（不连续），可以使用右侧Tool工具条中的图标i（info）对相应路段进行修改（当然，在线层的数据表中修改也是一样的）

此处，实际上路段是可以过饱和的，即饱和度大于1，各地的交评套路不一样，不要照搬操作，而是要灵活的运用，有时是不需要中间很多操作的~，此外本文仅介绍了路段的饱和度，并未对交叉口进行相关评价。

本次的分享就是这个咯，目前公众号未开通留言，以后会考虑建群交流的哈~































