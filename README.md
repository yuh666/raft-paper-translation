# The NorthEast Chinese Fanyi of the Raft Paper(In Search of an Understandable Consensus Algorithm) 
# 标题 一个能的整明白的(相对于Paxos)一致性算法的研究
# 作者 Diego Ongaro and John Ousterhout Stanford University
# 译者 Hao Yu BoHaiChuanBoHuangJianTingXueYuan(渤海船舶皇家舰艇学院)
# 摘要 
    Raft是一个管理复制日志的一致性算法。它能干的活和Paxos一样，效率也半斤八两，但是它的结构却和Paxos截然不同；这使得Raft比Paxos好理解，并且更加易于构建生产环境的系统。为了让Raft更好理解，Raft被分成几个关键的组成部分，选主（Leader Election）、日志复制（Log Repalication）以及安全性（Safety，实际意思就是容错性），并且Raft增强了每个状态的内聚性，使得要被理解的状态更少。学生调查报告显示学习Raft比学习Paxos更加容易。Raft也引入一个新的机制来保证集群节点之间的关系，它也和半数机制一起肩负起守卫Raft安全的重任。
 
