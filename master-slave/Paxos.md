

## 一、快速了解Paxos

资料来源：[https://www.quora.com/In-distributed-systems-what-is-a-simple-explanation-of-the-Paxos-algorithm](https://www.quora.com/In-distributed-systems-what-is-a-simple-explanation-of-the-Paxos-algorithm)

Paxos: [https://en.wikipedia.org/wiki/Paxos_algorithm](https://en.wikipedia.org/wiki/Paxos_algorithm)

Explanation and Proof: [https://pdos.csail.mit.edu/6.824/papers/paxos-simple.pdf](https://pdos.csail.mit.edu/6.824/papers/paxos-simple.pdf)

###1. Paxos原理

1. 建立在Majority Agree的基础上，Paxos算法向任何一个peer保证，一旦某个value被majority of peers agree，这个value将再也不会被修改

2. 因为已经agree的peer数量占多数，之后对value的任何修改，想要达成新的agreement，必须经过其中一个peer

3. 从1、2可得出，一旦agree达成，任何一个节点向重新达成协议，必然要经过大多数peer，必然会访问到至少1个先前达成agreement的peer，也就必然能够得到前一个agreement的值

###2. 运行过程：

假定有A、B、C三个节点，A希望将value写为“foo”，分三阶段运行：

* **阶段1：准备阶段**

	A发送带sequence_number的Prepare Request给A、B、C，要求A、B、C许诺只接收sequence_number更高的请求
	
	收到Prepare Request的节点返回先前的value值
	
	A必须用highest sequence_number来propose收到的value值，用来确保先前的agreement可以被preserved

* **阶段2：Accept阶段**

	A发送带sequence_number的Accept Request给A、B、C，询问三个节点是否接受新的value值
	
	如果Accept Request的sequence_number高于接收方先前许诺的值、或者之前已经Accept过，那么接收方会Accept发送方的请求
	
	如果A收到了超过半数node的Accept Reply，那么value的值已经被成功更新为"foo"，这一轮将不再agree to another value

* **阶段3：Decision阶段（非必须步骤，用于优化，但production implementation通常都会实现）**

	A收到半数以上的Accept Reply之后，发送Decided Message给A、B、C，让所有节点知道value的值已经被更新，加速decision过程，减少其他节点不必要的尝试

### 3. 算法的关键点

* **sequence number**
	
	所有Message必须携带sequence number
	
	sequence number必须单调递增，并且唯一（A、B不能都发送sequence number为k的Message）
	
	每个node必须track自己promise的、以及收到的message的highest sequence number

* **failure conditions**

	agree过程失败时，node只需用更高的sequence number重新发起
	
* **termination conditions**

	这个版本的paxos不需要terminate，某些算法变种需要用到








