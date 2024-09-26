# Setup (Hetzner)
## v1 (prefered)
- boot rescue
- download iso
  - wget -O pve.iso https://enterprise.proxmox.com/iso/proxmox-ve_8.2-1.iso
### notes from rescue system
- ipv4, mask in num format, gateway
- disk names (e.g. nvme0n1)
- network alias name from primäre devices, if multiple with MAC address (e.g. enp5s0f0 ) (cmd: ip a)
- bios / uefi (uefi if /sys/firmware/efi exists)
### install
- create qemu vm with physical disks and iso with following params
  -bios /usr/share/ovmf/OVMF.fd  // (for uefi only)
  -enable-kvm // enable hypervisor
  -smp {cores} // define vcores. get a warning if grather then physical core, but works
  -m {ram in MB} // define ram
  -k en-us // keyboard layout (only en-us available)
  -boot d -cdrom ./pve.iso // boot from iso
  -drive file=/dev/{device},format=raw,media=disk,if=virtio // add disk (multiple in order)
  -vnc :0 // PUBLIC vnc access default port
  or 
  -vnc 127.0.0.1:1 // via ssh tunnel only
- example for bios
  - qemu-system-x86_64 -enable-kvm -smp 48 -m 14096 -k en-us -boot d -cdrom ./pve.iso -drive file=/dev/nvme0n1,format=raw,media=disk,if=virtio -drive file=/dev/nvme1n1,format=raw,media=disk,if=virtio -vnc :0
- example for uefi
  - qemu-system-x86_64 -bios /usr/share/ovmf/OVMF.fd -enable-kvm -smp 48 -m 14096 -k en-us -boot d -cdrom ./pve.iso -drive file=/dev/nvme0n1,format=raw,media=disk,if=virtio -drive file=/dev/nvme1n1,format=raw,media=disk,if=virtio -vnc :0

- connect via vnc to vm
- start installer from iso (e.g. graphics)
- the installer complains about missig vt-support can be ignored
- choose disk with zfs1 (RAID1)
- define password // note you have an english keyboard layout
- define (a valid) email-address
- choose network device if necessary
- define hostname
- define ip etc. // dns 1.1.1.1 inital to ensure inet access
- install with auto reboot
- if vm was automatic restarted, stop vm

- create qemu vm again but without boot from iso part "-boot d -cdrom ./pve.iso"
- example for bios
  - qemu-system-x86_64 -enable-kvm -smp 48 -m 14096 -k en-us -drive file=/dev/nvme0n1,format=raw,media=disk,if=virtio -drive file=/dev/nvme1n1,format=raw,media=disk,if=virtio -vnc :0
- example for uefi
  - qemu-system-x86_64 -bios /usr/share/ovmf/OVMF.fd -enable-kvm -smp 48 -m 14096 -k en-us -drive file=/dev/nvme0n1,format=raw,media=disk,if=virtio -drive file=/dev/nvme1n1,format=raw,media=disk,if=virtio -vnc :0

- connect again via vnc to vm
- login
- replace ALL network devices from vm to physicals in /etc/network/interfaces
  - e.g. eno3 to enp5s0f0
- nothing more. vlan etc. comes later
- 
