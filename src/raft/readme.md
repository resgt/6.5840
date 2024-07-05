## lab2A 实现选举和心跳机制

#### 运行流程
```
2A部分要求完成的是Raft中的Leader选取和心跳函数, 通过阅读论文和文档, 我们知道Raft的运行状况是这样的:

正常运行 Leader不断发送心跳函数给Follower, Follower回复, 这个心跳是通过AppendEntries RPC实现的, 只不过其中entries[]是空的。
选举
当指定的心跳间隔到期时， Follower转化为Candidate并开始进行投票选举, 会为自己投票并自增term
每一个收到投票请求的Server(即包括了Follower, Candidate或旧的Leader), 判断其RPC的参数是否符合Figure2中的要求, 符合则投票
除非遇到了轮次更靠后的投票申请, 否则投过票的Server不会再进行投票
超过一半的Server的投票将选举出新的Leader, 新的Leader通过心跳AppendEntries RPC宣告自己的存在, 收到心跳的Server更新自己的状态
若超时时间内无新的Leader产生, 再进行下一轮投票, 为了避免这种情况, 应当给不同Server的投票超时设定随机值
```

#### 需要实现的功能
- 一个协程不断检测一个投票间隔内是接收到心跳或AppendEntries RPC(其实是一个东西), 如果没有接受到, 则发起投票
- 处理投票的协程, 发起投票并收集投票结果以改变自身角色
- 不断发送心跳的Leader的心跳发射器协程
- 处理投票请求的RPC
- 处理心跳的RPC 

#### git操作

1. 通过ssh连接远程仓库 git remote add origin git@github.com:resgt/6.5840.git 需要在~/.ssh中有sha文件
2. 删除远程连接 git remote rm origin
3. git 创建分支 git branch 2A git checkout 2A 创建分支、切换分支

#### 实现过程 
https://github.com/ToniXWD/MIT6.5840/blob/master/reports/Lab2_Raft_2A.md