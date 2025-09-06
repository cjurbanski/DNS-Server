# DNS Server Setup Guide

## Where to Begin

For this project you will need:

- **A server**  
  - This can be a **PC**, **NAS** (like UGREEN, Synology, QNAP), or a **cloud-hosted VM** (AWS, Azure, GCP, etc.).  
- **Docker installed** on your server  
  - [Install Docker](https://docs.docker.com/get-docker/) for your operating system.  
- **Basic networking knowledge**  
  - Ability to configure your router’s **DHCP** settings.  
  - Understanding of DNS (Domain Name System).  
- **Admin access** to your network/router  
  - Required for pointing all devices to use your new DNS server.

I am using a **UGREEN NASync DXP2800** as my server  

<img src="https://github.com/user-attachments/assets/5c5cfc60-6b65-4f40-93d6-751058fe3e38" alt="UGREEN NAS" width="50%"/>

[UGREEN NAS](https://nas.ugreen.com/products/ugreen-nasync-dxp2800-nas-storage?variant=44816230187202&country=US&currency=USD&utm_medium=product_sync&utm_source=google&utm_content=sag_organic&utm_campaign=sag_organic&dm_cam=22097359167&dm_grp=184775890272&dm_ad=770554481759&dm_kw=&dm_net=adwords&gad_source=1&gad_campaignid=22097359167&gbraid=0AAAAA9YUSrDoFCNDjZjEVaCWZpj-iNsQF&gclid=CjwKCAjwt-_FBhBzEiwA7QEqyIRKX2cM6oh8VCEGAHF1_MftGyxbqfbH2LAQjn52n3TSUt6NAIUrQRoCjUMQAvD_BwE)

## Set Up

### Begin by downloading the latest **Technitium** image off the Docker Hub
**Download Here:** [Official Technitium Docker Image](https://hub.docker.com/r/technitium/dns-server)

### Option 1: Using GUI (Docker Desktop or NAS Docker App)
1. Create a new container:  
- **Container Name:** `technitium-dns`  
- **Network Mode:** `host` (important for DNS to work correctly)  
- **Restart Policy:** Always restart  
2. Start the container.  
3. Open a browser and go to: http://your-server-ip:5380
  - Default login: `admin` / `admin`  
  - Change the password immediately.
 
### Option 2: Using Command Line (Linux/WSL/macOS)
1. Pull the Technitium DNS Image
docker pull technitium/dns-server
3. Run the Container in Host Mode
docker run -d \
  --name technitium-dns \
  --network host \
  --restart=always \
  technitium/dns-server
4. Verify it's running
docker ps
(You should see technitium-dns in the list)
5. Access the Web UI
Open a browser and go to: http://your-server-ip:5380
  - Default login: `admin` / `admin`  
  - Change the password immediately.

## After setup (GUI or CLI), go into your router’s DHCP settings and set your server’s IP as the primary DNS server so all devices on your network use Technitium.
**IMPORTANT:** *It is important to note that your clients will not pick up the new DNS server until their DHCP lease expires*
### To speed this up you can either:
1. Release/Renew on each device
   - For Windows:
      - Open PowerShell
          - Run commands:
             - ipconfig /release
             - ipconfig /renew
  - For Linux:
      - Open terminal
          - Run commands:
              - sudo dhclient -r
              - sudo dhclient
2. Reboot the router
  - Power cycling your router would clear all active DHCP leases and force devices to reconnect

## Inside Technitium Web UI

### Now that your devices are using Technitium as their DNS server we can begin blocking/allowing domains

<img width="1134" height="756" alt="DNS Server" src="https://github.com/user-attachments/assets/701a70cb-9b4e-4944-b034-7a0cc59dcee5" />

At the top of your Technitium web UI go to **Settings** --> **Blocking**
- Ensure Enable Blocking is selected


<img width="1049" height="277" alt="blocking" src="https://github.com/user-attachments/assets/b86b1fb9-9e37-4ca8-8950-f6e97f6b633f" />

Scroll down until you see **Allow/Block List URLs**
- From here you can either
    1. Select a premade block list URL from **Quick Add**
       - These are dynamically updated with the latest URLs for new Ad/Malicious sites
       - I recommend using a premade block list for best coverage
    2. Create your own block list text file and follow the URL format listed in the description
       - Ex.  On Linux the URL should look like file:///home/folder/myblocklist.txt and on Windows it should look like file:///c:/folder/myblocklist.txt

  

<img width="1105" height="705" alt="blocking-list" src="https://github.com/user-attachments/assets/cdb74015-5b17-42ae-a8a6-b7965ecae337" />

### IMPORTANT: *After making any changes select **"Save Settings"** in the bottom left*  

At the bottom of the dashboard you can now see your Top Domains and Top Blocked Domains
<img width="556" height="201" alt="domains" src="https://github.com/user-attachments/assets/6e9444b5-6fbb-44d7-ad02-ac4631f5090b" />  

If you want to Allow or Block a domain you can either:

1. Navigate to the **Allowed** or **Blocked** tabs next to Dashboard
2. Select the three dots next to a domain and select block domain or allow domain at the bottom of the dashboard

## **Troubleshooting**  

If you run into a website that has been blocked which you want to allow
- Copy the URL and add it under the **Allowed** tab  

For example: This blocked all sponsored Google ads at the top of my browser  
I commonly use Google for shopping so I allowed the Google domain utilized for these ads  

If you experience networking issues:
- Ensure you are using Host network mode on your Docker container

I originally used bridged mode which caused issues with DNS because it expects raw access not NAT











