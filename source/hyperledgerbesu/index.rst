企业以太坊 [Hyperledger Besu]
=================================

.. 背景::

    “据中华人民共和国国民经济和社会发展第十四个五年规划和2035年远景目标纲要，我国将会加快推动数字产业化进程，区块链作为数字经济重点产业进入国家规划布局，具体内容包括推动智能合约、共识算法、加密算法、分布式系统等区块链技术创新，以联盟链为重点发展区块链服务平台和金融科技、供应链管理、政务服务等领域应用方案，完善监管制度。”

    本文主要针对那些对Hyperledger比较陌生的人。对于那些有兴趣提供区块链解决方案架构师服务和做区块链企业咨询和开发的人来说，这篇技术指南将是一个很好的资源。

    本文所包含的资料将帮助你从整体上理解Hyperledger DLT，并以其高层次的概述作为指导，使每个Hyperledger项目发挥出最佳的效果。


.. HyperledgerBesu::

    Hyperledger Besu是一个开源的Ethereum客户端，在Apache 2.0许可下开发，用Java编写。它可以运行在Ethereum公共网络上，也可以运行在私有许可网络上，以及Rinkeby、Ropsten和Gorli等测试网络上。Hyperledger Besu支持多种共识算法，包括PoW、PoA和IBFT，并有专门为在联盟环境中使用而设计的综合权限方案。

    Besu实现了企业Ethereum联盟（EEA）规范。EEA规范的建立是为了在Ethereum内的各种开源和闭源项目之间建立共同的接口，以确保用户不会被供应商锁定，并为构建应用程序的团队创建标准接口。Besu实现了与EEA客户端规范一致的企业功能。

.. toctree::
    :maxdepth: 2
    :numbered: 2

    01_introduce
    02_quickstart
    03_network
    04_smartcontract
    05_upperinfra
    06_systermdesign
