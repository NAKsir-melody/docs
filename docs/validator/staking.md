---
id: staking
title: Running a Validator Node
sidebar_label: Running the Node
---

## Staking on official TestNet

### _READ THIS PART BEFORE YOU START_

Wait until your node is fully synced before you send a staking transaction. An out of sync node cannot produce or validate blocks, so if you're chosen as a validator, you're at risk of being kicked out of the validator pool and losing your rewards if your node doesn't maintain the appropriate uptime \(i.e. validate / produce the number of assigned blocks for that epoch\).

## Node requirements

To become a validator, you need a node running on your machine or cloud provider with the following minimum spec:

```bash
At least 2 CPUs
At least 4GB RAM
At least 50 GB free disk
```

## Setting up your environment

**IMPORTANT: Make sure you have the latest version of NEAR Shell and Node Version; 10.x**

If this is not the case, follow the setps below to set up your environment; don't worry this won't take long. To stake, make sure that you have

* an account with tokens. If you have not set up an account yet, please navigate to the following page, set it up and come right back: [Create Account](../local-setup/create-account.md)
* `near-shell`, our CLI tool, which which will require [node.js](https://nodejs.org/en/download/) and [npm](https://www.npmjs.com/get-npm). You can check whether you have node.js and npm already installed by

  1. Open your command line;
  2. Type in
    ```bash
    node -v
    ```
    This should display your node version in the command line.
    ```bash
    npm -v
    ```
    This should display your npm version in the command line.

    Otherwise, go ahead and install it with the following links [node.js](https://nodejs.org/en/download/). Note that node usually installs npm automatically. However, if you miss npm, please install it [from here](https://www.npmjs.com/get-npm).

Once node and npm are installed, go ahead and download the Near Shell; type the following in your terminal:

```bash
# Download Near Shell with npm:
npm i -g near-shell
```
Once this is installed, go ahead and run your node.

### Run a Node

Now that you have the Near Shell, we can set-up your node. Please go to the documentation on [How to run a Node](../local-setup/running-testnet.md)

**IMPORTANT you will need your account ID here, which is your username from the account that you created in the previous step.**

Please come back to this screen, once you have completed the previous steps.

When asked for the account ID, enter the username of the account you want to stake with. You will be returned a public key used for staking; this will look similar to:

```bash
Stake for user 'thefutureisnear' with 'ed25519:97JLghrxUQMaX2pcerVB5FNFu4qk8rx8J3fnWRyoEB7M'
```

Make sure you copy this validator\_key as you will need it for the next step. You can also find this public key at the following path in your near files `~/.near/validator_key.json`

## Send a staking transaction

Awesome! Once you completed the previous steps, you are all set for staking.

First let's authenticate near shell by running the command `near login`

You will be asked to navigate to a url to authenticate your staking account.

```bash
Please navigate to this url and follow the instructions to log in:
https://wallet.nearprotocol.com/login/?title=NEAR+Shell&public_key=FSgxX7YwuCveCeYqsSAB3sD8dgdy3XBWztCQcEjimpaN
```
Once done, enter that account ID in the shell:

```bash
Please enter the accountId that you logged in with:
```

When you have entered your account ID, it will display the following message:

`Missing public key for <asdfasdf> in default`
`Logged in with masternode24`

This message is not an error, it just means that it will create a public key for you.

Now you're ready to send a staking transaction.

```bash
near stake <accountId> <staking public key> <amount to stake>
```

Staking 10 NEAR should be enough on TestNet.

You should see a success message that looks something like:

```text
Staking 10 on thefutureisnear with public key = A4inyaard6yzt1HQL8u5BYdWhWQgCB87RbRRHKPepfrn.
```

## Being chosen to become a validator

After this, you will need to wait the ~5 minute bonding period on TestNet to see if you have staked enough to become a validator. You can see you are a validator when in the logs of the node you see "V/" - where V means this node is currently a validator.

![](assets/validators%20%281%29.png)

Legend: # 7153 | BlockHeight V/1 | 'V' (validator) or '—' (regular node)

The 0/0/40 shows the total validators: connected peers / up to date peers / my peers. This number may change over time.

To learn more about how validators are chosen, take a look at the [Validator FAQ](../validator/validator-faq.md).

## See current list of Validators and stake amounts

To see the current list of validators, you can take a look here: [http://rpc.nearprotocol.com/status](http://rpc.nearprotocol.com/status)

If you would like to see how much a validator is staking, you can run the command `near state <account name>`in Near Shell.

```bash
{
  amount: '100011163887239132720351',
  code_hash: '11111111111111111111111111111111',
  locked: '97985903901882082761',
  storage_paid_at: 25,
  storage_usage: 182
}
```

## Automatically re-staking

This is implemented as a separate service that runs in parallel with `nearcore` and re-stakes if it sees that given validator might be kicked out or is already kicked out.

You can run this service either on the same machine or on a different machine for extra security to keep your signing key separate.

Note that right now this uses `validator_key.json` to sign transactions as well.  We plan to add an option for a different signing key soon.

```bash
cargo build -p restaked --release
./target/release/restaked \
    --home=<path to folder with validator key>
    --rpc-url=http://url.to.your.node.or.any.node \
    --wait-period=<how often to check> \ # measured in seconds
    --stake-amount=<amount to stake>     # use 0 to re-stake the last seen amount
```
