## 合约设计
### 渠道存证合约
```sol
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;

contract Storage {

    struct Channel {
        uint256 id; 
        string createTime; 
        string updateTime; 
        string code;
        string name; 
        uint status;
        string remark;
    }

    mapping(uint256 => Channel) _channelMap;
    
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    // bytes32 public name = keccak256("name");

    modifier onlyOwner() {
        require(owner == msg.sender, "only owner can call this function");
        _;
    }

    function storeChannel(uint256 _id,
                          string memory _createTime, 
                          string memory _updateTime, 
                          string memory _code, 
                          string memory _name, 
                          uint _status, 
                          string memory _remark) public onlyOwner returns(Channel memory) {                      
        Channel memory _channel;
        _channel.id = _id;
        _channel.createTime = _createTime;
        _channel.updateTime = _updateTime;
        _channel.code = _code;
        _channel.name = _name;
        _channel.status = _status;
        _channel.remark = _remark;

        _channelMap[_id] = _channel;

        return _channelMap[_id];

    }

    function getInfo(uint256 _id) public view returns(Channel memory) {
        return _channelMap[_id];
    }

    function kill() public {
        selfdestruct(payable(owner));
    }
}
```

