## EIP

### ERC-20
#### Token发行
```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Token is ERC20 {
    constructor(uint256 initialSupply) ERC20("BToken", "BT") {
        _mint(msg.sender, initialSupply);
    }

    function burn(uint256 amount) public virtual {
        _burn(_msgSender(), amount);
    }
}
```


### ERC-721

http://112.74.169.88:8080/ipfs/QmQzqrM3fCdNq3BWckXmR1zWJ4oiQBUWVm7iRFqeGTdGxE

{
    "name": yz,
    "description": "this is a photo",
    "image": "http://112.74.169.88:8080/ipfs/QmQzqrM3fCdNq3BWckXmR1zWJ4oiQBUWVm7iRFqeGTdGxE",
    "strength": 300
}

#### NFT-1
```sol
//Contract based on [https://docs.openzeppelin.com/contracts/3.x/erc721](https://docs.openzeppelin.com/contracts/3.x/erc721 "https://docs.openzeppelin.com/contracts/3.x/erc721")
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";

contract MyNFT is ERC721URIStorage, Ownable {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("FirstNFT", "symbol") {}

    function mintNFT(address recipient, string memory tokenURI) public onlyOwner returns (uint256) {
        _tokenIds.increment();

        uint256 newItemId = _tokenIds.current();
        _mint(recipient, newItemId);
        _setTokenURI(newItemId, tokenURI);

        return newItemId;
    }
}
```

#### NFT-2
```sol
// contracts/GameItem.sol
// SPDX-License-Identifier: MIT

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
    
    function _setTokenURI(uint256 tokenId, string memory image_url) internal virtual {
        require(_exists(tokenId), "ERC721Metadata: URI set of nonexistent token");
        _tokenURIs[tokenId] = image_url;
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