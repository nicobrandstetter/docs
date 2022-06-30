---
sidebar_position: 2
---

# Become a validator

### Installing the Node

```bash
mkdir lukso-l16-testnet && cd lukso-l16-testnet
sudo curl https://raw.githubusercontent.com/lukso-network/lukso-cli/main/install.sh | sudo bash
```

The script will download the LUKSO cli into the folder. 
 
#### Setting up your node and node name
```bash
lukso network init --chain l16
```
 

## Starting the Node

```bash
# Start your nodes
lukso network start
```

### Setup Validator

```
lukso network validator setup
```

This will create a key store and a transaction wallet. The purpose of the transaction wallet is to call and pay for the deposit
transaction. You can check if the wallet has enough funds by calling

```
lukso network validator describe
```

Visit the [Faucet](https://faucet.l16.lukso.network) and paste the transaction wallet public key into the input field.

Transfer **enough** (#validators x staking_amount **+ extra LYXt to pay deposit fees**) funds to the transaction wallet public's address.

 

#### Submit the transaction.

Make a dry run first

```bash
lukso network validator deposit --dry
```

This will give you the possibility to peek in what is going to happen without executing a transaction.

If you are sure that everything is correct you run the command


```bash
lukso network validator deposit
```

It can take up to eight hours before your validator becomes active, but you can already start your validator in the meantime.

Once you deposited LYXt make sure to create a backup.

```bash
lukso network validator backup
```

Store the file **node_recovery.json** somewhere safe.

### Start the Validator Client

```bash
# Make sure your _consensus_ and _execution_ clients are running
lukso network validator start

# You can check logs with
lukso network log validator -f

You can close your logs by pressing ctrl+c

# You can stop the validator using, this will also stop all other nodes
lukso network validator stop
```

Occasionally check the status of your validator by either typing

```bash
lukso network validator describe
```

Or by visiting the [Explorer](https://explorer.consensus.l16.lukso.network)

## Check the Network Status

You can see your node on the following pages:

1. [https://stats.execution.l16.lukso.network](https://stats.execution.l16.lukso.network)
2. [https://stats.consensus.l16.lukso.network](https://stats.consensus.l16.lukso.network)


## Check your logs
```
lukso network log consensus -f
lukso network log execution -f
```

You can close your logs by pressing ctrl+c

## Stop your node
```
lukso network stop
```


##### If you selected a wrong chain, you could reset the setup. This will delete all related data except the keystores.
##### NOTE: the network must be stopped
```
lukso network clear
```

### Check the Network Status

You can see your node on the following pages:

1. [https://stats.execution.l16.lukso.network](https://stats.execution.l16.lukso.network)
2. [https://stats.consensus.l16.lukso.network](https://stats.consensus.l16.lukso.network)

## Terminology

### Validator Node 

**Validator Node** is a combination of services and an underlying keystore that if run together are 
syncing, validating and proposing blocks. In most cases it can be described as a directory that contains  
all necessary information to *run* this node. At LUKSO the directory has the following structure:

* **configs**
  * **configs.yaml**   // configuration of consensus service
  * **genesis.json**   // configuration of execution service
* data
  * **execution_data**   // db of execution service
  * **consensus_data**   // db of consensus service
  * **validator_data**   // db of validator service
* **keystore** 
  * **prysm/direct/account/all-accounts.keystore.json**     // keystore of valdiator keys
  * ...
  * **password.txt**        // password of keystore
* **docker-compose.yaml** // describes how to run the docker images
* **node_config.yaml**   // adjustable values on how to run the nodes
* **.env**   // auto genrated file derived from **node_config.yaml**


### Validator Keystore

The **Validator Keystore** is a directory with private key in formats for the respective validator service 
version (Teku, Lighthouse, Prysm,...). The keystore has a fixed number of keys. If you need to change
the number of keys you **must** create a new keystore. There is always **one** **Validator Keystore** for
one **Validator Node**

### Validator Key

The **Validator Key** is a private key that can have an active balance and is used to sign attestations
and proposed blocks. The key can have an arbitrary amount of staked LYX but it **won't** change the reward.
It is possible to deposit LYX multiple time to this validator key and that is important for the case the **Validator Key** missed duties and lost balance.

### Validator Key State

The **Validator Key State** is the state of one particular key. A **Validator Keystore** can have many
keys being in many states. When firstly created all the **Validator Keys** are in the state
NOT_DEPOSITED. (NOTE: If the keystore was recreated the state my differ for some keys)

| State         | Acitvated By | Comment |
|---------------|--|---------|
| NOT_DEPOSITED | ... | The keystore was created for the first time        |
| PENDING              | A deposit with *min staking amount* was made | There is a proven stake deposited in the Deposit Contract        |
| ACTIVE              | The deposit was observed by the consensus network |  The validator is eligible to be selected to propose and attest in the upcoming epochs       |

## How Validator Keys are created

A **Validator Key** is always part of a **Validator Keystore** - as a single key or a combination of many. The keys
are being derived by a [Mnemonic](https://wolovim.medium.com/ethereum-201-mnemonics-bb01a9108c38).
A Mnemonic can potentially create an infinite amount of keys. It is important to understand that
these keys are indexed. There is a possibility to (theoretically) create a certain range.

Once a mnemonic is known the creation of **Validator Keystores** is **not** random but **deterministic**.

### An Example

Given a mnemonic *m*. We create a keystore from position 0 to 2. This could result into:

* **Keystore A**
  * Key0: 0x8154..12
  * Key1: 0x7361..45
  * Key2: 0x7481..fe

Now let's assume we deleted this keystore, and we create a new one from position 1 to 3. This results into:

* **Keystore B**
  * Key1: 0x7361..45
  * Key2: 0x7481..fe
  * Key3: 0x78ca..89
  

As you can see the Key1 and Key2 are the same in **Keystore A** and **Keystore B**. This mechanism
allows for great power to rearrange your node setup.

### Node Setup Example

Let's assume - given a mnemonic *m* - we want to create 2 nodes with 30 keys in 
**Node A** and 16 keys in the other **Node B**.  Given our mnemonic *m* we would 
e.g. have the following setup:

**Node A** has a keystore with keys from position *0* to position *29*
**Node B** has a keystore with keys from position *30* to position *45*

Now let's assume we want to rearrange the **Validator Keys**'s by having an equal amount of keys on both nodes.

We should:
  1. Stop the validator nodes
  2. Delete the keystores
  3. Recreate the keystores with the same mnemonic **m**
  4. Start the nodes again

The setup could be

**Node A** has a keystore with keys from position *0* to position *22*
**Node B** has a keystore with keys from position *23* to position *45*


