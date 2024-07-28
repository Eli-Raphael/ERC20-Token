# ERC20 Token

This Solidity program is a demonstration of a contract that has different authorization levels.

## Description

In this contract, there are different permissions for the type of user. The owner is the sole user who has exclusive rights to mint new tokens, ensuring a controlled token supply. Other users can burn and transfer their tokens, but they are not allowed to mint. It basically shows real-life application of blockchain transactions and even cash payment. 

## Getting Started

### Executing program

To run this program, you can use Remix, an online Solidity IDE.

Go to the Remix website at https://remix.ethereum.org/ then create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension. Before compiling, copy and paste this code:

```javascript

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    address public owner;

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _;
    }

    constructor(string memory name, string memory symbol) ERC20(name, symbol) {
        owner = msg.sender;
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    function burn(uint256 amount) public {
        _burn(msg.sender, amount);
    }

    function transfer(address recipient, uint256 amount) public override returns (bool) {
        _transfer(_msgSender(), recipient, amount);
        return true;
    }
}

```

To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to "0.8.26" (or another compatible version), and then click on the Compile "ERC20 Token.sol" button.

Once the code is compiled, click "Deploy & Run Transactions" tab in the left-hand sidebar. Select the "ERC20 Token.sol" contract from the dropdown menu, type in the name that you want for the contract and then click on the "Deploy" button.


Proceed to the dropdown of whatever you have named your contract to interact with the user interface that will let you call on the different functions in here. If you want to test the three requirements:

Only the owner can mint tokens.
Any account can burn tokens.
Any account can transfer tokens.
Be mindful of the account that you are using, it may get confusing at times. Be sure that you have copied the right account as well to avoid getting confused.

Notes: To change accounts, click the "Deploy & Run Transactions" and click on the "Accounts" Drop down to select an account to use for minting, burning, and transferring. To copy the account code, click the button furthest from the "Accounts" label above the dropdown, copy it to the text box beside mint or burn to use that account for a specific function.


## Authors

Elijah Raphael A. Gaylan
