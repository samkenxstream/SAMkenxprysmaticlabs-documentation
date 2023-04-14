---
id: fee-recipient
title: Configure Fee Recipient 
sidebar_label: Configure Fee Recipient
---
import FeeRecipientPng from '@site/static/img/fee-recipient-ui.png'
import {HeaderBadgesWidget} from '@site/src/components/HeaderBadgesWidget.js';

<HeaderBadgesWidget commaDelimitedContributors="James,Mick" lastVerifiedDateString="September 12th, 2022" lastVerifiedVersionString="v4.0.0" />

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


:::tip Configure this or lose money

If you don't configure your fee recipient wallet address, your priority fee earnings will be deposited into a [burn address](https://etherscan.io/address/0x0000000000000000000000000000000000000000).

:::

**Fee Recipient** is a feature that lets you specify a priority fee recipient address on your validator client instance and beacon node.

Your fee recipient wallet address is a **standard Ethereum wallet address**, just like the wallet address used when sending and receiving tokens from Metamask. Execution clients deposit priority fees into this address whenever your validator client proposes a new block.

## Background

When users pay gas to submit transactions to the Ethereum network, they can specify a **priority fee**. Priority fees are like tips. End-users pay priority fees to incentivize block proposers to prioritize the inclusion of particular transactions in the blocks that they propose.

Priority fees are captured by execution clients in the execution layer <a class="footnote" href='#footnote-2'>[2]</a>, so validator clients need to tell execution clients where to forward the fees. This "forwarding address" is referred to as your **fee recipient** wallet address.


## Configure fee recipient

Your fee recipient wallet address can be configured on your **validator client instance** and on your **beacon node**. We recommend configuring it in both places. Your validator's configuration will override the beacon node configuration, while the beacon node configuration will be treated like a backup in the event that your validator configuration fails.


### Configure fee recipient via flags

Your fee recipient wallet address can be configured on both your beacon node and validator through the `--suggested-fee-recipient` flag:

 - **Beacon node**: `./prysm.sh beacon-chain --suggested-fee-recipient=<WALLET ADDRESS>`
 - **Validator**: `./prysm.sh validator --suggested-fee-recipient=<WALLET ADDRESS>`

For example: `./prysm.sh validator --suggested-fee-recipient=0xCHANGEME012345c769F504hs287200aF50400a`.

If your validator is running multiple keys (for example, staking 64 ETH using two validator public keys that have been imported into a single validator client instance), all validator public keys will use the wallet address specified through the `--suggested-fee-recipient` flag. You can optionally associate different fee recipient wallet addresses to individual validator public keys using the JSON/YAML configuration method detailed in the following section.


### Configure fee recipient via JSON/YAML (validator only)

You can assign different wallet addresses to each of your validator public keys using JSON/YAML configuration. Fee recipient address assignments specified through JSON/YAML override those configured through the `--suggested-fee-recipient` flag. This method of configuration uses the following JSON/YAML schema:


<Tabs groupId="format" defaultValue="json" values={[
        {label: 'JSON', value: 'json'},
        {label: 'YAML', value: 'yaml'}
    ]}>
  <TabItem value="json">


```
{
  "proposer_config": {
    "<VALIDATOR PUBLIC KEY>": {
      "fee_recipient": "<WALLET ADDRESS>"
    },
    "<VALIDATOR PUBLIC KEY>": {
      "fee_recipient": "<WALLET ADDRESS>"
    }
  },
  "default_config": {
    "fee_recipient": "<WALLET ADDRESS>"
  }
}
```


   </TabItem>
   <TabItem value="yaml">


```
---
proposer_config:
  '<VALIDATOR PUBLIC KEY>':
    fee_recipient: '<WALLET ADDRESS>'
  '<VALIDATOR PUBLIC KEY>':
    fee_recipient: '<WALLET ADDRESS>'
default_config:
  fee_recipient: '<WALLET ADDRESS>'
```


   </TabItem>
</Tabs>

Property definitions are as follows:

 - `proposer_config`: An object containing key-value pairs, where member keys are validator public keys.
 - `<VALIDATOR PUBLIC KEY>`: A validator public key (98 characters long - not a wallet address) used as a JSON property key. Example: `0x0123456748ed887f6c4c6adf334070efcd75140eada5ac83a92506dd7a057816155ad77931185101128655c0191bd0214`.
 - `fee_recipient`: A fee recipient wallet address. Example: `0x52FfeB84540173B15eEC5a486FdB5c769F50400a`.
 - `default_config`: An object containing a default fee recipient wallet address. Validator public keys not specified in `proposer_config` will use this wallet address.


The above example demonstrates configuring two 1:1 mappings between `validator public key`:`fee_recipient` and a default `fee_recipient`. In this case, the `default_config` fee recipient address would apply to all validator public keys not specified in `proposer_config`, and will override any wallet address specified by the `--suggested-fee-recipient` flag.

Tell your validator to use the JSON/YAML configuration through one of the following flags: 

 - `proposer-settings-file`: Points to a local JSON/YAML file. 
 - `proposer-settings-url`: Points to a remote JSON/YAML configuration endpoint in URL format. JSON should be delivered as a JSON payload, not as a JSON file. Your client will issue a GET request and expects the response <code>Content-Type</code> header to be <code>application/json</code>



### Advanced: Configure MEV builder and gas limit

In the previous section, we reviewed a sample JSON/YAML file. The following file reveals additional properties that can optionally be included in order to configure MEV builder validator registration and gas limits:

<Tabs groupId="format" defaultValue="json" values={[
        {label: 'JSON', value: 'json'},
        {label: 'YAML', value: 'yaml'}
    ]}>
  <TabItem value="json">


```
{
  "proposer_config": {
    "<VALIDATOR PUBLIC KEY>": {
      "fee_recipient": "<WALLET ADDRESS>",
      "builder": {
        "enabled": true,
        "gas_limit": "30000000"
      }
    },
    "<VALIDATOR PUBLIC KEY>": {
      "fee_recipient": "<WALLET ADDRESS>",
      "builder": {
        "enabled": false,
        "gas_limit": "30000000"
      }
    }
  },
  "default_config": {
    "fee_recipient": "<WALLET ADDRESS>",
    "builder": {
      "enabled": true,
      "gas_limit": "30000000"
    }
  }
}
```


   </TabItem>
   <TabItem value="yaml">


```
---
proposer_config:
  '<VALIDATOR PUBLIC KEY>':
    fee_recipient: '<WALLET ADDRESS>'
    builder:
      enabled: true
      gas_limit: '30000000'
  '<VALIDATOR PUBLIC KEY>':
    fee_recipient: '<WALLET ADDRESS>'
    builder:
      enabled: true
      gas_limit: '30000000'
default_config:
  fee_recipient: '<WALLET ADDRESS>'
  builder:
      enabled: true
      gas_limit: '30000000'

```


   </TabItem>
</Tabs>


New property definitions are as follows:

 - `builder`: An object containing key-value pairs related to builder configuration. Applicable only when using custom block builders. If you don't run a builder, you can ignore this.
 - `enabled`: A boolean value that determines whether or not the MEV builder validator registration is enabled. Applicable only when using custom block builders. If you don't run a builder, you can ignore this. `false` by default.
 - `gas_limit`: A gas limit. Default limit is 30M gwei - `30000000`.




### Advanced: Configure fee recipient through the Web UI (Warning: feature to be deprecated)

<img style={{maxWidth: 700 + 'px'}} src={FeeRecipientPng} /> 

Your fee recipient wallet address can also be set through the <a href='../prysm-usage/web-interface'>Web UI</a> dashboard. The Web UI uses the <a href='../how-prysm-works/keymanager-api'>Key Manager APIs</a> to set the fee recipient.
 
:::warning Fee Recipient changes from UI/API don't persist on client restart

If you configure your fee recipient wallet address through the web UI or Keymanager APIs, your configuration **will not persist** if you restart your validator client.

See [this issue](https://github.com/prysmaticlabs/prysm/issues/11322) to track the status of configuration persistence, and use the `--proposer-settings-file` or `--proposer-settings-url` flags for persistent validator settings in the meantime.

:::


## Frequently asked questions

**How do I know if fee recipient was properly configured?** <br />
If you don't see any errors after issuing one of the above commands, your fee recipient address has been successfully configured.

**What happened to `fee-recipient-config-file`?** <br />
`fee-recipient-config-file` and `fee-recipient-config-url` flags are deprecated and have been replaced with `proposer-settings-file` and `proposer-settings-url` flags as of Prysm v2.1.3.

**How do I ensure that builders receive my fee recipient wallet address?** <br />
When `enable-builder` is set to `true` on your validator, you can use either the `--suggested-fee-recipient` flag or the JSON/YAML configuration method to communicate your fee recipient wallet address to builders.

**When should I set my own `gas_limit`, and how do I know what to set?** <br />
This is an advanced configuration property related to custom builders (MEV) that most users won't have to think about. In general, large gas limits will result in you not being able to include many transactions in a block, while using low values won't be as profitable.  See [https://github.com/ethereum/builder-specs/issues/17](https://github.com/ethereum/builder-specs/issues/17) for related discussion.



------------------

Footnotes:

<strong id="footnote-1">1.</strong> The <a href='https://github.com/ethereum/consensus-specs/blob/master/specs/bellatrix/validator.md#block-proposal'>Bellatrix -- Honest Validator spec</a> contains Fee Recipient implementation details pertaining to validator clients. The <a href='https://github.com/ethereum/consensus-specs/blob/master/specs/bellatrix/beacon-chain.md#executionpayload'>Bellatrix -- The Beacon Chain spec</a> contains Fee Recipient implementation details pertaining to beacon nodes. <br /><br />

<strong id="footnote-1">2.</strong> See <a href='../concepts/nodes-networks'>Nodes and Networks</a> for a quick refresher on the fundamentals of Ethereum nodes. <br />


import {RequestUpdateWidget} from '@site/src/components/RequestUpdateWidget.js';

<RequestUpdateWidget />
