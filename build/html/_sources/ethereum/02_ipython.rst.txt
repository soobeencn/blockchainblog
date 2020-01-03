智能合约 [solidity]
=========================

什么是智能合约
--------------
* 以太坊上的程序,是代码和数据(状态)的集合
* 准图灵完备
* 典型应用:代币:EOS    游戏:迷恋猫

运行环境-->EVM
---------------
* solidity--->EVM(提供了一个沙盒的环境)
* java------->JVM

solidity 语言详解
---------------
文件结构
>>>>>>
    * 版本声明
    * import
    * 合约
        - 状态变量
        - 函数
        - 结构类型
        - 事件
        - 函数修改器
    * 代码注释
数据类型
>>>>>>
* 值类型(Value Type)
    - Booleans
    - Integers
        - int/uint        关键字unit8到uint256(以8步进)
    - Fixed Point Numbers
    - Fixed-Size Byte Arrays
    - Rational and Integer Literals
    - String Literals
    - Hexadecimal literals
    - Enums
    - Function Types
    - Address:表示一个账户地址(20字节,16进制数)
        - 成员
            - 属性:balance
            - 函数:transfer()
    - Address Literals
        - 地址合法性检查
* 引用 类型(Reference Type)    Data Location(memory、storage)
    * 数组(Arrays)
        * T[K]:元素类型为T,固定长度为K的数组
        * T[]:元素类型为T,长度动态调整
        * bytes string:一种特殊的数组
        * string可转化为bytes,bytes类似于byte[]
        * 成员
            - 属性:length
            - 函数:push()

结构体和映射
----------

映射(Mappings)
>>>>>>
* mapping(address=>uint)
* public balances

全局变量和函数
--------------
* 区块和交易属性(查看官方文档)
    - msg.sender(address)   交易附带地址
    - msg.value(uint)            当前交易附带的亿台币  (wei)
    - block.coinbase(address)     当前块的地址   当前矿工的地址
    - block.difficulty(uint)              当前块的难度
    - block.number(uint)               当前块的块号
    - block.timestamp(uint)           当前块的时间戳  1970.1.1 0.0
    - now(uint)                               timestamp的别名
    - tx.gasprice(uint)                    当前交易的价格
* 错误处理
    - 什么是错误处理
        - 程序发生错误时,处理方式---回调
    - 如何处理
        - assert   (gas回退)
        - require  (gas不足)
* 有关数字及加密功能
* 有关地址和合约

参数
---------
    * 输入参数
    * 输出参数
    * 命名参数
    * 参数解构
控制结构
-------
    * 没有switch goto
可见性
-------
* public   消耗的gas更多
        - 公开函数是合约接口的一部分,可以通过内部,或者消息来进行调用.对于public类型的状态变量,会自动创建一个访问器
        - 函数默认可见性是public
* private
        - 私有函数和状态变量仅在当前合约中可以访问,在继承的合约内,不可访问
* external     消耗的gas更少
        - 外部函数是合约接口的一部分,只能使用消息调用
* internal
        - 函数和状态变量只能通过内部访问.如在当前的合约中调用,或继承的合约里调用.
        - 状态变量默认的可见性是internal
函数
--------
    - 构造函数
    - 视图函数(constant/view)
    - 纯函数(pure)
    - 回退函数
    - 函数重载
