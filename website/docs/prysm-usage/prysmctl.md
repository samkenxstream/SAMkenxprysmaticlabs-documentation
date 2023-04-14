---
id: prysmctl
title: Use prysmctl
sidebar_label: Use prysmctl
---

import {HeaderBadgesWidget} from '@site/src/components/HeaderBadgesWidget.js';

<HeaderBadgesWidget commaDelimitedContributors="James" lastVerifiedDateString="March 20th, 2023" lastVerifiedVersionString="v4.0.0"/>

`prysmctl` is a command-line utility for common and one-off Ethereum proof-of-stake tasks, like helping users with validator exits or withdrawals. Most `prysmctl` commands require access to a fully synced beacon node.


### Run via binaries

Binaries for the latest `prysmctl` tool can be found on the [latest prysm release page](https://github.com/prysmaticlabs/prysm/releases). Each binary is a unique version with its own set of features. New releases may include new features for `prysmctl` that will need to be downloaded separately. The installed binaries will need to be renamed to `prysmctl` to use the example below. 

:::info

Some users will need to give permissions to the the downloaded binaries to be executable. Linux users can do this by right clicking the file, going to permissions, and clicking the `Allow executing file as program` checkmark. This may be different for each operating system.

:::

Example of running prysmctl by opening a terminal at the installed location after renaming.
```
./prysmctl --help
```

The binaries can be run through a terminal directly and won't need installation. Please refer to the **list commands** section for additional information. 

### Install via source

Dependencies:

- [Bazelisk](https://bazel.build/install/bazelisk) - this will automatically manage the version of **Bazel** required.
- [golang](https://go.dev/) installed
- The `cmake` package installed
- The `git` package installed
- `libssl-dev` installed
- `libgmp-dev` installed
- `libtinfo5` installed
- `libprotoc` version 3.14 installed

#### Install Bazelisk

:::caution
    Windows users should run through binaries as some users may have issues building through bazel. 
:::

Bazelisk is a launcher for Bazel which automatically downloads and installs the version of Bazel that you need. There are several ways to install Bazelisk:

- Using [a binary release](https://github.com/bazelbuild/bazelisk/releases) for Linux, macOS, or Windows
- Using npm: `npm install -g @bazel/bazelisk`
- Using Homebrew on macOS: `brew install bazelisk`
- By compiling from source using Go: `go install github.com/bazelbuild/bazelisk@latest`

#### Clone the Prysm project locally

Clone Prysm's [main repository](https://github.com/prysmaticlabs/prysm). Switch to the latest version (the latest version number can be found on the [releases page](https://github.com/prysmaticlabs/prysm/releases)). Once cloned, enter the directory:

`git clone https://github.com/prysmaticlabs/prysm && cd prysm`

#### Build `prysmctl`

`bazel build //cmd/prysmctl --config=release`

Bazel will automatically pull and install any dependencies as well, including Go and necessary compilers.

### List commands

```
./prysmctl --help
```

**Using Docker**
```
docker run -it gcr.io/prysmaticlabs/prysm/cmd/prysmctl:latest --help
```

The `—help` flag will provide a list of commands, subcommands, and flags to use.

Commands can also be found in our [Prysm parameter documentation](https://docs.prylabs.network/docs/prysm-usage/parameters)

### Frequently asked questions

**Q: One of the Prysm guides tells me to use a `prysmctl` command that isn't available. What do I do?**

A: You may be using an older version of `prysmctl` and are required to download a newer version. 

**Q: Is `prysmctl` accessible from prysm.sh, prysm.s1, or prysm.bat?**

A: No. This utility will only be accessible by building from source or by downloading binaries for specific versions of Prysm.

import {RequestUpdateWidget} from '@site/src/components/RequestUpdateWidget.js';

<RequestUpdateWidget />