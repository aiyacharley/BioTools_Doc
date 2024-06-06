
BioTools: 一个基于R语言shiny框架的生物信息学综合分析Web应用
===========================================================

介绍
------------
[BioTools网页工具](https://tool.gigaomics.com/BioTools/) 是一个基于R语言shiny框架开发的生物信息分析流程工具，目前包括表型分析（Phenotype）、丰度表分析（Single-omics）、菌群功能富集分析（MicrobiomeProfiler）、基因富集分析（DEGs enrichment），基因集富集分析（GSEA）等模块。

 - 维护人员：Chengrui Wang
 - 联系邮箱：chengrui.wang@gzucm.edu.cn
 - 版本号：2024.05.23

分析模块
------------

### 表型分析(Phenotype)
*(输入文件格式：行是样本，列是特征)*
上传文件后需要注意设置哪一列作为行名（`Column as rowname`），文件分隔符类型（逗号Comma, 制表符Tab, 空格Space），指定哪些变量为因子类型格式（`Specify which variables are factor types, one per line`），标准化方法（不做标准化none，powerTransform包做Box-cox标准化，bestNormalize选择最优的方式标准化），并提供下载示例数据。
表型分析模块允许用户浏览数据，汇总数据{*skimr*}，指定因子变量，标准化数值变量{*powerTransform*，*bestNormalize*}及其相关性热图{*linkET*}，并使用{*autoReg*}进行描述统计和逻辑回归分析。

以下是表型分析(Phenotype)各个分析部分的介绍：

 - **data**: 上传文件，指定分隔符、行名，支持。
 	- **Data**: 展示原始数据，默认显示前10行，可以选择10，25，50，100行展示。
 	- **Data skim**: 提供快速数据的大致概述，使用R包{*skimr::skim*}描述，对于连续型变量给出均值、标准差、四分位数及分布缩略图，对于因子类型/分类型变量给出每一因子/类的数量。
 - **Description**: 使用{*autoReg*}包的*gaze*函数，生成多个变量的分组描述性统计表，组间差异检验方法用到了t-test, ANOVA, Kruskal-Wallis, chi-squared, Fisher's,...等方法，支持用户自定义输入公式和对连续型/分类型变量选择不同分层的检验策略，详细使用说明可以点击上方的see detail软连接跳转官方说明文档。
 - **Logistic regression**: 使用{*autoReg*}包的*autoReg*函数自动进行单变量、多变量回归和逐步逻辑回归分析，支持用户自定义输入公式及其他可选参数，结果包括对logistic模型总结，单因素及多因素回归结果，混淆矩阵，ROC曲线图和列线图。详细使用说明可以点击上方的see detail软连接跳转官方说明文档。
 - **Correlation**: 使用{*linkET*}包对其中的数值型变量做相关性热图，可以选择计算相关性的方法，包括spearman, pearson和kendall方法，此外可以指定分组变量来显示各组的相关性。如果在Single-omics模块上传的组学丰度表并勾选correlative with Single-Omics选项，Correlation plot模块展示表型和组学之间的相关性，还可以执行Mental分析，建议筛选差异的特征进行分析，否则过多的变量会导致运行缓慢。

### 丰度矩阵分析(Single-omics)
*(输入文件格式：行是特征，列是样本)*
上传文件后需要注意选择文件分隔符类型（逗号Comma, 制表符Tab, 空格Space），**Mean cutoff**参数过滤行的均值阈值，**Prevalence cutoff**参数过滤行的非0流行度阈值，可以简单通过这两个阈值把低丰度低流行度的行去除。勾选**Re-scaled**参数则将每一列重新标化为总和为100的相对数值。勾选**is.metaphlan_name**则是当输入文件的行名为metaphlan格式（k__|p__|o__|c__|f__|g__|s__|t__）时使用，进一步选择不同taxonomy层级的菌。本模块同样提供下载示例数据。
**Grouping parameters**子模块是分析丰度数据过程中需要分组分析，可以上传相应的分组文件，可以在**Grouping Variable**和或**Stratificate Variable**子模块选择分组变量和或分层变量，这两个选择并不死板，有时候可以交换来探索你的数据，一旦选择了某一分组变量就可以在**Group rank**中自定义选择某些亚组及这些组的排序，分层变量同理。随之而来的，在之后的结果中也会有分组或分层的体现。注意确保丰度矩阵文件的列名和表型文件的行名都是样本名并保持一致。
**microeco taxa table**子模块是可选的功能，用户可以提供菌群丰度表的各层级taxonomy文件，提供之后效果和metaphlan格式文件类似，对于16s数据分析这个文件是需要的。
**ggplot2 parameters**子模块是调整ggplot2绘图参数的面板，目前提供的功能是调整图片字体大小。

以下是丰度矩阵分析(Single-omics)各个分析部分的介绍：

 - **Raw data**: 上传一个组学丰度矩阵文件，每一行是一个特征（基因、菌、代谢物等），每一列是一个样本。展示的结果类似于**Phenotype/data**模块。Data skim 展示的结果类似于**Phenotype/Data skim**模块。
 - **Composition**: 以堆叠柱状图的形式展示排名前N（默认是前10）的特征丰度，并且可以选择各组特征聚合的算法（均值mean或中位数median）。如果上传分组信息则还会展示分组/分层的堆叠柱状图。
 - **alpha diversity**: 使用{*vegan*}包计算alpha多样性指数（包括物种丰富度、香浓指数Shannon index、辛普森指数Simpson index、Chao指数）并以箱线图+小提琴图+点图展示和下载多样性计算结果文件。如果上传的是相对丰度表，这里相对丰度值将乘以10000并取整近似计算alpha多样性指数。如果上传分组信息则还会展示分组及对组间差异检验的结果，同时可以选择差异检验的算法wilcox.test和t.test，勾选signif_label选择是否显示具体的检验p值。
 - **Dimension reduction**: 降维分析
 	- **PCA**: 使用｛*prcomp*｝函数对给定的数据矩阵执行主成分分析，并使用{*factoextra*}包展示结果，包括PCA降维图，各特征对第一、二轴主成分的贡献度。如果上传分组信息则还会展示分组情况。
	- **PCoA**: 通过{*ape::pcoa*}函数计算距离矩阵的主坐标分析，该分析使用{*vegan::vegdist*}计算相异矩阵，dissimilarity index参数提供可选的距离计算方法，并使用{*vegan::adonis2*}函数进行组间PERMANOVA检验，两两组间PERMANOVA检验使用{*pairwiseAdonis::pairwise.adonis*} (https://github.com/pmartinezarbizu/pairwiseAdonis) 实现，此外PCoA图可以选择是否展示样本名（show sampleName参数）和椭圆置信区间（show ellipse参数）。
	- **CPCoA**: 限制性主坐标分析（Constrained Principal Coordinates Analysis），是一种多变量统计分析方法，它在主坐标分析（PCoA）的基础上增加了约束条件，在考虑自定分组信息的情况下，寻找能够最大解释组间差异的平面。它允许研究者在分析中加入分组约束，以探索不同组别样本间的差异性。CPCoA分析的结果可以展示在指定分组条件下的解析率，通常要求至少有3组样本才能进行此类分析。本分析使用{amplicon:beta_cpcoa}函数实现。
	- **PLS-DA**: （Partial Least Squares Discriminant Analysis）是一种统计分析方法，它结合了偏最小二乘回归（Partial Least Squares，PLS）和判别分析（Discriminant Analysis，DA）的技术，旨在找到数据中可以区分不同组别的成分。它通过PLS方法提取特征，然后使用这些特征进行判别分析，以区分不同的组别或类别。本分析使用{ropls:opls}函数实现，并提供额外的ggplot2形式的图结果展示，此外还有该函数下的PCA和OPLS-DA的分析结果和报告信息。
 - **Heatmap**: 使用{*pheatmap*}包绘制丰度矩阵的聚类热图。如果上传分组信息则还会展示列的标注情况，如果输入的菌群丰度表是metaphlan格式（k__|p__|o__|c__|f__|g__|s__|t__）则还会展示的菌群水平的分组情况。其他可选参数包括：是否显示行列名，是否对行列进行聚类，是否做对数转换，Scale是否对行或列进行中心化，计算距离的方法，聚类的方法等，以及支持在**Selected Features, one per line**中输入特定某些特征进行展示。
 - **diff test**: 差异检验
 	- **Simply Test**: 该模块执行简单的分组差异检验，需要上传分组信息，两两检验方法可选t.test/wilcox.test方法，多组检验方法是kruskal.test方法。
 	- **fastANCOM**: 使用{*fastANCOM*} (https://github.com/ZRChao/fastANCOM) 用来分析微生物组数据，特别是用于识别在不同组别间具有显著差异的微生物种类，实现了基于Mann-Whitney U统计的快速ANCOM版本。fastANCOM包括数据预处理步骤，例如检测结构性零（structural zeros）、移除异常值、删除稀有的操作分类单元（OTU）或测序深度低的样本。
 	- **Abu boxplot**: 以箱线图形式展示某一个特征的分布情况，同样上传分组文件可以展示不同分组的情况。
 - **DESeq2**: 从高通量测序分析中估计方差-均值依赖性，并基于负二项分布模型检验差异表达。常用于转录组read count数据做差异检验，需要输入的是整数数据，我们提供了转换成整数的选项（as.Integer），具体操作是平移到非负数再用原始值除以最小数再取整。必填参数是`design formula`公式，最简单的例子是`~Group`，组间比较的参数`contrast`书写格式是[分组变量名,处理组,空白组]。帮助文档可以查看[DESeq2 Help Doc](https://bioconductor.org/packages/devel/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)，分析结果包括差异检验的数据表格，火山图和某一特征的表达丰度。
 - **microeco**: microeco包快速模块化分析微生物组数据，支持分析16s和宏基因组数据。目前整合了三个分析子模块功能，分别是丰度分析（microeco_abund），多样性分析（microeco_diversity），差异分析（microeco_diff），详细的官方[帮助文档](https://chiliubio.github.io/microeco_tutorial/intro.html)
 	- **microeco_abund**: （需要microeco taxa table上传taxonomy文件或metaphlan格式菌名，单一层级菌名的文件又没有上传taxonomy文件会无法执行分析）丰度组成分析模块，提供选择分析菌群某一层级（`taxa_level`，默认门Phylumn水平），显示前10个菌群丰度（`show number`），自定义分面变量（`Customized facets`），取均值聚合变量（`Aggregate group (mean)`）和是否显示菌名称。详细介绍查阅官方文档的`Chapter 4 Composition-based class`模块。
 	- **microeco_diversity**: 物种多样性分析模块，包括alpha多样性和beta多样性，alpha多样性模块提供`measure`参数选择多样性指数，`cal_diff method`参数检验差异方法和`as.Integer`参数是否转换成整数矩阵；beta多样性模块提供`measure`参数选择多样性指数，`ordination`参数降维方法和绘图类型`plot_type`多选参数。详细介绍查阅官方文档的`Chapter 5 Diversity-based class`模块。
 	- **microeco_diff**: 菌丰度差异分析模块，`trans_diff methods`参数选择差异检验的方法，`p_adjust_method`参数选择p值校正方法，`taxa_level`参数默认是all，可以选择不同层级的菌群，但`plot_diff_cladogram`分析必须要求`taxa_level=all`。差异结果在`res_diff`面板中，差异菌条形图和丰度差异条形图分别在`plot_diff_bar`和`plot_diff_abund`面板中，`plot_diff_cladogram`面板是多层级菌群差异结果图。详细介绍查阅官方文档的`6.1 trans_diff class`模块。
 - **predictModel**: 使用Boruta包筛选特征变量+caret包构建预测模型，预测模型重要参数`formula`公式输入，比如纳入所有变量预测Group结局可以输入`Group~.`，默认是随机森林rf模型，数据预处理可以选择{caret::nearZeroVar}剔除低方差特征：删除只有一个唯一值(即零方差)的特征或同时具有以下两个特点的特征：它们具有很少的唯一值，并且最常见值的频率与第二最常见值的频率之比很大、{caret::findCorrelation}剔除高相关性变量（cutoff=0.9）：如果两个变量具有很高的相关性，该函数将查看每个变量的平均绝对相关性，并删除具有最大平均绝对相关性的变量、{caret::findLinearCombos}剔除共线性变量：枚举和解析数值矩阵中的线性组合。接下来选择交叉验证的参数，默认是5折交叉验证且每一折重复5次，75%的样本用于模型训练和交叉验证（训练集），25%的样本用于验证（测试集），这个比例支持用户自己调整。该模块目前适用二分类结局预测，对多分类结局建议先处理成二分类，对回归预测模型还未进行系统性测试，可能会有bug。
 	- **Feature Selection (Recommended)**: Boruta包筛选重要特征可以查看[介绍](https://mp.weixin.qq.com/s/1OGNnJLjiS_YmEH0WqKrLQ)，建议选择Boruta筛选变量步骤，筛选特征程度可选宽松loose和严格strict两种；
 	- **Tuning parameters (Optional)**: 网格调参，需要熟悉选择的模型支持哪些参数和如何设置，这里不建议初学者自定义，默认是随机森林rf模型的mtry=c(1:10)参数，并返回最优准确率的参数值；
 	- **Train data (Accuracy & Kappa)**: 训练集数据交叉训练的准确率和一致性kappa值结果；
 	- **varImp**: 预测模型中各特征重要性；
 	- **SHapley Additive exPlanations (SHAP)**: 机器学习模型可解释性SHAP值计算和可视化结果。
 	- **Test data (confusionMatrix & auc_roc)**: 测试集数据混淆矩阵和ROC曲线结果，此外支持上传额外的独立验证数据集。
 - **Network analysis**（待部署）: 使用{*bootnet*}网络分析，它通过使用{*estimateNetwork*}函数使用各种R包和模型框架来灵活估计网络结构，并且用{*qgraph::centralityPlot*}解释中心性和聚类系数，具体介绍方法可以看[这里](https://mp.weixin.qq.com/s/TjkwPbZ925b6lMGhy3WPgw)。

### 菌群功能富集分析（MicrobiomeProfiler）
*(输入格式：基因列表，一行一个)*
调用程序是R包[MicrobiomeProfiler](https://bioconductor.org/packages/release/bioc/vignettes/MicrobiomeProfiler/inst/doc/MicrobiomeProfiler.html)，一个基于ClusterProfiler的微生物组数据功能丰富工具,分析提供了几个可选丰富的功能：

 - **enrichKO**: 用于菌群数据中KO IDs富集分析；
 - **enrichModule**: 用于菌群数据中KEGG Module富集分析；
 - **enrichCOG**: 用于菌群数据中COG富集分析；
 - **enrichHMDB**: 用于菌群代谢物HMDB IDs富集分析；
 - **enrichMBKEGG**: 用于菌群代谢物KEGG IDs富集分析；
 - **enrichSMPDB**: 用于菌群代谢物smpdb IDs富集分析；
 - **enrichMDA**: 用于菌群-疾病关联富集分析；

 以上分析均支持用户自己提供背景基因集`Select Universe Gene Set`参数，并提供示例数据方便快速上手。


### 差异表达基因富集分析(DEGs enricher)
*(输入格式：基因列表，一行一个)*
调用程序是R包[clusterProfiler](https://yulab-smu.top/biomedical-knowledge-mining-book/)

 - **enrichGO**: 使用{*clusterProfiler::enrichGO*}对给定的基因列表进行基因本体论（GO）富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果。当前支持分析物种有("hsa","mmu","rno","dre")。
 - **enrichKEGG**: 使用{*clusterProfiler::enrichKEGG*}对给定的基因列表进行KEGG通路富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果当前支持分析物种有("hsa","mmu","rno","dre")。
 - **enrichDO**: 使用{*DOSE::enrichDO*}对给定的基因列表进行疾病本体论（DO）富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果。当前支持分析物种有("hsa","mmu")。

### 基因集富集分析(GSEA)
*(输入文件格式: 排序的有值基因列表。无表头，两列，一列是基因名，一列是数值，可以是倍数变化值FoldChange、差值或其他)*

 - **gseGO**: 使用{*clusterProfiler::gseGO*}对给定的基因列表进行基因本体论（GO）基因集富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果。当前支持分析物种有("hsa","mmu","rno","dre")。
 - **gseKEGG**: 使用{*clusterProfiler::gseKEGG*}对给定的基因列表进行KEGG通路基因集富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果。当前支持分析物种有("hsa","mmu","rno","dre")。
 - **gseDO**: 使用{*DOSE::gseDO*}对给定的基因列表进行疾病本体论（DO）基因集富集分析，并以Dotplot/barplot/treeplot/emapplot/cnetplot图片形式展示结果。当前支持分析物种有("hsa","mmu")。
 - **GSEA plot**: 使用{*enrichplot::gseaplot2*}绘制GSEA图，默认上图展示前5个基因集的结果，下图展示特定基因集。


### 彩蛋
点击[BioTools](https://tool.gigaomics.com/BioTools/)网站右下角魔术棒，召唤AI助手😊
