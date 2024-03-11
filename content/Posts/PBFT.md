如何避免收到”未来“的消息？
这里的未来是指，例如当前节点 node1 阶段处于 idle，而 master node0 已经下发了 pre-prepare，其中一些节点甚至已经广播了 prepare 消息。结果，node1 可能在收到 pre-prepare 之前就收到了 prepare 消息。按照策略，目前状态是 idle，收到 prepare 应该丢弃。
假设超过 1/3 的节点都遇到这种情况，就会导致被承认的 prepare 消息太少，进而无法产生足够的 commit 消息，最终导致用户 request 失败。

当前节点已经收到 pre-prepare，并且处于 pre-prepared 状态，正常情况下，当前节点需要发布 prepare 消息，并且在收到足够的来自 peer 的 prepare 消息之后切换到 prepared 状态。结果其它节点由于已经收到了足够的 preprepare 消息，已经切换到 prepared，并开始广播 commit 消息。这种情况下，当前节点会在 pre-prepared 状态下直接接收到 commit 消息。