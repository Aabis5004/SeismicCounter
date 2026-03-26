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
