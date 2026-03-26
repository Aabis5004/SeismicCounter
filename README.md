Deploy contract on seismic testnet
this guide works on WSL 2 (Ubuntu on Windows) or any Ubuntu 20.04+ machine. You don't need any prior blockchain experience just a working terminal

# Install system packages first
 
```sudo apt update
 sudo apt install -y curl git build-essential pkg-config libssl-dev gcc make
```
  Install Rust & Cargo
 The Seismic toolchain is written in Rust this takes 2t to 5 minutes to download
 
 ```bash
curl https://sh.rustup.rs -sSf | sh
```
Reload your shell so Rust commands work
```bash
source $HOME/.cargo/env
```
Verify Rust installed correctly
```bash
rustc --version
cargo --version
```
# Install Seismic Foundry
This installs sforge, sanvil, and ssolc expect 5–20 minutes
```bash
curl -L \
  -H "Accept: application/vnd.github.v3.raw" \
  "https://api.github.com/repos/SeismicSystems/seismic-foundry/contents/sfoundryup/install?ref=seismic" | bash
```
 Reload your terminal
```bash
source ~/.bashrc
```
 Run sfoundryup to install all tools (this is the slow step)
```bash
sfoundryup
```
 Reload terminal again, then verify
terminal
```bash
source ~/.bashrc   # or ~/.zshrc
sforge --version
```
Create and enter the project folder
terminal
```bash
sforge init SeismicCounter
cd SeismicCounter
```
Run a quick test to confirm everything works
terminal
```bash
sforge test
```
Open the contract file in a text editor
```bash
nano src/Counter.sol
```
# Replace the entire file with this contract
Delete everything in the file and paste this:
```bash
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract SeismicCounter {

    // Shielded value
    suint256 number;

    event NumberSet();
    event Incremented();

    function setNumber(suint256 newNumber) public {
        number = newNumber;
        emit NumberSet();
    }

    function increment() public {
        // use shielded literal
        number = number + suint256(1);
        emit Incremented();
    }
}
```
Save: CTRL + X → Y → ENTER
# write deploy script
```bash
nano script/Counter.s.sol
```
 Replace the entire file with this contract
Delete everything in the file and paste this:
```bash
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import {Script} from "forge-std/Script.sol";
import {SeismicCounter} from "../src/Counter.sol";

contract CounterScript is Script {
    function run() external {
        vm.startBroadcast();

        SeismicCounter counter = new SeismicCounter();

        vm.stopBroadcast();
    }
}
```
Build Contract
```bash
sforge build
```
Generate a fresh wallet save the output somewhere safe like save your PV KEY AND ADDRESS
```bash
scast wallet new
```
# Set environment variables
```bash
# Replace the value with YOUR private key from step
export PRIVATE_KEY="0xYourPrivateKeyHere"

# Seismic testnet RPC URL don't change this
export RPC_URL="https://gcp-1.seismictest.net/rpc"
```
get the faucet from here
 fund your wallet
 https://community-faucet.seismictest.net/
 
check bal
```bash
scast balance \
$(scast wallet address --private-key $PRIVATE_KEY) \
--rpc-url $RPC_URL
```
# Deploy to the Seismic testne
```bash
sforge script script/Counter.s.sol \
--rpc-url $RPC_URL \
--broadcast \
--private-key $PRIVATE_KEY
```
Copy the contract address from the output. Save it you'll need it for the next commands
```bash
# Replace with YOUR deployed address from the output above
export CONTRACT="0xYourContractAddressHere"
```
verify it
```bash
echo $CONTRACT
```
You should see:

0xD3E51E37f67fF4386D355CC7aD334D7003b2FA7C
