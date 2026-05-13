# LG webOS 2024 Ethernet Fix (RTL8152/8153)

This repository provides a pre-compiled kernel module for Realtek-based USB Gigabit Ethernet adapters (like the TP-Link UE300) on **2024 LG TVs**.

LG removed these drivers from the kernel in webOS 24 (Paparoa/K25lp boards). This fix restores Gigabit speeds for Moonlight, Sunshine, and high-bitrate streaming.

## Technical Specifications
*   **Kernel Version:** 5.4.268-294.17.paparoa.pine.2
*   **Architecture:** aarch64 (ARM64)
*   **Driver:** Realtek RTL8152 / RTL8153
*   **Confirmed Models:** 2024 QNED/OLED (K25lp / Paparoa SoC)

---

## Installation Guide

### 1. Upload the Driver
Download `r8152.ko` and send it to your TV:
```bash
scp r8152.ko root@YOUR_TV_IP:/home/root/
```

### 2. Test the Driver
SSH into your TV and run:
```bash
insmod /home/root/r8152.ko
ifconfig eth1 up
udhcpc -i eth1
```
Verify the connection with `ifconfig eth1`.

### 3. Auto-boot Script (Persistent)
Create the init script to load the driver on every boot:
```bash
cat << 'EOF' > /var/lib/webosbrew/init.d/99-usb-network
#!/bin/bash
# Load the driver
insmod /home/root/r8152.ko

# Optional: slight delay to ensure interface is ready
sleep 2

# Bring up the interface
ifconfig eth1 up
udhcpc -i eth1
EOF

chmod +x /var/lib/webosbrew/init.d/99-usb-network
```

## Disclaimer & Warning

Use this at your own risk. You are modifying kernel-level files on your TV. I am not responsible for any boot loops or system instability (though this driver is stable on my QNED86). Always make sure your kernel version matches before loading the module.

If you have a different 2024 model or a different chipset and want to try this, feel free to reach out! If the source code is available on LG's portal, I'm open to generating drivers for other TVs to help the community bypass this annoying limitation.

## License
MIT
