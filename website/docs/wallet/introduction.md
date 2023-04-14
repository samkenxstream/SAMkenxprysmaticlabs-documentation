---
id: introduction
title: Keys, wallets, and accounts
sidebar_label: Keys, wallets, and accounts
---

import {HeaderBadgesWidget} from '@site/src/components/HeaderBadgesWidget.js';

<HeaderBadgesWidget  commaDelimitedContributors="James" lastVerifiedDateString="February 20th, 2023"/>

This section explains everything about how to manage validator accounts using Prysm's built-in wallet, as well as setup instructions for different types of wallets including HD (hierarchical deterministic), and non-HD wallets.

:::tip Pro-Tip
Prysm's validator accounts are extensible enough to allow for the most basic setup all the way to advanced production setups where security is paramount.
:::

Out of the box, Prysm supports 3 basic kinds of wallets that encompass many different use-cases. In order of highest to lowest security:

1. **non-HD wallet**: (good security) A simple wallet in which accounts are password protected and validator keys are generated non-deterministically. This is the recommended approach if you want to import an account from the [Ethereum launchpad](https://launchpad.ethereum.org/) and you can read dedicated instructions [here](/docs/wallet/nondeterministic).
2. **HD wallet**: (least security) A common type of blockchain wallet which is generated from a english mnemonic, able to create new accounts deterministically. The encrypted seed is stored on day encrypted by a strong password. Given you are tying your HD wallet to the validator client, it is less secure than simply importing validating keys you need from an external source or running a remote signer. 

At the core of Prysm's validator accounts lies the notion of a validator private key, which is stored in a password-protected, keystore.json file. Prysm supports the ability to manage many validator accounts, making it easy to import and export them as well as easily list all the account info in your wallet. Prysm is compliant with the [EIP-2335](https://eips.ethereum.org/EIPS/eip-2335) standards for storing Ethereum consensus validator private keys, making it possible to move keys between different Ethereum consensus client implementations.

### Security considerations

Security for your keys is a big topic, but at its core, it is important to clarify the role of the private keys in Ethereum consensus. When you generate a validator key, you actually generate 2 different ones: a **withdrawal key** and a **validating key**. The withdrawal key should be stored offline, and can be used to withdraw your gains in the future as it is not used during the validating process. Your validating key, however, needs to be accessible by your running validator client software **at all times**.

As a validator, you're expected to be consistently online to produce blocks and vote on others' blocks, as this is how you get rewarded for participating in Ethereum consensus. To do this, your software needs to have instant access to your validating key, also referred to often as a "hot key" or access to a "hot wallet". Keeping your withdrawal key, or wallet mnemonic _far away_ from your validator client is what will give you **optimal security** in Ethereum consensus. If someone were to steal your validating keys, they wouldn't be able to withdraw your validator's staked ETH.

:::tip Keeping your wallet safe
When creating an HD wallet, you'll be given a 24-word mnemonic phrase which you need to store safely. Make sure you write it down somewhere safe offline, and do not leave traces of it on your computer. If someone gets ahold of this mnemonic, they can steal all your accounts!
:::

The ideal security for an average user participating as a validator is as follows:

- Create a wallet using the official [eth2.0-deposit-cli](https://github.com/ethereum/eth2.0-deposit-cli) and keep your mnemonic stored offline, safely.
- Import only the validating keys you need into your validator client, such as by following the instructions [here](../install/install-with-script#step-5-run-a-validator-using-prysm).

For **best security** in production cloud deployments, it's best you use a **remote signer**, as that offers absolute separation of your secret keys and your validator client software. Read more about remote signers [here](web3signer.md).

## Non-HD wallets (Importing Keystores)

Prysm supports a non-deterministic wallet, which is a very simple kind of wallet storing validating keystores on disk, which can be imported from an external source such as the [eth2.0-deposit-cli](https://github.com/ethereum/eth2.0-deposit-cli). This type of wallet makes it very easy to import and export accounts, useful for certain cloud deployments in which you don't want to store your eth2 withdrawal keys on-disk. This wallet is also the recommended approach if you generated a validator deposit using the [Ethereum launchpad](https://launchpad.ethereum.org/).

```text
wallet-directory/
    accounts/
        all-accounts.keystore.json
```

[Create and use a non-HD wallet](https://docs.prylabs.network/docs/wallet/nondeterministic)

## HD wallets

By default, a Prysm validator client uses its own **HD wallet**, which you can recover from a 24-word, simple english sentence should you lose access to it, such as:

```text
glue hawk service repeat album stable arctic piece kiss arrive viable labor connect film deer trap brain fashion duck omit beach ten slot goat
```

HD wallets are password protected via a high-entropy, strong password, and allow for easy creation of as many validator account as you wish.

[Create and use an HD wallet](https://docs.prylabs.network/docs/wallet/deterministic)
## Frequently asked questions

As you run your validator, you might run into unexpected errors or situations in which things aren't working as expected. Here are our answers to some of the most frequently asked questions.

#### How are my accounts stored? What data is stored on disk?

If you are running a **simple import wallet (non-HD)**, we keep an encrypted file called `all-accounts.keystore.json` protected by a strong password which contains your validating private keys and public keys. This file is stored in your wallet path.

If you are running an **HD wallet**, we store your encrypted wallet seed under your wallet path in a file named `encrypted.seed.json`. This file is protected by a strong password you set during wallet creation, and we do not store your password.

#### Why is my validator losing ETH despite my setup appearing ok?

If your validator client is running fine without errors but you're seeing your validator balance decrease, it is typically a sign your beacon node is either (a) crashed, (b) not synced to the chain head. This might also mean your beacon node doesn't have any peers and is likely not connected to anyone. To debug this problem, please read our guide on checking [everything is running as expected](/docs/monitoring/is-everything-fine). If this still does not resolve your issue, you can get in touch with our team on [Discord](https://discord.gg/prysmaticlabs) anytime.

#### How can I use a hardware wallet with my validator?

At the moment, there is no built-in hardware wallet support for validators, but teams such as Ledger are working on integrating BLS12-381 keys (the type of keys used by Ethereum consensus) into their products.

#### Help! Something is messed up with the validator and I can't start it

If you're encountering an unexpected issue that causes your client to crash or throw errors you cannot understand, you can always talk to your team on [Discord](https://discord.gg/prysmaticlabs).

#### How can I stop being a validator?

You can stop being a validator by issuing a **voluntary exit**, which is a special type of object included in the Ethereum beacon chain that signifies your validator is ready to stop validating and securely exit the validator set. Although during phase 0 of Ethereum consensus, you will **not** be able to withdraw your staking rewards, you can still issue a voluntary exit. You can find instructions for this process [here](/docs/wallet/exiting-a-validator).


import {RequestUpdateWidget} from '@site/src/components/RequestUpdateWidget.js';

<RequestUpdateWidget />