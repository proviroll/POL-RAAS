# Layer 2 CDK Blockchain Setup and Deployment Guide

This guide provides step-by-step instructions for setting up and deploying a Layer 2 blockchain using Kurtosis and other essential tools.

## Prerequisites

### Hardware Requirements
- **Operating System**: Linux-based OS (or WSL)
- **Minimum Requirements**: 8GB RAM, 2-core CPU
- **Architecture**: AMD64

### Software Requirements
- Kurtosis
- Foundry
- `yq`, `jq`
- Polygon CLI
- Go
- Python 3 & Pip

## Installation Steps

### 1. Kurtosis Installation

```bash
echo "deb [trusted=yes] https://apt.fury.io/kurtosis-tech/ /" | sudo tee /etc/apt/sources.list.d/kurtosis.list
sudo apt update
sudo apt install kurtosis-cli=1.0.0 -V
```

### 2. Install Required Tools

#### Foundry
```bash
curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```



#### Other Components
```bash
sudo apt install python3-pip
pip install yq==3.2.1
nano ~/.bashrc
export PATH="$HOME/.local/bin:$PATH"
source ~/.bashrc
```

#### Go Installation
```bash
wget https://go.dev/dl/go1.22.6.linux-amd64.tar.gz
sudo tar -C /usr/local/ -xzf go1.22.6.linux-amd64.tar.gz
nano ~/.bashrc
export PATH="$PATH:/usr/local/go/bin"
source ~/.bashrc
go version
```

### 3. Install Additional Dependencies
```bash
sudo apt install make
sudo apt install bc
sudo apt install -y protobuf-compiler
```
#### Polygon CLI
```bash
git clone https://github.com/0xPolygon/polygon-cli
cd polygon-cli
make install
```

## Setting Up the Kurtosis Environment

### 1. Clone the Repository
```bash
git clone https://github.com/0xPolygon/kurtosis-cdk.git
cd kurtosis-cdk
```
###  Validate Installation

After installing all dependencies, run the following script to check if all dependencies are correctly installed inside repo kurtosis-cdk :

```bash
./scripts/tool_check.sh
```

### 2. Create Wallet

Generate a new wallet mnemonic and inspect the wallet addresses:

```bash
cast wallet new-mnemonic
polycli wallet inspect --mnemonic "your seed phrase here" --addresses 9 |     jq -r '.Addresses[] | [.ETHAddress, .HexPrivateKey] | @tsv' |     awk 'BEGIN{split("sequencer,aggregator,claimtxmanager,timelock,admin,loadtest,agglayer,dac,proofsigner",roles,",")} {print "zkevm_l2_" roles[NR] "_address: "" $1 """; print "zkevm_l2_" roles[NR] "_private_key: "0x" $2 ""
"}'
```

Add the generated addresses and private keys to the `params.yml` file. The `params.yml` file contains important configurations like the chain ID and gas token smart contract address.

### 3. Node Configuration (`node-config.toml`)
Ensure the following parameters are correctly set in your `node-config.toml` file:

```toml
BatchMaxDeltaTimestamp = "15m"
LastBatchVirtualizationTimeMaxWaitPeriod = "15m"
MaxBatchesForL1 = 15
```

## Running the Chain Locally

### 1. Clean Existing Kurtosis Environments
Before deploying, clean up any existing Kurtosis environments:

```bash
kurtosis clean --all
```

### 2. Deploy the Chain

To deploy the blockchain on your local machine, run the following command from the `kurtosis-cdk` directory:

```bash
kurtosis run --enclave cdk-v1 --args-file params.yml --image-download always .
```

## Inspecting the Chain

To check the status of the Kurtosis enclave and the running services:

```bash
kurtosis enclave inspect cdk-v1
```

## References

- [Kurtosis CLI Installation](https://docs.kurtosis.com/install/#ii-install-the-cli)
- [Foundry Installation Guide](https://book.getfoundry.sh/getting-started/installation)
- [Go Installation](https://go.dev/dl/)
- [Polygon CDK Local Deployment](https://docs.polygon.technology/cdk/getting-started/local-deployment/#load-testing-the-chain)
- [Polygon CDK Setup Video](https://www.youtube.com/watch?v=6ykNLEhwxIs&t=6115s)
