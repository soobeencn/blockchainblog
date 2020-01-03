去中心化应用 [Dapp开发指南]
=========================

python环境准备
-------------
:: 

    pip install pipenv
    pipenv --python=python3.6.4
    pipenv install flask==0.12.2   (ignore warning)
    pipenv install requests==2.18.4

发行自己的数字货币
--------------
开发工具
>>>>>>
    - remix/metamask
代币合约开发
>>>>>>
    - solidity
实战
---------------
* 知识点
    - HTML、JavaScript、CSS
    - Web服务器
    - 智能合约
    - Web3.js
* 开发流程
    - 新建项目
    - 合约编写
    - 合约编译、部署、测试
    - 与合约交互
* 开发工具
    - Vs Code/Atom/Subline
    - Truffle/Ganache
    - MetaMask
* Truffle命令
    - truffle compile:讲solidity代码编译成字节码(EVM能理解的代码)   ganache模拟了EVM
    - truffle migrate:把代码部署到区块链
    - truffle console    truffle console  --network development
    - truffle test
    - npm run dev
    - npm install lite-server


