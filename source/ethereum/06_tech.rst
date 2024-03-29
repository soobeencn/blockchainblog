核心模块
==================

共识算法对比
-----------------


POW
>>>>>>>>>>
        - hash(B) < M/D(1) 
        - 其中D ∈[1 , M], hash = sha256
        - M 在不同的算法里面设置的可能会不一样,我们这里可以认为就是常量
        - D就是目标难度,D值越大(M为常量),那么M/D就会越小,所以要使用hash 找出这个值就会越难
POS
>>>>>>>>>>
        - hash(hash(Bprev), A, t) < balance(A) * M/D 
        - 其中hash = sha256
        - M/D 和 POW 的公式里面的M/D 是同一个意思
        - Bprev指上一块的hash值
        - 注意:右边变量乘上了用户的余额,意味着在M/D一样的情况下, 那么余额越大算出的概率也会越大
    * 潜在的攻击问题:  
        * Nothing at stake， 由于 PoS 不需要消耗太多算力，所以当出现分叉时，矿工为了利益最大化会选择同时在两个分叉进行挖矿，从而导致更多的分叉。而 PoW 是算力敏感的，矿工只能选择押注其中一条路径。
        * 长距离攻击，在 PoW 中恶意节点即使拥有 51% 以上的算力，如果想篡改账本也是需要花费大量的成本。而 PoS 对于算力的要求很低，那么就存在被篡改的风险
        * 币龄累积攻击，有些算法实现中除了考虑拥有的资产之外还加上了币龄，那么就有可能导致部分节点不需要持有 51% 的资产就可以产生攻击

DPOS
>>>>>>>>>>>
        

总结
>>>>>>>>>>>>>>>>>>
    - pow 每个矿工都是一个节点  矿工加入到节点之后  开始hash(nonce) 计算一个数值  这个过程就存在一些问题   加入节点之后  每个矿工都会去计算一个数值  如果有一个人计算出来   其他人所做的工作(利用cpu或者显卡计算)等于 0 也就是无用功.另外一点qps也是一个瓶颈, pow机制下并没有很好的解决方案去突破qps.第三点,我想到的就是多节点同步问题  (分布式,集群方案中存在多节点问题,偶尔会出现某个节点挂掉) 
    - pos 在pow算法上  加入了balance的概念  也就是我拥有的余额多  那么"我"所占的权重更大,相比较pow中的人群, 我计算hash值的难度越低.(还需要再研究一下)
    - dpos 中心化  所有的节点通过投票选举出超级节点 这个超级节点就类似于pow和pos的矿工(节点) 换句话说 pow/pos是全网的矿工都是节点  都要去挖矿、广播、验证,每个矿工都有可能出块  dpos是只有选举出来的超级节点出块  dpos比pow/pos多出来的步骤(矿工投票、选举验证人、验证候选人[pow/pos中的矿工只需要去验证出块节点的对错]) 所以在dpos上关于如何选举   选举候选人的规则  候选人短期内作为超级节点  如果被恶意攻击或者其他网络原因造成宕机等结果,那么重新选举的机制如何设定等等  需要我再读源码去了解 



以太坊协议标准
--------------------------

ERC777对比ERC-20的优点:
>>>>>>>>>>>>>>>>>>>>>>>>

* 减少以太坊区块链上的交易摩擦
    - 使用ERC-20时,用户通常调用两个事件才能与smart contract进行交互.必须先将智能合约存入或者批准为Token的运营商,完成这些必要条件之后,才能在smart contract范围内调用所需的功能.此时,必须创建第二笔交易[消耗额外的费用和网络(延迟)].显然这不是交易货币及与区块链互动最有效的方式.
    - 为了解决这个问题,ERC777允许一个事件就触发契约的功能,这减少了以太坊整个区块链上的交易开销,减轻了以太坊区块链上的的流量问题和交易结算,从而帮助改善区块链上的延迟问题.
    - 因此,在分布式交易合约中交换一个Token给另一个Token,可以在一次交易中完成,而不是在两次,这使得用户转移Token更加的迅速和便宜.
* 发送交易数据
    - ERC777可以像以太坊那样,发送交易数据,调用智能合约的功能,使得可以超出以太坊本地可定制容量的Token;
    - ERC777的上述功能还可以促进所有类型应用程序之间的交互,并在智能合同,数字身份和分布式存储环境中使用加密货币.使用这一功能的区块链开发平台,则有可能在分布式网络中开启下一时代的应用程序、产品、服务.
* 减少加密货币意外丢失的可能性
    - 因为与ERC777兼容的地址,能够拒绝某些特定类型的Token传入事件,例如来自黑名单历史的Token传入事件;
    - 这个功能使Token可以处理KYC和AML的法规需求,这对以太坊未来的可靠性和透明度至关重要.
* ERC777向下兼容
    - ERC777兼容所有ERC-20兼容的应用程序,作为先前Token标准的升级.这些应用程序包括:EtherDelta,IDEX,0x等.它具有ERC-20所有的功能,同时自增的附加功能,比如阻止转移到不支持的智能合约而导致永久性丢失Token的情况发生,并与以太相同的方式调用smart contract中的功能.

总结:
>>>>>>>>>>
- ERC777已经改进了所有人对加密货币的体验,以促进加密货币的大规模使用,这是以太坊网络第一个可以完成所有事情的ERC标准;
- ERC777同时是完全可扩展的,企业可以根据自己的需求进行更加丰富的功能设置.
