# NVMeDirect

NVMeDirect is a user-space I/O framework for NVMe SSDs, which allows user
applications to access the storage device directly. This will enable various
application-specific optimizations on NVMe SSDs.
For further details on the design and implementation of NVMeDirect, please
refer to the following [paper](https://www.usenix.org/conference/hotstorage16/workshop-program/presentation/kim).

- Hyeong-Jun Kim, Young-Sik Lee, and Jin-Soo Kim, "NVMeDirect: A user-space I/O Framework for Application-specific Optimization on NVMe SSDs,"
Proceedings of the 8th USENIX Workshop on Hot Topics in Storage and File
Systems (HotStorage), Denver, Colorado, USA, June 2016.


## How to build and run

### Create a VM of Ubuntu 16.04

Download 64-bit Ubuntu 16.04 and install virtualbox
```
wget https://releases.ubuntu.com/16.04/ubuntu-16.04.7-server-amd64.iso
sudo apt-get install virtualbox virtualbox-ext-pack
```

1. Open VirtualBox  
2. Install Ubuntu16.04  
3. When done, navigate to File -> Host Network Manager at the top-left of VirtualBox  
4. Add a new Host Adapter
    - Remember the IPv4 Address
    - In my case, it's 192.168.56.1
5. Close out of this menu and go to Settings -> Network  
6. For Adapter 1, go to Advanced -> Port Forwarding  
    - The host port can be anything unused; I use 4632  
    - The guest port should be 22  
7. Go to Adapter 2, set Attached to: Host-only Adapter  

### Connect to VM through SSH
#### Install OpenSSH on the VM
```
sudo apt-get install git openssh-server make gcc
sudo nano /etc/ssh/sshd_config
#Set PermitEmptyPasswords to yes (on line 45)
sudo service sshd restart
```

#### Setup passwordless authentication to the VM on the host machine
1. ssh-keygen  
2. ssh-copy-id -f -i ~/.ssh/id_rsa -p 4632 lukemartinlogan@192.168.56.1  
3. chmod 600 ~/.ssh/authorized_keys  
4. ssh -p 4632 lukemartinlogan@192.168.56.1  

### Install Kernel 4.3.3 Headers

For 64-bit systems (use this):
```
mkdir kern
cd kern
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-headers-4.3.3-040303_4.3.3-040303.201512150130_all.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-headers-4.3.3-040303-generic_4.3.3-040303.201512150130_amd64.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-image-4.3.3-040303-generic_4.3.3-040303.201512150130_amd64.deb
sudo dpkg -i *.deb
```

For 32-bit systems (included for completeness, don't use):
```
mkdir kern
cd kern
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-headers-4.3.3-040303_4.3.3-040303.201512150130_all.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-headers-4.3.3-040303-generic_4.3.3-040303.201512150130_i386.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v4.3.3-wily/linux-image-4.3.3-040303-generic_4.3.3-040303.201512150130_i386.deb
sudo dpkg -i *.deb
```

### Build NVMeDirect

```
git clone https://github.com/lukemartinlogan/nvmedirect.git
cd nvmedirect
mkdir build
cd build
cmake ../
make
make build-module
```

### Playing with NVMeDirect

To install/remove the kernel module:
```
make install-module
make remove-module
```

To print and clear the kernel log (could be useful for debugging):
```
make print-klog
make clear-klog
```

Modify the CMakeLists.txt in order to build a program that uses the NVMeDirect API.
A template is given to you at the bottom of the CMake.

## How to use NVMeDirect APIs


Please refer to the [NVMeDirect-APIs.md](https://github.com/nvmedirect/nvmedirect/blob/master/NVMeDirect-APIs.md) file.
## Contacts

The NVMeDirect framework is being currently maintained by [Computer Systems
Laboratory](http://csl.skku.edu) in Sungkyunkwan University, South Korea.
NVMeDirect is an on-going work and we welcome your contribution and feedback.
If you have any questions or suggestions, please contact the following people.

- Hyeong-Jun Kim (hjkim@csl.skku.edu)

- Jin-Soo Kim (jinsookim@skku.edu)
