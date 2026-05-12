![](images/978-1-4842-8045-4_CoverFigure.jpg)

ISBN 978-1-4842-8044-7e-ISBN 978-1-4842-8045-4 [https://doi.org/10.1007/978-1-4842-8045-4](https://doi.org/10.1007/978-1-4842-8045-4) © Davi Pedro Bauer 2022 Standard Apress Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image we use the names, logos, and images only in an editorial fashion and to the benefit of the trademark owner, with no intention of infringement of the trademark. The use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identified as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights. The publisher, the authors and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, express or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

Introduction

This book is a step-by-step guide for everyone who wants to get started as an Ethereum developer. It was designed for those who have never programmed anything in the blockchain and want to get started.

I will cover everything from the basic requirements of installation to writing, testing, and deploying smart contracts. I will also cover topics such as IPFS, Filecoin, ENS, Chainlink, Truffle, Ganache, OpenZeppelin, Pinata, Fleek, Infura, MetaMask, and OpenSea, among others.

In Chapter [1](#521550_1_En_1_Chapter.xhtml), I will go through all the necessary requirements to start the activities described in this book. It covers software and tools such as Docker, Truffle, Ganache, MetaMask, and Infura.

In Chapter [2](#521550_1_En_2_Chapter.xhtml), you will learn how to create a basic Solidity project using the VS Code extension and then compile and deploy the smart contract to a local blockchain.

In Chapter [3](#521550_1_En_3_Chapter.xhtml), you will learn how to code smart contracts to create your own coin and deploy it to a local blockchain. Fungible tokens are interchangeable, so they are perfect to solve problems such as double spending. You will also be able to add this token to your own wallet and send it to different wallets, as well as send other coins that you already have.

In Chapter [4](#521550_1_En_4_Chapter.xhtml), you will learn how to create a unit test file for a smart contract, as well as write test assertions, run the unit tests, and check the unit test results.

In Chapter [5](#521550_1_En_5_Chapter.xhtml), you will be able to create smart contracts for badge tokens. You can use badge tokens, also known as NFTs, to represent physical things in the virtual world, such as digital collectibles, game items, digital art, etc. Each NFT token is unique and can have a unique value. In this chapter, you will learn how to code the smart contract with the help of the OpenZeppelin library. You will also create the badge and add it to IPFS node. After that, you will learn to pin it so it is available for everyone, everywhere. Next, you will learn how to migrate the contract to different environments such as a local blockchain using Ganache and testnets using Infura. Finally, you will learn how to sell your own NFT on OpenSea.

In Chapter [6](#521550_1_En_6_Chapter.xhtml), we will cover different ways to fund your wallet using faucets. This part is important because you will need some ether in your wallet in order to pay for the transaction. Most of the examples will be deployed on testnets so you won’t need real money to execute them.

In Chapter [7](#521550_1_En_7_Chapter.xhtml), you will learn how to create and save files on a decentralized file system. I also cover some tools such as a browser extension that will help you manage the node, as well as Pinata to help you pin your files remotely instead of keeping them locally. In addition, you will be able to host your own site on IPFS using Fleek.

In Chapter [8](#521550_1_En_8_Chapter.xhtml), I will cover ways to preserve files on a local node. The idea behind Filecoin is the same of IPFS, with the difference that Filecoin has an incentive mechanism and incentive nodes to preserve files. Filecoin was built on top of IPFS.

In Chapter [9](#521550_1_En_9_Chapter.xhtml), you will learn how to register a custom domain on the Ethereum Name System. You can use it to host a site under this domain name or even as a domain for your wallet to receive cryptos, tokens, or NFTs.

In Chapter [10](#521550_1_En_10_Chapter.xhtml), I will cover use cases where you need to pull data from off-chain using oracles. You will learn how to use price feeds and then crypto prices inside smart contracts.

In Chapter [11](#521550_1_En_11_Chapter.xhtml), you will learn how to create a simple project to connect to Web3 using the .NET platform and how to retrieve data from the blockchain to display wallet balances.

Chapter [12](#521550_1_En_12_Chapter.xhtml) concludes the book.

About the Author About the Technical Reviewer

# 1. Getting Started

In this chapter, I’ll explain what Ethereum is and take you through the installations you’ll need to perform before you can start using it.

## What Is Ethereum?

Ethereum solves problems that go beyond Bitcoin. When developing decentralized applications, we need a platform where we can code not only coins but a wide variety of solutions.

Ethereum is a platform that allows you to code smart contracts in the Solidity language. Using it, you can compile your code into bytecode to be interpreted by the Ethereum Virtual Machine (EVM).

This virtual machine will interpret the instructions contained in the bytecode of your smart contract and will create a new state based on the rules described in the contract. It’s like you have a state machine in your hand, where with every new state update, a new record is updated on the blockchain.

A virtual machine like EVM consumes resources, so you need a mechanism that generates incentives for more people to be nodes on the network while also preventing spam attacks. Because of this, an element called *gas* is required for the actions to be executed.

To secure gas, you must first have *ether*, which is the currency of the Ethereum network. You use gas to pay for computing, so think of it as the cost of using the system. You will need gas to perform most of the activities described in this book.

The Ethereum platform allows you to build decentralized applications, which are applications where the source code is immutable and the data is impossible to change after writing. This opens up a range of new solutions, such as voting, supply chain, and decentralized finance, among others.

The best way to learn is by coding, so let’s get started.

## Installing Visual Studio Code

Before you can start using Ethereum, you’ll need to install some software. First is Visual Studio Code,^([¹](#fn1)) an open source code editor that includes features for debugging, task execution, and version management. It provides developers with only the tools they need for a fast code-build-debug cycle, leaving more complicated processes to full-featured IDEs like Visual Studio IDE.

You can download it for free for different platforms like Windows, Linux, and Mac. All exercises in the book were based on the use of this tool.

## Installing Docker

Docker^([²](#fn2)) is a free and open platform for creating, delivering, and executing applications. Docker allows you to decouple your apps from your infrastructure, allowing you to deploy software more quickly. Docker allows you to manage your infrastructure in the same manner that you control your apps. By utilizing Docker’s techniques for fast shipping, testing, and deploying code, you may substantially shorten the time between developing code and executing it in production. You will need Docker to be started before compiling using Truffle.

## Installing the Blockchain Dev Kit Extension on VS Code

The Blockchain Developer Kit for Ethereum^([³](#fn3)) was designed for both new users of Ethereum and those who are already familiar with the process. One of the primary goals is to assist users in creating a project structure for these smart contracts; it also helps users compile and build the assets, deploy the assets to blockchain endpoints, and perform contract debugging.^([⁴](#fn4))

### Installing the Extension

Go to Extensions and search for *Blockchain Development Kit for Ethereum*. Click the extension created by Microsoft; it will usually be the first one (Figure [1-1](#521550_1_En_1_Chapter.xhtml#Fig1)).

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig1_HTML.jpg)

A screenshot of the Extensions window of the marketplace. The search bar displays Blockchain Development Kit for Ethereum. The blockchain development extension is the first one and is selected. The properties of this extension include development, deployment, debugging, and management.

Figure 1-1

Installing the Blockchain Development Kit for Ethereum

Click Install and wait for the installation to finish. That’s done!

## Installing Truffle

Truffle^([⁵](#fn5)) is an Ethereum development environment, testing framework, and asset pipeline that aims to make life easier for Ethereum developers. We will use this tool throughout the book.^([⁶](#fn6))

### Installing Truffle

Go to the terminal window and install the Truffle package.

```
$ npm install -g truffle
```

### Checking Truffle Installation

Now you can check whether the installation completed successfully. If you see the result shown in Figure [1-2](#521550_1_En_1_Chapter.xhtml#Fig2), the installation was successful.

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig2_HTML.jpg)

A screenshot of the truffle command output has details of installation on top, followed by Truffle v 5 point 2 point 6, a development framework for Ethereum. Further below, the usage details are provided and there is a long list of commands and their functions.

Figure 1-2

Truffle command output result

```
$ truffle
```

## Installing the Ganache CLI

Ganache^([⁷](#fn7)) is a personal blockchain that allows for the rapid development of the Ethereum and Corda distributed applications. Ganache can be used throughout the development cycle, allowing you to develop, deploy, and test your DApps in a secure and deterministic environment.^([⁸](#fn8))

### Installing Ganache

Go to the terminal window and install the Ganache command line.

```
npm install -g ganache-cli
```

### Starting Ganache Locally

Start the Ganache CLI on 127.0.0.1:8545 using the following command:

```
ganache-cli
```

Using this command, in addition to starting Ganache locally, it will generate ten accounts with their respective public and private keys so that you can use them for test purposes (Figure [1-3](#521550_1_En_1_Chapter.xhtml#Fig3)).

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig3_HTML.jpg)

A screenshot of the available accounts generated by Ganache. Details of installation are displayed on top, followed by Ganache C L 1 v 6. point 12 point 2, ganache-core: 2 point 13 point 2\. Further below, there is a list of available accounts, with long codes and 100 E T H within parentheses with each code.

Figure 1-3

Accounts generated by Ganache

## Installing and Setting Up MetaMask Wallet

MetaMask^([⁹](#fn9)) is a browser extension that allows you to access Ethereum-enabled distributed applications, or DApps. The add-on injects the Ethereum Web3 API^([^(10)](#fn10)) into the JavaScript context of every website, allowing DApps to read from the blockchain.^([^(11)](#fn11))

When a DApp wants to make a transaction and publish it to the blockchain, MetaMask gives the user a secure interface to evaluate the transaction before approving or rejecting it through private keys, local client wallets, and hardware wallets like Trezor.^([^(12)](#fn12))

### Installing the Wallet

Go to [`https://metamask.io`](https://metamask.io) and click Install MetaMask. Click Add to Brave or your browser name and then click “Add extension.” Finally, click “Get started.”

### Configuring the Wallet

Click “Create a wallet” and then click “No thanks” (if you prefer, click “I agree” instead). Define the password that you will use to open your wallet and then confirm the password. Agree to the terms of use. Finally, click Create. Now you can back up your secret phrase (you can also do it later). For now, click “Remind me later.” Your wallet is done!

### Accessing Your Wallet

Click Extensions and pin MetaMask to your bar. Now, click the MetaMask icon, and your wallet will be shown.

### Discovering Your Wallet Address

Click the three dots on the upper-right side and then click “Account details.” Notice that you can see your wallet address in hash format and in QR code format. You can also copy your wallet address by clicking the account name. That’s it!

### Create an Account on Infura

Infura^([^(13)](#fn13)) delivers the tools and infrastructure that enable developers to rapidly transition their blockchain application from testing to scaled deployment, all while maintaining simple, dependable access to Ethereum and IPFS. A well-known use case that uses Infura as a data interface is Uniswap.^([^(14)](#fn14),[^(15)](#fn15))

### Creating a New Account

Go to [Infura](https://infura.io/)^([^(16)](#fn16)) and click “Get started for free.” Enter your email and password and then click “Sign up.” A verification email will be sent to your address (Figure [1-4](#521550_1_En_1_Chapter.xhtml#Fig4)).

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig4_HTML.jpg)

A screenshot of the Infura welcome page has Etherium and Filecoin on the left. The window reads, Let's get started. Our E t h 2 A P I is live. Request access to our private beta now. Building with Filecoin? Join the waitlist for the Infura Filecoin A P I. Get started and create your first project to access the Ethereum network exclamation mark.

Figure 1-4

The Infura welcome page you will see after logging in

Check your email and confirm your account by clicking the verification link. After that, you will be redirected to your dashboard. Click the Ethereum tab in the left menu.

Now that your account is created, you can start setting up a new project (Figure [1-5](#521550_1_En_1_Chapter.xhtml#Fig5)).

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig5_HTML.jpg)

The project's homepage has Etherium and Filecoin on the left, where Ethereum is chosen. The window reads, Instant access over H T T P S and Web Sockets to the Ethereum network. Below the text, the Create a project button is shown.

Figure 1-5

Clicking Ethereum takes you to the project’s home page

### Setting Up Your Infura Project

Access your dashboard and click Ethereum. Then click “Create a project” and define the project name. Notice that you can connect with different testnets and also to the mainnet. Save the changes.

After the project is created, information about the ID, secret, and endpoint for the connection is provided (Figure [1-6](#521550_1_En_1_Chapter.xhtml#Fig6)).

![](images/521550_1_En_1_Chapter/521550_1_En_1_Fig6_HTML.jpg)

The Infura project page has project details on top and Keys below. There is a bar for name and a button labelled save changes under the bar. Under keys, the sections are Project I D with the displayed I D, Project Secret with a bar, and endpoints, where Mainnet is entered. There are two links below.

Figure 1-6

Infura project details page

## Summary

In this chapter, you learned what Ethereum is and how to install the necessary software to begin developing smart contracts.

In the next chapter, you will explore Solidity and learn how to set up your first project in this language.

Footnotes [1](#521550_1_En_1_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_1_Chapter.xhtml#Fn2_source)   [3](#521550_1_En_1_Chapter.xhtml#Fn3_source)   [4](#521550_1_En_1_Chapter.xhtml#Fn4_source)   [5](#521550_1_En_1_Chapter.xhtml#Fn5_source)   [6](#521550_1_En_1_Chapter.xhtml#Fn6_source)   [7](#521550_1_En_1_Chapter.xhtml#Fn7_source)   [8](#521550_1_En_1_Chapter.xhtml#Fn8_source)   [9](#521550_1_En_1_Chapter.xhtml#Fn9_source)   [10](#521550_1_En_1_Chapter.xhtml#Fn10_source)   [11](#521550_1_En_1_Chapter.xhtml#Fn11_source)   [12](#521550_1_En_1_Chapter.xhtml#Fn12_source)   [13](#521550_1_En_1_Chapter.xhtml#Fn13_source)   [14](#521550_1_En_1_Chapter.xhtml#Fn14_source)   [15](#521550_1_En_1_Chapter.xhtml#Fn15_source)   [16](#521550_1_En_1_Chapter.xhtml#Fn16_source)  

# 2. Solidity

Solidity is an object-oriented, high-level programming language that is used to construct smart contracts that automate blockchain transactions. The language was proposed in 2014 by Gavin Wood and developed by participants of the Ethereum project. Solidity was influenced by C++, Python, and JavaScript, so you will find similar language structures as in those languages. The language is primarily used to build smart contracts on the Ethereum blockchain, but it can also be used to create smart contracts on other blockchains.

Solidity, being a high-level language, eliminates the need to type code in ones and zeros. It makes it much easier for people to create programs in a form that they can comprehend, by combining letters and numbers.

Because Solidity is statically typed, each variable must be specified by the user. Data types enable the compiler to validate variable usage. Solidity data types are often divided into two categories: value types and reference types.

The Ethereum ecosystem is distinctive in that it can be used by a wide range of cryptocurrencies and decentralized apps. On Ethereum, smart contracts enable the creation of solutions for all types of enterprises and organizations.

At the end of this chapter, you will be able to do the following:

*   Create a basic Solidity project using the VS Code extension

*   Compile the contract

*   Deploy the contract to a local blockchain

## Getting Started with the Solidity Project on VS Code

Ethereum is the most often utilized platform for smart contracts. Ethereum is the first programmable blockchain in the world. It enables the creation of smart contracts to aid in the transfer of digital assets such as ether.

Solidity^([^(17)](#fn17)) is the language you will use to build contracts; it is Turing-complete, which means that it allows you to build complex contracts in a well-defined and coded manner.

### Creating a New Project

Select “View ➤ Command Palette” and then click “Blockchain: New Solidity Project” (Figure [2-1](#521550_1_En_2_Chapter.xhtml#Fig1)). Finally, click “Create basic project” (Figure [2-2](#521550_1_En_2_Chapter.xhtml#Fig2)).

![](images/521550_1_En_2_Chapter/521550_1_En_2_Fig2_HTML.jpg)

A Solidity command palette window has 3 bars with text. The first one reads Select type of solidity project. The second one reads Create basic project, which is selected. The third one reads Create Project from Truffle box.

Figure 2-2

Creating a basic project

![](images/521550_1_En_2_Chapter/521550_1_En_2_Fig1_HTML.jpg)

A Solidity command palette window has 2 bars. The first bar has the text right angle bracket Blockchain colon New S and the cursor after S. The second bar reads Blockchain colon New Solidity Project. On the right corner of the bar, there is a settings icon.

Figure 2-1

New Solidity project

Select a folder where the project will be scaffolded and wait for the project to be created. Make sure the project structure was created, as shown in Figure [2-3](#521550_1_En_2_Chapter.xhtml#Fig3).

![](images/521550_1_En_2_Chapter/521550_1_En_2_Fig3_HTML.jpg)

A Solidity project creation palette has the menu icons on the left. On the main screen, there are sections under Get started, such as contracts, migrations, node underscore modules, test, and more. Helloblockchain dot Sol is selected under contracts.

Figure 2-3

Solidity project structure created

### Compiling the Project

Right-click the `HelloBlockchain.sol` file, select “Build contracts,” and wait for contracts to be built.

### Deploying to a Development Blockchain

Right-click the `HelloBlockchain.sol` file, select “Deploy contracts,” and then select Development 127.0.0.1:8545\. Wait for the contracts to be deployed to the blockchain development network. That’s it!

## Summary

In this chapter, you learned what Solidity is, and you created, compiled, and deployed your first smart contract.

In the next chapter, you will explore the ERC-20 token standard and learn how to create and deploy to development, test, and production environments.

Footnotes [1](#521550_1_En_2_Chapter.xhtml#Fn1_source)  

# 3. ERC-20: Fungible Tokens

Fungible tokens are tokens where each unit has the same value, in the same way as fiat currency. This means you can exchange one unit of this currency for another unit of this currency for the same amount. Thinking about replicating this behavior on the blockchain, Fabian Vogelsteller and Vitalik Buterin proposed the creation of ERC-20, “Ethereum Request for Comments 20,” in November 2015 to create a simple format for Ethereum-based tokens. These tokens work within the Ethereum blockchain and are able to interact with other cryptocurrencies on the network. In this chapter, you will create simple contracts in the ERC-20 standard and learn how to deploy them to test and production networks.

At the end of this chapter, you will be able to do the following:

*   Write a simple contract in the ERC-20 standard.

*   Write a fixed supply contract.

*   Inherit key implementations with OpenZeppelin.

*   Compile the contract using Truffle.

*   Start a localhost blockchain using Ganache.

*   Deploy the existing contract to Ganache.

*   Configure MetaMask to connect to Ganache.

*   Add the deployed token contract to your MetaMask wallet.

*   Migrate the contract to Ganache.

*   Transfer tokens between accounts.

*   Add Polygon Mumbai to MetaMask networks.

*   Activate the Polygon add-on on Infura.

*   Configure the private key to sign the contract.

*   Deploy the smart contract on Polygon Mumbai.

*   Add the Polygon mainnet to MetaMask networks.

*   Configure the network to use the Polygon mainnet.

*   Deploy the smart contract on the Polygon mainnet.

*   Verify the smart contract on the Polygon mainnet.

## Write a Simple ERC-20 Token Using OpenZeppelin

Let’s use Truffle to develop a simple ERC-20 Ethereum^([^(18)](#fn18)) smart contract and then import the OpenZeppelin contracts library.

OpenZeppelin is an open source and auditable library that allows you to reuse code from more common implementations, thus serving as an initial code base that is always the same. Using OpenZeppelin allows you to focus more on coding the business need rather than repeating unnecessary code.

We will use the OpenZeppelin library in this example and in subsequent chapters of this book.

Tokens can represent virtually everything in Ethereum, such as the following:

*   Reputation points in an online platform

*   Skills of a character in a game

*   Lottery tickets

*   Financial assets like a share in a company

*   A fiat currency like USD

*   An ounce of gold

### Preparing the Environment

Initialize Truffle using the following command:

```
$ truffle init
```

Now, initialize the project folder.

```
$ npm init
```

Finally, install the OpenZeppelin contracts package.

```
$ npm install @openzeppelin/contracts
```

### Writing the Contract

Create a new file under the `contracts` folder with the name `ERC20MinerReward.sol`. Add the license directive, define the Solidity minimum version, and import the OpenZeppelin ERC-20 contract library. Finally, define the contract class, the contract constructor, the contract name, and the contract symbol.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contract ERC20MinerReward is ERC20 {
constructor() ERC20("MinerReward", "MRW"){}
}
```

### Setting the Solidity Compiler Version

Copy the Solidity version used in this contract and then open `truffle-config.js`*.* Uncomment the `solc` block and set the Solidity version by pasting in the copied value.

```
compilers: {
solc: {
version: "0.8.0",
docker: true,
settings: {
optimizer: {
enabled: false,
runs: 200
},
evmVersion: "byzantium"
}
}
},
```

### Compiling the Contract

Now it is the time to compile the contract.

```
$ truffle compile
```

The contract was compiled successfully!

### Verifying the Result

Notice that a new folder build/contract was created (Figure [3-1](#521550_1_En_3_Chapter.xhtml#Fig1)). The new contract is there!

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig1_HTML.jpg)

A screenshot of the truffle compile results. On the left is a section titled explorer in which solidarity, build backward slash contracts is selected. On the top of the main screen, terminal is selected and a program under it reads dollar truffle compile, compiling your contracts with a list of compiling under it.

Figure 3-1

Truffle compile results

## Deploy the ERC-20 Token to the Ganache Development Blockchain

Ethereum Ganache is a local in-memory blockchain that is intended for development and testing. It mimics the characteristics of a real Ethereum network, including the availability of a number of accounts funded with test ether.

This is a nice way to deploy contracts before moving them to a main network. Using a development blockchain, you can focus on the implementation without worrying about spending real money to deploy the contracts.

### Preparing the Migration

Create a new migration file named `2_deploy_contracts.js` under the `migrations` folder. In the first line, add a reference to the smart contract and add an export function to deploy the smart contract (Figure [3-2](#521550_1_En_3_Chapter.xhtml#Fig2)).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig2_HTML.jpg)

A screenshot of the migrations folder under the Solidity block that has the 2 underscore deploy underscore contracts dot j s tab selected. The command prompt reads, const E R C 20 Miner Reward equals artifacts dot require open parenthesis open quotes E R C 20 Miner Reward close quotes, close parenthesis. Below, the command continues.

Figure 3-2

New migration file

### Starting the Blockchain

Open a new terminal and start the Ganache blockchain.

```
$ ganache-cli
```

A new Ganache blockchain is listening on 127.0.0.1:8545.

### Configuring the Blockchain Network

Open `truffle-config.js` and uncomment the `development` block from `networks`. Make sure the host and port are correct (Figure [3-3](#521550_1_En_3_Chapter.xhtml#Fig3)).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig3_HTML.jpg)

A screenshot of the truffle hyphen config dot j s tab is selected. Within that the networks section is open. There are several instructions provided under networks. The other sections provided below are development, where details of the host, port, and network i d are given. At the bottom, there is further information of advanced options.

Figure 3-3

Development network

### Deploying the Contract

Compile the contract using the following command:

```
$ truffle compile
```

Migrate the contract using the following command:

```
$ truffle migrate
```

The contract was deployed to the Ganache blockchain, and a contract address was created (Figure [3-4](#521550_1_En_3_Chapter.xhtml#Fig4)).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig4_HTML.jpg)

A screenshot of the truffle migrate contract has 4 tabs on top, problems, output, debug console and terminal, of which terminal is selected. There are 4 main sections in the terminal: compiling your contracts, starting migrations, 1 underscore initial migration dot j s, and summary.

Figure 3-4

Truffle migrate contract

### Adding Ganache to MetaMask Networks

Open the MetaMask extension and click the Network drop-down. Select the Custom RPC option and set the following fields, as shown in the Figure [3-5](#521550_1_En_3_Chapter.xhtml#Fig5):

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig5_HTML.jpg)

A screenshot of the networks pane under Ethereum Mainnet. There is a warning message about a malicious network followed by Network name, Localhost 8 5 4 5, New R P C U R L with the link, Chain I D, 1 3 3 7, Currency Symbol, optional, with E T H. At the bottom the text reads Block Explorer U R L, which also optional.

Figure 3-5

MetaMask network configuration

*   Set the network name to **Localhost 8545**.

*   Set the RPC URL to **http://localhost:8545**.

*   Set the chain ID to **1337**.

*   Set the currency symbol to **ETH**.

### Adding the Token to a Wallet

Go to the Brave browser (or any browser compatible with MetaMask) and select the “Localhost 8585” network.

Click “Add token” and click “Custom token.” Copy the contract address. Paste it into the “Token contract address” field.

The “Token symbol” and “Decimals of precision” fields are filled automatically. Click “Next” and click “Add token.” The token was added to the MetaMask wallet. The token is there!

## Create an ERC-20 Token with a Fixed Supply

The total number of tokens allowed in the smart contract is defined by ERC-20 fixed supply tokens. You cannot update the contract once it has been deployed to the blockchain. This means that your coin will have that fixed amount after deployed and you will not be able to fund with more coins later.

### Creating the Project

Initialize a new and empty Ethereum project.

```
$ truffle init
```

Create a `package.json` file for your project.

```
$ npm init
```

Install the OpenZeppelin contracts package. It contains reusable smart contracts written in Solidity.

```
$ npm install @openzeppelin/contracts
```

### Writing a Fixed Supply Contract

Create a new solidity file and do the following:

1.  Include the license declaration (this is mandatory).

2.  Define the Solidity minimum version.

3.  Import the OpenZeppelin ERC-20 contract library.

4.  Define the fixed supply contract class, inheriting from ERC-20.

5.  Call the constructor, passing the name and symbol.

6.  Assign the total supply to the sender address (who created the contract).

7.  Override the decimals function.

8.  Set the number of decimals that this token will have.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contract ERC20FixedSupply is ERC20 {
constructor() ERC20("Fixed", "FIX"){
_mint(msg.sender, 1000);
}
function decimals() public view virtual override returns (uint8){
return 0;
}
}
```

Go to `truffle-config.js` and uncomment the `solc` block (Ctrl+;). Now, update the Solidity version number.

```
compilers: {
solc: {
version: "0.8.0",
docker: true,
settings: {
optimizer: {
enabled: false,
runs: 200
},
evmVersion: "byzantium"
}
}
},
```

Under the `migrations` folder, create a new file. Set the name to `2_deploy_contracts.sol`.

```
$ touch migrations/2_deploy_contracts.sol
```

In this file, set the required method to your contract file and export a function to deploy the contract.

```
var ERC20FixedSupply = artifacts.require("./ERC20FixedSupply.sol");
module.exports = function(deployer){
deployer.deploy(ERC20FixedSupply);
}
```

### Compiling the Contract

Now it is time to compile the contract.

```
$ truffle compile
```

The contract was compiled successfully!

### Starting the Ganache Development Blockchain

Split the terminal view. Now, start the Ganache development blockchain on 127.0.0.1:8545.

```
$ ganache-cli
```

Go to `truffle-config.js`, and under `networks`, uncomment the `development` block.

```
networks: {
development: {
host: "127.0.0.1",
port: 8545,
network_id: "*"
},
}
```

### Migrating the Contract to Ganache

Run the `migrate` command to deploy contracts, as shown in Figure [3-6](#521550_1_En_3_Chapter.xhtml#Fig6).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig6_HTML.jpg)

A screenshot of the truffle compile results has the menu pane on the left with truffle hyphen config dot j s selected. On the main part of the screen, there are four tabs: Problem, Output, Terminal and Debug Console, of which terminal is selected. The terminal text reads compiling your contracts. It is compiled and waiting for input.

Figure 3-6

VS Code: migrating the project using the truffle command line

```
$ truffle migrate
```

Before proceeding to the next section, copy the private key of the account that deployed the token.

### Configuring MetaMask

Open MetaMask. Click your account and then click “Import account.” In this step, paste the account private key. Click on “Import”, as shown in Figure [3-7](#521550_1_En_3_Chapter.xhtml#Fig7).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig7_HTML.jpg)

A screenshot of the import account page. Below the heading import account, a message reads imported account will not be associated with your originally created metamask account. Below it, the select type field has private key selected. Below it, the field to enter the private key string has a dotted line. Import button is highlighted at the bottom.

Figure 3-7

MetaMask: importing an existing wallet using the seed phase

Click the Networks list and then click Localhost:8545\. Using the localhost network means you will be pointing your wallet to your local development blockchain, as shown in Figure [3-8](#521550_1_En_3_Chapter.xhtml#Fig8).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig8_HTML.jpg)

A screenshot of the networks page. The text on the top reads show or hide test networks with a dismiss button on the right. The list of some visible networks is as follows. Polygon Mumbai, ropsten test network, kovan test network, rinkeby test network, and so on. Ropsten test network is check-marked on the left. Add network button is at the bottom.

Figure 3-8

MetaMask: network selection list

### Adding the Token to MetaMask

Click “Add token” and then select “Custom token.” Paste in the token contract address and click “Next”.

Adding a token is a matter of adding the contract public address of the created token. MetaMask will read the symbol and the number of decimal places automatically after that. Make sure you get the same result as shown in Figure [3-9](#521550_1_En_3_Chapter.xhtml#Fig9).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig9_HTML.jpg)

A screenshot of the Add Tokens window has 2 tabs, Search and Custom Token. Custom token is selected. Below, there is a bar for Token Contract address with the address, Token symbol with fix, and Decimals of precision with a 0.

Figure 3-9

MetaMask: adding a custom token

Click “Add tokens.” The token symbol as well as your balance will be shown on this screen (Figure [3-10](#521550_1_En_3_Chapter.xhtml#Fig10)).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig10_HTML.jpg)

A screenshot of the Add Tokens window has the question Would you like to add these tokens? On the left is the word Token and a colourful circular icon labelled Fix. On the right, the word Balance is given with 1000 Fix.

Figure 3-10

MetaMask: new custom token overview

Now, go back to VS Code (see Figure [3-6](#521550_1_En_3_Chapter.xhtml#Fig6) for the `ganache-cli` terminal view) and copy another account private key. Return to MetaMask and repeat the steps you did for the first account, including adding the token.

### Transferring Tokens Between Accounts

Now, switch to the first imported account (the one that has all the tokens). Click “Send” and then click “Transfer between my accounts.” Select the second created account. Enter **115 FIX** as the amount to transfer and click “Next”. Finally, click “Confirm”.

The transaction was sent, but it’s in a pending state. Wait a moment for the transaction to be confirmed. Once that happens, the total number of tokens will be updated. Select the second imported account; now this account has 115 FIX!

## Deploy the ERC-20 Token to a Testnet Using Infura

Infura can be used to deploy smart contracts to test networks such as Ropsten, Kovan, Rinkeby, Goerli, and also the mainnet. For the testnet, you will need to create a new project on Infura and have access to the wallet’s private key, which you will use to deploy the contracts. To execute the contract creation transaction, this wallet must have an ether balance.

### Installing the Prerequisites

Open a new terminal and install the `fs` package. Installing this package provides useful functionality to access and interact with the file system.

```
$ npm install fs
```

Now, install the wallet provider `hdwallet` package. This is used to sign transactions for addresses derived from a 12- or 24-word mnemonic.

```
$ npm install @truffle/hdwallet-provider@1.2.3
```

### Setting Up Your Infura Project

Go to [`http://infura.io`](http://infura.io) and access your dashboard. Click “Ethereum” and then click “Create a Project”. Define the project name. Notice that you can connect with different testnets and also to the mainnet. Copy the project ID and save the changes.

### Setting Up Your Smart Contract

Go to Visual Studio Code and open `truffle-config.js`. Uncomment the four constants: `hdwalletprovider`, `infurakey`, `fs`, and `mnemonic`. Paste the project ID as a value for the Infurakey constant. Uncomment the `ropsten` block. Make sure you are using the correct project ID in the `ropsten` endpoint.

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const infuraKey = "fj4jll3k.....";
const fs = require('fs');
const mnemonic = fs.readFileSync(".secret").toString().trim();
```

### Configuring the Private Key

Go to the browser and open your MetaMask wallet connected to the Infura network. Click “*your account*” and then click “settings,” and finally click “security & privacy” (Figure [3-11](#521550_1_En_3_Chapter.xhtml#Fig11)).

You have the option to view your seed phrase, but be aware that this information is sensitive and if someone has access to it, they will be able to restore your wallet and make use of your funds.

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig11_HTML.jpg)

A screenshot of the Security and Privacy window in the Ropsten Test Network. A phrase on the screen reads Reveal Seed Phrase, with a button with the cursor on it, which also reads Reveal Seed Phrase.

Figure 3-11

MetaMask: revealing the seed phrase

Click “Reveal Seed Phrase” and enter your wallet password to continue; then copy the private key.

Go back to Visual Studio Code (Figure [3-6](#521550_1_En_3_Chapter.xhtml#Fig6)) and create a new file named `.secret`. Paste the private key into this file. You can also create this file using the command line (Figure [3-12](#521550_1_En_3_Chapter.xhtml#Fig12)).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig12_HTML.jpg)

A screenshot has the migrations folder selected under the Solidity block. The command prompt reads dollar sign touch space dot secret.

Figure 3-12

Secret file

```
$ touch .secret
```

### Deploying the Smart Contract

Open the terminal and run the `migrate` command to deploy the contracts on the Ropsten network.

```
$ truffle migrate --network ropsten
```

### Checking Your Wallet Balance

Go to your MetaMask wallet again and notice now that your balance has been reduced.

### Verifying the Smart Contract on Etherscan

Open a new window and copy the contract address that was created in the deploy stage. Go to [`https://ropsten.etherscan.io`](https://ropsten.etherscan.io) and paste the contract address into the search field. Click the Find button. The smart contract is there!

The tokens were created and transferred to the wallet that created the contract. Click the Fixed (FIX) token link. Here you can see an overview of your newly created token.

## Deploy the ERC-20 Token to the Polygon Testnet (Layer 2)

Polygon is a protocol and framework for building and connecting Ethereum-compatible blockchain networks. You can aggregate scalable solutions on Ethereum to support a multichain Ethereum ecosystem.

MATIC, the native token of Polygon, is an ERC-20 token running on the Ethereum blockchain. The tokens are used for payment services on Polygon and as a settlement currency between users who operate within the Polygon ecosystem.

### Installing the Prerequisites

Open a new terminal and install the `fs` package, if you haven’t already done so. This package provides a lot of useful functionality to access and interact with the file system.

```
$ npm install fs
```

Now, install the wallet provider `hdwallet` package, if you haven’t already done so. It is used to sign transactions for addresses derived from a 12- or 24-word mnemonic.

```
$ npm install @truffle/hdwallet-provider@1.4.0
```

### Adding Polygon Mumbai to MetaMask Networks

Open the MetaMask extension and click the Network drop-down. Then select the Custom RPC option. Set the following values, as shown in Figure [3-13](#521550_1_En_3_Chapter.xhtml#Fig13):

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig13_HTML.jpg)

A screenshot of the MetaMask network configuration page reads as follows. Network Name, Matic Testnet. New R P C U R L has the link entered below. Chain I D with 8 0 0 0 1; Currency Symbol, optional with MATIC. The last line reads Block Explorer U R L, optional, with the provided link.

Figure 3-13

MetaMask: network configuration page

*   Set the network name to **Matic Testnet**.

*   Set the RPC URL to [**https://rpc-mumbai.maticvigil.com**](https://rpc-mumbai.maticvigil.com).

*   Set the chain ID to **80001**.

*   Set the currency symbol to **MATIC**.

*   Set the Block Explorer URL to [**https://explore-mumbai.maticvigil.com**](https://explore-mumbai.maticvigil.com).

### Activating the Polygon Add-on on Infura

Go to [`https://infura.io/upgrade`](https://infura.io/upgrade) and click Select Addon in the Polygon PoS under Network Add-ons*,* as shown in Figure [3-14](#521550_1_En_3_Chapter.xhtml#Fig14)*.* The Polygon PoS is currently in beta version on Infura, and you need to activate it.

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig14_HTML.jpg)

A screenshot of the Network Add-Ons window describes the polygon PoS as a hybrid Plasma Proof-of-Stake sidechain to Ethereum's mainnet which utilizes a Tendermint consensus validator layer and a Plasma sidechain for block production. Select AddOn and More coming soon buttons are shown below. The pricing of $200 per month is struck out.

Figure 3-14

Infura: Polygon PoS activation page

After activating it, you will be redirected to the summary page. The free layer is limited to 100,000 requests a day. You will be asked to provide a credit card in order to confirm; as the total cost is zero, you will not be charged. If you agree, click Get Started Now. You should see a page similar to the one shown in Figure [3-15](#521550_1_En_3_Chapter.xhtml#Fig15).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig15_HTML.jpg)

A screenshot of the Summary window lists the order total as $0 per month, total requests as 100,000 per day, core tier of 100,000 requests per day as $0 per month, add-ons of Polygon PoS as $0 per month. There is a discount code that can be applied. Below there is a checkout section with a credit card already saved.

Figure 3-15

Infura: Summary page order after adding the Polygon PoS plugin

### Setting Up Your Infura Project

Make sure that you have a project already set up on Infura. If you haven’t already, please follow the steps in Chapter [1](#521550_1_En_1_Chapter.xhtml).

### Setting Up Your Smart Contract

Go to Visual Studio Code and open `truffle-config.js`*.* Uncomment the four constants: `hdwalletprovider`, `infurakey`, `fs`, and `mnemonic` and *p*aste the project ID as a value for `infurakey` constant.

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const infuraKey = "fj4jll3k.....";
const fs = require('fs');
const mnemonic = fs.readFileSync(".secret").toString().trim();
```

### Configuring the Network (Using the Matic Endpoint)

The first way to connect to a Polygon network is using the Matic network. Now, create a `matic_testnet` configuration under `networks` in the `truffle-config.js` file and set the following values:

*   Set the wallet URL to [`https://rpc-mumbai.maticvigil.com`](https://rpc-mumbai.maticvigil.com).

*   Set `network_id` to `80001`.

```
matic_testnet: {
provider: () => new HDWalletProvider(mnemonic, `https://rpc-mumbai.maticvigil.com`),
network_id: 80001,
confirmations: 2,
timeoutBlocks: 200,
skipDryRun: true
},
```

### Configuring the Network (Using the Infura Endpoint)

Another way to connect to the Polygon network is using the Infura endpoint. Create a `matic_testnet` configuration under `networks` and set the following values:

*   Set the wallet URL to [`https://polygon-mumbai.infura.io/v3/${infuraKey}`](https://polygon-mumbai.infura.io/v3/%2524%257binfuraKey%257d).

*   Set `network_id` to `80001`.

```
matic_testnet: {
provider: () => new HDWalletProvider(mnemonic, `https://polygon-mumbai.infura.io/v3/${infuraKey}`),
network_id: 80001,
confirmations: 2,
timeoutBlocks: 200,
skipDryRun: true,
chainId: 80001,
networkCheckTimeout: 1000000
},
```

To use the Polygon network, you will need to activate the network add-on.

### Configuring the Private Key

Go to the browser and open your MetaMask wallet connected to the Infura network. Click “*your account*” and then click “settings.” Finally, click “security & privacy,” as you can see in Figure [3-16](#521550_1_En_3_Chapter.xhtml#Fig16).

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig16_HTML.jpg)

A screenshot of the Security and Privacy window in the Ropsten Test Network. A phrase on the screen reads Reveal Seed Phrase, with a long button with the cursor on it, which also reads Reveal Seed Phrase.

Figure 3-16

MetaMask: revealing the seed phrase

You have the option to view your seed phrase, but be aware that this information is sensitive, and if someone has access to it, they will be able to restore your wallet and make use of your funds.

Click Reveal Seed Phrase and enter your wallet password to continue. Copy the private key.

Go back to VS Code (on the `ganache-cli` terminal view) and create a new file named `.secret`. Paste the private secret recovery phrase on this file.

### Deploying the Smart Contract

Run the `migrate` command to deploy contracts to the `matic_testnet` network.

```
$ truffle migrate --network matic_testnet
```

If you get this error on the terminal, you will need to get the test MATIC from Faucet first.

```
1_initial_migration.js
======================
Deploying 'Migrations'
----------------------
Error:  *** Deployment Failed ***
"Migrations" -- insufficient funds for gas * price + value.
```

### Checking Your Wallet Balance

Go to your MetaMask wallet again and notice that your balance has been reduced. This happens because you need to pay for each contract deployment. It has an equivalent cost in gas, and that cost is calculated according to the instructions you use inside a smart contract. This means that the more machine processing you need, the higher the gas cost for you to execute this contract. You can find a more detailed explanation of how this is calculated in the [Ethereum yellow paper](https://ethereum.github.io/yellowpaper/paper.pdf).

### Verifying the Smart Contract on PolygonScan

Copy the contract address that was created in the deploy (this address will be shown in the console after `truffle migrate` has finished running) and go to [`https://mumbai.polygonscan.com`](https://mumbai.polygonscan.com). Paste the contract address in the search field and click the Find button. The smart contract is there!

The tokens were created and transferred to the wallet that created the contract. Now, click the Fixed (FIX) token link, and here you can see the overview of your newly created token!

## Deploy the ERC-20 Token to the Polygon Mainnet (Layer 2)

The mainnet network is used for real transactions, while testnets are used for testing smart contracts and decentralized applications (DApps). Polygon is used as a second layer and gained popularity because of the transaction cost that are lower than the mainnet.

### Adding the Polygon Mainnet to MetaMask Networks

Open the MetaMask extension, click the Network drop-down, and then select the Custom RPC option. Set the following values as shown in Figure [3-17](#521550_1_En_3_Chapter.xhtml#Fig17):

![](images/521550_1_En_3_Chapter/521550_1_En_3_Fig17_HTML.jpg)

A screenshot of the network configuration page has the following fields filled in. Network name, Matic Mainnet. New R P C U R L, with the link. Chain I D, 1 3 7\. Currency symbol, optional. Matic. Block Explorer U R L. optional, with the link added.

Figure 3-17

MetaMask: network configuration page

*   Set the network name to **Matic Mainnet**.

*   Set the RPC URL to [**https://rpc-mainnet.maticvigil.com**](https://rpc-mainnet.maticvigil.com).

*   Set the chain ID to **137**.

*   Set the currency symbol to **MATIC**.

*   Set the Block Explorer URL to [**https://explore-mainnet.maticvigil.com**](https://explore-mainnet.maticvigil.com).

### Configuring the Network (Using the Infura Endpoint)

Another way to connect to the Polygon network is to use the Infura endpoint. Create a `matic_mainnet` configuration under `networks` and set the following values:

*   Set the wallet URL to [`https://polygon-mainnet.infura.io/v3/${infuraKey}`](https://polygon-mumbai.infura.io/v3/%2524%257binfuraKey%257d).

*   Set `network_id` to `137`.

```
matic_mainnet: {
provider: () => new HDWalletProvider(mnemonic, `https://polygon-mainnet.infura.io/v3/${infuraKey}`),
network_id: 137,
gasPrice: 100000000,
confirmations: 2,
timeoutBlocks: 200,
skipDryRun: true,
chainId: 137,
networkCheckTimeout: 1000000
},
```

### Deploying the Smart Contract

Run the `migrate` command to deploy contracts to the `matic_mainnet` network.

```
$ truffle migrate --network matic_mainnet
```

### Checking Your Wallet Balance

Go to your MetaMask wallet again and notice that your balance has been reduced.

### Verifying the Smart Contract on PolygonScan

Copy the contract address that was created in the deployment and go to PolygonScan.^([^(19)](#fn19)) Paste the contract address in the search field and click the Find button. The smart contract is there!

The tokens were created and transferred to the wallet that created the contract. Now, click the Fixed (FIX) token link, and here you can see the overview of your newly created token.

## Summary

In this chapter, you learned what the ERC-20 token standard is and learned how to create and deploy fungible tokens to Ganache to the testnet and mainnet networks on the Ethereum and Polygon blockchains.

In the next chapter, you will explore unit tests on smart contracts and learn how to write your first unit test.

Footnotes [1](#521550_1_En_3_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_3_Chapter.xhtml#Fn2_source)  

# 4. Unit Tests for Smart Contracts

Unit tests are used to test code execution scenarios and ensure that they behave as expected. Crucially, unit tests allow you to refactor code and make new changes without breaking existing behaviors.

In smart contracts, unit tests are even more important, as once the contract is deployed, it is no longer possible to fix it unless you deploy a new contract. Because of this, it is of fundamental importance that you incorporate unit tests into all the smart contracts you write, looking for as much coverage as possible.

In this chapter, you will learn how to write unit tests using Mocha as the test runner and Chai as the assertion library.

At the end of this chapter, you will be able to do the following:

*   Create a unit test file

*   Write unit tests for the smart contract

*   Write test assertions

*   Run the unit tests

*   Check the unit test results

## Writing Unit Tests for ERC-20 Smart Contracts

Truffle has an automated testing framework by default, making it considerably easier to test your contracts. This framework allows you to create basic and manageable tests in a variety of ways. Let’s start coding our first unit test.

### Creating a New Unit Test File

Open a new terminal and execute the following command:

```
$ truffle create test erc20FixedSupply
```

### Writing a Test for the Total Supply Contract

In the file `ERC20FixedSupply.js`, write a new test to assert that the contract was created with a fixed supply of 1,000 coins.

```
const erc20FixedSupply = artifacts.require("erc20FixedSupply");
contract("erc20FixedSupply", function () {
it("should assert true", async function() {
let token = await erc20FixedSupply.deployed();
let name = await token.name();
assert.equal(name, 'Fixed');
});
it("should return total supply of 1000", async function() {
const instance = await erc20FixedSupply.deployed();
const totalSupply = await instance.totalSupply();
assert.equal(totalSupply, 1000);
});
});
```

Test using the following command:

```
$ truffle test --network development
```

Make sure that the test will pass.

### Writing Test Assertions for the Balance Contract

In the file `ERC20FixedSupply.js`, add one more test to assert that the balance is correct after a new transfer is made between two accounts.

```
it("should transfer 150 FIX", async function(){
const instance = await erc20FixedSupply.deployed();
await instance.transfer(account[1], 150);
const balanceAccount0 = await instance.balanceOf(accounts[0]);
const balanceAccount1 = await instance.balanceOf(accounts[1]);
assert.equal(balanceAccount0.toNumber(), 850);
assert.equal(balanceAccount1.toNumber(), 150);
});
```

### Running the Unit Tests

Now, execute the test again.

```
$ truffle test --network development
```

Once again, make sure that all the tests pass. If the unit test has passed, a green check mark will appear; otherwise, a red x symbol will appear.

### Checking the Unit Test Results

Make sure you get the same output as shown in Figure [4-1](#521550_1_En_4_Chapter.xhtml#Fig1).

![](images/521550_1_En_4_Chapter/521550_1_En_4_Fig1_HTML.jpg)

A screenshot of the V S code presents the following output. Contract colon e r c 20 FixedSupply. check mark for: should assert true, 72 milliseconds. check mark for: should return total supply of 1000, 83 milliseconds. check mark for: should transfer 150 FIX, 494 milliseconds, 3 passing, 776 milliseconds.

Figure 4-1

VS Code: unit test results succeed

Try to change some values like the account balance and see how the results change from pass to fail, as shown in Figure [4-2](#521550_1_En_4_Chapter.xhtml#Fig2).

![](images/521550_1_En_4_Chapter/521550_1_En_4_Fig2_HTML.jpg)

A screenshot of the V S code presents the following output. Contract colon e r c 20 FixedSupply; checkmark for: should assert true: 51 milliseconds; checkmark for: should return total supply of 1000, 51 milliseconds; 1, should transfer 150 FIX. The next section reads Events emitted during test with details and an explanation for them.

Figure 4-2

VS Code: unit test results failed

```
it("should transfer 150 FIX", async function(){
const instance = await erc20FixedSupply.deployed();
await instance.transfer("account[1]", 150);
const balanceAccount0 = await instance.balanceOf(accounts[0]);
const balanceAccount1 = await instance.balanceOf(accounts[1]);
assert.equal(balanceAccount0.toNumber(), 750);
assert.equal(balanceAccount1.toNumber(), 250);
});
```

## Summary

In this chapter, you learned the importance of writing unit tests for smart contracts and wrote your first unit test.

In the next chapter, we will explore the ERC-721 token standard and how it differs from ERC-20\. In addition, you’ll learn to create and deploy contracts in this standard.

# 5. ERC-721 Nonfungible Tokens

A nonfungible token (NFT) is a digital asset that represents physical objects such as art, music, in-game items, and videos. In this chapter, I’ll show you how to create an NFT ERC-721 and deploy it to the Ethereum testnet network, as well as how to add it to your MetaMask mobile wallet.

At the end of this chapter, you will be able to do the following:

*   Create a new NFT project

*   Configure the network to deploy on Ganache

*   Configure the private key

*   Create the badge image

*   Add the badge to the local IPFS

*   Pin the badge to a remote IPFS

*   Create the badge metadata

*   Deploy the smart contract

*   Award the badge to your wallet

*   Check the badge on Etherscan

*   Add the badge to your mobile wallet

## Create Your Art NFT Using Ganache and OpenZeppelin

Let’s configure your first NFT using the OpenZeppelin library and create the metadata that will store the token information and then deploy it to a test network. Finally, you will view the token in your wallet.

### Creating the Project

Create a new project using Truffle.

```
$ truffle init
```

Install the OpenZeppelin contracts.

```
$ npm install @openzeppelin/contracts
```

Create a new Solidity smart contract.

```
$ touch contracts/UniqueAsset.sol
```

Open the `UniqueAsset.sol` file and import the ERC721URIStorage.sol extension and Counters.sol utility. Create a new class extending `ERC721URIStorage`. Declare the counters variable and declare the constructor passing the coin name and the code.

Create a new method named `awardItem`. Inside the new method, increment the token ID. Get the new token number using `_tokenIds.current()`.

Mint a new item using the method `_mint`. Finally, set the token URI passing the metadata using the method `_setTokenURI`.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
contract UniqueAsset is ERC721URIStorage {
using Counters for Counters.Counter;
Counters.Counter private _tokenIds;
constructor() ERC721("UniqueAsset", "UNA") {}
function awardItem(address recipient, string memory metadata)
public
returns (uint256)
{
_tokenIds.increment();
uint256 newItemId = _tokenIds.current();
_mint(recipient, newItemId);
_setTokenURI(newItemId, metadata);
return newItemId;
}
}
```

Create a new migration file using the `touch` command. This command creates a new file in the `migrations` folder.

```
$ touch migrations/2_deploy_contracts.sol
```

Inside the `2_deploy_contracts.js` file, export the smart contract in the migration file.

```
const UniqueAsset = artifacts.require("UniqueAsset");
module.exports = function (deployer) {
deployer.deploy(UniqueAsset);
}
```

### Configuring the Wallet to Sign Transactions

Install the file system `fs` package.

```
$ npm install fs
```

Install the wallet provider `hdwallet` package.

```
$ npm install @truffle/hdwallet-provider@1.2.3
```

Open the `truffle-config.js` file and uncomment the `HDWalletProvider` code section.

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const infuraKey = '';
const fs = require('fs');
const mnemonic = fs.readFileSync(".secret").toString().trim();
```

Paste your Infura project ID as a value for the variable `infuraKey`.

### Configuring the Network

Inside the `truffle-config.js` file, uncomment the `ropsten` network section and make the following changes:

*   Change `ropsten` to `rinkeby`.

*   Change the Ropsten Infura URL to `rinkeby`.

*   Change `YOU-PROJECT-ID` to `${infuraKey}`.

*   Change `network_id` to `42`.

```
rinkeby: {
provider: () => new HDWalletProvider(mnemonic, `https://rinkeby.infura.io/v3/${infuraKey}`),
network_id: 42,
gas: 5500000,
confirmations: 2,
timeoutBlocks: 200,
skipDryRun: true
},
```

### Configuring the Solidity Compiler

Also, inside the `truffle-config.js` file, uncomment the `compilers` section and change the version to 0.8.0*.*

```
compilers: {
solc: {
version: "0.8.0",
docker: true,
settings: {
optimizer: {
enabled: false,
runs: 200
},
evmVersion: "byzantium"
}
}
},
```

### Configuring the Private Key

Go to your browser and open your MetaMask wallet connected to the Infura network. Click “*your account*” and then click “settings.” Finally, click “security & privacy” (Figure [5-1](#521550_1_En_5_Chapter.xhtml#Fig1)).

You have the option to view your seed phrase, but be aware that this information is sensitive, and if someone has access to it, they will be able to restore your wallet and make use of your funds.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig1_HTML.jpg)

A screenshot of the Security and Privacy window in the Ropsten test network. A phrase on the screen reads reveal seed phrase. A button with the cursor on it is at the bottom, which also reads reveal seed phrase.

Figure 5-1

MetaMask: revealing the seed phrase

Click Reveal Seed Phrase and enter your wallet password to continue. Copy the private key.

Go back to Visual Studio Code and create a new file named `.secret`. Paste the private secret recovery phrase on this file.

### Creating the Badge Image

Create the `badge` folder.

```
$ mkdir badge
```

Now, go to the `badge` root folder.

```
$ cd badge
```

Download the image that you will use as a badge from the Internet. You can also copy and paste an existing image into this folder. The `curl` command is used for transferring data via URL syntax.

```
$ curl https://planouhost.z15.web.core.windows.net/badge.png > badge-image.png
```

### Adding the Badge to Your Local IPFS

Initialize your local IPFS node. This command will start an IPFS local server on 127.0.0.1:5001.

```
$ ipfs daemon
```

Add your badge image to IPFS.

```
$ ipfs add badge-image.png
```

Running this command, you will receive a hash. This hash is your image address in IPFS. Make sure that you see the output shown in Figure [5-2](#521550_1_En_5_Chapter.xhtml#Fig2).

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig2_HTML.jpg)

A screenshot of the I P F S output after adding a file. It reads: dollar, i p f s add badge-image dot p n g. Further details of the file are presented in the next few lines, with the file size and a progress bar.

Figure 5-2

IPFS output after adding a file

### Pinning the Badge to a Remote IPFS Node

Pin your badge using Pinata as a remote IPFS service.

```
ipfs pin remote add --service=pinata --name=badge-image.png QmZPxKJWqJTdudyaZUyf6uBzwwAT41QQyxhTHmMZWB9yx4
```

You will get a response indicating the file was pinned successfully.

```
CID: QmZPxKJWqJTdudyaZUyf6uBzwwAT41QQyxhTHmMZWB9yx4
Name: badge-image.png
Status: pinned
```

### Creating the Badge Metadata

Create the badge metadata JSON file.

```
touch badge-metadata.json
```

Open the file `badge-metadata.json` and set the badge name, description, and image address. For the last one, you can use an IPFS gateway for the image to be displayed on any wallet that supports this badge type; otherwise, you will depend on the destination wallet support displaying images from IPFS hashes directly.

```
{
"name": "My badge",
"description": "My badge description",
"image": "https://ipfs.io/ipfs/QmZPxKJWqJTdudyaZUyf6uBzwwAT41QQyxhTHmMZWB9yx4"
}
```

Add your badge metadata to IPFS.

```
$ ipfs add badge-metadata.json
```

Pin your badge metadata using a remote IPFS service.

```
$ ipfs pin remote add --service=pinata --name=badge-metadata.json QmRzcwAtLWbeYqyaZUyf6uBzwwAT41QQyxhTHmMZWBfUTa
```

### Compiling the Smart Contract

Compile the contract using Truffle.

```
$ truffle compile
```

### Migrating the Smart Contract

Migrate the contract to the Rinkeby network using Truffle.

```
$ truffle migrate --network rinkeby
```

### Instantiate the Smart Contract

Instantiate the contract using the Truffle console.

```
$ truffle console --network rinkeby
```

Get the instance of the deployed contract.

```
truffle(rinkeby) let instance = await UniqueAsset.deployed()
```

### Awarding a Badge to a Wallet

Call the method `awardItem` and pass the Ethereum address as a first parameter and the IPFS address for the badge metadata. Make sure that the IPFS address corresponds to your badge metadata.

```
truffle(rinkeby) let result = await instance.awardItem("0x62761466bB3A3Da83B408B5F5fE00ac7b2a5A996","https://ipfs.io/ipfs/QmRzcwAtLWBeYqUx3ba1BkYKubSDLNTHCuiUB7WAmdfUTa")
```

### Checking a Badge on Etherscan

Once your contract is deployed, you will be able to see the public address of your contract. Find in the terminal the contract address created and copy it.

Go to [`https://rinkeby.etherscan.io`](https://rinkeby.etherscan.io) and paste the contract address in the search bar (Figure [5-3](#521550_1_En_5_Chapter.xhtml#Fig3)). You can use the Rinkeby Testnet Explorer tool to view the details of the created smart contract.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig3_HTML.jpg)

A screenshot of Rinkeby Testnet Explorer. On the top, the heading is Rinkeby Testnet Explorer. Below the heading is a drop-down option labeled all filters and an input bar with a random alphanumeric address.

Figure 5-3

Rinkeby Testnet Explorer: searching for a smart contract

Click the search icon. Now you can see that the contract was deployed successfully (Figure [5-4](#521550_1_En_5_Chapter.xhtml#Fig4)). On this details page, you can view data such as transactions.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig4_HTML.jpg)

A screenshot of a page titled contract. The top left and top right are labeled contract overview and more info, respectively. At the bottom, there are 3 options labeled transactions, contract, and events out of which the first tab transaction is selected.

Figure 5-4

Rinkeby Testnet Explorer: viewing smart contract transactions

You can also realize that the last transaction made was for awarding a new item.

### Adding the NFT Token to Your Wallet

Open your MetaMask wallet on your mobile phone and click Collectibles (Figure [5-5](#521550_1_En_5_Chapter.xhtml#Fig5)). Notice that the collectibles are available only on a mobile version.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig5_HTML.jpg)

A screenshot of the Metamask wallet. On the top is the heading wallet, Rinkeby Test Network. Below the heading are account 2, dollars 8548.21009, 0 x 6276 ellipsis A 996\. Receive, send, and swap options are at the bottom.

Figure 5-5

MetaMask: Collectibles tab

Click Add Collectibles***.*** Paste the token contract address here (the same one that you copied in the previous section) and enter the token ID (as it is the first token you will enter a 1 here).

Click Add and wait for a few seconds (Figure [5-6](#521550_1_En_5_Chapter.xhtml#Fig6)). The NFT token was added!

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig6_HTML.jpg)

A screenshot of Metamask wallet. The page is titled Wallet, Rinkeby Test Network. Below the title, are Account 2, dollar 8548.21009, 0 x 6276 ellipses A996\. Receive, send, and swap options. 2 tabs at the bottom are tokens and collectibles. Collectibles tab is selected and a unique asset, 0 U N A option is below the tab.

Figure 5-6

MetaMask: after adding the smart contract, it will appear here

Click UniqueAsset*.* Now you will be able to see all the badges that you earn (Figure [5-7](#521550_1_En_5_Chapter.xhtml#Fig7)). You can have multiple tokens originating from the same smart contract, each of which will be distinguished by a unique identifier.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig7_HTML.jpg)

A screenshot of Metamask unique asset. The page is titled unique asset, Rinkeby Test Network. Below the title is an icon labeled 1 unique asset and send, add, and info options. An icon labeled my badge, hashtag 1 with a selectable option is at the bottom.

Figure 5-7

MetaMask: badge listing

Click “My badge.” Now you can see the badge details! Also, you have a Send button so that you can send the badge to another wallet (Figure [5-8](#521550_1_En_5_Chapter.xhtml#Fig8)).

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig8_HTML.jpg)

A screenshot of Metamask unique asset. The page is titled unique asset, Rinkeby Test Network. Below the title is a badge labeled M E S T R E. Text my badge hashtag 1 and a send button is at the bottom.

Figure 5-8

MetaMask: badge display

That’s it! You just created your first NFT token!

## Sell Your Art NFT on OpenSea

OpenSea is a marketplace for digital goods such as collectibles, gaming items, digital art, and other digital assets backed by a blockchain such as Ethereum. You can purchase, sell, and trade any of these things with anyone in the world on OpenSea.

### Connecting to OpenSea

Go to OpenSea^([^(20)](#fn20)) and make sure that you are connected to the wallet that contains the NFT and that you are using the Rinkeby test network.

### Viewing Your Badge

Go to My Profile. Click Activity and then click the badge title. These are your badge details. The details page allows you to view various information regarding the negotiation of your badge.

### Listing Your Badge for Sale

Click Sell and then click Set Price. In Price, set the price that you desire to sell the NFT. On this page you can set the badge pricing method as well as schedule it to be listed at a future date (Figure [5-9](#521550_1_En_5_Chapter.xhtml#Fig9)).

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig9_HTML.jpg)

A screenshot of unique asset, my badge. It has three options for select your sell method, they are set price, highest bid, and bundle. Below them is a price input option. Include ending price, schedule for a future time, and privacy are with switch options.

Figure 5-9

OpenSea: badge pricing page

Click Post Your Listing. You will be redirected to the Summary page (Figure [5-10](#521550_1_En_5_Chapter.xhtml#Fig10)). On this page you can see the total fees that will be deducted when selling your badge.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig10_HTML.jpg)

A screenshot of the summary page. There are 3 sections, listing, bounties, and fees. Listing has post your listing with a selectable option. Bounties and fees have instructions and details about affiliates. There is an edit button next for Bounties and there is a link to learn more under fees.

Figure 5-10

OpenSea: badge’s Summary page

MetaMask will be open in order to validate the transaction (Figure [5-11](#521550_1_En_5_Chapter.xhtml#Fig11)). Click Confirm. In this step you need to approve the transaction that will confirm the listing of your badge for sale on the platform.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig11_HTML.jpg)

A screenshot of the Metamask notification window. On the top is Rinkeby Test Network, account 1 rightward arrow 0 x 3301 ellipsis f 83 A. Below them is a link and a button for set approval for all. 2 tabs details and data in which data is selected and it displays gas fee and total. The reject and confirm buttons are at the bottom.

Figure 5-11

MetaMask: confirm OpenSea transaction

Now you will need to provide some more details about you, such as your email and nickname. You will be asked to provide additional information after the transaction is approved (Figure [5-12](#521550_1_En_5_Chapter.xhtml#Fig12)).

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig12_HTML.jpg)

A screenshot of a window titled listing successful. The screenshot contains input options for email asterisk and nickname asterisk, send me occasional updates about Opensea is selected, and save button on the bottom left.

Figure 5-12

OpenSea: additional information

Click Save. Now, OpenSea will list your NFT for you!

### Exploring Listing Details

Scroll down to Trading History (Figure [5-13](#521550_1_En_5_Chapter.xhtml#Fig13)); as you can see, a new event was created named List and with Price set at US 10\. On this page you can see all the badge trading history on the platform.

![](images/521550_1_En_5_Chapter/521550_1_En_5_Fig13_HTML.jpg)

A screenshot of the page titled trading history. There is an option named filter on the top. At the bottom, there are columns named event, price, from, and to. Under event, there are three heads, namely, list, transfer, and created.

Figure 5-13

OpenSea: trading history

Click the Share icon. You can copy the link or share it on your social networks.

## Summary

In this chapter, you learned how to create tokens in the ERC-721 standard, pin the image in IPFS, and import it in OpenSea and put it up for sale.

In the next chapter, we will find out how to use faucets and why they are important in testnets.

Footnotes [1](#521550_1_En_5_Chapter.xhtml#Fn1_source)  

# 6. Faucets

You can use faucets to test smart contracts on the test network with no need to use real ether for this purpose (because ether from the mainnet is not valid in testnets, and vice versa). Ether in a test network has no real value except for testing purposes in smart contracts development.

At the end of this chapter, you will be able to do the following:

*   Access the faucet on the Ropsten network

*   Access the faucet on the Rinkeby network

*   Access the faucet on the Polygon Mumbai test network

*   Access the faucet on the Polygon main network

*   Send test ether to your wallet

*   Send test MATIC to your wallet

*   Check the updated balance in your wallet

## Getting Test Ether from the Faucet on the Ropsten Network

This Ethereum test faucet^([^(21)](#fn21)) drips 1 ether every five seconds. You have a request limit of 1 eth for every 24 hours to avoid network spam.

### Accessing the Faucet

Go to the Ropsten Ethereum (rETH) Faucet^([^(22)](#fn22)) and copy your wallet address (make sure the network selected is Ropsten). Paste your contract address in the form field. Click “Send Ropsten ETH”, as shown in Figure [6-1](#521550_1_En_6_Chapter.xhtml#Fig1).

![](images/521550_1_En_6_Chapter/521550_1_En_6_Fig1_HTML.jpg)

A screenshot of the Ropsten Faucet home page has 'Ropsten Ethereum r E T H Faucet' at the centre. Below, the text reads 'Receive 1 r E T H' per request. A section below is provided for entering a Ropsten Address, followed by a captcha and a button labelled Send Ropsten E T H.

Figure 6-1

Ropsten faucet home page

### Waiting for the Transaction

Click the transaction hash (which opens a new window) and wait for the transaction to be completed. Once the transaction has successfully completed, go to your MetaMask wallet, and you will see that you now have 1 ether!

## Getting Test Ether from the Faucet on the Rinkeby Testnet

This ether faucet is connected to the Rinkeby network. Requests are tied to common third-party social network accounts to prevent malicious actors from exhausting all the available funds or accumulating enough ether to mount long-running spam attacks. Anyone with a Twitter or Facebook account may request funds up to the allowed limits.

### Preparing for Funding

Open your MetaMask wallet and copy your wallet address to clipboard. Go to your Twitter account and paste in your wallet address. Click your tweet and copy your tweet address (the URL in the address bar).

### Funding Your Wallet

Go to [`https://faucet.rinkeby.io`](https://faucet.rinkeby.io) and paste your tweet address in the text field, as shown in Figure [6-2](#521550_1_En_6_Chapter.xhtml#Fig2). Click “Give me Ether” and select one of the options available (for example, 3 Ethers / 8 hours). The request will be funded in a few seconds.

It is important to note that there are varieties of faucets available. Periodically some of them may be empty, or over time they may run out of activity. Or, new faucets may even be created. If any of them become unavailable, you can search for new ones on the Internet.

![](images/521550_1_En_6_Chapter/521550_1_En_6_Fig2_HTML.jpg)

A screenshot of the Rinkeby Faucet homepage reads Funding request accepted for techtip 9 3 6 1 1 5 8 3 into, followed by the Ropsten address. At the centre, the text reads Rinkeby Authenticated Faucet, with a Twitter link and an option of Give me Ether. There are several codes and other task details provided below.

Figure 6-2

Rinkeby faucet home page

### Checking Your Wallet

Wait a few moments and check your MetaMask wallet. You will get 3 ether in your wallet account!

## Getting Test MATIC from the Faucet on the Mumbai Testnet

This faucet transfers TestToken/MATIC-ETH on Matic testnets and the corresponding parent chain.

### Preparing for Funding

Open your MetaMask wallet and copy your wallet address to the clipboard.

### Funding Your Wallet

Go to [`https://faucet.matic.network`](https://faucet.matic.network). For the token, select MATIC Token, and for the network, select Mumbai. Paste your tweet address in the text field and click Submit (Figure [6-3](#521550_1_En_6_Chapter.xhtml#Fig3)). The confirmation details will be provided for you. Click Confirm. The request will be funded in a few seconds.

![](images/521550_1_En_6_Chapter/521550_1_En_6_Fig3_HTML.jpg)

A screenshot of the Matic Faucet webpage reads, Login to Discord widget to report any issues with this Faucet. Beside Select token, there are many options provided with checking in options. Below there is a section for Select Network where 3 options are provided. At the bottom there is a bar to enter the account address and submit the data.

Figure 6-3

Matic faucet home page

### Checking Your Wallet

Wait a few moments and check your MetaMask wallet. You will get 0.1 MATIC in your wallet account!

## Getting the Test MATIC from the Faucet on the Mainnet

This faucet transfers TestToken/MATIC-ETH on Polygon testnets and the corresponding parent chain.

### Preparing for Funding

Open your MetaMask wallet and copy your wallet address to the clipboard.

### Funding Your Wallet

Go to [`https://matic.supply`](https://matic.supply) and click “Connect” to connect to your MetaMask wallet (Figure [6-4](#521550_1_En_6_Chapter.xhtml#Fig4)). Make sure your MetaMask wallet is connected to the Matic mainnet. Click “I am human” and solve the captcha.

![](images/521550_1_En_6_Chapter/521550_1_En_6_Fig4_HTML.jpg)

A screenshot of the Polygon Faucet home page has the logo and name on top and a large Receive button in the centre. Below the button, there is a checkbox with a captcha.

Figure 6-4

Polygon faucet home page

### Checking Your Wallet

Wait a few moments and check your MetaMask wallet. You have just received 0.001 MATIC in your wallet!

## Summary

In this chapter, you learned how to get test ether from faucets on the Internet. This will be very useful for deploying contracts on test networks so you don’t have to spend real ether.

Footnotes [1](#521550_1_En_6_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_6_Chapter.xhtml#Fn2_source)  

# 7. InterPlanetary File System

The InterPlanetary File System^([^(23)](#fn23)) (IPFS) is a protocol and peer-to-peer network that allows data to be stored and shared in a distributed file system. IPFS employs content addressing to distinguish each file in a global namespace that connects all computing devices.

At the end of this chapter, you will be able to do the following:

*   Install the IPFS node package and initialize a node

*   View the IPFS node peers

*   Test and explore the IPFS node

*   Add files to IPFS

*   View the file content on the console and check the file in the web UI

*   View the file content directly in a browser

*   Install the IPFS browser extension

*   Configure the IPFS node type and start a node

*   Import a file into the IPFS node

*   Start a local IPFS node and add files to it

*   Check the files added and verify whether a file has been pinned

*   Pin the files manually

*   Set up API keys on Pinata

*   Set up Pinata as a remote service

*   Pin your file to the remote IPFS node and also unpin it

*   Log in to Fleek

*   Clone an existing repository

*   Install and initialize the Fleek package

*   Deploy a site to Fleek

## Create Your IPFS Node

Now, let’s create an IPFS node using the command line and upload your first file.

### Installing the Node

Install the IPFS using the Choco package manager.^([^(24)](#fn24)) The package `go-ipfs` is an IPFS implementation in Go.

```
$ choco install go-ipfs
```

### Configuring the Node

Initiate the IPFS local repository.

```
$ ipfs init
```

Start the IPFS local server. The `daemon` command starts an IPFS local server on 127.0.0.1:5001.

```
$ ipfs daemon
```

### Testing the Node

You can test the IPFS node showing the peers that are directly connected to your node.

```
$ ipfs swarm peers
```

You can also view some IPFS file content by using the `cat` command and passing the hash as a parameter (Figure [7-1](#521550_1_En_7_Chapter.xhtml#Fig1)).

![](images/521550_1_En_7_Chapter/521550_1_En_7_Fig1_HTML.jpg)

A screenshot of the I P F S command output has the terminal tab open with links provided. The text below reads Hello and Welcome to I P F S, where I P F S is large and stylized. Text at the bottom reads If you're seeing this, you have successfully installed I P F S and are now interfacing with the i p f s merkledag.

Figure 7-1

IPFS cat command output

```
$ ipfs cat 
```

### Exploring Your IPFS Node

Go to a browser and access the web UI at `http://127.0.0.1:5002/`. Your node is now connected to IPFS! Now, click Files and notice that are no files here yet (Figure [7-2](#521550_1_En_7_Chapter.xhtml#Fig2)).

Click “Explore” and then click “Peers”. These are the peers that you are connected to. Finally, click “Settings”. This is where you can see your node settings.

![](images/521550_1_En_7_Chapter/521550_1_En_7_Fig2_HTML.png)

A screenshot of the I P F S Web U I has the following icons: Status, Files, where the cursor lies, Explore, Peers, and Settings. In the Files window, a note reads, No files here yet! Add files to your local IPFS node by clicking the Import button above. A section containing the import button along with other details is at the top right.

Figure 7-2

IPFS Web UI

## Add Files to the IPFS

A computer running IPFS can ask all the peers to which it is connected if they have a file with a specific hash, and if one of them does, that peer sends back the entire file. That would not be possible without a short, unique identifier, such as a cryptographic hash.

### Adding the File

Start the IPFS local server. The `daemon` command starts an IPFS local server on 127.0.0.1:5001.

```
$ ipfs daemon
```

Now, create a new file called `hello.txt` using the `echo` command. This command outputs the given text to a new file.

```
$ echo "test" hello.txt
```

Add the newly created file to your local IPFS node using the `ipfs add` command.

```
$ ipfs add hello.txt
```

The file was added to IPFS, resulting in a hash identifier.

### Viewing the File Content on the Console

You can see the file content of this newly added file simply using the `ipfs cat` command followed by the hash. For that, replace the `<`*your_hash*`>` code snipped by the result hash identifier generated in the previous step by the command “`ipfs add hello.txt`”.

```
$ ipfs cat 
```

After running this command, the file content will be displayed on the terminal.

### Checking the File in the Web UI

Got to [`http://127.0.0.1:5001/webui`](http://127.0.0.1:5001/webui) and click “Files”. Now, click “Pins” and copy the hash. Search by using this hash, and you will see that this hash is there.

### Viewing the File Content in a Browser

Open a new tab and go to `ipfs://<`*your_hash*`>`. Now you can see your file content in the browser. Here again, replace the `<`*your_hash*`>` code snipped by the result hash identifier generated for your file with the command “`ipfs cat <your_hash>`”.

## Set Up the IPFS Browser Extension

The IPFS Companion Extension^([^(25)](#fn25)) allows you to run an IPFS node locally inside your preferred browser, providing support for `ipfs://` addresses, automatic IPFS gateway loading of websites and file paths, simple IPFS file import and sharing, and more.

### Installing the Browser Extension

Go to the IPFS Companion Extension^([^(26)](#fn26)) and click “Add to Brave” or the name of your browser. Click “Add extension” and then click the “Extensions” icon. Finally, pin the IPFS Companion Extension to the extensions bar.

### Configuring the Node Type

Click the IPFS Companion icon and then click the gear icon. For IPFS Node Type, select External.

### Starting an External Node

Go to Visual Studio Code and open a new terminal. Start a new IPFS local server.

```
$ ipfs daemon
```

### Importing a File

Click the IPFS Companion icon and then click “Import”. Click “Pick a file” and select a file from your local disc. The file will be stored in your IPFS node.

## Pin and Unpin IPFS Files on the Local Node

Data can be pinned to one or more IPFS nodes to ensure that it remains on IPFS and is not removed during garbage collection. Pinning allows you to manage storage space and data retention. Therefore, you should go ahead pin any content that you want to keep on IPFS indefinitely. IPFS’s default behavior is to pin files to your local IPFS node.

### Starting Your Local Node

Start the IPFS local server. The daemon command starts an IPFS local server on 127.0.0.1:5001.

```
$ ipfs daemon
```

### Adding a File to Your Node

Now, create a new file called `hello.txt` using the `echo` command. This command outputs the given text to a new file.

```
$ echo "world" hello.txt
```

Add the newly created file to your local IPFS node using the `ipfs add` command.

```
$ ipfs add hello.txt
```

The file was added to IPFS, resulting in a hash identifier. When you add a file, this is automatically pinned to your local node.

### Checking the File Was Added

To check that your file was added, you can use the `ipfs cat` command to output the file content to the terminal.

```
$ ipfs cat your_file_hash
```

### Verifying Your File Was Pinned

Go to `http://127.0.0.1:5001/webui`, click Files, and then click Pins. Your file is there!

### Unpinning Your File

You can unpin the file from your local IPFS node simply by using the following command:

```
$ ipfs pin rm 
```

### Pinning Your File Manually

You can pin files manually using the following command. Remember that you need to copy the hash of your file to pin or unpin it.

```
$ ipfs pin add 
```

You’re done; your file was pinned again!

## Pin and Unpin Files on a Remote Node Using Pinata

You can also pin your files to a remote pinning service. These third-party services allow you to pin files to nodes that they operate rather than your own local node. You don’t have to be concerned about your own node’s disk space or uptime.

While you can manage IPFS files pinned to a remote pinning service using its own GUI, CLI, or other dev tools, you can also work directly with pinning services using your local IPFS installation, eliminating the need to learn a pinning service’s unique API or other tooling.

### Setting Up API Keys on Pinata

Log in on your Pinata account and go to API Keys. Click New Key and then check the Admin box. Enter **admin-cli** as your key name and finally click Create Key. A new key will be generated for you. Copy the JWT value from this window.

### Setting Up Pinata as a Remote Service on Your Terminal

Add Pinata as a pinning remote service. Paste your JWT inside the `<`*your_jwt_key*`>` chunk.

```
$ ipfs pin remote service add pinata https://api.pinata.cloud/psa 
```

List all the existing remote services and check that Pinata is there.

```
$ ipfs pin remote service ls
```

### Adding a New File to Your Local IPFS Node

Add a new file to your IPFS local node.

```
$ echo "world" > hello.txt
$ ipfs add hello.txt
```

Copy the hash identifier generated after adding the file to the local node.

### Pinning Your File to the Remote IPFS Node

Pin your file using the following command. Paste the file hash inside the `<`*your_file_hash*`>` chunk.

```
$ ipfs pin remote add --service=pinata -name=hello.txt 
```

Go back to the Pinata website and click Pin Manager. Your file will appear on this page!

### Unpinning Your File from the Remote IPFS Node

Unpin your file using the following command. Paste the file hash inside your `<`*your_file_hash*`>` chunk.

```
$ ipfs pin remote rm --service=pinata -name=hello.txt 
```

Go back to the Pinata website and click Pin Manager. Your file will no longer appear on this page; that means your file was unpinned.

## Host Your Site on IPFS Using Fleek

Fleek enables you to create a base-layer architecture based on Open Web protocols. Create and host your sites, apps, DApps, and other services on trustless, permissionless, and open technologies that are geared toward enabling user-controlled, encrypted, private, peer-to-peer experiences.

### Logging In on Fleek

Go to [`https://fleek.co`](https://fleek.co) and log in with your account; then go to your VS Code editor.

### Cloning Your Existing Repository

Clone an existing repository with some sample code.

```
$ git clone https://github.com/johnnymatthews/random-planet-facts
```

### Installing Fleek

Install the Fleek command line.

```
$ npm install -g @fleekhq/fleek-cli
```

Log in to your Fleek account (you will be prompted to complete the flow in your browser).

```
$ fleek login
```

### Initializing Fleek

Initialize the Fleek site in your current directory.

```
$ fleek site:init
```

You will be asked to select which team you want to use (use the arrow keys for selecting). Also, select which site you want to use. Finally, select the public directory for deployment.

### Deploying Your Site

Deploy the changes in your `publish` directory.

```
$ fleek site:deploy
```

Go back to the Fleek site and click Hosting; then click Verify on IPFS. This is your site hosted on IPFS. You can now see your deployed site online.

Go back to Hosting and click `your-site.on.fleek.co`. This is your site host’s friendly address (Figure [7-3](#521550_1_En_7_Chapter.xhtml#Fig3)).

![](images/521550_1_En_7_Chapter/521550_1_En_7_Fig3_HTML.jpg)

A screenshot where the frosty hyphen frog hyphen 8 1 5 8 folder under the Hosting node is open. Below there are 2 points that read deployed locally and verify on I P F S. The next part has the overview tab open where there are 2 points provided. 1\. Site is deployed. 2\. Set up a custom domain with S S L.

Figure 7-3

Fleek hosting overview

## Summary

In this chapter, you learned how to install and create an IPFS node, as well as manage files with this protocol.

In the next chapter, you will learn how to create a Filecoin project.

Footnotes [1](#521550_1_En_7_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_7_Chapter.xhtml#Fn2_source)   [3](#521550_1_En_7_Chapter.xhtml#Fn3_source)   [4](#521550_1_En_7_Chapter.xhtml#Fn4_source)  

# 8. Filecoin

Protocol Labs, the same company that invented and maintains IPFS, created Filecoin. It expands the IPFS concept by establishing an open source decentralized storage network and incentivizes users to keep data pinned to IPFS.

Filecoin is great because it offers a decentralized storage solution that does not require reliance on bigger centralized storage solutions. Unlike pure IPFS-based solutions, Filecoin nodes have financial incentives to stay active.

We will use a Filecoin implementation named Lotus in this example. It verifies network transactions, manages an FIL wallet, and can execute storage and retrieval deals.

At the end of this chapter, you will be able to do the following:

*   Create a Filecoin project

*   Configure Truffle to use Filecoin endpoints

*   Start the local Filecoin server

*   Add images to be preserved on Filecoin

## How to Preserve Files on the Filecoin Local Node

To preserve files in Filecoin, you will learn how to create a project from scratch and configure Truffle so that it points to the correct address. You will start the local endpoints and finally add an image to Filecoin using the command line.

### Creating the Project

Go to the terminal and click New Terminal. Now, create a new folder to start your project.

```
$ mkdir create filecoin
```

### Configuring Truffle

Create the `truffle-config.js` file.

```
$ touch truffle-config.js
```

In this file, configure the file setting for the IPFS and Filecoin lotus servers. Set the following values:

*   Set the IPFS address to `http://127.0.0.1:5001`.

*   Set the Filecoin address to `http://localhost:7777/rpc/v0`.

You will use Ganache Filecoin to start these endpoints later.

```
module.exports = {
environments: {
development: {
ipfs: {
address: "http://127.0.0.1:5001",
},
filecoin: {
address: "http://localhost:7777/rpc/v0",
storageDealOptions: {
epochPrice: "2500",
duration: 518400,
}
},
}
}
};
```

### Adding an Image to Be Preserved

Create the `badge` folder.

```
$ mkdir badge
```

Go to the `badge` root folder.

```
$ cd badge
```

Download the image that you will use to preserve on Filecoin. You can also copy and paste an existing image in this folder. The `curl` command is used for transferring data with a URL syntax.

```
$ curl https://planouhost.z15.web.core.windows.net/badge.png > badge-image.png
```

### Installing Dependencies

Install the base Ganache package with a Filecoin tag.

```
$ npm install ganache@filecoin
```

Install the Filecoin peer dependency package.

```
$ npm install @ganache/filecoin
```

Start the Lotus and IPFS local servers using Ganache. It also creates 10 development accounts with 100 Filecoins (FIL) each.

### Starting Local Endpoints

Start Ganache Filecoin locally.

```
$ npx ganache filecoin
```

### Preserving Files to Filecoin

Open a new terminal and preserve the folder `badge`.

```
$ truffle preserve ./badge/ --filecoin
```

If everything went well, the output will be as follows:

```
Preserving target: ./badge/
===========================
Warning: multiple plugins found that output the label "ipfs-cid".
✓ Loading target...
✓ Reading directory ./badge/...
✓ Opening ./badge\badge-image.png...
✓ Preserving to IPFS...
✓ Connected to IPFS node at http://127.0.0.1:5001
✓ Uploading...
Root CID: QmNjGGACAMpm718WjeM7oN3WJ5ntXKgwurH4mMivU6JXoS
./badge-image.png: QmemrBxhPpg8Hw9sPpAUTWRq3kNAFhCPKUDhMnc5U9KptS
✓ Preserving to Filecoin...
✓ Connected to Filecoin node at http://localhost:7777/rpc/v0
✓ Retrieving miners...
✓ Proposing storage deal...
Deal CID: bafyreidrt2pvuh44umo7vzebnxnw46qzntneyojah6oym4ximokyzvwxgq
✓ Waiting for deal to finish...
Deal State: StorageDealActive
```

Now you have the image preserved in your local Filecoin node!

## Summary

In this chapter, you learned how to preserve files using Filecoin.

In the next chapter, you will learn what ENS is and how to use it.

# 9. Ethereum Name Service

The Ethereum Name Service (ENS) allows users to send and receive Ethereum as well as access special websites by using simple names rather than long, complex sequences of letters and numbers.

At the end of this chapter, you will be able to do the following:

*   Search for a domain name on the ENS network

*   Register an available domain

*   Manage an ENS registration name

*   Check an ENS name resolution

## Register Your ENS to Receive Crypto, Tokens, or NFTs in Your Wallet

Let’s search for an available domain name in ENS and register it. Next, you will manage the address through a registration page. Finally, you’ll check if the domain is resolving correctly to the address that is configured.

### Searching for Your Domain Name

Go to the ENS Domains^([^(27)](#fn27)) page and click Launch App. Search for the domain name that you want to register (for example, `planou.eth`). Check the registration period (for example, a minimum of one year) and the registration price.

### Registering Your Name

Click Request to Register. A MetaMask notification will open to confirm the transaction. Click Confirm and wait for the transaction to be confirmed on the blockchain. Once it’s confirmed, click Register. A new MetaMask notification will appear. Click Confirm once again and wait for the transaction to be confirmed on the blockchain.

### Managing Your Registration Name

Click “Manage name” and scroll down to Addresses. Realize that the ETH address is set to the wallet that created the domain, in this case, your wallet (Figure [9-1](#521550_1_En_9_Chapter.xhtml#Fig1)).

![](images/521550_1_En_9_Chapter/521550_1_En_9_Fig1_HTML.jpg)

A screenshot of the webpage titled 'planou dot e t h' has the description, Learn how to manage your name. The Parent field is set to e t h, registrant and controller to an alphanumeric string starting with 0, expiration date to a date and time, and resolver to an alphanumeric string. The Records table lists addresses.

Figure 9-1

ENS domain registration page

### Checking the Name Resolution

Click your MetaMask wallet and click Send. Type your ENS name (for example, `planou.eth`). Note that the name has resolved to a wallet address (Figure [9-2](#521550_1_En_9_Chapter.xhtml#Fig2)).

![](images/521550_1_En_9_Chapter/521550_1_En_9_Fig2_HTML.jpg)

A screenshot of the Add Recipient window has Rinkeby Test Network dropdown on top. Below there is a section for typing Add recipient where planou dot e t h is typed, with the cursor hovering over the drop-down option to select planou dot e t h with part of a hexadecimal code visible.

Figure 9-2

MetaMask ENS resolution name

Now you can use the ENS name as a recipient instead of using the wallet hash address for your transactions.

## Summary

In this chapter, you learned how simple configuring an ENS domain is, and you learned how to start using it right away.

In the next chapter, you will start learning about how to get off-chain data with oracles using Chainlink.

Footnotes [1](#521550_1_En_9_Chapter.xhtml#Fn1_source)  

# 10. Chainlink

Chainlink^([^(28)](#fn28)) is a decentralized network of nodes that uses oracles to transfer data and information from off-blockchain sources to on-blockchain smart contracts. In this chapter, you will learn how to use the ETH/USD price feed on the Kovan testnet to access the most recent cryptocurrency price inside smart contracts.

At the end of this chapter, you will be able to do the following:

*   Create a simple smart contact for price consumption

*   Set up an Infura project

*   Configure the private key to sign transactions

*   Deploy the smart contract on the Kovan network

*   Get price information from the smart contact on the Kovan network

## Get Crypto Prices Inside Smart Contracts Using Chainlink Oracles

Let’s start by creating a new project and then installing the Chainlink contracts package. You will use an existing contract address that tells you the price of the ETH/USD pair, and then you will be able to see that price being returned by your smart contact.

### Creating the Project

Go to the Terminal menu, click New Terminal, and initialize a new Truffle project.

```
$ truffle init
```

Now, initialize the project folder.

```
$ npm init
```

Finally, install the Chainlink contracts package.^([^(29)](#fn29))

```
$ npm install @chainlink/contracts@0.1.9
```

### Creating the Smart Contract

Create a new smart contract for price consumption.

```
$ touch contracts/PriceConsumer.sol
```

Open the file `PriceConsumer.sol` (Figure [10-1](#521550_1_En_10_Chapter.xhtml#Fig1)).

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig1_HTML.jpg)

A screenshot of the V S code contracts folder has the Price Consumer dot s o l tab open. The left pane 'Explorer' has other sections: Contracts under which Migrations dot s o l and Price Consumer dot s o l (which is selected) is present, migrations, node underscore modules, test and two dot j s o n and one dot j s file.

Figure 10-1

VS Code contracts folder

In the `PriceConsumer.sol` file, define the Solidity version and then import the Chainlink contract interface. After that, define the contract name and the contract constructor.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";
contract PriceConsumer {
AggregatorV3Interface internal priceFeed;
constructor(){
priceFeed = AggregatorV3Interface()
}
}
```

Go to [`https://docs.chain.link/docs/ethereum-addresses`](https://docs.chain.link/docs/ethereum-addresses) and scroll down to the Kovan section. Copy the Proxy address on the line “ETH/USD” (Figure [10-2](#521550_1_En_10_Chapter.xhtml#Fig2)).

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig2_HTML.jpg)

A screenshot of the chainlink price feeds has 'Using Price Feeds' listed on the left. There are multiple options under 'Using Price Feeds', one of them being 'Contact Addresses' which is selected. Under it, 'Etherium Price Feeds' is selected. On the right, there is a table with 3 columns with details of the price feeds.

Figure 10-2

Chainlink price feeds

Paste the address into the `AggregatorV3Interface` constructor. After that, create the function to get the price.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";
contract PriceConsumer {
AggregatorV3Interface internal priceFeed;
constructor(){
priceFeed = AggregatorV3Interface(0x9326BFA02ADD2366b30bacB125260Af641031331);
}
function getThePrice() public view returns (int){
(
uint80 roundID,
int price,
uint startedAt,
uint timeStamp,
uint80 answeredInRound
) = priceFeed.latestRoundData();
return price;
}
}
```

### Creating the Migration

Create the migration file in the `migrations` folder.

```
$ touch migrations/2_deploy_contracts.sol
```

Write code to deploy the `PriceConsumer` smart contract (Figure [10-3](#521550_1_En_10_Chapter.xhtml#Fig3)).

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig3_HTML.jpg)

A screenshot of the V S code migrations folder has the 2 underscore deploy underscore contracts dot j s tab open. The number 1 is displayed on the window.

Figure 10-3

VS Code migrations folder

```
const PriceConsumer = artifacts.require("PriceConsumer");
module.exports = function(deployer){
deployer.deploy(PriceConsumer);
};
```

### Setting Up Your Infura Project

Go to [`https://infura.io`](https://infura.io) and access your dashboard. Click Ethereum and then click “Create a project”. Finally, define the project name and copy the project ID (Figure [10-4](#521550_1_En_10_Chapter.xhtml#Fig4)). Notice that you can connect with different testnets and also to the mainnet.

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig4_HTML.jpg)

A screenshot of the Infura settings has a dashboard on the left. The bar on top has the title ethereum. The Settings tab is open. The Project Details have to be entered on the main screen. The first section has Name with 'ethereum' entered, which is a required field, and the second section has Keys with the Project I D displayed.

Figure 10-4

Infura settings

Now, click Save Changes.

### Configuring the Wallet to Sign Transactions

Install the file system `fs` package. This package provides a lot of useful functionality to access and interact with the file system.

```
$ npm install fs
```

Install the wallet provider `hdwallet` package. This also installs the HD wallet-enabled Web 3 provider, which is used to sign transactions for addresses derived from a 12- or 24-word mnemonic.

```
$ npm install @truffle/hdwallet-provider@1.2.3
```

Open the `truffle-config.js` file and uncomment the `HDWalletProvider` code section.

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const infuraKey = '';
const fs = require('fs');
const mnemonic = fs.readFileSync(".secret").toString().trim();
```

Paste your Infura project ID as a value for the variable `infuraKey`.

### Configuring the Network

In `truffle-config.js`, uncomment the `ropsten` network section and change the following values:

*   Change `ropsten` to `kovan`.

*   Change the Ropsten Infura URL to `kovan`.

*   Change `YOUR-PROJECT-ID` to `${infuraKey}`.

*   Change `network_id t`o `42`.

```
kovan: {
provider: () => new HDWalletProvider(mnemonic, `https://kovan.infura.io/v3/${infuraKey}`),
network_id: 42,
gas: 5500000,
confirmations: 2,
timeoutBlocks: 200,
skipDryRun: true
},
```

### Configuring the Solidity Compiler

Still in `truffle.config.js`, uncomment the `compilers` section and change the version to `0.8.0`.

```
compilers: {
solc: {
version: "0.8.0",
docker: true,
settings: {
optimizer: {
enabled: false,
runs: 200
},
evmVersion: "byzantium"
}
}
},
```

### Configuring the Private Key

Create the secret file as follows:

```
$ touch .secret
```

Go to the browser and open your MetaMask wallet connected to the Infura network. Click “Your Account” and then click “Settings”. Finally, click “Security & Privacy” (Figure [10-5](#521550_1_En_10_Chapter.xhtml#Fig5)).

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig5_HTML.jpg)

A screenshot of the Security and Privacy window for the Reveal Seed Phrase setting. There is text that reads 'Reveal Seed Phrase' and a button below with the same text, where the cursor is placed.

Figure 10-5

MetaMask: revealing the seed phrase

You have the option to view your seed phrase, but be aware that this information is sensitive, and if someone has access to it, they will be able to restore your wallet and make use of your funds.

Click Reveal Seed Phrase and enter your wallet password to continue. Copy the private key. Go back to Visual Studio Code and paste the private secret recovery phrase in the file `.secret`.

### Compiling the Smart Contract

Compile the contract using Truffle.

```
$ truffle compile
```

### Deploying the Smart Contract

Deploy the contract to the Kovan network using Truffle. The `migrate` command runs migrations to deploy contracts on the Kovan network.

```
$ truffle migrate --network kovan
```

Wait for the contract to be deployed and the transactions to be confirmed on the blockchain. Now, check your contract address that was created (Figure [10-6](#521550_1_En_10_Chapter.xhtml#Fig6)).

![](images/521550_1_En_10_Chapter/521550_1_En_10_Fig6_HTML.jpg)

A screenshot of the V S code deployed contract output window has Problems, Output, Terminal and Debug console tabs. The codes are displayed in the terminal tab, with details of transaction, blocks, contract address, block number and timestamp, account, balance, gas used, gas price, value sent, and total cost.

Figure 10-6

VS Code deployed contract output

### Getting the Price Information from the Smart Contract

Instantiate the contract using the Truffle console. This console command opens a basic interactive console that connects to an Ethereum client on the Kovan network:

```
$ truffle console --network kovan
```

Now, use the deployed command to return the deployed contract instance on the Kovan network, as shown here:

```
truffle(kovan) let instance = await PriceConsumer.deployed()
```

Call the method `getThePrice`. The `let` command stores the method result in the variable `price`, and the `await` command will execute the method asynchronously.

```
truffle(kovan) let price = await instance.getThePrice()
```

Finally, output the result to number. The method `toNumber()` converts big number objects to regular numbers.

```
truffle(kovan) price.toNumber()
265499339990
```

That’s it, you just created a smart contract and consumed the Chainlink price feed oracle!

## Summary

In this chapter, you learned how to create a simple smart contract using Chainlink to get price information from a Chainlink oracle.

In the next chapter, you will learn about Nethereum, a .NET library for Ethereum.

Footnotes [1](#521550_1_En_10_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_10_Chapter.xhtml#Fn2_source)  

# 11. Nethereum

Nethereum^([^(30)](#fn30)) is an open source .NET integration library for Ethereum that simplifies smart contract maintenance and interaction with public and private Ethereum nodes. This framework exposes a `Web3` class where it is possible to interact with methods of wallets or smart contracts. In the example that you will see in this chapter, you will use the `GetBalance` method of a wallet to find out its balance.

At the end of this chapter, you will be able to do the following:

*   Create a new console project using dotnet

*   Create a method to get a wallet balance using Nethereum

*   Display the result in the console

## Getting Your Ether Balance Using Nethereum

Let’s start by creating a new console project and adding the Nethereum `Web3` package to our application. Then you will create the method that will fetch the balance of a specific wallet address. Finally, you will print this information to the console in wei and in ether.

### Creating the Project

Go to the terminal and click New Terminal. Create a new `dotnet` console project as follows. This command creates a new project, configuration file, or solution based on the specified template:

```
$ dotnet new console -o sample
```

Go to the project’s root directory.

```
$ cd sample
```

### Installing Web3

Install the Nethereum `Web3` package. This command adds a package reference to a project file:

```
$ dotnet add package Nethereum.Web3
```

Restore all the project packages. This command restores the dependencies and tools of a project:

```
$ dotnet restore
```

### Creating the Method

Open the `Program.cs` file and add a reference for threading and `Web3`. Now, add a new method for getting the account balance and then instantiate a new `Web3` object.

Go to your Infura project settings and select the Ropsten network. Copy the Ropsten `https` endpoint (Figure [11-1](#521550_1_En_11_Chapter.xhtml#Fig1)).

![](images/521550_1_En_11_Chapter/521550_1_En_11_Fig1_HTML.jpg)

A screenshot of the Infura Projects keys has the project I D on the top left, project secret and the bar on the top right, endpoints with the Ropsten option selected and 2 links provided below, with clickable copy icons next to them.

Figure 11-1

Infura project keys

Use this endpoint as a parameter for the `Web3` object constructor. In the `Program.cs` file, get the balance from `Web3` using your wallet’s public address as a parameter. Write the code to output the balance in wei and then convert the wei balance to ether. Finally, write the code to output the balance in ether. Now, change your main method to call `GetAccountBalance()`.

```
using System;
using System.Threading.Tasks;
using Nethereum.Web3;
namespace NethereumSample
{
class Program
{
static void Main(string[] args)
{
GetAccountBalance().Wait();
Console.ReadLine();
}
static async Task GetAccountBalance()
{
var web3 = new Web3("https://ropsten.infura.io/v3/39180531508b4b659780ef7a36426a86");
var balance = await web3.Eth.GetBalance.SendRequestAsync("0x03d1b3162DBFaB4A175038eAa4EA4b39423d5A6F");
Console.WriteLine($"Balance in Wei: {balance.Value}");
var etherAmount = Web3.Convert.FromWei(balance.Value);
Console.WriteLine($"Balance in Ether: {etherAmount}");
}
}
}
```

### Getting the Balance

Build the project. This command builds a project and all of its dependencies:

```
$ dotnet build
```

Run the project. This command runs the source code without any explicit compile or launch commands:

```
$ dotnet run
```

Check the terminal output and make sure that you get the result shown in Figure [11-2](#521550_1_En_11_Chapter.xhtml#Fig2).

![](images/521550_1_En_11_Chapter/521550_1_En_11_Fig2_HTML.jpg)

A screenshot of the V S code balances in Wei and ether. The Program dot c s file is selected and the terminal tab is open. The details of the same, the balance in Wei and the balance in Ether are displayed in the window below the command: dollar sign dot net run. There is no space between dot and net in the command.

Figure 11-2

VS code balances in wei and ether

## Summary

In this chapter, you learned how to create a console project that gets a wallet balance using Nethereum.

Footnotes [1](#521550_1_En_11_Chapter.xhtml#Fn1_source)  

# 12. Conclusion

During the course of this book I presented the most common use cases for the blockchain today in a simple manner. The idea was to keep it simple and get straight to the point. All the examples provided in this book were created to make you advance quickly down the learning path. As a developer, I know that the best way to learn is by coding, so I wanted to share that way of learning with you.

Learning how to be a blockchain developer is challenging. This is an area that is constantly changing, and staying up-to-date as a professional will take you many hours and a lot of effort. Don’t let it be overwhelming for you. We all start from scratch and learn new skills with baby steps. I hope this book gave you the knowledge you need to get started.

With this knowledge, you can now create your own cryptocurrency or create your own NFT and put it up for sale on OpenSea, or better yet, you can host your first site on IPFS with a custom domain in ENS. You can also query off-chain data using Chainlink oracles or use Filecoin to preserve data in a distributed fashion. The possibilities are endless.

Remember, the blockchain is gaining traction, and it’s no longer limited to Bitcoin and Ethereum. In the future, we will see lots of projects growing and others losing space. But never forget that technology exists to solve real problems. To apply what you learned in this book, think first about what problems you are trying to solve and whether they are best solved with off-chain solutions. At the end of the day, the blockchain will help you solve problems related to trust. Is a centralized data source trustworthy? Is a centralized application trustworthy? If not, go with the blockchain.

I hope you enjoyed reading this book; last but not least, never stop learning. I hope to see you soon as a blockchain developer.

Here are some references for further reading:

*   [OpenSea](https://opensea.io/)^([^(31)](#fn31))

*   [OpenSea Testnets](https://testnets.opensea.io/)^([^(32)](#fn32))

*   [Rinkeby Authenticated Faucet](https://faucet.rinkeby.io/)^([^(33)](#fn33)) (social validation)

*   [Rinkeby Ether Faucet](http://rinkeby-faucet.com)^([^(34)](#fn34)) (no social validation)

*   [Matic Faucet](https://faucet.matic.network)^([^(35)](#fn35))

*   [Polygon Faucet](https://matic.supply)^([^(36)](#fn36))

*   [IPFS](https://matic.supply) [documentation](https://docs.ipfs.io)^([^(37)](#fn37))

*   [Filecoin documentation](https://docs.filecoin.io)^([^(38)](#fn38))

*   [Filecoin Lotus](https://docs.filecoin.io/get-started/lotus/)^([^(39)](#fn39))

*   [Truffle, Filecoin](https://www.trufflesuite.com/docs/filecoin/truffle/quickstart)^([^(40)](#fn40))

*   [Ethereum Name Service](https://ens.domains/)^([^(41)](#fn41))

*   [Chainlink documentation](https://docs.chain.link)^([^(42)](#fn42))

*   [Nethereum documentation](http://docs.nethereum.com/en/latest/)^([^(43)](#fn43))

Footnotes [1](#521550_1_En_12_Chapter.xhtml#Fn1_source)   [2](#521550_1_En_12_Chapter.xhtml#Fn2_source)   [3](#521550_1_En_12_Chapter.xhtml#Fn3_source)   [4](#521550_1_En_12_Chapter.xhtml#Fn4_source)   [5](#521550_1_En_12_Chapter.xhtml#Fn5_source)   [6](#521550_1_En_12_Chapter.xhtml#Fn6_source)   [7](#521550_1_En_12_Chapter.xhtml#Fn7_source)   [8](#521550_1_En_12_Chapter.xhtml#Fn8_source)   [9](#521550_1_En_12_Chapter.xhtml#Fn9_source)   [10](#521550_1_En_12_Chapter.xhtml#Fn10_source)   [11](#521550_1_En_12_Chapter.xhtml#Fn11_source)   [12](#521550_1_En_12_Chapter.xhtml#Fn12_source)   [13](#521550_1_En_12_Chapter.xhtml#Fn13_source)  Index A, B Blockchain Developer Kit C Chainlink Oracles contracts package decentralized network deployment Infura project migration file network configuration price feeds price information private key project creation seed phrase sign transactions smart contract solidity compiler VS Code contracts folder D Docker’s techniques E Ethereum Name Service (ENS) crypto/tokens/NFT domain registration page domains page registration page resolution name Ethereum Virtual Machine (EVM) Blockchain Developer Kit description Docker Ganache gas MetaMask Truffle Visual Studio Code F Faucets description ether testing Polygon rETH TestToken/MATIC-ETH Filecoin badge folder dependencies file setting implementation local endpoints local node project creation terminal and preserve files truffle-config.js file Fleek account details deployment hosting overview initialization installation IPFS repository Fungible tokens (ERC-20 standard) description fixed supply tokens account transaction compilation Ganache development blockchain MetaMask configuration migrate command project creation solidity file token (MetaMask) overview truffle command line Ganache Infura mainnet network deployment MetaMask extension network configuration PolygonScan wallet balance OpenZeppelin SeeOpenZeppelin polygon G, H Ganache command line Ganache development blockchain command-line ERC-20 standard blockchain Brave browser contract deployment deployment development network MetaMask extension migration file I, J, K, L Infura project account creation dashboard details page ERC-20 standard dashboard deployment Etherscan verification prerequisites private key secret file smart constants testnet wallet balance home page welcome page InterPlanetary File System (IPFS) companion extension browser installation external node importing file node type description file information browser content local server Web UI Fleek node description cat command output configuration installation testing web UI pin/unpin files checking file hello.txt local node pin files remote node server unpin option verification M MATIC Token MetaMask Wallet accessing option account details badge display collectibles tab configuration ERC-20 standard configuration custom token network selection list ENS Infura account creation dashboard details page project’s home page welcome page installation network configuration page NFT N Nethereum balance command description ether balance `GetAccountBalance() method` Infura project keys `Program.cs file` project creation Web3 package wei and ether Nonfungible token (NFT) description Ganache/OpenZeppelin library badge image compilation instantiation IPFS local server metadata MetaMask wallet migration network configuration pinning badge private key project creation Rinkeby Testnet Explorer sign transactions solidity compiler wallet OpenSea additional information connection description details page pricing page Summary page trading history transaction page O OpenZeppelin contract library compiler version writing contract initialization representation verification P, Q Pinata API Keys file hash local node pinning remote service remote node unpinning node Polygon ERC-20 token testnet Addon constants deployment Infura project mainnet network Matic network MetaMask networks PolygonScan PoS activation page prerequisites private key summary page order values wallet balance Polygon faucet Preserve files SeeFilecoin R Rinkeby faucet Ropsten Ethereum (rETH) Faucet home page transaction hash S Solidity project compilation contracts deployment high-level language project creation structure creation T Truffle package U Unit tests description smart contracts assertions fixed supply output results terminal and execute test execution V, W, X, Y, Z Visual Studio Code