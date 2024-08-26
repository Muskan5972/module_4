# Module4
This is a smart contract in the form of Token for Degen Gaming where several functionalities can be performed.

# Getting Started
Executing program Steps to be followed inorder to run the contract seamlessly:

To run the DegenToken contract seamlessly, first set up your environment by installing MetaMask and ensuring it has some test ETH, and choose a Solidity development environment like Remix, Truffle, or Hardhat. Next, compile the contract using your chosen development tool. Once compiled, deploy the contract to a test network—if using Remix, connect with MetaMask to deploy, or use truffle migrate or npx hardhat run for Truffle or Hardhat. After deployment, interact with the contract's functions, such as mint, burn, listItem, and redeem, to manage tokens and items. Finally, thoroughly test all functionalities on a testnet to verify the contract works correctly before deploying it to the mainnet.

# code
'''javascript
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract DegenToken is ERC20, Ownable {

    struct Item {
        uint id;
        string name;
        uint price;
        bool available;
    }
    
    mapping(uint => Item) private items;
    uint private itemCount;

    mapping(address => Item[]) private redeemedItems;

    event ItemListed(uint indexed id, string name, uint price);
    event ItemRedeemed(address indexed account, uint indexed itemId, string itemName, uint itemPrice);

    constructor(address initialOwner) 
    ERC20("Degen", "DGN") 
    Ownable(initialOwner) {
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    function transfer(address to, uint256 amount) public override returns (bool) {
        _transfer(_msgSender(), to, amount);
        return true;
    }
     function burn(uint256 amount) public {
        require(amount > 0, "Amount must be greater than zero");
        require(balanceOf(msg.sender) >= amount, "Insufficient balance");

        _burn(msg.sender, amount);
    }

    function redeem(uint itemId) public {
        require(itemId > 0 && itemId <= itemCount, "Invalid item ID");
        require(items[itemId].available, "Item not available");
        require(balanceOf(msg.sender) >= items[itemId].price, "Insufficient balance");

        _transfer(msg.sender, owner(), items[itemId].price);
        redeemedItems[msg.sender].push(items[itemId]);
        items[itemId].available = false;

        emit ItemRedeemed(msg.sender, itemId, items[itemId].name, items[itemId].price);
    }
    
    function listItem(string memory name, uint price) public onlyOwner {
        itemCount++;
        items[itemCount] = Item(itemCount, name, price, true);

        emit ItemListed(itemCount, name, price);
    }
    function _addInitialItems() internal onlyOwner {
        listItem("Sword", 100 * 10 ** 18);
        listItem("Water Shield", 150 * 10 ** 18);
        listItem("Health Potion", 50 * 10 ** 18);
        listItem("Cape", 50 * 10 ** 18);
    }

    function getItem(uint itemId) public view returns (Item memory) {
        require(itemId > 0 && itemId <= itemCount, "Invalid item ID");
        return items[itemId];
    }

    function getItemCount() public view returns (uint) {
        return itemCount;
    }

    function checkBalance(address account) public view returns (uint256) {
        return balanceOf(account);
    }


    function getRedeemedItems(address account) public view returns (Item[] memory) {
        return redeemedItems[account];
    }
}
javascript '''
# Working
After the Contract is deployed we can perform the following operations in it:

Minting Tokens: Creating new Tokens (can only be done by the ownwer). Burning Tokens: Burn the Tokens from the whole Smart Contract. RedeemTokens: Buy different merchandise by using Tokens. Transfer Tokens: Transfer Token from a particular address to another address. GetBalance: Check the balance of a particular address at a particular time.

Apart from it we also have to flatten the file because the JSON format has to be adaptible as normal import statements are not compatible in the verification portal.

# Authors
Muskan 

