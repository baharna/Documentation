### Installing VMWare Workstation 17.5.2 on Ubuntu 24.04
Modified install procedure due to changes in kernel 6.9
1. Ensure gcc is installed
2. Download software from https://softwareupdate.vmware.com/cds/vmw-desktop/ws/17.5.2/23775571/linux/core/VMware-Workstation-17.5.2-23775571.x86_64.bundle.tar
3. Untar and run the file:
```
tar xvf Downloads/VMWare-Workstation-17.5.2-23775571.x86_64.bundle.tar -C ~
sudo ./VMWare-Workstation-17.5.2-23775571.x86_64.bundle
```
4. Run the script to modify the headers and compile the modules for VMWare
```
git clone -b tmp/workstation-17.5.2-k6.9.1 https://github.com/nan0desu/vmware-host-modules.git && cd vmware-host-modules/ && sudo make tarballs && sudo cp -v vmmon.tar vmnet.tar /usr/lib/vmware/modules/source/ && sudo vmware-modconfig --console --install-all
```
5. Sign the modules for vmmon and vmnet. You will be prompted to enter a password for the signing
```
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=VMWare"
sudo /usr/src/linux-headers-`uname -r`/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(modinfo -n vmmon)
sudo /usr/src/linux-headers-`uname -r`/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(modinfo -n vmnet)
sudo mokutil --import MOK.der
```
6. Reboot the computer
```
sudo shutdown -r now
```
7. On reboot the computer will come up to the "Enroll MOK" screen. Continue through and enroll the keys, entering the password generated at signing when prompted.
8. Open VMWare and go through the wizard dialog.
9. Open VMware, it should work. 


### References
- https://knowledge.broadcom.com/external/article?legacyId=2146460
- https://github.com/mkubecek/vmware-host-modules/issues/250
