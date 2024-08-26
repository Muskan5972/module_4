# Module4
This is a smart contract in the form of Token for Degen Gaming where several functionalities can be performed.

# Getting Started
Executing program Steps to be followed inorder to run the contract seamlessly:

First we have to take test credit in the form of AVAX tokens in the Avalanche Fuji C-Chain. Now we will go to core web app to request for Tokens and there we have to enter the coupon code We will get 0.5 AVAX per day and after that we can use those to test our contract. Now we will go back to Remix and then choose Environment as Injected Provider - Metamask and after that deploy the contract. Now we wil copy the address of the deployed contract and then paste it in Snowtrace Testnet and then click on verify and publish contracts. Then we will click on verify and then select chain as Fuji, enter the address of the new contract and then select the compiler version mentioned in the solidity file. After all is done we will now perform transaction in the contract and those will be logged in the Snowtrace.

# code
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
# Working
After the Contract is deployed we can perform the following operations in it:

Minting Tokens: Creating new Tokens (can only be done by the ownwer). Burning Tokens: Burn the Tokens from the whole Smart Contract. RedeemTokens: Buy different merchandise by using Tokens. Transfer Tokens: Transfer Token from a particular address to another address. GetBalance: Check the balance of a particular address at a particular time.

Apart from it we also have to flatten the file because the JSON format has to be adaptible as normal import statements are not compatible in the verification portal.

# Authors
Muskan 

