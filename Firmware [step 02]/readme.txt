================================================================================
  SERCOMM S3 (ETISALAT S3) - COMPLETE OPENWRT INSTALLATION & UPGRADE GUIDE
================================================================================

This document provides complete, step-by-step instructions for flashing 
OpenWrt onto the Sercomm S3 (Etisalat S3) router using SSH only.

--------------------------------------------------------------------------------
TABLE OF CONTENTS
--------------------------------------------------------------------------------
1. Prerequisites & Prerequisites Checklist
2. Downloading Correct Firmware Images
3. Understanding Image Types (Factory vs Sysupgrade)
4. Phase 1: Uploading Modified Config & Enabling SSH
5. Phase 2: Switching Boot Slots (Slot 0 -> Slot 1)
6. Phase 3: Flashing OpenWrt Factory Image via SSH
7. Phase 4: Initial OpenWrt Setup & LuCI Installation
8. Future Maintenance: Upgrading via Sysupgrade
9. Troubleshooting & Recovery

================================================================================
1. PREREQUISITES & PREPARATION
================================================================================
- Sercomm S3 (Etisalat S3) MT7621 Router.
- Computer connected to the router via an Ethernet cable (LAN port).
- SSH/SCP Client (Terminal on Linux/macOS, or PuTTY/WinSCP on Windows).
- Stock Router Gateway: 192.168.1.1
- Stock SSH Credentials (after config modified):
    * Username: SuperUser
    * Password: E12345678 (or your router's Serial Number from the back sticker)

================================================================================
2. DOWNLOADING FIRMWARE IMAGES
================================================================================
1. Open your browser and go to:
   https://firmware-selector.openwrt.org/

2. In the "Model" search box, type:
   Sercomm S3

3. Select:
   Etisalat S3 / Sercomm S3

4. Download BOTH files for future use:
   - Factory Image:   ...-squashfs-factory.img
   - Sysupgrade Image: ...-squashfs-sysupgrade.bin

================================================================================
3. UNDERSTANDING IMAGE TYPES
================================================================================
* FACTORY IMAGE (*-factory.img):
  Used when converting the router from Stock Etisalat/Sercomm Firmware to 
  OpenWrt for the FIRST TIME.

* SYSUPGRADE IMAGE (*-sysupgrade.bin):
  Used when your router is ALREADY running OpenWrt and you want to update 
  to a newer OpenWrt release (via LuCI web interface or `sysupgrade` CLI).

================================================================================
4. PHASE 1: UPLOADING MODIFIED CONFIG & ENABLING SSH
================================================================================
(Note: Skip to Phase 2 if you have already uploaded the config file.)

1. Log in to stock Web Interface at http://192.168.1.1/
2. Go to Management / Backup & Restore.
3. Upload your modified configuration file that enables `SuperUser` SSH access.
4. Reboot the router when prompted.

================================================================================
5. PHASE 2: SWITCHING BOOT SLOTS (SLOT 0 -> SLOT 1)
================================================================================
The Sercomm S3 uses a dual-boot layout. You must switch active execution 
to Slot 1 so you can safely flash OpenWrt onto Slot 0 (`mtd4`).

1. Open your terminal / command prompt on your PC.
2. Connect to the stock router via SSH:
   ssh SuperUser@192.168.1.1

3. Enter standard shell mode:
   sh

4. Switch active boot partition flag to Slot 1:
   printf 1 | dd bs=1 seek=7 count=1 of=/dev/mtdblock3

5. Reboot the device:
   reboot

6. Wait ~2 minutes for the system to boot into Slot 1.

================================================================================
6. PHASE 3: FLASHING OPENWRT FACTORY IMAGE VIA SSH
================================================================================

Step 3.1: Transfer Factory Image to Router (from PC terminal)
-------------------------------------------------------------
Open a NEW terminal window on your PC inside the folder where your downloaded 
Factory image resides:

scp openwrt-24.10.2-ramips-mt7621-etisalat_s3-squashfs-factory.img SuperUser@192.168.1.1:/tmp/

Step 3.2: Verify MTD Partition Mapping
--------------------------------------
SSH back into the router:

ssh SuperUser@192.168.1.1
sh

Check partition layout:
cat /proc/mtd

Look for `mtd4` (it should correspond to `kernel0` or `os0`).

Step 3.3: Write Image to MTD Partition
--------------------------------------
Navigate to `/tmp` and flash the Factory image to `mtd4`:

cd /tmp
mtd -r write openwrt-24.10.2-ramips-mt7621-etisalat_s3-squashfs-factory.img mtd4

The router will write the binary block-by-block and automatically reboot once 
finished. Wait ~3 minutes for the system to complete initialization.

================================================================================
7. PHASE 4: INITIAL OPENWRT SETUP & LUCI INSTALLATION
================================================================================
Once rebooted, OpenWrt is running on your router!

1. SSH into OpenWrt:
   ssh root@192.168.1.1
   (No password required initially)

2. Set a root password:
   passwd

3. Connect WAN port to an active internet connection, then update packages 
   and install LuCI Web Interface:
   opkg update
   opkg install luci

4. Access Web Interface:
   Open browser and navigate to http://192.168.1.1/

================================================================================
8. FUTURE MAINTENANCE: UPGRADING VIA SYSUPGRADE
================================================================================
When upgrading between OpenWrt versions in the future:

METHOD A: Via LuCI GUI
1. Go to System -> Backup / Flash Firmware.
2. Select "Flash new firmware image".
3. Upload `openwrt-*-squashfs-sysupgrade.bin`.
4. Choose whether to keep settings and click "Continue".

METHOD B: Via SSH CLI
1. Upload Sysupgrade image to `/tmp/`:
   scp openwrt-*-sysupgrade.bin root@192.168.1.1:/tmp/
2. Execute sysupgrade:
   sysupgrade -v /tmp/openwrt-*-sysupgrade.bin

================================================================================
9. TROUBLESHOOTING & RECOVERY
================================================================================
- Router unreachable after flash? 
  Set manual PC IP to `192.168.1.2`, subnet `255.255.255.0`, gateway `192.168.1.1`.
- Fails to boot OpenWrt?
  Power-cycle the router 3 consecutive times during early boot phase to trigger 
  dual-boot failover back to stock firmware on Slot 1.
================================================================================