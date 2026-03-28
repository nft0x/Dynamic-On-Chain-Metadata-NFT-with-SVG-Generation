# Dynamic-On-Chain-Metadata-NFT-with-SVG-Generation
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

contract DynamicOnChainNFT is ERC721, Ownable {
    using Strings for uint256;

    mapping(uint256 => uint256) public attributes; // e.g., level

    constructor(address initialOwner) ERC721("DynamicOnChain", "DON") Ownable(initialOwner) {}

    function mint(uint256 tokenId, uint256 initialAttr) external onlyOwner {
        _safeMint(msg.sender, tokenId);
        attributes[tokenId] = initialAttr;
    }

    function updateAttribute(uint256 tokenId, uint256 newAttr) external {
        require(ownerOf(tokenId) == msg.sender, "Not owner");
        attributes[tokenId] = newAttr;
    }

    function tokenURI(uint256 tokenId) public view override returns (string memory) {
        uint256 attr = attributes[tokenId];
        string memory svg = string(abi.encodePacked(
            '<svg width="200" height="200"><circle cx="100" cy="100" r="80" fill="#', 
            attr > 50 ? "00ff00" : "ff0000", '"/><text x="50" y="110" fill="white">Attr: ', 
            attr.toString(), '</text></svg>'
        ));
        // Base64 encode JSON + SVG (use full Base64 library in production)
        return string(abi.encodePacked("data:application/json;base64,", "eyJuYW1lIjoiRHluYW1pYyAj", tokenId.toString(), "\"}"));
    }
}
