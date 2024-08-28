# Module4
This is a smart contract in the form of Token for Degen Gaming where several functionalities can be performed.

## Descripition
DegenToken is an ERC-20 token built on the Ethereum blockchain using the Solidity programming language. The contract is based on OpenZeppelin's ERC-20 and Ownable implementations, and it introduces unique features for listing and redeeming items. Token holders can redeem items using their tokens, and only the contract owner has the authority to list new items.

## Functions:
* __Constructor:__
   * Initializes the token with the name "Degen" and symbol "DGN".
   * Sets the initial owner of the contract.
* __mint(address to, uint256 amount):__
  * Allows the owner to mint new tokens to the specified address.
* __transfer(address to, uint256 amount):__
  * Overrides the ERC-20 transfer function to facilitate token transfers between addresses.
* __burn(uint256 amount):__
  * Allows users to burn their tokens, permanently reducing the total supply.
* __redeem(uint itemId):__
  * Allows users to redeem an item by transferring the item's price in tokens to the owner.
  * Records the redeemed item under the user's account and marks the item as unavailable.
* __listItem(string memory name, uint price):__
  * Allows the owner to list a new item by specifying its name and price.
    
## code
'''

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
'''

## Working
The DegenToken project is an ERC-20 token with additional features for item listing and redemption. Here's a brief overview of how it works:

*__Minting Tokens:__ The contract owner can create new Degen (DGN) tokens and distribute them to users

*__Transferring and Burning:__ Users can transfer tokens to others or burn them to reduce the total supply.

*__Listing Items:__ The owner lists items with a name and price in DGN tokens. These items are available for users to redeem.

*__Redeeming Items:__ Users can spend their DGN tokens to redeem listed items. Once redeemed, the item becomes unavailable, and the tokens are transferred to the owner.

*__Querying:__ Users can check item details, their token balance, and view items they've redeemed.

## Authors
Muskan 

