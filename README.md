
# The NorthEast Chinese Fanyi of the Raft Paper(In Search of an Understandable Consensus Algorithm) 
# 标题 一个能的整明白的(相对于Paxos)一致性算法的研究
# 作者 Diego Ongaro and John Ousterhout Stanford University
# 译者 Hao Yu BoHaiChuanBoHuangJianTingXueYuan(渤海船舶皇家舰艇学院)
# 英文paper链接 https://raft.github.io/raft.pdf
# 摘要 
    Raft是一个管理复制日志的一致性算法。它能干的活和Paxos一样，效率也半斤八两，但是它的结构却和Paxos截然不同；这使得Raft比Paxos好理解，并且更加易于构建生产环境的系统。为了让Raft更好理解，Raft被分成几个关键的组成部分，选主（Leader Election）、日志复制（Log Repalication）以及安全性（Safety，实际意思就是容错性），并且Raft增强了每个状态的内聚性，使得要被理解的状态更少。学生调查报告显示学习Raft比学习Paxos更加容易。Raft也引入一个新的机制来保证集群节点之间的关系，它也和半数机制一起肩负起守卫Raft安全的重任。
# 1.引言
    一致性算法允许一堆机器作为一个耦合的整体工作，并且能保证在一些节点挂了的情况下也能正常工作。因为这个，他们扮演了构建大型分布式系统的关键角色。在过去十多年里，Paxos在一致性算法领域就是神一样的存在，大多数一致性系统都直接基于它，或者简单改吧改吧凑合就安排上了，在教育领域Paxos也是当之无愧的老大，你学也得学，你TM不学也得学。
    不幸的是，Paxos太JB难了，尽管已经有很多人对它进行了简化。此外，它的架构需要非常复杂的修改才能真正用于生产环境。正因为此，开发者和学生心里都是一万句MMP。
    在和Paxos相爱相杀之后，我们也开始发展一个新的能为系统开发和教育提供更好基础的一致性算法。我们的目标很不寻常，我们能不能自己搞一个明显比Paxos简单好学一致性算法？此外我们还想要这个算法能一目了然，做到知其然，知其所以然。
    我们最终鼓捣出来的这个算法叫Raft。在Raft的设计阶段，我们花费了很大力气让它易于理解，主要体现在在低复杂性（选主，日志复制，安全，Raft仅有这三种操作),状态空间减少(相比于Paxos，Raft减小了不确定程度和服务节点达到一致性的方式)。一项来自两个大学43名学生的调查报告显示，Raft确实比Paxos好理解多了，在学习这两个算法之后，33名学生回答关于Raft的问题明显比回答关于Paxos的问题有谱。
    Raft和现存的一致性算法有很多相同之处，但是它也有几个独家秘术:
        Strong leader：相对于其他一致性算法，Raft有一个强主关系。说白了就是日志复制只能是由主到从，这也简化了日志复制管理，并且让Raft更加容易理解。
        Leader election:Raft用随机定时器选主。这只是给其他一致性算法都用的心跳机制加了一个小小的修改，却轻而易举的解决了冲突的问题（意思就是 即使leader挂了也要等定时器到了才能发起投票，不像Paxos一拥而上，自己理解，仅供笑话）。
        Membership changes: Raft还采用吊炸天的联合一致性机制来保证集群配置文件变更前后的平稳过渡。这使得集群能在配置文件变更期间正常工作。

# 2.复制状态机
    一致性算法基本上都是基于复制状态机的。按照这种状态机的实现方式，一个集群里面的服务节点处理每个状态相同的副本，及时里面有一些节点已经挂了。复制状态机在分布式系统中被用来解决各种各样的容错问题。有一个主节点的大型分布式系统，例如 GFS、HDFS、RAMCLOUD，都用了一个单独的状态机来管理集群的选主和配置的存储，以此来保证及时leader以及挂了，集群还能正常运行。这些复制状态机有Zookeeper 或者 Chubby等。
    ![Image text](https://raw.githubusercontent.com/yuh666/raft-paper-translation/master/imgs/state%20machine.png)
    