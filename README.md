# The NorthEast Chinese Fanyi of the Raft Paper(In Search of an Understandable Consensus Algorithm) 
# 标题 一个能的整明白的(相对于Paxos)一致性算法的研究
# 作者 Diego Ongaro and John Ousterhout Stanford University
# 译者 Hao Yu BoHaiChuanBoHuangJianTingXueYuan(渤海船舶皇家舰艇学院)
# 摘要 
    Raft是一个管理复制日志的一致性算法。它能干的活和Paxos一样，效率也半斤八两，但是它的结构却和Paxos截然不同；这使得Raft比Paxos好理解，并且更加易于构建生产环境的系统。为了让Raft更好理解，Raft被分成几个关键的组成部分，选主（Leader Election）、日志复制（Log Repalication）以及安全性（Safety，实际意思就是容错性），并且Raft增强了每个状态的内聚性，使得要被理解的状态更少。学生调查报告显示学习Raft比学习Paxos更加容易。Raft也引入一个新的机制来保证集群节点之间的关系，它也和半数机制一起肩负起守卫Raft安全的重任。
# 引言
    一致性算法允许一堆机器作为一个耦合的整体工作，并且能保证在一些节点挂了的情况下也能正常工作。因为这个，他们扮演了构建大型分布式系统的关键角色。在过去十多年里，Paxos在一致性算法领域就是神一样的存在，大多数一致性系统都直接基于它，或者简单改吧改吧凑合就安排上了，在教育领域Paxos也是当之无愧的老大，你学也得学，你TM不学也得学。
    不幸的是，Paxos太JB难了，尽管已经有很多人对它进行了简化。此外，它的架构需要非常复杂的修改才能真正用于生产环境。正因为此，开发者和学生心里都是一万句MMP。
 
