Hyperledger Fabric 性能评测
==========================

环境配置
--------------------------
- go version : 1.13.4
- fabric version : v1.4.0 / v1.4.1
- caliper version : master 
- node version : v8.16.0 
- npm version : v6.4.1
- docker version : latest
- docker compose : latest
- fabric evm: 1order 2org 4peer

部署流程
-------------------------
* caliper源码部署
    * git clone https://github.com/hyperledger/caliper-benchmarks.git
* 初始化环境
    * npm init -y   
    * npm install --only=prod @hyperledger/caliper-cli
* 绑定fabric-sdk版本 
    * npx caliper bind --caliper-bind-sut fabric --caliper-bind-sdk 1.4.0 
* 按照配置文件执行测试
    * npx caliper benchmark run --caliper-workspace . --caliper-benchconfig benchmarks/scenario/simple/config.yaml --caliper-networkconfig networks/fabric/fabric-v1.4/2org1peergoleveldb/fabric-go.yaml

报告分析:
---------------------------
* 第一次测试(默认配置文件)
    - 服务器配置: 4core-8processor
    - 交易数量: 100笔   
    - tps(opts):
        - open:50
        - query:100
        - transfer:50
        - 结果如下:
        .. image:: /images/fabric_test_01.jpeg
* 第二次测试(默认配置文件)
    - 服务器配置: 8core-16processor
    - 交易数量: 100笔   
    - tps(opts):
        - open:50
        - query:100
        - transfer:50
        - 结果如下:
        - 此时发现,提升cpu性能之后, Fabric整体性能并未得到提升.于是开始修改网络配置文件,自定义交易数量及可选择的tps峰值选项.

* 第三次测试
    - 服务器配置: 8core-16processor
    - 交易数量: 10000笔   
    - tps(opts):
        - open:50
        - query:100
        - transfer:50
        - 结果如下: 

        - 上述结果,只修改了交易数量 100 --> 10000 , 发现Fabric整体性能并未得到提升.

* 第四次测试
    - 服务器配置: 8core-16processor
    - 交易数量: 10000笔   
    - tps(opts):
        - open:500
        - query:1000
        - transfer:500
        - 结果如下:

        - 加大tps峰值属性, 此时Fabric整体性能提升了四倍左右.

* 第五次测试
    - 服务器配置: 8core-16processor
    - 交易数量: 10000笔   
    - tps(opts):
        - open:5000
        - query:10000
        - transfer:5000
        - 结果如下:

        - 经过第四次与第五次测试发现, 提升了opts中的tps峰值, 已无法提升Fabric整体性能, 于是此时继续提升服务器性能.

* 第六次测试
    - 服务器配置: 16core-32processor
    - 交易数量: 1000笔   
    - tps(opts):
        - open:500
        - query:1000
        - transfer:500
        - 结果如下:

        - 为保证结果足够准确,提升服务器性能之后, 仅扩大tps峰值10倍, 进行了上述测试, 发现与8core-16processor服务器结果几乎一致, 因此继续修改网络配置文件.

* 第七次测试
    - 服务器配置: 16core-32processor
    - 交易数量: 10000笔   
    - tps(opts):
        - open:5000
        - query:10000
        - transfer:5000
        - 结果如下:

        - 同样与上述测试几乎相当, 测试到此结束.

总结: 
----------------------
* 1.Fabric不同场景下, open TPS维持在220左右, query TPS保持在440左右, transfer TPS维持在180左右, 同时交易成功率仅有25% ~ 50%左右, 另外交易延迟区间也足够大, 这点可能会影响到账速度.
* 2.在测试过程中,执行open测试的时候, 发现cpu占用率达到100%以上,同时经过多轮提升cpu性能均会出现cpu占用率超过100%的情况,无法提升send rate, 导致吞吐率无法从根本上提升.
* 3.以上数据均根据caliper性能测试工具得出,结果可能会与Fabric官方给出的数据存在些许偏差.

    

