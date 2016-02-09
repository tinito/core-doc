# Quick start

## Development environment

### Setup the SDK
    git clone https://github.com/r2p/r2p-sdk.git
    cd r2p-sdk
    git submodule init
    git submodule update

### Download the toolchain
Linux users can execute the following commands

    wget ftp.elet.polimi.it/users/Martino.Migliavacca/r2p/tools-linux-x86.tar.gz
    tar zxf tools-linux-x86.tar.gz
    
Windows users have to download the toolchain from [here](http://ftp.elet.polimi.it/users/Martino.Migliavacca/r2p/tools-windows-32bit.zip), and manually uncompress the content into the `r2p-sdk` directory (the result should be `r2p-sdk/tools/...`).

### Setup a demo workspace
    git clone https://github.com/r2p/workspace-demo.git workspace-demo

## Usage

### Launch the Eclipse-based IDE
    ./r2p-ide.sh
    
### Add support for 32bit executables on 64 bit machines

    sudo dpkg --add-architecture i386
    sudo apt-get install libc6:i386 libncurses5:i386