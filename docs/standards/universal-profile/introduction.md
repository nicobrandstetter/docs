---
sidebar_label: 'Introduction'
sidebar_position: 1
---

# Universal Profiles

:::success Useful Tip

The [Guides Section](../../guides/universal-profile/create-profile) will take you through the process of creating a Universal Profile and deploying it on the [L14 testnet](../../networks/l14-testnet.md), check out [universalprofile.cloud](https://universalprofile.cloud/). It lets you easily browse the deployed profiles.

:::

## Introduction

Building an identity on top of keys is almost impossible because keys are likely to be leaked or lost forever. Holding assets and building reputation on these keys makes it worse. A better representation of identity would be with blockchain-based accounts.

Blockchain-based accounts can change the way of interacting on-chain, allowing the usage of multiple components together. Used in combination with a Controller (see Key Manager), they could allow any entity to execute or set some data on your profile. Finally, contracts for social recovery could be used in the event of keys being lost. All these components together can enhance the blockchain experience.

## Universal Profiles

| Standard                                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ![LSP0-ERC725Account](../../../static/img/standards/lsp0-erc725account-contract.jpeg)               | **[LSP0 - ERC725Account](./01-lsp0-erc725account.md)**: This standard represents the core account contract. It consists of [ERC725X](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-725.md#erc725x), allowing a contract to interact with any address or smart contract on the blockchain, and [ERC725Y](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-725.md#erc725y), a generic key-value store that allows storing an unlimited amount of data in the smart contract. It also contains [ERC1271](https://eips.ethereum.org/EIPS/eip-1271) to verify if messages were signed by the ERC725Account's owner. If the owner is a Key Manager supporting ERC1271, it will delegate the call to the KeyManager. Lastly, it contains the [LSP1-UniversalReceiver](../generic-standards/02-lsp1-universal-receiver.md), which allows this contract to be notified of any incoming asset. Using the [LSP1-UniversalReceiverDelegate](../universal-profile/02-lsp1-universal-receiver-delegate.md) logic, you can delegate the universal receiver call that an asset will do to an external contract, customizing the behaviour you want towards the asset. More on that below. |
| ![LSP6-KeyManager](../../../static/img/standards/lsp6-key-manager-contract.jpeg)                 | **[LSP6-KeyManager](./04-lsp6-key-manager.md)**: A standard representing a smart contract that can act as the owner of an [LSP0ERC725Account](./01-lsp0-erc725account.md). It reads permissions of addresses from the ERC725Account's key-value store, and restricts access based on these permissions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ![LSP1-UniversalReceiverDelegate](../../../static/img/standards/lsp1-universal-receiver-delegate-contract.jpeg) | **[LSP1-UniversalReceiverDelegate](./02-lsp1-universal-receiver-delegate.md)**: By setting the **[`LSP1UniversalReceiverDelegate`](../generic-standards/lsp1-universal-receiver#extension)** key in your ERC725Account to a contract address, you can delegate any call to the `universalReceiver(...)` function of the account to this contract, which allows you to revert on certain incoming assets or add other logic. The [standard **LSP1-UniversalReceiverDelegate** implementation](../smart-contracts/lsp1-universal-receiver-delegate-up.md) will write every [LSP7 and LSP8](../nft-2.0/01-introduction.md) asset you receive into your ERC725Account using the [LSP5-ReceivedAsset](./06-lsp5-received-assets.md) standard. This allows any interface to list all token contracts that hold a balance of your account, right from the smart contract.                                                                                                                                                                      |

:::note

An LSP0-ERC725Account can work standalone and doesn't require a Key Manager, or a Universal Receiver Delegate to work, but this will limit the user experience.

:::

## Adding profile information

The **[LSP0-ERC725Account](./01-lsp0-erc725account.md)** standard represents a blockchain-based account that does not contain any metadata describing the account. It's important to standardize specific keys to give the account a unique character and look like a normal **web2 profile**.

**[LSP3-UniversalProfile-Metadata](./03-lsp3-universal-profile-metadata.md)** is a standard used to add profile information by setting its defined keys in the account storage. The combination of these two standards forms a **Universal Profile**.

## References

- [LUKSO Standards Proposals: LSP0 - ERC725 Account (Standard Specification, GitHub)](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-0-ERC725Account.md)
- [LUKSO Standards Proposals: LSP3 - Universal Profile Metadata (Standard Specification, GitHub)](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-3-UniversalProfile-Metadata.md)
- [LUKSO Standards Proposals: LSP6 - Key Manager (Standard Specification, GitHub)](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-6-KeyManager.md)
- [LSP1 Universal Receiver: Solidity implementations (GitHub)](https://github.com/lukso-network/lsp-universalprofile-smart-contracts/tree/develop/contracts/LSP1UniversalReceiver)