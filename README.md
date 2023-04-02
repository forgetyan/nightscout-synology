# Nightscout Synology Drive Setup

This guide will help you set up Nightscout on a Synology Drive, so you can host it locally in your home and access it from anywhere through the internet.

## Prerequisites

Before starting the setup process, you will need:

- A Synology Drive running Docker
- Access to your router to create a forwarding port rule to redirect port 443 to your Synology Drive
- Basic knowledge of the Synology Drive control panel

## Creating a DuckDNS Account and Domain Name

DuckDNS is a service that provides free DNS hosting to allow you to create a custom domain name for your home network, so you can access your Nightscout installation remotely. Follow these steps to create a DuckDNS account and domain name:

1. Go to the [DuckDNS website](https://www.duckdns.org/) and register using one of the "Sign in" options in the top of the page.

2. Once you are logged in, select a new domain name in the form of `yourdomainname.duckdns.org`. For example, that could be: `johnnysnightscout.duckdns.org`.

3. After selecting your domain name, click on "Add Domain" to create your new domain. Your new domain name will now appear on the "Domains" page.

4. The "current ip" should be fed with the public IP of the machine you used to create the domain name but you could change it here. Make sure the "current ip" is set to the IP address of your home. You can ask google "What's my IP address" from a PC in your home to get that IP address.

5. Once we're done, you should now be able to use your new domain name to access your home IP. Open a terminal and ping your domain name to make sure it works. For example, if your new domain name is `johnnysnightscout.duckdns.org`, you need to enter the following command:

   ```sh
   ping johnnysnightscout.duckdns.org
   ```

6. You should receive a response that contain you IP address. You may ignore any error while pinging. The important part is that your IP address should be part of the message. Suppose your IP address is `172.217.52.27`, you might receive the folloowing response:

   ```sh
    Pinging johnnysnightscout.duckdns.org [172.217.52.27] with 32 bytes of data:
    [You can ignore following text]
   ```

Make sure to note down your new domain name, as you will need it later in the setup process.

## Configuring Router for Port Forwarding

To make your Nightscout installation available from the internet, you will need to create a port forwarding rule in your router to redirect traffic on port 443 to your Synology drive. Follow these steps to configure your router:

1. Access your router's configuration page by entering your router's IP address in your web browser. This is usually `192.168.1.1` or `192.168.0.1`, but you can check your router's documentation for the correct IP address.

2. Once you are logged in to your router's configuration page, find the DHCP settings and look for the option to reserve an IP address for a specific device. This will ensure that your Synology drive always gets assigned the same IP address from your router's DHCP server.

3. Choose your Synology drive from the list of devices and assign a fixed IP address to it. Make sure to note down this IP address, as you will need it later in the setup process.

4. Next, find the port forwarding settings in your router's configuration page. Create a new port forwarding rule to redirect incoming traffic on port 443 to the fixed IP address you assigned to your Synology drive.

5. Save your new port forwarding rule and exit the router's configuration page. Your router should now be configured to redirect incoming traffic on port 443 to your Synology drive, making your Nightscout installation available from the internet.

## Creating a Reverse Proxy

Now, to redirect incoming traffic to your Nightscout installation, you will need to set up a reverse proxy on your Synology Drive. Follow these steps to create a reverse proxy:

1. Log in to the Synology Drive control panel and open the **Application Portal**.

2. Click on the **Reverse Proxy** in the **Advanced** tab and then click the **Create** button.

3. In the **General** tab, enter a name for the reverse proxy ex: "`Johnny's nightscout`" and select the HTTPS protocol.

4. In the **Source** section, enter the 443 port number and enter your host name ex: `johnnysnightscout.duckdns.org`.

5. In the **Destination** section, select the HTTP protocol, enter '`localhost` as the hostname and select port '`1337`' for Nightscout. (You could also use a different port number if it's already in use or if you need to create multiple Nightscout sites on the same server)

6. Click the **Save** button to create the reverse proxy.

7. Your reverse proxy should now be set up and ready to redirect incoming traffic to your future Nightscout installation.

## Generating a Let's Encrypt Certificate

To enable HTTPS connections to your Nightscout installation, you will need to generate a Let's Encrypt certificate. Follow these steps to generate a certificate using the Synology Drive:

1. Log in to the Synology Drive control panel and open the **Security** tab.

2. Click on the **Certificate** tab and then click the **Add** button.

3. In the **Add Certificate** wizard, select the option to **Add a new certificate** and then click the **Next** button.

4. In the **Choose an action** step, select **Get a certificate from Let's Encrypt**

5. In the **Certificate** tab, enter the domain name that you created earlier using DuckDNS. Ex: `johnnysnightscout.duckdns.org` Now add your email addres and click **Apply**.

6. If your domain name reach your Synology drive properly, your Let's Encrypt certificate should now be generated and ready to use with your Nightscout installation.

## Setting up Docker and Nightscout

To set up Docker and Nightscout on your Synology Drive, follow these steps:

1. Log in to your Synology Drive via SSH. You can use a program like PuTTY or "ssh" command in the windows terminal to do this.

2. Create a new directory for Docker by typing the following command:

   ```sh
   mkdir /volume1/docker
   ```

3. Change to the new directory using the following command:

   ```sh
   cd /volume1/docker
   ```

4. Download the sample `docker-compose.yml` file from the Nightscout GitHub repository using the following command:

   ```sh
    wget -O docker-compose.yml https://raw.githubusercontent.com/forgetyan/nightscout-synology/main/docker-compose-single-installation.yml
   ```

5. Create a directory to store MongoDB data by typing the following command:

   ```sh
    mkdir /volume1/docker/mongodb-data
   ```

6. Use the `nano` text editor to modify the `docker-compose.yml` file and set the necessary environment variables. For example:

   ```sh
    nano docker-compose.yml
   ```

7. Make sure to replace at least following environment variables with the appropriate values for your installation:

   - API_SECRET=`myapisecret`
   - BRIDGE_PASSWORD=`mybridgepassword`
   - BRIDGE_SERVER=`EU`
   - BRIDGE_USER_NAME=`mybridgeusername`
   - CUSTOM_TITLE=`Johnny's Nightscout`
   - DISPLAY_UNITS=`mmol`
   - LOOP_APNS_KEY= 
   - LOOP_APNS_KEY_ID=`myapnskeyid`
   - LOOP_DEVELOPER_TEAM_ID=`mydeveloperteamid`
   - VIRTUALHOST=`johnnysnightscout.duckdns.org`

8. Start the Docker containers by running the following command:

   ```sh
   docker-compose up -d
   ```

   This will start the MongoDB and Nightscout containers in the background.

9. Your Nightscout installation should now be up and running and accessible via the internet at the domain name that you specified. Try accessing it locally with `http://[Your Synology Drive IP]:1337` or from the internet using `https://[your domaine name].duckdns.org`. Some ISP might allow you to acces your own public port as you were outside your network but on some configuration it is possible that using your external domain name from inside of your network may not work. If it's the case, you could disable wifi on a cellphone and connect using the SIM card's internet access.
