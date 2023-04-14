---
id: install-with-bazel
title: Build Prysm from source
sidebar_label: Build from source
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import {HeaderBadgesWidget} from '@site/src/components/HeaderBadgesWidget.js';

<HeaderBadgesWidget />

Prysm can be installed on GNU/Linux, MacOS, and Arm64 using our build tool, [Bazel](https://bazel.build). This page includes instructions for performing this method.

:::tip Not familiar with Bazel? Try our quickstart

This guidance is targeted at users who are already comfortable with Bazel and staking. See our [Quickstart](/docs/install/install-with-script) for beginner-friendly installation instructions.

:::


## Why Bazel?

Instead of using the `Go` tool to build Prysm, our team relies on the [Bazel](https://bazel.build) build system used by major companies to manage monorepositories. Bazel provides reproducible builds and a sandboxed environment that ensures everyone building Prysm has the same experience and can build our entire project from a single command. For more detailed rationale on why Bazel, how it works in Prysm, and all important information about how exactly building from source works, read our rationale [here](/docs/reading/bazel).


<div class='bazel-guide'>

## Review system requirements

<table>
    <tr>
        <th>Minimum</th>
        <th>Recommended</th>
    </tr>
    <tr>
      <td>
        <ul> 
          <li><strong>OS</strong>: 64-bit Linux, Mac OS X 10.14+, Windows 64-bit</li> 
          <li><strong>CPU</strong>: Intel Core i5–760 or AMD FX-8100 or better</li> 
          <li><strong>Memory</strong>: 8GB RAM</li> 
          <li><strong>Storage</strong>: SSD with 20GB+ available</li> 
          <li><strong>Internet</strong>: Broadband connection</li> 
          <li><strong>Software</strong>: The latest release of <a href='https://docs.docker.com/install/'>Docker</a> installed.</li> 
        </ul> 
      </td>
      <td>
        <ul> 
          <li><strong>CPU</strong>: Intel Core i7–4770 or AMD FX-8310 or better</li> 
          <li><strong>Memory</strong>: 16GB RAM</li> 
          <li><strong>Storage</strong>: SSD with 100GB+ available</li> 
        </ul> 
      </td>
    </tr> 
</table>

### Dependencies

* [Bazelisk](https://bazel.build/install/bazelisk) this will automatically manage the version of ***Bazel*** required. 
* The `cmake` package installed
* The `git` package installed
* `libssl-dev` installed
* `libgmp-dev` installed
* `libtinfo5` installed
* `libprotoc` version 3.14 installed



## Install Bazel using Bazelisk

Bazelisk is a launcher for Bazel which automatically downloads and installs an appropriate version of Bazel. Use Bazelisk to automatically manage the version of Bazel required.  

You can install Bazelisk in multiple ways, including:

* Using [a binary release](https://github.com/bazelbuild/bazelisk/releases) for Linux, macOS, or Windows
* Using npm: `npm install -g @bazel/bazelisk`
* Using Homebrew on macOS: `brew install bazelisk`
* By compiling from source using Go: `go install github.com/bazelbuild/bazelisk@latest`


## Install Prysm using Bazel

Clone Prysm's [main repository](https://github.com/prysmaticlabs/prysm). Make sure you switch to the latest version (the latest version number can be found from the [releases page](https://github.com/prysmaticlabs/prysm/releases)). Once cloned, enter the directory:

```text
git clone https://github.com/prysmaticlabs/prysm
cd prysm
git checkout <version>
```

Build both the beacon chain node and the validator client:

```text
bazel build //cmd/beacon-chain:beacon-chain --config=release
bazel build //cmd/validator:validator --config=release
```

Bazel will automatically pull and install any dependencies as well, including Go and necessary compilers.


## Run an execution node

:::info Knowledge Check

**Not familiar with nodes, networks, and related terminology?** Consider reading [Nodes and networks](../concepts/nodes-networks.md) before proceeding. 

:::

To run a beacon node, you'll need access to an execution node. See [Configure execution node](/docs/execution-node/configuring-for-prysm) for detailed instructions if you don't already have an execution node configured.




## Run a beacon node

Note: `<YOUR_ETH_EXECUTION_NODE_ENDPOINT>` is in the format of an http endpoint such as `http://host:port` (ex: `http://localhost:8545` for geth) or an IPC path such as `/path/to/geth.ipc`.

import MultidimensionalContentControlsPartial from '@site/docs/partials/_multidimensional-content-controls-partial.md';

<MultidimensionalContentControlsPartial />

<div class='hide-tabs'>

<Tabs groupId="network" defaultValue="mainnet" values={[
        {label: 'Mainnet', value: 'mainnet'},
        {label: 'Goerli-Prater', value: 'goerli-prater'},
        {label: 'Sepolia', value: 'sepolia'}
    ]}>
      <TabItem value="mainnet">

```text
bazel run //beacon-chain --config=release -- --execution-endpoint=<YOUR_ETH_EXECUTION_NODE_ENDPOINT>
```

  </TabItem>
      <TabItem value="goerli-prater">

Download the Goerli-Prater genesis state from [Github](https://github.com/eth-clients/eth2-networks/raw/master/shared/prater/genesis.ssz) to a local file. Then issue the following command:

```text
bazel run //beacon-chain --config=release -- --execution-endpoint=<YOUR_ETH_EXECUTION_NODE_ENDPOINT> --prater --genesis-state=/path/to/genesis.ssz
```

  </TabItem>
      <TabItem value="sepolia">

Download the Sepolia genesis state from [Github](https://github.com/eth-clients/merge-testnets/blob/main/sepolia/genesis.ssz) to a local file, then run

```text
bazel run //beacon-chain --config=release -- --execution-endpoint=<YOUR_ETH_EXECUTION_NODE_ENDPOINT> --sepolia --genesis-state=/path/to/genesis.ssz
```


  </TabItem>
</Tabs>

</div>

## Run a validator

Ensure that your beacon node is fully synced before proceeding. See [Check node and validator status](../monitoring/checking-status.md) for detailed status-checking instructions.

Navigate to the [Mainnet Launchpad](https://launchpad.ethereum.org/summary) and follow the instructions. If you want to participate in the **testnet**, you can navigate to the [Goerli-Prater](https://goerli.launchpad.ethereum.org/summary/).

:::danger Exercise extreme caution

The correct address for the launchpad is https://launchpad.ethereum.org and the only, official validator deposit contract is [0x00000000219ab540356cbb839cbe05303d7705fa](https://etherscan.io/address/0x00000000219ab540356cbb839cbe05303d7705fa). Don't send ETH directly to the contract - use the Ethereum.org launchpad instead.

:::

Throughout the process, you'll be asked to generate new validator credentials using the official Ethereum deposit command-line-tool [here](https://github.com/ethereum/eth2.0-deposit-cli). Make sure you use the `mainnet` option when generating keys with the deposit CLI. During the process, you will have generated a `validator_keys` folder under the `eth2.0-deposit-cli` directory. Copy the path to the `validator_keys` folder under the `eth2.0-deposit-cli` directory you created during the launchpad process. For example, if your `eth2.0-deposit-cli` installation is in your `$HOME` (or `%LOCALAPPDATA%` on Windows) directory, you can then run the following command to import your keys:

```text
bazel run //validator:validator -- accounts import --keys-dir=$HOME/eth2.0-deposit-cli/validator_keys --accept-terms-of-use
```

Next, open a second terminal window and issue the followimg command to start your validator.

```text
bazel run //validator --config=release
```


## Wait for your validator assignment

Please note it will take time for nodes in the network to process a deposit. To understand the timeline of becoming a validator and how long it takes on average, see [this knowledge base](https://kb.beaconcha.in/ethereum-2.0-depositing). In the meantime, leave both terminal windows open and running; once the validator is activated by the ETH2 network, it will immediately begin receiving tasks and performing its responsibilities. If the eth2 chain has not yet started, the validator will be ready to start proposing blocks and signing votes as soon as the genesis time is reached.

To check on the status of your validator, we recommend checking out the popular block explorers: [beaconcha.in](https://beaconcha.in) by Bitfly and [beacon.etherscan.io](https://beacon.etherscan.io) by the Etherscan team.

![image](https://i.imgur.com/CDNc6Ft.png)

</div>



## Advanced: Build Docker images from source

We use Bazel to build the Docker images for Prysm as well. This section outlines comprehensive instructions on how to build them by yourself, run them in Docker, and push to an image registry if desired. In particular, we use [`bazel rules docker`](https://github.com/bazelbuild/rules_docker) which provides us the ability to specify a base, barebones image, and essentially builds our binary and creates a Docker container as a simple wrapper over our binaries.

We do not write our own Dockerfiles, as Bazel provides us a more sandboxed, simple experience with all of its benefits. To see an example use of `bazel rules docker` for how we build a particular package, see [here](https://github.com/prysmaticlabs/prysm/blob/aa389c82a157008741450ba1e04d898924734432/tools/bootnode/BUILD.bazel#L36). 

### Dependencies needed

* All specified dependencies for building with Bazel [here](/docs/install/install-with-bazel#dependencies)
* Python installed and available in your computer

### Build process

#### Regular Docker images

At the moment, Prysm docker images can only be built on **Linux** operating systems. The standard images are very thin wrappers around the Prysm beacon-chain and validator binaries, and do not contain any other typical components of Docker images such as a bash shell. These are the Docker images we ship to all users, and you can build them yourself as follows:

```bash
bazel build //beacon-chain:image_bundle --config=release
bazel build //validator:image_bundle --config=release
```

The tags for the images are specified [here](https://github.com/prysmaticlabs/prysm/blob/ff329df808ad68fbe79d11c73121fa6a7a0c0f29/cmd/beacon-chain/BUILD.bazel#L58) for the beacon-chain and [here](https://github.com/prysmaticlabs/prysm/blob/ff329df808ad68fbe79d11c73121fa6a7a0c0f29/cmd/validator/BUILD.bazel#L59) for the validator. The default image tags for these images are:


<!-- todo: RC links to gcr.io -->

```text
gcr.io/prysmaticlabs/prysm/beacon-chain:latest
gcr.io/prysmaticlabs/prysm/validator:latest
```

You can edit these in the links above to your liking.

#### Alpine images

Prysm also provides Alpine images built using:

```bash
bazel build //beacon-chain:image_bundle_alpine --config=release
bazel build //validator:image_bundle_alpine --config=release
```

#### Debug images

Prysm also provides debug images built using:

```bash
bazel build //beacon-chain:image_bundle_debug --config=release
bazel build //validator:image_bundle_debug --config=release
```

### Running images

You can load the images into your local Docker daemon by first building a `.tar` file as follows for your desired image bundle:

```text
bazel build cmd/beacon-chain:image_bundle.tar
bazel build cmd/validator:image_bundle.tar
```

Then, you can load it into Docker with:
```text
docker load -i bazel-bin/my/image/helloworld.tar
```

For example, you may see output such as this:

```
docker load -i bazel-bin/cmd/beacon-chain/image_bundle.tar
fd6fa224ea91: Loading layer [==================================================>]  3.031MB/3.031MB
87c9f1582ca0: Loading layer [==================================================>]  15.44MB/15.44MB
231bdbae9aea: Loading layer [==================================================>]  1.966MB/1.966MB
a6dc470c72b7: Loading layer [==================================================>]  10.24kB/10.24kB
a0de9c673ef6: Loading layer [==================================================>]  56.37MB/56.37MB
84ff92691f90: Loading layer [==================================================>]  10.24kB/10.24kB
Loaded image: gcr.io/prysmaticlabs/prysm/beacon-chain:latest
Loaded image: prysmaticlabs/prysm-beacon-chain:latest
```

### Pushing to a container registry

#### Authentication

You can use these rules to access private images using standard Docker
authentication methods.  e.g. to utilize the [Google Container Registry](
https://gcr.io). See
[here](https://cloud.google.com/container-registry/docs/advanced-authentication) for authentication methods.

See:
* [Amazon ECR Docker Credential Helper](
  https://github.com/awslabs/amazon-ecr-credential-helper)
* [Azure Docker Credential Helper](
  https://github.com/Azure/acr-docker-credential-helper)

#### Push

To push the actual images, you do not need to build the image bundle beforehand. You can do a simple:

```text
bazel run //beacon-chain:push_images --config=release
bazel run //validator:push_images --config=release
```

Which will deploy all images with the tags specified in [here](https://github.com/prysmaticlabs/prysm/blob/ff329df808ad68fbe79d11c73121fa6a7a0c0f29/cmd/beacon-chain/BUILD.bazel#L58) for the beacon-chain and [here](https://github.com/prysmaticlabs/prysm/blob/ff329df808ad68fbe79d11c73121fa6a7a0c0f29/cmd/validator/BUILD.bazel#L59) for the validator. 

By default, this will deploy to Prysmatic Labs' Google Container Registry namespace: `gcr.io/prysmaticlabs/prysm`, which you will not have authentication access to, so make sure you edit the image tags to your appropriate registry and authenticate as needed.


import {RequestUpdateWidget} from '@site/src/components/RequestUpdateWidget.js';

<RequestUpdateWidget />
