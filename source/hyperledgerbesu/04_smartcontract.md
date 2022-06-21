## 智能合约开发规范

使用 Truffle 为区块链平台开发与使用 Truffle 为以太坊网络开发相同。Truffle 支持 Besu，唯一的区别是 Besu 不支持私钥管理。要将 Besu 与 Truffle 一起使用，必须配置一个 Truffle 钱包。

### 必要条件
安装环境
```bash
npm install -g truffle
```

### 安装Truffle钱包
```bash
npm install --save @truffle/hdwallet-provider
```
**笔记**

_使用 Truffle 5，您必须使用支持 Web3 1.0 的钱包，否则 Truffle 任务会挂起。_

### 更新Truffle配置文件

要添加钱包提供程序，请更新truffle-config.js项目目录中的文件。代替：

- JSON-RPC-http-endpoint: 使用 Besu 节点的 JSON-RPC 端点（IP 地址和端口）。
- account-private-key: 使用包含 Ether 的 Ethereum 帐户的私钥。

```js
const PrivateKeyProvider = require("@truffle/hdwallet-provider");
const privateKey = "<account-private-key>";
const privateKeyProvider = new PrivateKeyProvider(privateKey, "<JSON-RPC-http-endpoint>");

module.exports = {
  // See <http://truffleframework.com/docs/advanced/configuration>
  // for more about customizing your Truffle configuration!
  networks: {
    besuWallet: {
      provider: privateKeyProvider,
      network_id: "*"
    },
  }
};
```

### 编写合约
#### 使用Truffle
##### step1: 创建项目目录
```bash
mkdir ticket-dapp && cd ticket-dapp/
```

##### step2: 项目初始化
```bash
truffle init
```

##### step3: 创建合约
```bash
cd contracts/
```
```bash
touch contracts/NFT.sol && vim NFT.sol
```
result:
```sol
// SPDX-License-Identifier: MIT
// OpenZeppelin Contracts v4.4.1 (access/Ownable.sol)

pragma solidity ^0.8.1;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract TicketNFT is ERC721, Ownable {
    ...    
}
```
##### step4: 安装依赖
**与remix不同之处在于, truffle中不支持对URL的导入**
```bash
npm install @openzeppelin/contracts
```

#### 使用Remix

在项目根目录下的contracts/下创建合约文件, 例如: NFT.sol, 然后编辑:
```sol
pragma solidity ^0.8.1;

import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol';
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol';

contract NFT is ERC721, Ownable {
    
    using Strings for uint256;
    
    // Optional mapping for token URIs
    mapping (uint256 => string) private _tokenURIs;

    // Base URI
    string private _baseURIextended;

    constructor(string memory _name, string memory _symbol) ERC721(_name, _symbol) {}
    
    function setBaseURI(string memory baseURI_) external onlyOwner() {
        _baseURIextended = baseURI_;
    }
    
    function _setTokenURI(uint256 tokenId, string memory _tokenURI) internal virtual {
        require(_exists(tokenId), "ERC721Metadata: URI set of nonexistent token");
        _tokenURIs[tokenId] = _tokenURI;
    }
    
    function _baseURI() internal view virtual override returns (string memory) {
        return _baseURIextended;
    }
    
    function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {
        require(_exists(tokenId), "ERC721Metadata: URI query for nonexistent token");

        string memory _tokenURI = _tokenURIs[tokenId];
        string memory base = _baseURI();
        
        // If there is no base URI, return the token URI.
        if (bytes(base).length == 0) {
            return _tokenURI;
        }
        // If both are set, concatenate the baseURI and tokenURI (via abi.encodePacked).
        if (bytes(_tokenURI).length > 0) {
            return string(abi.encodePacked(base, _tokenURI));
        }
        // If there is a baseURI but no tokenURI, concatenate the tokenID to the baseURI.
        return string(abi.encodePacked(base, tokenId.toString()));
    }
    
    function mint(address _to, uint256 _tokenId, string memory image_url) external onlyOwner() {
        _mint(_to, _tokenId);
        _setTokenURI(_tokenId, image_url);
    }
}
```

### 部署合约
#### 使用Truffle
```bash
truffle migrate --network besuWallet
```
result:
```bash
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.


Starting migrations...
======================
> Network name:    'besuWallet'
> Network id:      1337
> Block gas limit: 4700000 (0x47b760)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x834ad971e6cbe802ba33e4449d2867740541f9e1e7a2f7529dfbdf73f3ad8fdd
   > Blocks: 0            Seconds: 12
   > contract address:    0x38cF23C52Bb4B13F051Aec09580a2dE845a7FA35
   > block number:        375744
   > block timestamp:     1650534867
   > account:             0x627306090abaB3A6e1400e9345bC60c78a8BEf57
   > balance:             90000
   > gas used:            248854 (0x3cc16)
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH

Summary
=======
> Total deployments:   1
> Final cost:          0 ETH

```

#### 使用Remix
##### step1: 设置环境 
```bash
Injected web3
```

##### step2: 关联metamask

##### step3: 点击 Deploy

##### step4: 调用contract



------------------------

