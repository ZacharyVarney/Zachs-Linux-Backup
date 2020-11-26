# Zachs Linux Backup
Backed up files of things I use & configs I have set for my GNU/Linux experience


## Single GPU Passthrough
We all know Linux gaming is getting better & better, but there are still occasions where you need to play a game on Windows for whatever reason, mine ends up being anti-cheat-related reasons. If you're like me you hate having to run that OS natively, so I KVM virtualize it with single GPU passthrough. Here are the steps I take to get it running on my current machine, "specs listed below". Hope this helps you as it has me.

### Current PC Specs
CPU: AMD Ryzen 7 1700 (16) @ 3.00GHz  
GPU: MSI Gaming X 4G Geforce GTX 1050Ti  
Motherboard: ASRock B450M Pro4  
RAM: Crucial Ballistix (2x8GB)16GB DDR4 3000MHz  
PSU: EVGA B1 Bronze 500W  
Storage: 2 7200RPM 300GB Hard drives  
Case: DIYPC D480W

### Requirments
- A motherboard capable of IOMMU
- A motherboard with sane IOMMU group separation("NOT NECISARALLY A REQUIREMENT BUT YOU WANT THAT")
- A dedicated GPU obviously

### Getting Started
#### 1. Install the necessary virtualization packages for your distribution

**I ONLY USE ARCH**  
So you will have to do a bit of research to follow the differences on fedora or a debian based distro.

```
sudo pacman -S qemu libvirt ovmf virt-manager ebtables iptables dnsmasq --noconfirm
```

#### 2. Setting up the services to run & auto start with your PC

```
sudo systemctl enable libvirtd.service  
sudo systemctl start libvirtd.service  
sudo systemctl enable virtlogd.socket  
sudo systemctl start virtlogd.socket  
sudo sudo systemctl restart libvirtd  
sudo virsh net-start default  
sudo virsh net-autostart default
```

#### 3. Getting IOMMU ready

To enable IOMMU you will need to add some parameters to your grub config file located at /etc/default/grub  

Find the line towards the top titled GRUB_CMDLINE_LINUX_DEFAULT & add amd_iommu=on iommu=pt or intel_iommu=on iommu=pt depending on what CPU you have, for example for me it would be
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 amd_iommu=on iommu=pt video=efifb:off"
```

After saving the changes to your grub config file we need to regenerate it by running the following command
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

