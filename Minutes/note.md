# 会议纪要： 2023/12/26

## 会议主题 VoI 问题讨论

### VoI的历史来源

"VoI"通常指的是"Value of Information"，在决策理论中，信息价值（VoI）是一个概念，用于评估获取额外信息对决策结果的潜在价值。这个概念在经济学、商业分析、风险管理、医疗决策以及更广泛的统计决策理论中都有应用。

> [1] R. A. Howard, "Information Value Theory," in IEEE Transactions on Systems Science and Cybernetics, vol. 2, no. 1, pp. 22-26, Aug. 1966, doi: 10.1109/TSSC.1966.300074.

原文摘要中关于不确定性和信息价值的描述：

> It is necessary to be concerned not only with the probabilistic nature of the uncertainties that surround us, but also with the economic impact that these uncertainties will have on us.

> In this paper the theory of the value of information that arises from considering jointly the probabilistic and economic factors that affect decisions is discussed and illustrated.

不确定性（uncertain）可视为信息本身，不但受到概率（信息熵）的影响，也受到economic 影响。

香农在描述信息[2]时候考虑过其语义，但并未进一步研究。

> The fundamental problem of communication is that of reproducing at one point either exactly or approximately a message selected at another point. Frequently the messages have meaning; that is they refer to or are correlated according to some system with certain physical or conceptual entities. These semantic aspects of communication are irrelevant to the engineering problem. 

> [2] Shannon C E. A mathematical theory of communication[J]. The Bell system technical journal, 1948, 27(3): 379-423.


### 给出定义（Generally）

- VoI反应了信息对决策结果的潜在价值，更进一步说在通信系统中VoI可表示传输信息对任务能效的潜在影响。

- RL架构下VoI 可以定义为上一状态与下一状态在策略PI下动作价值函数期望差值。

根据论文[1]给出的形式定义：
> A specific value assigned to an information indicating the impact or importance of this information on a specific decision-maker in a specific system.

可能存在的数学定义方式有很多, 视系统不同不尽相同，但其核心思想符合上述描述。

### 与语义的区别

VoI与语义区别比较明显，VoI可视为对信息的一种评分，不改变信息原始数据结构。语义可视为对信息的重新释意。

更进一步讨论了基于VoI的通信系统和基于语义的通信系统的区别在于基于VoI的通信系统对信息进行筛选，而基于语义的通信系统对信息重新编码。

补充思路1：VoI可以与编码结合，信息的VoI与信道状态有关；不重要的信息可以采用误码率比较高的编码方式。

### VoI处理应该在哪一层

VoI处理应该位于语义编码之前的应用层。即VoI对信息进行了一定程度上的筛选。信息可以先进行筛选，再进行编码。

扩展问题：对于某些模态的数据，如图片/视频，可能只能对于其整体评估信息价值，然而实际传输问题并非直接传输整体，而是其中某一部分，如何判断其信息价值。可能的解决思路：以图片为例，mask掉其中部分像素点对还原图片的影响很小。

### VoI怎么在网络（即多跳）中体现

- 通过多跳网络中的节点，分布式评估VoI，即多跳传输的过程对应VoI的评估过程，VoI不改变路由；（类似邮箱理论）
- 以VoI为指标设计路由算法，针对视频流/数据流（）
  
问题：分析/利用VoI 是否一定需要解码，能否不对数据流的信息价值进行建模。如果可以，则可考虑多节点系统，任务流可在某个节点处理/丢弃/根据价值变化情况选择接下来的路由。

### 如何带来增益？ 都有哪些方面的增益

- 提高资源利用率，主要是计算资源
- 缓解通信资源稀缺的困境
- 提高任务QoE，时延/可靠性/吞吐量等网络指标未必能保证任务性能
- 灵活自适应通信

### 如何构建理论体系？有哪些思路和框架？
- 资源投入的边际效应（通信，计算）
通信资源投入的边际效应：以状态更新系统为例，其最小状态更新周期低于决策者根据信息做出决策的时间是没有意义的。

示例：假设通信系统信息为状态 S_t , 其动作 a_t 代表传输决策，\pi 代表通信系统的策略，则通信资源投入可由通信频率刻画。通信资源投入的边际效应可描述如下：

$$
\max{k} 
$$

$$
s.t.\ \ Q(S_t\ ,\ \pi(S_t))\ \ =\ Q(S_{t+1}\ ,\ \pi(S_{t+1}))=......=\ Q(S_{t+k}\ ,\ \pi(S_{t+k}))
$$

即求最大通信间隔k 满足动作价值函数不改变。

- 计算资源投入的边际效应：信息价值的评估希望筛选传输数据中的有用信息，然而对于确定任务的确定数据，其中的有用信息是确定的。因此无论如何评估其价值，投入多少计算资源，对于有用信息的筛选不可能比原始信息中蕴含的有用信息更多。（类似信息瓶颈）
  
可能思路：信息的价值密度是否可以度量。

- 通算资源的置换（通算一体化）

难点在于如何量化，可能的方式是排队系统？

可能思路： 排队论方法，考虑两个级联队列模型，其中队列1的到达速率为\lambda1，服务速率为u1，代表评估VoI投入的算力。队列2的到达速率取决与队列1的服务速率u1和\lambda1，顾客在队列1个队列2之间有概率被丢弃（对应根据VoI对数据筛选），队列2的服务速率代表通信资源投入。可以考虑系统吞吐量变化和资源投入之间的关联（参考牛老师PPT）

### 可以和哪些系统结合，如何结合

- 控制系统/状态更新系统/传感网络（现有）思路是根据VoI改变更新频率
- 视频流传输/点云还原/图片分类（现有主要差别在数据模态上），思路是根据VoI敲除一部分数据（比如无用帧/点云点/像素点）
- FL 根据重要度聚合参数（已经有类似论文）
- 边缘服务器缓存/计算卸载（类似流行度）
- 基站的启动/睡眠模式切换（节能）
主要性能指标：精度（控制/学习任务）；缓存命中率/流行度；能耗/电池寿命（物联网系统）；

### VoI的主要形式：

- AHP: 基于层次分析法，可以权衡任务所关注的多个指标，所获得的VoI形式是几个带权重指标的组合。
- 信息熵：源点当前未观测状态与接收器处一系列观测测量之间的互信息。
- 动作价值函数差值：信息价值定义为不同策略动作价值函数之差。
- 任务性能收益：信息价值定义为决策a下的动作价值函数
- 隐藏形式：直接使用神经网络拟合Value function/或者使用AC架构，通过评论家网络拟合value funciton。
