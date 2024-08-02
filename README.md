# ERC20 Token

This Solidity program is a demonstration of a contract that has different authorization levels.

## Description

In this contract, there are different permissions for the type of user. The owner is the sole user who has exclusive rights to mint new tokens, ensuring a controlled token supply. Other users can burn and transfer their tokens, but they are not allowed to mint. It basically shows real-life application of blockchain transactions and even cash payment. 

## Getting Started

### Executing program

To run this program, you can use Remix, an online Solidity IDE.

Go to the Remix website at https://remix.ethereum.org/ then create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension. Before compiling, copy and paste this code:

Assessment.sol

    ```javascript
        // SPDX-License-Identifier: UNLICENSED
        pragma solidity ^0.8.9;
        
        contract Assessment {
            address payable public owner;
            mapping(address => uint256) public balances;
        
            event Deposit(address indexed account, uint256 amount);
            event Withdraw(address indexed account, uint256 amount);
        
            constructor() {
                owner = payable(msg.sender);
            }
        
            function getBalance(address account) public view returns(uint256){
                return balances[account];
            }
        
            function deposit(uint256 _amount) public payable {
                uint _previousBalance = balances[msg.sender];
        
                // perform transaction
                balances[msg.sender] += _amount;
        
                // assert transaction completed successfully
                assert(balances[msg.sender] == _previousBalance + _amount);
        
                // emit the event
                emit Deposit(msg.sender, _amount);
            }
        
            // custom error
            error InsufficientBalance(uint256 balance, uint256 withdrawAmount);
        
            function withdraw(uint256 _withdrawAmount) public {
                uint _previousBalance = balances[msg.sender];
                if (balances[msg.sender] < _withdrawAmount) {
                    revert InsufficientBalance({
                        balance: balances[msg.sender],
                        withdrawAmount: _withdrawAmount
                    });
                }
        
                // withdraw the given amount
                balances[msg.sender] -= _withdrawAmount;
        
                // assert the balance is correct
                assert(balances[msg.sender] == (_previousBalance - _withdrawAmount));
        
                // emit the event
                emit Withdraw(msg.sender, _withdrawAmount);
            }
        }  
    ```

index.js

     ```javascript
            import { useState, useEffect } from "react";
            import { ethers } from "ethers";
            import atm_abi from "../artifacts/contracts/Assessment.sol/Assessment.json";
            
            export default function HomePage() {
              const [ethWallet, setEthWallet] = useState(undefined);
              const [account, setAccount] = useState(undefined);
              const [atm, setATM] = useState(undefined);
              const [balance, setBalance] = useState(undefined);
              const [transactionCount, setTransactionCount] = useState(undefined);
            
              const contractAddress = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
              const atmABI = atm_abi.abi;
            
              const getWallet = async () => {
                if (window.ethereum) {
                  setEthWallet(window.ethereum);
                }
            
                if (ethWallet) {
                  const accounts = await ethWallet.request({ method: "eth_accounts" });
                  handleAccount(accounts);
                }
              };
            
              const handleAccount = (accounts) => {
                if (accounts.length > 0) {
                  console.log("Account connected: ", accounts[0]);
                  setAccount(accounts[0]);
                } else {
                  console.log("No account found");
                }
              };
            
              const connectAccount = async () => {
                if (!ethWallet) {
                  alert('MetaMask wallet is required to connect');
                  return;
                }
            
                const accounts = await ethWallet.request({ method: 'eth_requestAccounts' });
                handleAccount(accounts);
            
                getATMContract();
              };
            
              const getATMContract = () => {
                const provider = new ethers.providers.Web3Provider(ethWallet);
                const signer = provider.getSigner();
                const atmContract = new ethers.Contract(contractAddress, atmABI, signer);
            
                setATM(atmContract);
              };
            
              const getBalance = async () => {
                try {
                  if (atm && account) {
                    console.log("Fetching balance for account:", account);
                    const balance = await atm.getBalance(account);
                    console.log("Balance fetched:", balance.toString());
                    setBalance(balance.toNumber());
                  } else {
                    console.error("ATM contract or account is not set");
                  }
                } catch (error) {
                  console.error("Error fetching balance:", error);
                }
              };
            
              const deposit = async () => {
                try {
                  if (atm) {
                    let tx = await atm.deposit(1);
                    await tx.wait();
                    getBalance();
                    getTransactionCount(); // Update transaction count after deposit
                  }
                } catch (error) {
                  console.error("Error depositing:", error);
                }
              };
            
              const withdraw = async () => {
                try {
                  if (atm) {
                    let tx = await atm.withdraw(1);
                    await tx.wait();
                    getBalance();
                    getTransactionCount(); // Update transaction count after withdrawal
                  }
                } catch (error) {
                  console.error("Error withdrawing:", error);
                }
              };
            
              const getTransactionCount = async () => {
                if (!ethWallet || !account) return;
            
                const provider = new ethers.providers.Web3Provider(ethWallet);
                const transactionCount = await provider.getTransactionCount(account);
                setTransactionCount(transactionCount);
              };
            
              const initUser = () => {
                if (!ethWallet) {
                  return <p>Please install Metamask in order to use this ATM.</p>;
                }
            
                if (!account) {
                  return <button onClick={connectAccount}>Please connect your Metamask wallet</button>;
                }
            
                if (balance === undefined) {
                  getBalance();
                }
            
                return (
                  <div>
                    <p>Your Account: {account}</p>
                    <p>Your Balance: {balance}</p>
                    <p>Total Transactions: {transactionCount}</p> {/* Display transaction count */}
                    <button onClick={deposit}>Deposit 1 ETH</button>
                    <button onClick={withdraw}>Withdraw 1 ETH</button>
                  </div>
                );
              };
            
              useEffect(() => {
                getWallet();
                getTransactionCount(); // Fetch transaction count after initializing wallet and account
              }, []);
            
              return (
                <main className="container">
                  <header><h1>Welcome to the Metacrafters ATM!</h1></header>
                  {initUser()}
                  <style jsx>{`
                    .container {
                      text-align: center;
                    }
                  `}
                  </style>
                </main>
              );
            }

        ```
After following the instructions, you have the front end already, through **npm run dev** in the first terminal. Now that you have the front end you are free to interact with the buttons and see the interactions with the metamask wallet.

## Authors

Elijah Raphael A. Gaylan
