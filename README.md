<div align="center" style="white-space: nowrap;">
  <img src="https://github.com/4LifeStrategy/4LifeStrategy/blob/88ffe3009f1399de4502d4d5641c8f7a0fd56852/4LifeStrategy%20Logo%20Center.png" alt="4LifeStrategy Logo" width="100" style="display:inline-block; vertical-align:middle; margin-right:10px;">
  <h1 style="margin:0; vertical-align:middle;">Network Attached Storage Server Deployment</h1>
</div>

## Description

**NAS Deployment** is building a NAS server to host services such as a media server, media requester, file server, and server dashboard with remote access. Ensured efficient storage management and seamless access to data and multimedia resources.

## Project Scope

**Objective**: To host services such as a file server, media server, media requester, and photo storage that are able accessed via ip address or domain names.

## Tools

**Hardware**
- [Synology  DS423+](https://www.synology.com/en-us/products/DS423+)
- [4x Synology8TB SATA HDD](https://www.synology.com/en-us/products/drives/hdd/plus-hat#specs)
- [Synology 400GB SNV3410 NVMe M.2 2280 SSD](https://www.synology.com/en-us/products/drives/ssd/enterprise-snv#specs)
- [Synology 4GB DDR4 2666 MHz Non-ECC SO-DIMM Memory](https://www.synology.com/en-us/products/DDR4)
- Uninterruptible Power Supply (UPS)

## Locating Synology NAS

We will need to find the synology NAS. There are 3 ways to locate the nas. If you follow our pfSense configuration, the method that locate the nas from pfSense's DHCP leasing.

**Using Web Browser**
1. Navigate to the https://finds.synology.com website and it will automatically start searching your network.
2. If any devices are found, it will automatically show them in the web browser and you can select **Connect**

**Using Synology Assistant Application**
1. Navigate to Synology’s [Download Center](https://www.synology.com/en-us/support/download/), you can download the Synology Assistant. The Synology Assistant allows you do to a few different things, but the main function is to actually find your Synology NAS.
2. Select **NAS**
3. Select your NAS model **DS423+**
4. Select **Desktop Utilities**
5. On **Synology Assistant** click **Download**
6. After installing it, launch the Synology Assistant application. It will automatically search your network and should return the Synology NAS devices that it finds.

**Using pfSense DHCP Leases and Alias**
1. Make sure that the NAS is powered on and connected to OPT1 on the Protectli Vault
2. Login to pfSense intense
3. Click **Status**
4. Click **DHCP Leases**
5. Locate the active ip address in the 192.168.215.(100-254) space. There should only be one active.
6. Click **Add static mapping**
7. For **IP Address** type **192.168.215.2**<br />*Any number in between (2-99) for the 4th octet.
8. For **Hostname** type **NAS**
9. Click **Save**
10. Click **Apply changes**
11. Click **Firewall**
12. Click **Aliases**
13. Click **Add**
14. For **Name** type **NAS_Services**
15. For **Description** add a description
16. For **Type** select **Host(s)**
17. For **IP or FQDN** add your nas's static ip **192.168.215.2**
18. For **Description** type **NAS**
19. Click **Save**
20. Click **Apply changes**
21. Click **Firewall**
22. Click **Rules**
23. Select **LAN**
24. Click **Add up arrow**
25. For **Action** select **Pass**
26. For **Protocol** select **Any**
27. For **Source** select **Address or Alias**
28. Type **Trusted_Devices**
29. For **Destination** select **Address or Alias**
30. Type **NAS_Services**
31. For **Description** add a description
32. Click **Save**
33. Click **Apply Changes**
34. Click **Copy** on the new rule
35. For **Interface** select **OPT2**
36. Click **Save**
37. Click **Apply changes**
38. Restart the Synology NAS by using the power button.
39. Navigate to a web browser and go to **192.168.215.2**

## Initial Setup

Once connecting to the NAS you will have to accept the End User License Agreement and install the latest version of DSM. Once it download and installed, the nas will reboot when update in complete. After a few minutes have passed, open a new tab and navigate to your nas's ip address or http://find.synology.com.

1. Give your NAS a Device Name, Administrator Username and Password
2. Select **Automatically install important DSM and package updates only (Recommended)**
3. Sign-in with your synology account
4. Next it will ask you to enable Synology Active Insight and configuration backups. Select whichever you’d prefer, then proceed.

You will be prompted install synology services, enable warranty and enable 2FA which are highly recommended. Next to set up a Storage Pool and Volume, but if you’re not, open the Storage Manager and select Storage, then Create a Storage Pool.
1. Click **Create Now**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/2491e53de2ac93fa5c2a1a5d2b5fbfe88998deff/Synology%20Create%20Storage%20Pool.png" width="500">
2. Click **Start**
3. For **RAID type** select **SHR**
4. For **Drive type** select **SATA HDD**
5. Click **Next**
6. Select all the drives
7. Click **Next**
8. Select **Perform drive check**<br />*Highly recommended for new drives to check for bad blocks and replace the drive this check return any errors*
9. Click **Next**
10. For **Modify allocated size** click **Max**
11. Click **Next**
12. Select **Btrfs**<br />*This will allow you to create and automate snapshots*
13. Click **Next**
14. Select **Encrypt this volume**<br />*Only if you are aware that if you lose this file that you will not be able to regain access to the nas. Keep this file secure*
15. Click **Next**
16. Set a **Vault Password**<br />*Have this password in a secure location*
17. Click **Enable**
18. Click **Apply**
19. It will download the **Recovery Key**<br />*Reminder to keep this file in a secure location and recoverable. You will only need this file or password when someone is has the nas physically and attempting to hack into it.*

## Data Protection & Monitoring

While the DSM is performing the dive check performance will be impacted. Next we'll schedule Data Scrubbing, SMART test, Snapshots, Recycle Bin Task, Storage Analyzer,UPS, and notifications.

**Data scrubbing** inspects your volumes and modifies detected inconsistencies. In simple terms, this protects your NAS against bit-rot. There isn’t a specific schedule that’s mandatory, but it’s a good idea to run it at a minimum, bi-annually.

1. Within **Storage Manager** select **Schedule Data Scrubbing**
