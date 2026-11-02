// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.7.0
pragma solidity ^0.8.27;

import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import {ERC1363} from "@openzeppelin/contracts/token/ERC20/extensions/ERC1363.sol";
import {ERC20FlashMint} from "@openzeppelin/contracts/token/ERC20/extensions/ERC20FlashMint.sol";
import {ERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
import {ERC20Bridgeable} from "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Bridgeable.sol";

/// @custom:security-contact Alchemy@tether.io
contract MonetisedUSD is ERC20, ERC20Bridgeable, Ownable, ERC1363, ERC20Permit, ERC20FlashMint {
    address public tokenBridge;
    error Unauthorized();

    constructor(address tokenBridge_, address initialOwner)
        ERC20("Monetised USD", "MUSDT")
        Ownable(initialOwner)
        ERC20Permit("Monetised USD")
    {
        require(tokenBridge_ != address(0), "Invalid tokenBridge_ address");
        tokenBridge = tokenBridge_;
    }

    function _checkTokenBridge(address caller) internal view override {
        if (caller != tokenBridge) revert Unauthorized();
    }

    function setTokenBridge(address tokenBridge_) public onlyOwner {
        require(tokenBridge_ != address(0), "Invalid tokenBridge_ address");
        tokenBridge = tokenBridge_;
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // The following functions are overrides required by Solidity.

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC20Bridgeable, ERC1363)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}