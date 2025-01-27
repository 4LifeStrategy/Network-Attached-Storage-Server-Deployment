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
19. It will download the **Recovery Key**<br />*Reminder to keep this file in a secure location and recoverable. You will only need this file or password when someone is has the nas physically and attempting to hack into it.*<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/4634a91a7f3336635ef6a524abbee730786daec0/Synology%20Storage%20Manager%20Overview.png" width="500">

**Create Read SSD Cache**

**SSD Read Cache** would allow regularly accessed files to be read of the speed of an SSD and not of the HDD.
1. Within **Storage Manager** select **Volume 1**
2. Click **Create**
3. Select **Create SSD Cache**
4. Click **Next**
5. Select **Read only cache**
6. Click **Next**
7. Click **Next**
8. Select **M.2 Drive**
9. Click **Next**
10. For **Modify allocated capacity (GB)** allocate 10%-20% less of the max capacity of your SSD drive. For 372 GB would be **300**
11. Click **Apply**

**NAS Application**
I would recommend installing some applications such as Synology Photos, Hybrid Share, Surveillance Station, Surveillance Video Extension, Advanced Media Extensions, Synology Drive Server, Synology Contact, and Active Backup for Business.

1. Open **Package Center**
2. Select **All Packages**
3. Select the **Application** you want to install
4. Click **Install**

## Create Users and Groups**

Recommend to create normal users that does not have the same level as admin. These users could me your main account to access all the apps as a user to separate your admin account that you created in the initial setup. We use naming schema Admins= **ADM_username+number** Normal Users= **username+number**. We will demonstrate **Basic Users**, **Super Users**.

**Create Users**
1. Open **Control Panel**
2. Click **Create** to create a new user
3. Fill in the **Name**, **Email**, and **Password**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/c891e862f25a1d9eb1eca41c8a24313adf2b0bd7/Synology%20Create%20User.png" width="500">
4. Click **Next**
5. Click **Next**
6. Click **Next**
7. Click **Next**
8. Click **Next**
9. Click **Done**

**Create Basic Groups**
1. Within **Users & Groups**
2. Select **Groups**
3. Click **Create**
4. For **Group Name** put the desired name, we are going to put **Basic Users**
5. Select the **basic users**
6. Select the desired apps in the **Read Only** column<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/07cdb17de893bd141777975608d6433a004c8833/Synology%20Group%20Creation%20Wizard%20Basic%20Users%20Permission%20.png" width="500">
7. Click **Next**
8. Click **Next**
9. Select the **desired applications**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/7430fcc5b45b4d3170777711235c538bc36461f6/Synology%20Group%20Creation%20Wizard%20Basic%20Users%20App%20Access.png" width="500">
10. Click **Next**
11. Click **Next**
12. Click **Done**

**Create Super Users Group**
This will be your main profile will be part of and will have access to all the application you want to access as a user with right permission to said applications.
1. Within **Users & Groups**
2. Select **Groups**
3. Click **Create**
4. For **Group Name** put the desired name, we are going to put **Super Users**
5. Select the **Your main user profile**
6. Select the desired apps in the **Read and Right permissions** column<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/9c797e08d083eaaff1b2ee9c3e17daee820981c6/Synology%20Super%20User%20Permission.png" width="500">
7. Click **Next**
8. Click **Next**
9. Select the **desired applications**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/9c797e08d083eaaff1b2ee9c3e17daee820981c6/Synology%20Super%20User%20Access.png" width="500">
10. Click **Next**
11. Click **Next**
12. Click **Done**

**Create Shared Folder**

After the storage pool and volume is created, we can now create a shared folder. Shared folders are the backbone of NAS units and are what you’ll store all your files on.

1. Open **Control Panel**
2. Click **Shared Folder**
3. Click **Create**
4. Select **Create Shared Folder**
5. For **Name** put your desired name, we are going to put **Shared Media**
6. Click **Next**
7. Click **Next**
8. Click **Enable data checksum for advanced data integrity**<br />*When we schedule Data scrubbing it will self-heal data with in the folder.*
9. Click **Next** 
10. Click **Next**
11. Select **Local groups**
12. Select  **Desired Permission** for each group<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/947f538d8f760d0b6f9d8d7075e426a8e5eee042/Synology%20Shared%20Folder%20Goup%20Permissoin.png" width="500">
13. Click **Apply**

## Data Protection & Monitoring

While the DSM is performing the dive check performance will be impacted. Next we'll schedule Data Scrubbing, SMART test, Snapshots, Recycle Bin Task, Storage Analyzer, UPS, and notifications.

**Data scrubbing** inspects your volumes and modifies detected inconsistencies. In simple terms, this protects your NAS against bit-rot. There isn’t a specific schedule that’s mandatory, but it’s a good idea to run it at a minimum, bi-annually.

1. Within **Storage Manager** select **Schedule Data Scrubbing**
2. Click on **Enable data scrubbing scrubbing**
3. Select all volumes
4. For **Frequency** select **Repeat every three months**
5. Click **Run data scrubbing only during specific periods**
6. Click **Set Time Grid**
7. Select from midnight to 5am<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/4634a91a7f3336635ef6a524abbee730786daec0/Synology%20Scedule%20Data%20Scrubbing.png" width="500">
8. Click **OK**
9. Click **Save**

**S.M.A.R.T** tests the desks to determine if the any of the drives are going to fail soon.

**Quick S.M.A.R.T test**
1. Within **Storage Manager** select **HDD/SSD**
2. Click **Settings**
3. Click **Create**
4. For **Task name** put **Quick SMART**
5. Click **OK**

**Extended S.M.A.R.T test**
1. Click **Create**
2. For **Task name** put **Quick SMART**
3. For **S.M.A.R.T Test Type** select **Extended Test**
4. Select **Schedule**
5. Under the date select **Repeat every six months**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/bbca92c3a9329b2b48ae02d1ce061beac443c889/Extended%20SMART.png" width="500">
6. Click **OK**
7. Click **Apply**

**Enabling Snapshots & Recycle Bin**

**Snapshots** are a feature of the Btrfs file system that allows you to capture the state of a folder at a specific point in time, enabling you to revert to that state later if needed. The follow steps will need to be done for each Shared Folder.

1. Open **Package Center**
2. Search for **Snapshot**
3. Install **Snapshot Replication**
4. After install, launch **Snapshot Replication** application.
5. Select the **Shared Folder** that you want to enable snapshots.
6. Click **Snapshots**
7. Click **Settings**
8. Click **Enable snapshot schedule**
9. For **Frequency** select **Every 2 hours**
10. Select **Retention**
11. Click **Enable retention policy**
12. Select **Advanced retention policy**
13. Click **Set Rule**
14. Copy the schedule from the screenshot<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/d2feeef82b8103b093bf6554d5cd1bef35f12f7e/Synology%20Retention%20Policy.png" width="500">
15. Click **OK**
16. Select **Advanced**
17. Click **Make snapshots visible**
18. Click **OK**

**Create Recycle Bin Task**
1. Open **Control Panel**
2. Select **Task Scheduler**
3. Click **Create**
4. Select **Scheduled Task**
5. Select **Recycle Bin**
6. For **Task** enter **Recycle Bin**
7. Check **Enabled**
8. Select **Schedule**
9. Confirm it set to run **Daily**
10. Select **Task Settings**
11. For **Retention Policy** select **Number of days to retain deleted files** and put **14**
12. Click **OK**
13. Click **Apply**

**Set up the Storage Analyzer**

Storage analyzer allows you to see what files/folders are taking up space on your NAS and if any duplicates exist. It’s a powerful tool that periodically comes in handy.

1. Open **Package Center**
2. Search **Analyzer**
3. Install **Storage Analyzer**
4. When it’s done installing, **Open** the package. 
5. You will be asked to specify a location to save your reports. Select **Yes**. Specify a location to save your reports and the frequency you’d like reports generated.
6. A wizard will then start to assist you in the creation of the scheduled task. Give your report a name, set a schedule, and then specify the total number of reports you’d like to keep.
7. Keep all Report Types selected (unless you don’t want specific ones generated) and click **Next**.
8. Select **Analyze all current and future shared folders**
9. Click **Next**
10. Select the settings you’d like to use to find duplicate files.
11.  Click **Generate reports now**
12.  Click **Done**

**Uninterruptible Power Supply (UPS)**

If your data is important to you, a UPS is the cheapest security blanket you can buy. It ensures that consistent power is delivered to your NAS and in the event of a power outage, it can safely power down your NAS. If you don’t have a UPS, it requires an additional purchase, but in my opinion, it’s well worth it.

1. Open **Control Panel**
2. Click **Hardware & Power**
3. Click **UPS**
4. Click **Enable USP support**
5. For **USB type** select **USB UPS**
6. Select **Customized time**
7. Set to **5 minute(s)**

**Setup Notifications**

Notification should had already been set if you completed the initial setup with a synology account. If you didn't follow these steps.

1. Open **Control Panel**
2. Click **Notification**
3. Confirm that the box is **Checked** under **Synology Account**
4. For **Rule** select **All**
5. Click **Apply**

## Security Configuration

**Set up Automatic DSM Update**

1. Open **Control Panel**
2. Click **Update & Restore**
3. Click **Update Settings**
4. Select **Automatic install important updates that fixed critical security issues and bugs (Recommended)**
5. For **Check schedule** set **day and time**<br />*Pick a date and time (preferably during the middle of the night) that updates will be installed.*
6. Click **OK**

**Change Default Ports**

After the initial Synology NAS setup process is completed, the default HTTP port is 5000 and the default HTTPS port is 5001. I always change these ports as it’s a good security practice to do so.

If you don’t intend on exposing your NAS to the internet, I wouldn’t say it’s necessary, but if you do intend on exposing it, I would change these ports.

1. Open **Control Panel**
2. Click **Login Portal**
3. Change port 5000/5001 to ports of your choosing. We are using **5050/5051**
4. Click **Automatically redirect HTTP connection to HTTPS for DSM desktop**
   
**Enable Denial-of-Service (DoS) Protection**

A Denial-of-Service (DoS) attack’s purpose is to shut down your machine or network making it inaccessible. Synology has an easy way to protect against this.

1. Open **Control Panel**
2. Click **Security**
3. Click **Protection**
4. Click **Enable DoS protection**
5. Click **OK**

**Configure Auto Block**

An IP address will be blocked if it reaches the number of failed login attempts within the time period entered.

Open **Control Panel**
Click **Security**
Click **Protection**
Click **Enable auto block**
For **Login attempts** to **3**
For **Within (minutes)** to **10**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/77ad1e923d8e5bbc5ecb3c99a4bf7a52dd09e318/Auto%20Block%20Policy.png" width="500">
Click **Apply**

## Synology’s QuickConnect

Synology QuickConnect allows you to access your NAS from outside of your local network. This is Synology’s way of ensuring you don’t need to open ports to your NAS to access your data externally.

1. Open **Control Panel**
2. Click **External Access**
3. Click **Enable QuickConnect**
4. For **QuickConnect ID** put the ID you want.
5. Click **Advanced Settings**
6. Confirm **Enable QuickConnect relay service**
7. Deselect **DSM**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/ea8ce0d6295994aa3d2df3bf9707e5a8bc2c481d/Synology%20QuickConnect%20Permission.png" width="500">
8. Click **Apply**
9. Click **Apply**

## Plex Media Server

Plex is a one-stop destination to stream your self-hosted movies, TV shows, and music. We will download [Plex for synology NAS 7.2](https://www.plex.tv/media-server-downloads/?cat=nas&plat=synology-dsm72#plex-media-server) The follow to install and setup Plex. IT recommended to have a Plex Pass but not required.

1. Open **Package Center**
2. Click **Manual Install**
3. Choose the **[Plex for synology NAS 7.2](https://www.plex.tv/media-server-downloads/?cat=nas&plat=synology-dsm72#plex-media-server)** file you downloaded
4. Select the location of where the Log Files will be installed – Can be left blank and it will save to the default directory
5. Open **Control Panel**
6. Click **Shared Folders**
7. Select **Folder where your Media is located**
8. Click **Edit**
9.  Click **Permissions**
10. Select **System internal user**
11. Click Read/Write for **PlexMediaServer**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/d6df656a4eacb9c3b2588e5c22228605d6049a2d/Synology%20Plex%20Shared%20Folder%20Permission.png" width="500">
12. Click **Save**
13. Select **Shared Media**
14. Click **Edit**
15.  Click **Permissions**
16. Select **System internal user**
17. Click Read/Write for **PlexMediaServer**
18. Head back into the **Package Center** window and click **OK** to verify you gave plex proper permissions.
19. Once installed click **Open**
20. Click **Claim Server** and create or sign-in to your Plex account
21. Click on your **NAS**
22. Click **Manage Libraries**
23. Click **Add Library** and map it to the **Shared Media** folder we created.<br />*You would need to create **Movie**, **TV Shows**, and **Music** subfolder*<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/7181790bb4373161d5d08faecfc5d62467a1d41d/Plex%20Libaries.png" width="500">
24. Click on **Remote Access**
25. Click **Enable Remote Access**
26. Click **Manual specified public port**
27. Set port to **32400**
28. Click **Apply**<br /><img src="https://github.com/4LifeStrategy/Network-Attached-Storage-Server-Deployment/blob/a3487bf4fff3dc8e7ce96fa7f69c54400aea4b6a/Plex%20Remote%20Connect.png" width="500">
