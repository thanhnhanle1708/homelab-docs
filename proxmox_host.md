# Proxmox Host Configuration

## Hardware
- CPU: Intel i5-12400 (non-F)
- Motherboard: MSI PRO B760M-P DDR4
- RAM: 32GB DDR4-3200 CL16 (2x16GB DIMM)
- Storage: 1x SSD 120GB, 2x HDD 500GB
- GPU: RTX 2080 Ti (Passthrough to VM103)

## BIOS & Boot
- Secure Boot: **Disabled**
- GPU Passthrough:
  - `hostpci0: 0000:01:00` (SeaBIOS, machine: pc)
  - No `pcie=1` or `x-vga=1` required

## Network
- Tailscale IP: `100.127.187.70` (router for subnet `192.168.1.0/24`)
- Gateway: `192.168.1.1`
- Power Monitor:
  - Script: `/usr/local/bin/power-monitor.sh`
  - Monitors Smart Plug at `192.168.1.3`
  - Checks internet gateway `192.168.1.1` and DNS `8.8.8.8` before shutdown

## Tailscale Service
- Service: `tailscale-up.service`
- Config:
  ```bash
execStart: tailscale up --advertise-routes=192.168.1.0/24 --accept-routes --accept-dns=false
  ExecStartPost: iptables rules to fix NAS access over Tailscale
```

## WiFi AP
- Two USB WiFi cards:
  - `wlx74dada5ff54b` (card1, ch6)
  - `wlx74dada1c09cd` (DWA-132, card2, ch11, driver `rt2800usb`)
- Service: `hostapd.service` (bridge `br-robot`)
- SSID: `Robot-Net`
- WireGuard: `wg-robot` (private key must be set manually after reboot)

## Power Monitor Script
```bash
echo "Monitoring power status for shutdown..."