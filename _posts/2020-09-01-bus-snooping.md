---
layout: post
title:  "总线嗅探"
categories: 微处理器架构
---

# bus snooping
总线嗅探是一种维持SMP架构缓存一致性（coherence）的机制。有coherency controller的cache被称为snoopy cache。

多个缓存共享某一个数据，一旦某个缓存中的数据发生修改，就需要将修改传播到其他缓存副本，从而保证coherence。这种数据修改可以通过snooping机制实现监听。一旦修改共享cache block的transaction出现在总线上，所有snooper都会检查它们的cache是否有这个共享cache block的副本，并进行相应地动作，比如刷新（write-update）或者使缓存无效（write-invalidate）——具体实现取决于缓存一致性协议，大多数协议都选用后者，比如MSI、MESI、MOSI、MOESI、MESIF，少数选用前者，如Dragon、firefly。

总线嗅探机制与基于目录的缓存一致性机制相比更快，但缺点是不能很好地scale。对cache频繁嗅探会导致与处理器访问之间产生竞争，从而增加处理器访问cache的时延和耗电。此外，每次访问都被广播到所有节点意味着总线大小和带宽都会随着系统变大而增长，因此大规模的ccNUMA系统就采用基于目录的缓存一致性协议。









