# AIOps在美团的探索与实践——故障发现篇

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">一、背景</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">AIOps，最初的定义是Algorithm IT Operations，是利用运维算法来实现运维的自动化，最终走向无人化运维。随着技术成熟，逐步确定为Artificial Intelligence for IT Operations——智能运维，将人工智能应用于运维领域，基于已有的运维数据（日志、监控信息、应用信息等），通过机器学习的方式来进一步解决自动化运维无法解决的问题。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">早期的运维工作大部分是由运维人员手工完成的，手工运维在互联网业务快速扩张、人力成本高企的时代，难以维系。于是，自动化运维应运而生，它主要通过可被自动触发、预定义规则的脚本，来执行常见、重复性的运维工作，从而减少人力成本，提高运维的效率。总的来说，自动化运维可以认为是一种基于行业领域知识和运维场景领域知识的专家系统。随着整个互联网业务急剧膨胀，以及服务类型的复杂多样，“基于人为指定规则”的专家系统逐渐变得力不从心，自动化运维的不足，日益凸显，当前美团在业务监控和运维层面也面临着同样的困境。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">DevOps的出现，部分解决了上述问题，它强调从价值交付的全局视角，但DevOps更强调横向融合及打通，AIOps则是DevOps在运维（技术运营）侧的高阶实现，两者并不冲突。AIOps不依赖于人为指定规则，主张由机器学习算法自动地从海量运维数据（包括事件本身以及运维人员的人工处理日志）中不断地学习，不断提炼并总结规则。AIOps在自动化运维的基础上，增加了一个基于机器学习的大脑，指挥监测系统采集大脑决策所需的数据，做出分析、决策，并指挥自动化脚本去执行大脑的决策，从而达到运维系统的整体目标。综上看，自动化运维水平是AIOps的重要基石，而AIOps将基于自动化运维，将AI和运维很好地结合起来，这个过程需要三方面的知识：</font>

1. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">行业、业务领域知识，跟业务特点相关的知识经验积累，熟悉生产实践中的难题。</font>
2. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">运维领域知识，如指标监控、异常检测、故障发现、故障止损、成本优化、容量规划和性能调优等。</font>
3. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">算法、机器学习知识，把实际问题转化为算法问题，常用算法包括如聚类、决策树、卷积神经网络等。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">美团技术团队在行业、业务领域知识和运维领域的知识等方面有着长期的积累，已经沉淀出不少工具和产品，实现了自动化运维，同时在AIOps方面也有一些初步的成果。我们希望通过在AIOps上持续投入、迭代和钻研，将之前积累的行业、业务和运维领域的知识应用到AIOps中，从而能让AIOps为业务研发、产品和运营团队赋能，提高整个公司的生产效率。</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">二、技术路线规划</font>
### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">2.1 AIOps能力建设</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">AIOps的建设可以先由无到局部单点探索，在单点探索上得到初步的成果，再对单点能力进行完善，形成解决某个局部问题的运维AI学件，再由多个具有AI能力的单运维能力点组合成一个智能运维流程。行业通用的演进路线如下：</font>

1. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">开始尝试应用AI能力，还无较为成熟的单点应用。</font>
2. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">具备单场景的AI运维能力，可以初步形成供内部使用的学件。</font>
3. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">有由多个单场景AI运维模块串联起来的流程化AI运维能力，可以对外提供可靠的运维AI学件。</font>
4. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">主要运维场景均已实现流程化免干预AI运维能力，可以对外提供供可靠的AIOps服务。</font>
5. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">有核心中枢AI，可以在成本、质量、效率间从容调整，达到业务不同生命周期对三个方面不同的指标要求，可实现多目标下的最优或按需最优。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">所谓学件，亦称AI运维组件[1]（南京大学周志华老师原创），类似程序中的API或公共库，但API及公共库不含具体业务数据，只是某种算法，而AI运维组件（或称学件），则是在类似API的基础上，兼具对某个运维场景智能化解决的“记忆”能力，将处理这个场景的智能规则保存在了这个组件中，学件（Learnware）= 模型（Model）+规约（Specification）。AIOps具体的能力框架如下图1所示：</font>

![1742201410886-b7e65bfa-8246-4e70-84b4-403b6cc2bf8d.png](./img/Q9Qx1ExRHX-Z1w3W/1742201410886-b7e65bfa-8246-4e70-84b4-403b6cc2bf8d-293997.png)

<font style="color:rgb(119, 119, 119);">图1 AIOps能力框架图</font>

### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">2.2 关联团队建设</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">AIOps团队内部人员根据职能可分为三类团队，分别为SRE团队、开发工程师（稳定性保障方向）团队和算法工程师团队，他们在AIOps相关工作中分别扮演不同的角色，三者缺一不可。SRE能从业务的技术运营中，提炼出智能化的需求点，在开发实施前能够考虑好需求方案，产品上线后能对产品数据进行持续的运营。开发工程师负责进行平台相关功能和模块的开发，以降低用户的使用门槛，提升用户的使用效率，根据企业AIOps程度和能力的不同，运维自动化平台开发和运维数据平台开发的权重不同，在工程落地上能够考虑好健壮性、鲁棒性、扩展性等，合理拆分任务，保障成果落地。算法工程师则针对来自于SRE的需求进行理解和梳理，对业界方案、相关论文、算法进行调研和尝试，完成最终算法落地方案的输出工作，并不断迭代优化。各团队之间的关系图如下图2所示：</font>

![1742201410950-0474548a-76aa-48f6-9eef-2e0310dd99c1.png](./img/Q9Qx1ExRHX-Z1w3W/1742201410950-0474548a-76aa-48f6-9eef-2e0310dd99c1-917400.png)

<font style="color:rgb(119, 119, 119);">图2 AIOps关联团队关系图</font>

### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">2.3 演进路线</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">当前，我们在质量保障方面的诉求最迫切，服务运维部先从故障管理领域探索AIOps实践。在故障管理体系中，从故障开始到结束主要有四大核心能力，即故障发现、告警触达、故障定位、故障恢复。故障发现包含了指标预测、异常检测和故障预测等方面，主要目标是能及时、准确地发现故障；告警触达包含了告警事件的收敛、聚合和抑制，主要目标是降噪聚合，减少干扰；故障定位包含了数据收集、根因分析、关联分析、智能分析等，主要目标是能及时、精准地定位故障根因；故障恢复部分包含了流量切换、预案、降级等，主要目标是及时恢复故障，减少业务损失，具体关系如下图3所示：</font>

![1742201411012-67026cf0-01e3-47b2-a660-26c3a77c599d.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411012-67026cf0-01e3-47b2-a660-26c3a77c599d-925616.png)

<font style="color:rgb(119, 119, 119);">图3 故障管理体系核心能力关系图</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">其中在故障管理智能化的过程中，故障发现作为故障管理中最开始的一环，在当前海量指标场景下，自动发现故障和自动异常检测的需求甚为迫切，能极大地简化研发策略配置成本，提高告警的准确率，减少告警风暴和误告，从而提高研发的效率。除此之外，时序数据异常检测其实是基础能力，在后续告警触达、故障定位和故障恢复环节中，存在大量指标需要进行异常检测。所以将故障发现作为当前重点探索目标，解决当前海量数据场景下人工配置和运营告警策略、告警风暴和准确率不高的核心痛点。整个AIOps体系的探索和演进路线如下图4所示。每个环节均有独立的产品演进，故障发现-Horae（美团服务运维部与交易系统平台部共建项目）、告警触达-告警中心、故障定位-雷达、故障恢复-雷达预案。</font>

![1742201411036-4741fab7-0238-40f1-8eac-c57ec7e9d42d.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411036-4741fab7-0238-40f1-8eac-c57ec7e9d42d-246562.png)

<font style="color:rgb(119, 119, 119);">图4 AIOps在故障管理方面的演进路线</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">三、AIOps之故障发现</font>
### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">3.1 故障发现</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">从美团现有的监控体系可以发现，绝大多数监控数据均为时序数据（Time Series），时序数据的监控在公司故障发现过程中扮演着不可忽视的角色。无论是基础监控CAT[2]、MT-Falcon[3]、Metrics（App端监控），还是业务监控Digger（外卖业务监控）、Radar（故障发现与定位平台）等，均基于时序数据进行异常监控，来判断当前业务是否在正常运行。然而从海量的时序数据指标中可以发现，指标种类繁多、关系复杂（如下图5所示）。在指标本身的特点上，有周期性、规律突刺、整体抬升和下降、低峰期等特点，在影响因素上，有节假日、临时活动、天气、疫情等因素。原有监控系统的固定阈值类监控策略想要覆盖上述种种场景，变得越来越困难，并且指标数量众多，在策略配置和优化运营上，人力成本将成倍增长。若在海量指标监控上，能根据指标自动适配合适的策略，不需要人为参与，将极大的减少SRE和研发同学在策略配置和运营上的时间成本，也可让SRE和研发人员把更多精力用在业务研发上，从而产生更多的业务价值，更好地服务于业务和用户。</font>

![1742201410960-d46eff54-8d53-4951-9b85-3e2a057d3bd7.png](./img/Q9Qx1ExRHX-Z1w3W/1742201410960-d46eff54-8d53-4951-9b85-3e2a057d3bd7-100430.png)

<font style="color:rgb(119, 119, 119);">图5 时序数据种类多样性</font>

### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">3.2 时序数据自动分类</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">在时序数据异常检测中，对于不同类型的时序数据，通常需要设置不同的告警规则。比如对于CPU Load曲线，往往波动剧烈，如果设置固定阈值，瞬时的高涨会经常产生误告，SRE和研发人员需要不断调整阈值和检测窗口来减少误告，当前，通过Radar（美团内部系统）监控系统提供的动态阈值策略，然后参考历史数据可以在一定程度上避免这一情况。如果系统能够提前预判该时序数据类型，给出合理的策略配置建议，就可以提升告警配置体验，甚至做到自动化配置。而且在异常检测中，时序数据分类通常也是智能化的第一步，只有实现智能化分类，才能自动适配相应的策略。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">目前，时间序列分类主要有两种方法，无监督的聚类和基于监督学习的分类。Yading[4]是一种大规模的时序聚类方法，它采用PAA降维和基于密度聚类的方法实现快速聚类，有别于K-Means和K-Shape[5]采用互相关统计方法，它基于互相关的特性提出了一个新颖的计算簇心的方法，且在计算距离时尽量保留了时间序列的形状。对KPI进行聚类，也分为两种方法，一种是必须提前指定类别数目（如K-Means、K-Shape等）的方法，另一种是无需指定类别数目（如DBSCAN等），无需指定类别数目的聚类方法，类别划分的结果受模型参数和样本影响。至于监督学习的分类方法，经典的算法主要包括Logistics、SVM等。</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.2.1 分类器选择</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">根据当前监控系统中时序数据特点，以及业内的实践，我们将所有指标抽象成三种类别：周期型、平稳型和无规律波动型[6]。我们主要经历了三个阶段的探索，单分类器分类、多弱分类器集成决策分类和卷积神经网络分类。</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">单分类器分类</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：本文训练了SVM、DBSCAN、One-Class-SVM（S3VM）三种分类器，平均分类准确率达到80%左右，但无规律波动型指标的分类准确率只有50%左右，不满足使用要求。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">多弱分类器集成决策分类</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：参考集成学习相关原理，通过对SVM、DBSCAN、S3VM三种分类器集成投票，提高分类准确率，最终分类准确率提高7个百分点，达到87%。</font>
3. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">卷积神经网络分类</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：参考对Human Activity Recognition（HAR）进行分类的实践[7]，我们用CNN（卷积神经网络）实现了一个分类器，该分类器在时序数据分类上表现优秀，准确率能达到95%以上。CNN在训练中会逐层学习时序数据的特征，不需要成本昂贵的特征工程，大大减少了特征设计的工作量。</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.2.2 分类流程</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">我们选择CNN分类器进行时序数据分类，分类过程如下图6所示，主要步骤如下：</font>

![1742201411876-7374bbbc-1dd4-4549-9b8a-e9f437a81307.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411876-7374bbbc-1dd4-4549-9b8a-e9f437a81307-553909.png)

<font style="color:rgb(119, 119, 119);">图6 时序数据分类处理流程</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">缺失值填充</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：时序数据存在少量数据丢失或者部分时段无数据等现象，因此在分类前先对数据先进行缺失值填充。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">标准化</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：本文采用方差标准化对时序数据进行处理。</font>
3. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">降维处理</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：按分钟粒度的话，一天有1440个点，为了减少计算量，我们进行降维处理到144个点。PCA、PAA、SAX等一系列方法是常用的降维方法，此类方法在降低数据维度的同时还能最大程度地保持数据的特征。通过比较，PAA在降到同样的维度（144维）时，还能保留更多的时序数据细节，具体对比如下图7所示。</font>
4. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">模型训练</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：使用标注的样本数据，在CNN分类器中进行训练，最终输出分类模型。</font>

![1742201411889-2368b465-671c-4f6e-9ff6-926d1f7a40ad.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411889-2368b465-671c-4f6e-9ff6-926d1f7a40ad-555234.png)

<font style="color:rgb(119, 119, 119);">图7 PAA、SAX降维方法对比</font>

### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">3.3 周期型指标异常检测</font>
**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.3.1 异常检测方法</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">基于上述时序数据分类工作，本文能够相对准确地将时序数据分为周期型、平稳型和无规律波动型三类。在这三种类型中，周期型最为常见，占比30%以上，并且包含了大多数业务指标，业务请求量、订单数等核心指标均为周期型，所以本文优先选择周期型指标进行自动异常检测的探索。对于大量的时序数据，通过规则进行判断已经不能满足，需要通用的解决方案，能对所有周期型指标进行异常检测，而非一个指标一套完全独立的策略，机器学习方法是首选。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">论文Opprentice[8]和腾讯开源的Metis[9]采用监督学习的方式进行异常检测，其做法如下：首先，进行样本标注得到样本数据集，然后进行特征提取得到特征数据集，使用特征数据集在指定的学习系统上进行训练，得到异常分类模型，最后把模型用于实时检测。监督学习整体思路[10]如下图8所示，其中 (x1,y1),(x2,y2),…,(xn,yn)是训练数据集，学习系统由训练数据学习一个分类器P(Y∣X)或Y=f(X)，分类系统通过学习到的分类器对新的输入实例xn+1进行分类，预测其输出的类别yn+1。</font>

![1742201411938-22cff899-712b-4655-a847-fe85cb831881.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411938-22cff899-712b-4655-a847-fe85cb831881-209670.png)

<font style="color:rgb(119, 119, 119);">图8 监督学习在分类问题中的应用</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.3.2 异常注入</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">一般而言，在样本数据集中，正负样本比例如果极度不均衡（比如1:5，或者更悬殊），那么分类器分类时就会倾向于高比例的那一类样本（假如负样本占较大比例，则会表现为负样本Recall过高，正样本Recall低，而整体的Accuracy依然会有比较好的表现），在一个极度不均衡的样本集中，由于机器学习会对每个数据进行学习，那么多数数据样本带有的信息量就比少数样本信息量大，会对分类器学习过程中造成干扰，导致分类不准确。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">在实际生产环境中，时序数据异常点是非常少见的，99%以上的数据都是正常的。如果使用真实生产环境的数据进行样本标注，将会导致正负样本比例严重失衡，导致精召率无法满足要求。为了解决基于监督学习的异常检测异常点过少的问题，本文设计一种针对周期型指标的自动异常注入算法，保证异常注入足够随机且包含各种异常场景。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">时序数据的异常分为两种基本类型，异常上涨和异常下跌，如下图9（图中数据使用Curve[11]标注），通常异常会持续一段时间，然后逐步恢复，恢复过程或快或慢，影响异常两侧的值，称之为涟漪效应（Ripple Effect），类似石头落入水中，波纹扩散的情形。受到该场景的启发，异常注入思路及步骤如下：</font>

![1742201411875-f5796be8-e296-4e43-989e-1ff94691e49d.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411875-f5796be8-e296-4e43-989e-1ff94691e49d-321281.png)

<font style="color:rgb(119, 119, 119);">图9 异常case中异常数据分布</font>

1. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">给定一段时序值S，确定注入的异常个数N，将时序数据划分为N块。</font>
2. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">在其中的一个区域X中，随机选定一个点Xi作为异常种子点。</font>
3. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">设定异常点数目范围，基于此范围产生随机出异常点数n，异常点随机分布在异常种子两侧，左侧和右侧的数目随机产生。</font>
4. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">对于具体的异常点，根据其所在位置，选择该点邻域范围数据作为参考数据集m，需要邻域在设定的范围内随机产生。</font>
5. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">产生一个随机数，若为奇数，则为上涨，否则下跌。基于参考数据集m，根据3Sigma原理，生成超出±3σ的数据作为异常值。</font>
6. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">设定一个影响范围，在设定范围内随机产生影响的范围大小，左右两侧的影响范围也随机分配，同时随机产生异常衰减的方式，包括简单移动平均、加权移动平均、指数加权移动平均三种方式。</font>
7. <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">上述过程只涉及突增突降场景，而对于同时存在升降的场景，通过分别生成上涨和下跌的上述两个异常，然后叠加在一起即可。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">通过上面的异常注入步骤，能比较好地模拟出周期型指标在生产环境中的各种异常场景，上述过程中各个步骤的数据都是随机产生，所以产生的异常案例各不相同，从而能为我们生产出足够多的异常样本。为了保证样本集的高准确性，我们对于注入异常后的指标数据还会进行标注，以去除部分注入的非异常数据。具体异常数据生成效果如图10所示，其中蓝色线为原始数据，红色线为注入的异常，可以看出注入异常与线上环境发生故障时相似，注入的异常随机性较大。</font>

![1742201411896-55ef3d4e-44d8-48a2-a801-7bd67aff0a5d.png](./img/Q9Qx1ExRHX-Z1w3W/1742201411896-55ef3d4e-44d8-48a2-a801-7bd67aff0a5d-369116.png)

<font style="color:rgb(119, 119, 119);">图10 异常注入效果图</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.3.3 特征工程</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">针对周期型指标，经标注产生样本数据集后，需要设计特征提取器进行特征提取，Opprentice中设计的几种特征提取器如图11所示：</font>

![1742201412299-8201144a-7f01-4260-bfc0-2fba52b5a354.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412299-8201144a-7f01-4260-bfc0-2fba52b5a354-028534.png)

<font style="color:rgb(119, 119, 119);">图11 论文Opprentice特征提取器</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">上述特征主要是一些简单的检测器，包括如固定阈值、差分、移动平均、SVD分解等。Metis将其分为三种特征，一是统计特征，包括方差、均值、偏度等统计学特征；二是拟合特征，包括如移动平均、指数加权移动平均等特征；三是分类特征，包含一些自相关性、互相关性等特征。参考上述提及的特征提取方法，本文设计了一套特征工程，区别于上述特征提取方法，本文对提取的结果用孤立森林进行了一层特征抽象，使得模型的泛化能力更强，所选择的特征及说明如下图12所示：</font>

![1742201412325-ad93eb5b-d47c-412b-a804-ed3e72e0aef5.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412325-ad93eb5b-d47c-412b-a804-ed3e72e0aef5-905494.png)

<font style="color:rgb(119, 119, 119);">图12 特征选择及说明</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.3.4 模型训练及实时检测</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">参考监督学习在分类问题中的应用思路，对周期型指标自动异常检测方案具体设计如图下13所示，主要分为离线模型训练和实时检测两大部分，模型训练主要根据样本数据集训练生成分类模型，实时检测利用分类模型进行实时异常检测。具体过程说明如下：</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">离线模型训练</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：基于标注的样本数据集，使用设计的特征提取器进行特征提取，生成特征数据集，通过Xgboost进行训练，得到分类模型，并存储。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">实时检测</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：线上实时检测时，时序数据先经过预检测（降低进入特征提取环节概率，减少计算压力），然后根据设计的特征工程进行特征提取，再加载离线训练好的模型，进行异常分类。</font>
3. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">数据反馈</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：如果判定为异常，将发出告警。进一步地，用户可根据实际情况对告警进行反馈，反馈结果将加入样本数据集中，用于定时更新检测模型。</font>

![1742201412534-5d874699-a628-4d79-a1ed-655d2f157b22.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412534-5d874699-a628-4d79-a1ed-655d2f157b22-011856.png)

<font style="color:rgb(119, 119, 119);">图13 模型检测和实时检测流说明</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.3.5 特殊场景优化</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">通过上述实践，本文得到一套可完整运行的周期型指标异常检测系统，在该系统应用到生产环境的过程中，也遇到不少问题，比如低峰期（小数值）波动幅度较大，节假日和周末趋势和工作日趋势完全不同，数据存在整体大幅抬升或下降，部分规律波动时间轴上存在偏移，这些情况都有可能产生误告。本文也针对这些场景，分别提出对应的优化策略，从而减少周期型指标在这些场景下的误告，提高异常检测的精召率。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">1）</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">低峰期场景</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：低峰期主要表现是小数值高波动，低峰期的波动比较普遍，但是常规检测时，只获取当前点前后7min的邻域内的数据，可能无法获取到本身已经出现过多次的较大波动，导致误判为异常。所以对于低峰期，需要扩大比较窗口，容纳到更多的正常的较大波动场景，从而减少被误判。如下图14所示，红色是当日数据，灰色是上周同日数据，如果判断窗口为w1，w1内蓝色点有可能被认为是异常点，而时间窗口范围扩大到w2后，大幅波动的蓝色点和绿色点都会被捕获到，出现类似大幅波动时不再被判定为异常，至于低峰期范围可以通过历史数据计算进行识别。</font>

![1742201412353-a55c3fa1-268d-4b7a-ac50-6b6d77fe32ee.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412353-a55c3fa1-268d-4b7a-ac50-6b6d77fe32ee-576573.png)

<font style="color:rgb(119, 119, 119);">图14 低峰期时不同时段的相似大幅波动</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">2）</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">节假日场景</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：节假日前一天以及节假日之后一周的数据，和正常周期的趋势都会有较大差别，可能会出现误告。本文通过提前配置需要进行节假日检测的日期，在设置的日期范围内，除了进行正常的检测流程，对于已经检测出异常的数据点，会再进入到节假日检测流程，都异常才会触发告警。节假日检测会取最近1h的数据，分别计算其波动比、周同比、日环比等数据，当前时间的这些指标通过“孤立森林”判断都为异常，才会认为数据是真正异常。除此之外，对于节假日，模型的敏感度会适当调低以适应节假日场景。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">3）</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">整体抬升/下降场景</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：场景特点如下图15所示，在该场景下，会设置一个抬升/下跌率，比如80%，如果今天最近1h数据80%相对昨日和上周都上涨，则认为是整体抬升，都下跌则认为是整体下降。如果出现整体抬升情况，会降低模型敏感度，并且要求当前日环比、周同比在1h数据中均为异常点，才会判定当前的数据异常。</font>

![1742201412359-26072807-e35f-4cb6-aeb5-aa32a74310ae.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412359-26072807-e35f-4cb6-aeb5-aa32a74310ae-567084.png)

<font style="color:rgb(119, 119, 119);">图15 整体抬升下降场景</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">4）</font>**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">规律波动偏移场景</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：部分指标存在周期性波动，但是时间上会有所偏移，如图16所示案例中时序数据由于波动时间偏移导致误告。本文设计一种相似序列识别算法，在历史数据中找出波动相似的序列，如果存在足够多的相似波动序列，则认为该波动为正常波动。相似序列提取过程如下：最近n分钟的时序作为基础序列x，获取检测时刻历史14天邻域内的数据（如前后30min），在邻域数据中指定滑动窗口（如3min）滑动，把邻域数据分为多个长度为n的序列集Y，计算基础序列x与Y中每个序列的DTW距离，通过“孤立森林”对距离序列进行异常判断，对于被判定为异常值的DTW距离，它所对应的序列将被视为相似序列。如果相似序列个数超出设定阈值，则认为当前波动为规律偏移波动，属于正常现象。根据上述方法，提取到对应的相似序列如图16右边所示，其中红实线为基础序列。</font>

![1742201412706-5d1d7118-a00c-4d39-8491-f51ee59ccfc3.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412706-5d1d7118-a00c-4d39-8491-f51ee59ccfc3-101487.png)

<font style="color:rgb(119, 119, 119);">图16 规律波动偏移相似序列提取</font>

### <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">3.4 异常检测能力平台化</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">为了把上述时序数据异常检测探索的结果进行落地，服务运维部与交易系统平台部设计和开发了时序数据异常检测系统Horae。Horae致力于时间序列异常检测流程的编排与调优，处理对象是时序数据，输出是检测流程和检测结果，核心算法是异常检测算法、时间序列预测算法以及针对时间序列的特征提取算法。除此之外，Horae还会针对特殊的场景开发异常检测算法。Horae核心能力是可根据提供的算法，编排不同的检测流程，对指标进行自动分类，并针对指标所属类型自动选择合适的检测流程，进行流程调优得到该指标下的最优参数，从而确保能适配指标并得到更高的精召率，为各个对时序数据异常检测有需求的团队提供高准确率的异常检测服务。</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.4.1 Horae系统架构设计</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">Horae系统由四个模块组成：数据接入、实时检测、实验模块和算法模块。用户通过数据接入模块注册需要监听时序数据的消息队列，Horae系统将监听注册的Topic采集时序数据，并根据粒度（例如分钟、小时或天）更新每个时间序列，每个时序点都存储到时序数据库中，实时检测模块对每个时序点执行异常检测，当检测到异常时，通过消息队列将异常信息传输给用户。下图17详细展示了Horae系统的整体架构图。</font>

1. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">数据接入</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：用户可以通过创建数据源用于数据上报，数据源可以包含一个或多个指标，指标更新频率最小为一分钟。不同数据源中指标的时序数据相互隔离，时序数据更新到使用Elasticsearch改造后的时序数据库中。</font>
2. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">实时检测</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：采集到实时的时序数据后，会根据指标绑定的执行流程立即进行异常检测，如果没有训练调优，会先执行训练调优以保证更佳的精召率。实时检测的结果会通过消息队列通知到用户，用户根据异常检测的结果进行进一步处理判断是否需要发出告警。</font>
3. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">实验模块</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：该模块主要功能是样本管理、算法注册、流程编排、模型训练和评估、模型发布。该模块提供样本管理功能，可对样本进行标注和存储；对于已经实现的算法，可以注册到系统中，供流程编排使用；通过算法编排得到的流程，可以用在模型训练或者异常检测中；训练流程会使用到标注好的样本数据调用算法离线服务进行离线训练并存储模型；对于已经编排好的检测流程，可以对指标进行模拟观察检测异常检测效果，或者离线回归判断检测流程在该指标上的具体精召率数据。</font>
4. **<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">算法模块</font>**<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">：算法模块提供了所有在实验模块注册的异常检测算法的具体实现，算法模块既可以执行单个算法，也可以接受多个算法编排的流程进行执行。当前支持的算法大类主要有预处理算法（异常值去除、空值填充、降维、归一化等），时序特征算法（统计类特征、拟合特征、分类特征等），机器学习类算法（RF、SVM、XGBoost、GRU、LSTM、CNN、聚类算法等），检测类算法（孤立森林、LOF、SVM、3Sigma、四分位、IQR等），预测类算法（Ewma、Linear Weighted MA、Holt-Winters、STL、SAIMAX、Prophet等），自定义算法（形变分析、同环比、波动比等）。</font>

![1742201412760-0c24417c-6cdb-4127-b24c-86f27d7ba73a.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412760-0c24417c-6cdb-4127-b24c-86f27d7ba73a-992622.png)

<font style="color:rgb(119, 119, 119);">图17 Horae时序数据异常检测系统架构图</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.4.2 算法注册和模型编排</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">算法模型是对算法的抽象，通过唯一字符串标识算法模型，注册算法时需要指定算法的类型、接口、参数、返回值和处理单个时序点所需要加载的时序数据配置。成功注册的算法模型根据算法类型的不同，会生成用于模型编排的算法组件或对异常检测模型进行训练的组件。用于模型编排的算法组件主要包括：预处理算法、时序特征算法、评估算法、预测算法、分类算法、异常检测算法等，用于模型训练的算法分为两大类：参数调优和机器学习模型训练。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">注册后的算法模型通常不会直接用于异常检测，会对算法模型进行编排后得到一个流程模型，流程模型可以用于执行异常检测或者执行训练。实验模块支持两种类型的流程模型：执行流程和训练流程。执行流程是一个异常检测流程，指定指标和检测时间段，得到检测时间段每个时序点的异常分值；训练流程是一个执行训练的流程模型，主要包括参数调优训练流程和机器学习模型训练流程。使用算法进行流程编排如下图18所示，左侧菜单为算法组件，中间区域可对算法执行流程进行编排调整，右侧区域是具体算法的参数设置。</font>

![1742201412820-7f0ccd85-9421-41d6-9ba5-2edd4f914445.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412820-7f0ccd85-9421-41d6-9ba5-2edd4f914445-521186.png)

<font style="color:rgb(119, 119, 119);">图18 流程编排示意图</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.4.3 离线训练和实时检测</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">在模型编排阶段，可编排执行流程和训练流程，执行流程主要用在指标实时异常检测过程，而训练流程主要用在离线模型训练和参数调优。执行流程由流程配置和异常分值配置构成，由实验模块的流程调度引擎负责执行调度，下图19展示了执行流程的详细构成。流程调度引擎在对执行流程调度执行之前，会从流程的最深叶子节点的算法组件开始递归计算需要加载的时序数据集，根据流程中算法组件的参数配置，加载前置训练流程的训练结果，最后对流程中的算法组件依次调度执行，得到检测时间段每个时序点的异常分值。最终实现后的执行流程编排如图18所示。</font>

![1742201412776-5fb0d10a-b5fa-4c99-8f81-9b602af7a277.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412776-5fb0d10a-b5fa-4c99-8f81-9b602af7a277-184042.png)

<font style="color:rgb(119, 119, 119);">图19 执行流程组成和处理过程</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">训练流程由流程配置、训练算法、样本加载配置和训练频次等信息构成，由实验模块的流程调度引擎负责调度执行，下图20展示了训练流程的详细构成。训练流程主要分为两大类，参数调优训练和机器学习模型训练。参数调优训练是指为需要调优的参数设置参数值迭代范围或者枚举值，通过贝叶斯调优算法对参数进行调优，得到最优参数组合；机器学习模型训练则通过设计好特征工程，设置分类器和超参数范围后调优得到机器学习模型文件。训练流程执行训练的样本集来源于人工标注的样本或者基于自动样本构造功能生成的样本。训练流程编排具体过程和执行流程类似，不同的是训练流程可设置定时任务执行，训练的结果会存储供后续使用。</font>

![1742201412960-45c4e1e0-87e2-4dd7-8808-5e4bcf55f5ae.png](./img/Q9Qx1ExRHX-Z1w3W/1742201412960-45c4e1e0-87e2-4dd7-8808-5e4bcf55f5ae-772673.png)

<font style="color:rgb(119, 119, 119);">图20 训练流程组成和处理过程</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">异常检测模型中会包含很多凭借经验设定的超参数，不同的指标可能需要设置不同的参数值，保证更高的精召率。而指标数据会随着时间发生变化，设置参数需要定期的训练和更新，在实验模块中可以为训练流程设置定时任务，实验模块会定时调度训练流程生成离线训练任务，训练任务执行完成可以看到训练结果和效果。下图21示例展示了一个参数调优训练流程的示例。</font>

![1742201413305-58f1c26c-ed4b-40fe-813d-cfb35c5e9b7f.png](./img/Q9Qx1ExRHX-Z1w3W/1742201413305-58f1c26c-ed4b-40fe-813d-cfb35c5e9b7f-122058.png)

<font style="color:rgb(119, 119, 119);">图21 参数调优训练结果示例</font>

**<font style="color:rgb(0, 0, 0);background-color:rgb(253, 253, 253);">3.4.4 模型案例和结果评估</font>**

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">根据在周期型指标上探索的结果，在Horae上编排分类模型训练流程，训练和测试所使用的样本数是28000个，其中用于训练的比例是75%，用于验证的比例是25%，具体分类模型训练结果如下图22所示，在测试集上的准确率94%，召回率89%。同时编排了与之对应的执行流程，它的检测流程除了异常分类，还主要包含了空值填充、预检测、特征提取、分类判断、低峰期判断、偏移波动判断等逻辑，该执行流程适用范围是周期型和稳定型指标。除此之外，还提供了流程调优能力，检测流程中的每个算法可以暴露其超参数，对于具体的指标，通过该指标的样本数据可以训练得到该流程下的一组较优超参数，从而提高该指标的异常检测的精召率。</font>

![1742201413140-ee6a4947-3f24-4620-a7bb-e934205a00af.png](./img/Q9Qx1ExRHX-Z1w3W/1742201413140-ee6a4947-3f24-4620-a7bb-e934205a00af-674294.png)

<font style="color:rgb(119, 119, 119);">图22 异常分类模型训练结果</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">该异常检测流程应用到生产环境的指标后，具体检测效果相关案例如下图23所示，对于周期型指标，能及时准确地发现异常，对异常点进行反馈，准确率达到90%以上。除此之外，还对比了形变分析异常检测，对于生产环境中遇到的三个形变分析无法发现的4个案例，周期型指标异常检测流程能发现其中3个，表现优于形变分析。</font>

![1742201413151-20326464-586e-461c-ac93-0df59cb92752.png](./img/Q9Qx1ExRHX-Z1w3W/1742201413151-20326464-586e-461c-ac93-0df59cb92752-833688.png)

<font style="color:rgb(119, 119, 119);">图23 周期型指标异常检测模型生产环境检测结果</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">四、总结与展望</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">时序数据异常检测作为AIOps中故障发现环节的核心，当前经过探索和实践，已经在周期型指标异常检测上取得了一定的成绩，并落地到Horae时序异常检测系统中。在时序数据异常检测部分，后续会陆续实现平稳型、无规律波动型指标自动异常检测，增加指标数据预测相关能力，提高检测性能，从而实现所有类型的海量指标自动异常检测的目的。</font>

<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">除此之外，在告警触达方面，我们当前在进行告警收敛、降噪和抑制相关的规则和算法的探索，致力于提供精简有效的信息，减少告警风暴及干扰；在故障定位方面，我们已经基于规则在定位上取得比较不错的效果，后续还会进行更全面的定位场景覆盖和关联性分析、根因分析、知识图谱相关的探索，通过算法和规则提升故障定位的精召率。因篇幅所限，告警触达（告警中心）和故障定位（雷达）两部分内容将会在后续的文章中详细进行分享，敬请期待。</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">五、参考资料</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[1] 周志华. 机器学习: 发展与未来[R]. 报告地: 深圳, 2016.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[2] 美团实时监控系统CAT[EB/OL].</font><font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);"> </font>[<font style="color:rgb(255, 196, 2);background-color:rgb(253, 253, 253);">https://tech.meituan.com/CAT_in_Depth_Java_Application_Monitoring.html</font>](https://tech.meituan.com/CAT_in_Depth_Java_Application_Monitoring.html)<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">, 2018-11-01.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[3] 美团系统指标监控Mt-Falcon[EB/OL].</font><font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);"> </font>[<font style="color:rgb(255, 196, 2);background-color:rgb(253, 253, 253);">https://tech.meituan.com/Mt-Falcon_Monitoring_System.html</font>](https://tech.meituan.com/Mt-Falcon_Monitoring_System.html)<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">, 2017-02-24.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[4] Ding R, Wang Q, Dang Y, et al. Yading: fast clustering of large-scale time series data[J]. Proceedings of the VLDB Endowment, 2015, 8(5): 473-484.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[5] Paparrizos J, Gravano L. k-shape: Efficient and accurate clustering of time series[C]. Proceedings of the 2015 ACM SIGMOD International Conference on Management of Data. ACM, 2015: 1855-1870.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[6] H. Ren, Q. Zhang, B. Xu, Y. Wang, C. Yi, C. Huang, X. Kou, T. Xing, M. Yang, and J. Tong, “Time-series anomaly detection serviceat microsoft,” in Proceedings of the ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, pp. 3009–3017, ACM, Jun. 2019.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[7] Tom Brander. Time series classification with Tensorflow[EB/OL].</font><font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);"> </font>[<font style="color:rgb(255, 196, 2);background-color:rgb(253, 253, 253);">https://burakhimmetoglu.com/2017/08/22/time-series-classification-with-tensorflow</font>](https://burakhimmetoglu.com/2017/08/22/time-series-classification-with-tensorflow)<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">, 2017-08-22.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[8] Liu D, Zhao Y, Xu H, et al. Opprentice: Towards practical and automatic anomaly detection through machine learning[C]//Proceedings of the 2015 Internet Measurement Conference. ACM, 2015: 211-224.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[9] Metis is a learnware platform in the field of AIOps[EB/OL].</font><font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);"> </font>[<font style="color:rgb(255, 196, 2);background-color:rgb(253, 253, 253);">https://github.com/Tencent/Metis</font>](https://github.com/Tencent/Metis)<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">, 2018-10-12.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[10] 李航. 统计学习方法 [M]. 第2版. 北京: 清华大学出版社, 2019.28-29.</font>
+ <font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">[11] An tool to help label anomalies on time-series data[EB/OL].</font><font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);"> </font>[<font style="color:rgb(255, 196, 2);background-color:rgb(253, 253, 253);">https://github.com/baidu/Curve</font>](https://github.com/baidu/Curve)<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">, 2018-08-07.</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">六、作者简介</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">胡原、锦冬、俊峰，基础技术部-服务运维部工程师；长伟、永强，到家事业群-交易系统平台部工程师。</font>

## <font style="color:rgb(42, 41, 53);background-color:rgb(253, 253, 253);">招聘信息</font>
<font style="color:rgb(51, 51, 51);background-color:rgb(253, 253, 253);">基础技术部-服务运维部-运维工具开发组-故障管理开发组主要负责故障发现、故障定位、故障恢复、故障运营、告警中心、风险管理、数据仓库等工作。目前团队诚招高级工程师、技术专家。欢迎有兴趣的同学投送简历至tech@meituan.com（邮件主题注明：运维工具）</font>



> 更新: 2025-03-17 16:50:41  
> 原文: <https://www.yuque.com/u12222632/as5rgl/wfdfdhx3fbge9806>