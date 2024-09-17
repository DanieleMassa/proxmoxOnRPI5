# Proxmox on Raspberry Pi 5

Step-by-step guide to install a proxmox port on the rpi5
----------

#### This has been written after my troubleshooting, so the only source I can mention is [this](https://github.com/pimox/pimox7).


Prerequisites
---------
- Raspberry Pi 5
- An SD card
- A computer with internet connection
- An ethernet cable for the pi

Step 1 - Preparation
--------

Flash the original Raspberry Pi OS 64BIT Lite version on an SD card. **Make sure to bind also the Wi-Fi network** during the installation otherwise you may face network issues during the installation. Set the hostname and write it down somewhere.
When the SD is ready plug it into the SD card reader, plug the ethernet cable, turn it on and connect to it. Connecting to it via HDMI and keyboard is fine; but if you can't, remember not to use the ethernet IP address but rather the Wi-Fi one.

Step 2 - Proxmox installation
--------

Run the following commands:
1. `sudo -s`
2. `apt update`
3. `apt upgrade`
4. `curl https://mirrors.apqa.cn/proxmox/debian/pveport.gpg -o
/etc/apt/trusted.gpg.d/pveport.gpg` if this results in an error, remove the `https` and try with `http`
5. `echo "deb https://mirrors.apqa.cn/proxmox/debian/pve bookworm port" | tee -a
/etc/apt/sources.list` here as well
6. `apt update`
7. `apt dist-upgrade -y`
8. `nano /etc/network/interfaces`
9. inside write:
```
auto lo
iface lo inet loopback

iface eth0 inet manual

auto vmbr0
iface vmbr0 inet static
    address 192.168.1.100/24
    gateway 192.168.0.1
    bridge-ports eth0
    bridge-stp off
    bridge-fd 0
```
In the address write an ip for proxmox (choose one that is unused on your local network) and the netmask in the CIDR notation.
In the gateway put the router ip

10. `nano /etc/hosts`
11. inside write:
```
127.0.0.1   localhost
192.168.1.100 raspberrypi
```
Change the second ip address with the one you've set in the `/etc/network/interfaces` file and `raspberrypi` with the hostname you've written down during the installation (or use `hostname` to find it)

12. `reboot now`
13. `sudo -s`
14. `apt install pve-qemu-kvm proxmox-ve` (if you face any issue, try insalling `pve-qemu-kvm` first and then `proxmox-ve`)
15. `reboot now`
16. `sudo -s`
17. `curl https://raw.githubusercontent.com/pimox/pimox7/master/RPiOS64-IA-Install.sh > installer.sh`
18. `chmod +x installer.sh`
19. `./installer.sh`
20. after having followed all the prompts and rebooted, run:
21. `sudo -s`
22. `apt upgrade -y`
23. `reboot now`
24. now open the ip address specified in the `/etc/network/interfaces` file, followed by the port 8006. In my case: `https://192.168.1.100:8006`
25. Proxmox has successfully been installed and is redy to use

