+++
title = "A brief introduction to RGB protocols"
description = ""
date = 2021-11-08
+++
<p align="center">
  <img src="/images/RGB_Logo.png" alt="RGB"/>
</p>

On January 3, 2009 Satoshi Nakamoto launched the first Bitcoin node, from that moment new nodes joined and Bitcoin begin to behave as if it were a new form of life, a form of life that has not stopped evolving, little by little it has become the safest network in the world as a result of its unique design, very well thought out by Satoshi since, through economic incentives, it attracts users commonly called miners to invest in energy and computing power which contributes to network security.

As Bitcoin continues its growth and adoption it faces scalability issues, the Bitcoin network allows a new block with transactions to be mined in an approximate time of 10 minutes, **assuming we have 144 blocks in a day with maximum values ​​of 2700 transactions per block, Bitcoin would have allowed only 4.5 transactions per second,** Satoshi was aware of this limitation, we can see it in an email[^1] sent to Mike Hearn in March 2011 where he explains how what we know today as a payment channel works. micropayments quickly and safely without waiting for confirmations. This is where off-chain protocols come in.

According to Christian Decker[^2] Off-chain protocols are usually systems in which users use data from a blockchain and manage it without touching the blockchain itself until the last minute. Based on this concept, the Lightning Network was born, a network that uses off-chain protocols to allow Bitcoin payments to be made almost instantaneously and since not all these operations are written on the block chain, it allows thousands of transactions per second and scale Bitcoin.

Research and development in the area of ​​off-chain protocols on Bitcoin has opened a pandora's box, today we know that we can achieve much more than value transfer in a decentralized way, the non-profit [LNP/BP Standards Association](https://lnp-bp.org) focuses on development of layer 2 and 3 protocols on Bitcoin and the Lightning Network, among these projects [RGB](https://rgbfaq.com) stands out.

# What is RGB?
RGB has appeared from the research by Peter Todd[^3] on single-use-seals and client-side-validation, which was coined in 2016-2019 by Giacomo Zucco and community into a better asset protocol for Bitcoin and Lightning network. Further evolution of these ideas led to a development of RGB into a fully-fledged smart contract system by Maxim Orlovsky, who is leading its implementation since 2019 with community participation.

We can define RGB as a set of open source protocols that allows us to execute complex smart contracts in a scalable and confidential way. It is not a particular network (like Bitcoin or Lightning); each smart contract is just a set of contract participants which can interact using different communication channels (defaulting to Lightning network). RGB uses Bitcoin blockchain as a layer of state commitment and maintains the code of the smart contract and the data off-chain, which makes it scalable, leveraging Bitcoin transactions (and Script) as a ownership control system for smart contracts; while the evolution of the smart contract is defined by an off-chain scheme, finally it is important to note that everything is validated on the client side.

In simple terms, **RGB is a system that allows the user to audit a smart contract, execute it and verify it individually at any time without having an additional cost since for this it does not use a blockchain as "traditional" systems do,** complex smart contracts systems were pioneered by Ethereum but due to it requires the user to spend significant amounts of gas for each operation, they never achieved the scalability they promised by consequence it never was an option to bank the users excluded from the current financial system.

Currently, the *blockchain* industry promotes that both the code of smart contracts and the data must be stored in the blockchain and executed by each node of the network, regardless of the excessive increase in size or the misuse of computational resources. **The scheme proposed by RGB is much more intelligent and efficient since it cuts with the blockchain paradigm by having smart contracts and data separated from the blockchain and thus avoids the saturation of the network seen in other platforms,** in turn it does not force each node to execute each contract but rather the parties involved which adds confidentiality to a level never seen before.

![RGB vs Ethereum](/images/rgb-vs-eth.png)

# Smart contracts in RGB
In RGB smart contract developer defines a scheme specifying rules how the contract evolves over time. The scheme is the standard for the construction of smart contracts in RGB, and both an issuer when defining a contract for issuance and a wallet or exchange must adhere to a particular scheme against which they must validate the contract. Only if the validation is correct can each party accept requests and work with the asset.

**A smart contract in RGB is a directed acyclic graph (DAG) of state changes,** where only a portion of the graph is always known and there is no access to the rest. The RGB scheme is a core set of rules for the evolution of this graph the smart contract starts with. Each contract participant may add to those rules (if this is allowed by the schema) and the resulting graph is built from the iterative application of those rules.

# Fungible assets
The fungible assets in RGB follow the LNPBP RGB-20 specification[^4], when an RGB-20 is defined, the asset data known as "genesis data'' is distributed through the Lightning network, which contains what is required to use the asset. The most basic form of assets do not allow secondary issuance, token burning, renomination or replacement.

Sometimes the issuer will need to issue more tokens in the future, i.e. stablecoins such as USDT, which keeps the value of each token tied to the value of an inflationary currency such as the USD. To achieve this more complex RGB-20 schemata exists, and in addition to the genesis data they require the issuer to produce **consignments**, which will be also circulating in the lightning network; with this information we can know the total circulating supply of the asset. The same applies for burning assets, or changing its name.

The information related to the asset can be public or private: if the issuer requires confidentiality, he/she can choose not to share information about the token and perform operations in absolute privacy, but we also have the opposite case in which the issuer and the holders need the whole process to be transparent. This is achieved by sharing the token data.

# RGB-20 procedures
The burning procedure disables tokens, burned tokens can’t be used anymore.

Replacement procedure occurs when tokens are burned and a new amount of the same token is created. This helps reduce the size of the asset's historical data, which is important to maintain asset speed.

To support the use case where it is possible to burn assets without having to replace them, a sub-scheme of RGB-20 is used that only allows burning assets.

# Non fungible assets
The non-fungible assets in RGB follow the LNPBP RGB-21 specification[^5], when we work with NFTs we also have a main scheme and a sub-scheme. These schemes have an engraving procedure, which allows us to attach custom data by part of the token owner, the most common example we see in NFTs today is digital art linked to the token. The token issuer can prohibit this data engraving by using the RGB-21 sub-scheme. Unlike other NFT blockchain systems, **RGB allows to distribute large-size media token data in a complete decentralized and censorship-resistant manner, utilizing extension to the Lightning P2P network called Bifrost,** which is also used for building many other forms of RGB-specific smart contract functionalities.

Additionally to fungible assets and NFTs, RGB and Bifrost can be used to produce other forms of smart contracts, including DEXes, liquidity pools, algorithmic stable coins etc, which we will cover in future articles.

# NFT from RGB vs NFT from other platforms
* No need for expensive blockchain storage
* Not need for IPFS, a Lightning network extension (called Bifrost) is used instead (and it’s fully end-to-end encrypted)
* No need for a special data management solution – again, Bifrost takes that role
* No need to trust websites to maintain data for NFT tokens or about issues assets / contract ABIs
* Built-in DRM encryption and ownership management
* Infrastructure for backups using the Lightning Network (Bifrost)
* Ways to monetize content (not only selling the NFT itself, but access to the content, several times)

# Conclusions
Since the launch of Bitcoin, almost 13 years ago there has been a lot of research and experimentation in the area, both the successes and the mistakes have allowed us to understand a little more how decentralized systems behave in practice, what makes them really decentralized and what actions tend to lead them to centralization, all this has led us to conclude that real decentralization is a rare and difficult phenomenon to achieve, real decentralization has only been achieved by Bitcoin and it is for this reason that we focus our efforts to build on top of it.

RGB has its own rabbit hole within the Bitcoin rabbit hole, while I am falling down through them I will post what I have learned, in the next article we will have an introduction to the LNP and RGB nodes and how to use them.
***
[^1]: <https://plan99.net/~mike/satoshi-emails/thread4.html>

[^2]: <https://btctranscripts.com/chaincode-labs/chaincode-residency/2018-10-22-christian-decker-history-of-lightning/>

[^3]: <https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-June/012773.html>

[^4]: <https://github.com/LNP-BP/LNPBPs/blob/master/lnpbp-0020.md>

[^5]: <https://github.com/LNP-BP/LNPBPs/blob/master/lnpbp-0021.md>