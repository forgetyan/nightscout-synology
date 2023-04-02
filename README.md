# Nightscout Synology Drive Setup

This guide will help you set up Nightscout on a Synology Drive, so you can host it locally in your home and access it from anywhere through the internet.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Creating a DuckDNS Account and Domain Name](#creating-a-duckdns-account-and-domain-name)
3. [Configuring Router for Port Forwarding](#configuring-router-for-port-forwarding)
4. [Creating a Reverse Proxy](#creating-a-reverse-proxy)
5. [Generating a Let's Encrypt Certificate](#generating-a-lets-encrypt-certificate)
6. [Setting up Docker and Nightscout](#setting-up-docker-and-nightscout)
7. [Dual installation](#dual-installation)
    + [Creating a new Domain Name](#creating-a-new-domain-name)
    + [Creating a new Reverse Proxy](#creating-a-new-reverse-proxy)
    + [Generating a new Let's Encrypt Certificate](#generating-a-new-lets-encrypt-certificate)
    + [Nightscout installation](#nightscout-installation)
8. [Installation of Nightscout sites using Traefik](#installation-of-nightscout-sites-using-traefik)
    + [Create DuckDns domain name](#create-duckdns-domain-name)
    + [Traefik Dashboard](#traefik-dashboard)

## Prerequisites

Before starting the setup process, you will need:

+ A Synology Drive running Docker (See marketplace in Synology drive if not installed)
+ Access to your router to create a forwarding port rule to redirect port 443 to your Synology Drive
+ Basic knowledge of the Synology Drive control panel

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

   + API_SECRET=`myapisecret`
   + BRIDGE_PASSWORD=`mybridgepassword`
   + BRIDGE_SERVER=`EU`
   + BRIDGE_USER_NAME=`mybridgeusername`
   + CUSTOM_TITLE=`Johnny's Nightscout`
   + DISPLAY_UNITS=`mmol`
   + LOOP_APNS_KEY= `your-key`
   + LOOP_APNS_KEY_ID=`myapnskeyid`
   + LOOP_DEVELOPER_TEAM_ID=`mydeveloperteamid`
   + VIRTUALHOST=`johnnysnightscout.duckdns.org`

   Please note that the LOOP_APNS_KEY should not be pasted as-is. Every end line should be doubled.

   So, if your key is:

   ```certificate
   -----BEGIN PRIVATE KEY-----
   ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZ22Ys7mCQ9x4I
   ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZfYNJz3IC2Vfj
   ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZb5dBysUjs87t
   px56uZHQ
   -----END PRIVATE KEY-----
   ```

   You'll have to enter this in the configuration file: (See samples)

   ```certificate
   - LOOP_APNS_KEY=
    -----BEGIN PRIVATE KEY-----
    
    ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZ22Ys7mCQ9x4I
    
    ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZfYNJz3IC2Vfj
    
    ABCDEFGHIJKLMNOPQRSTUVWXYZABCDEFGHIJKLMNOPQRSTUVWXYZb5dBysUjs87t
    
    px56uZHQ
    
    -----END PRIVATE KEY-----
   ```

8. Start the Docker containers by running the following command:

   ```sh
   docker-compose up -d
   ```

   This will start the MongoDB and Nightscout containers in the background.

   If you change any so the Nightscout parameters in the docker-compose file, you have to run this command line again to recreate the containers using the new parameters. Docker will only restart the containers that were changed.

9. Your Nightscout installation should now be up and running and accessible via the internet at the domain name that you specified. Try accessing it locally with `http://[Your Synology Drive IP]:1337` or from the internet using `https://[your domaine name].duckdns.org`. Some ISP might allow you to acces your own public port as you were outside your network but on some configuration it is possible that using your external domain name from inside of your network may not work. If it's the case, you could disable wifi on a cellphone and connect using the SIM card's internet access.

## Dual installation

The process for the dual (or more) installations is similar to the single installation but we have some key differences to do.

### Creating a new Domain Name

While we can use the same account to do so, we have to create a new domain name for each of our Nightscout accounts. [See documentation above](#creating-a-duckdns-account-and-domain-name) but create two domain names instead of one. Both name should redirect to the same IP address.

### Creating a new Reverse Proxy

Each domain name should have their own reverse proxy configured. [Follow documentation above](#creating-a-reverse-proxy) but create multiple reverse proxy as required to match all the domain names that were created.

### Generating a new Let's Encrypt Certificate

Again, each of the domain names should have their own Let's Ecrypt certificate generated by the Synology drive [Follow the documentation above](#generating-a-lets-encrypt-certificate) to know how to proceed

### Nightscout installation

1. First, we have to create a distinct database folder for each installations. That mean that if we have, for example, a dual installation for both Jonny and Sophia, instead of writing:

   ```sh
    mkdir /volume1/docker/mongodb-data
   ```

   We'll have to create two folders by entering the command two times:

   ```sh
    mkdir /volume1/docker/mongodb-data-johnny
    mkdir /volume1/docker/mongodb-data-sophia
   ```

2. After that, we'll need to download the docker-compose sample for dual installation instead of the single one.

   ```sh
    wget -O docker-compose.yml https://raw.githubusercontent.com/forgetyan/nightscout-synology/main/docker-compose-dual-installation.yml
   ```

3. Finally, make sure to make the proper replacements for both kids. It's very important that Johnny's Nightscout refer to Jonny's database and the same precaution need to be made for Sophia's docker instances. Both of them can use the same LOOP APNS Keys if their Loop installation were built using the same Apple Developer licence.

4. Make sure the LOOP parameter are set differently for each installation.
   + API_SECRET: Can be the same on both installations
   + BRIDGE_PASSWORD: Can be the same on both installations but depends on your Dexcom bridge installations
   + BRIDGE_USER_NAME: Need to be different for each account. We don't want to mixup their bgs
   + CUSTOM_TITLE: It's important to change the title so we know easily which Nightscout site is opened by looking on the top left corner
   + LOOP_APNS_KEY: Can be the same on both installations
   + LOOP_APNS_KEY_ID: Can be the same on both installations
   + LOOP_DEVELOPER_TEAM_ID: Can be the same on both installations
   + VIRTUALHOST: We need a different domain name for both installations

5. Start the Docker containers by running the following command:

   ```sh
   docker-compose up -d
   ```

   This will start all MongoDB and Nightscout containers in the background.

   If you change any so the Nightscout parameters in the docker-compose file, you have to run this command line again to recreate the containers using the new parameters. Docker will only restart the containers that were changed.

6. Once completed, we should be able to access all the nightscout sites installed on the same Synology drive.

## Installation of Nightscout sites using Traefik

There is an alternate way to install Nightscout using a docker image named "Traefik". This will handle the reverse proxy and the Let's encrypt operations. It could be used to install Nightscout without exposing port 443 of the synology drive. Also, it could be used to install Nightscout using Docker in a PC that does not have all the tools that Synology drive gives us. `Any PC with Docker installed should be able to support this installation.`

### Create DuckDns domain name

You still need a DuckDns account & domain name to access your installation. [Follow documentation above to do so](#creating-a-duckdns-account-and-domain-name).

### Nightscout configuration with Traefik

1. Your still need to [configuring your router for port forwarding](#configuring-router-for-port-forwarding). However, still we won't expose the port 443 of the Synology Drive, we need to redirect port 443 (external) to another free port number on the Synology Drive. We'll use port 4443 in the current sample.

2. Log in to your Synology Drive via SSH. You can use a program like PuTTY or "ssh" command in the windows terminal to do this.

3. Create a new directory for Docker by typing the following command:

   ```sh
   mkdir /volume1/docker
   ```

4. Change to the new directory using the following command:

   ```sh
   cd /volume1/docker
   ```

5. Download the sample `docker-compose.yml` file from the Nightscout GitHub repository using the following command:

   ```sh
    wget -O docker-compose.yml https://raw.githubusercontent.com/forgetyan/nightscout-synology/main/docker-compose-installation-with-traefik.yaml
   ```

   Note that this sample file is specific to traefik and is different from the single file installation above.

6. Create a directory to store MongoDB data by typing the following command:

   ```sh
    mkdir /volume1/docker/mongodb-data
   ```

7. Create a new folder for let's encrypt

   Let's encrypt need to be able to store your https certificate somewhere. We'll add a volume for traefik so it's be able to reuse the certificate it made when we restart our docker instances.

   Run this command to create the new folder

   ```sh
   mkdir /volume1/docker/letsencrypt
   ```

8. Use the `nano` text editor to modify the `docker-compose.yml` file and set the necessary environment variables. For example:

   ```sh
    nano docker-compose.yml
   ```

   [Use the documentation above](#setting-up-docker-and-nightscout) to set all nightscout variables and see next step for additional changes required for traefik

9. Change the additional traefik parameters in the `docker-compose.yml` file.

   Note that there is a new `labels:` section in the Nightscout section. It's important to change this line according to your domain name:

   ```yaml
   - 'traefik.http.routers.nightscout.rule=Host(`johnnysnightscout.duckdns.org`)'
   ```

   You should also change this line in the "command" section of the traefik docker instance. As tempting as it may be, **DO NOT SKIP THIS STEP**. If the email is invalid, Let's Encrypt will NOT generate a certificate for your domain name and your installation won't work.

   ```yaml
   - '--certificatesresolvers.le.acme.email=youremailaddress@domain.com'
   ```

10. Once modification are completed, run this command to launch all docker instances:

      ```sh
      docker-compose up -d
      ```

      This will start MongoDB, Nightscout and traefik containers in the background.

      If you change any so the Nightscout parameters in the docker-compose file, you have to run this command line again to recreate the containers using the new parameters. Docker will only restart the containers that were changed.

      The internet request will be forwarded as follow:

      1. The request will hit your router on port 443 as HTTPS
      2. The request will be redirected on the Synology Drive but on port 4443, still in HTTPS. On this port, the Synology Drive reverse proxy is **not**  running.
      3. Traefik will catch this request and send a request internally in docker to the Nightscout instance as HTTP (Not secured) on port 1337.
      4. Nightscout will Answer and the response will be sent.

      The first time you launch the site, it's possible that the HTTPS certificate is not generated yet. Traefik will then communicate with Let's Encrypt and, if your configurations were modified correctly, it will generate a new certificate for you. The following request will be encrypted as expected.

### Traefik Dashboard

Optionaly, you can access your traefik Dashboard internally using port 8080 on your Synology Drive. On your local network only.

If you do not want to access this dashboard, you can comment the folowing line in the `ports` section of the traefik instance

```yml
      #- '8080:8080'
```
