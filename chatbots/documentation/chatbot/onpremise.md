---
description: Installing and running the entire Mesibo platform on your own premise or private cloud. Benefitting the ultimate control over your sensitive data, unlimited storage & bandwidth etc. 
keywords: chat system on-premise, messaging platform, chat api platform, voice, video calling on-premise, installing chat platform on private cloud, mesibo communication platform
title: Mesibo On-Premise - Installing &amp; Running Mesibo on your own premise or private cloud
---
Mesibo On-Premise solution allows you to run the entire Mesibo platform in your own premise OR private cloud. All the messages and calls go through your own data center and stay in your own database. All you have to do is download Mesibo On-Premise and run it in your own data center. That's it!

## Why On-Premise
Although our cloud services let you start immediately, there are several reasons why you should consider **On-Premise**.


### Privacy and Data Security
One of the key reasons is to have ultimate control over your data.  More than ever before, it has become imperative to protect and be more sensitive towards your user data - be it personal data, financial data, medical records, and also other reasons like GDPR, HIPPA, unauthorized harvesting of your data, companies selling, misusing your data for advertisement, to name a few. Cambridge Analytica Scandal is an example that your data is most safe when it’s within your control.

Mesibo On-premise offering puts you complete control of your data since everything runs on your own servers or private clouds.

### Extendibility and other Technical Advantages
On-Premise offers many features which are not possible with the cloud deployment. For example, loadable modules that let you load your own code and enable tighter integration with your backend. You can interface Mesibo On-premise solution with machine learning and AI tools like Tensorflow, Matlab, and much more. Refer tutorials for more details.

### Pricing
Pricing is the most salient part of our On-premise offering. It comes to you at no additional cost - you only pay for the number of active users per month! No monthly commitment, no bandwidth charges, no storage charges, or any other hidden charges. This makes On-premise even more economical and attractive than our Cloud offering.

> If you do not wish to host Mesibo on your own server infrastructure, you have the option of using the Cloud offering by Mesibo. Also, **even when running On-Premise**, you can always use Mesibo cloud as a **back-up**, gives you best of the both worlds.

### Features
- All Mesibo features, including Messaging, Voice, and Video calling running from your own premise
- Complete control over your data. All the messages and calls route through and stay in your own infrastructure 
- Unlimited bandwidth and Messages
- Unlimited storage and data retention
- Private and public deployment
- Auto fallback to Mesibo cloud as a back-up, if required 
- Push notifications
- Loadable modules and scripting

All these at no additional cost

### Prerequisites
Before we dive into installing and running mesibo On-Premise Platform on your server, ensure that you are:

- Familiar with mesibo API and already using mesibo on our cloud services. If not, please refer to [getting started](/documentation/get-started/) guide and tutorials to familiarize yourself before setting up mesibo On-Premise.

- Familiar with setting up a Linux server and MySQL database. If not, refer to online tutorials for the Linux distribution of your choice. 

- Although not essential, basic knowledge of setting up Docker and using Docker containers would be helpful. You can refer to [Docker documentation](https://docs.docker.com) and various online tutorials on Docker. 


### Requirements
Mesibo On-Premise Platform requires the following:

- Linux 64-bit server (or an instance) with at least 1GB free RAM. Mesibo supports all the major Linux distributions, including: 

	- RHEL / CentOS 7.x or 8.x

	- Ubuntu 16.x or 18.x

	- Fedora 28 or 29

	- Debian 9 or 10

	- SLES 15

	- Oracles Linux 7.x

- MySQL (or MariaDB) database 5.x and later

### Should I recompile my apps to work with Mesibo On-Premise?
Definitely not. If your apps are using latest Mesibo APIs, you only need to configure your On-Premise server in [Mesibo Console](/console) and tell Mesibo to redirect all your users to your own server. All your users will immediately stop using Mesibo cloud servers and start using your own servers. Rest everything remains the same.   

Mesibo cloud servers use the same software as On-Premise. So whether you use Mesibo Cloud Service or setup a dedicated server running Mesibo On-Premise, there is absolutely no change in the way you use Mesibo APIs or deploy your application. In fact, in case your data-center has issues, you can always have the option of falling back to Mesibo Cloud Services, with just the click of a button.

## Setting up Mesibo-On Premise
Although Mesibo is a highly sophisticated piece of software, we have ensured that it is straightforward to set up. You only need to provide bare minimum information regarding how your users will connect to your On-Premise server (**your hostname**) and where the On-Premise platform should save the data (**your database**),  Mesibo will take care of the rest.

To setup Mesibo On-Premise, follow the steps below:

### Step 1 - Install Docker
Mesibo On-Premise server is distributed as a docker image so that you can easily install it on most Linux distributions. All you need is to install Docker to run it. If you have already installed and running Docker on your server, you can skip to Step 2. 

You can install Docker by running the command below:

```
$ sudo curl -sSL https://get.docker.com/ | sh
```

Once Docker is installed, you need to start the Docker daemon. Most Linux distributions use `systemctl` to start services. If you do not have `systemctl`, use the `service` command.

- **`systemctl`**:

  ```
  $ sudo systemctl start docker
  ```

- **`service`**:

  ```
  $ sudo service docker start
  ```

Once the installation is over, you can verify it by running 

```
$ sudo docker run hello-world
```

It should show something like:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

```

### Step 2 - Download Mesibo On-Premise Platform
Download Mesibo On-Premise docker image by running the following command

```
$ sudo docker pull mesibo/mesibo
```

This will download and install Mesibo On-Premise platform on your server. However, before we launch it, we need to configure mesibo in the [Mesibo console](/console).

### Step 3 - Configure Mesibo On-Premise

As mentioned earlier, Mesibo made On-Premise configuration straightforward. You don't need to wrestle with complicated configuration files. Instead, the complete On-Premise configuration is done through [Mesibo Console](/console/). All you need to do is to pass your app token to Mesibo On-Premise server, and it will automatically fetch and configure the entire server for you. 

Login to Mesibo console, and select the application you want to configure on your On-Premise host. **Make a note of the app token**. You need to use this app token later to run Mesibo On-Premise Server.

![App token Mesibo Console](/images/on-premise/app-token.jpg)

Now, Go to `App Settings →  On-Premise Hosting` in Mesibo Console. Here you will find two sections: `Enable On-Premise` and `On-Premise Configuration`.  Enter all the required configuration for your On-Premise setup in the configuration section.

> Warning: **DO NOT turn ON** the `Enable On-Premise` switch before configuring and running Mesibo On-Premise.

![Console Screenshot](/images/on-premise/on-premise-settings.jpg)

Following are the configuration entities: 

- Your Hostname - All your users will connect to this hostname and hence ensure that it is correct.

- Database Information - You will need a database for Mesibo, and a user who has all privileges for accessing and modifying it. Mesibo requires this information (database host, name, username and password) to access the database.

![Config Complete](/images/on-premise/config-not-running.jpg)

There are two more configuration checkboxes which you can enable if required:

- **Privately Hosted** - In this mode, mesibo console will not check for the correctness of hostname, or any network connectivity. Select this if you are running Mesibo On-Premise in your private network/intranet setup.

- **Auto Fallback** - Enable this if you want to fallback to cloud when your data-center is having an issue. Note that, some of the offline messages will not be delivered in fallback mode since Mesibo cloud has no access to your database. 

### Configuring TLS Certificate
Mesibo uses the latest Transport Layer Security (TLS) which is the successor to SSL. To enable TLS, you need to configure a valid TLS certificate with your Mesibo On-Premise installation.

> Although Mesibo can automatically generate a self-signed certificate for you, it is recommended that you configure a valid CA-signed certificate. This is especially required for JavaScript code since all the major web browsers will throw a warning when using a self-signed certificate.

You can use any existing certificate which you are using with your web server, OR purchase it from any provider of your choice, OR use [Letsencrypt](https://letsencrypt.org/) which is a free service (recommended) to get a secure certificate. 

To configure TLS/SSL certificate, you only need to provide the folder path containing your certificate (cert.pem), key (privkey.pem) and CA chain (chain.pem) files. For example,

```
$ ls /etc/letsencrypt/live/mesibo.example.com/
README  cert.pem  chain.pem  fullchain.pem  privkey.pem

```

> Note that wild card certificate is not recommended. 


### Step 4 - Run Mesibo

We are now all set to run Mesibo. You will notice that the Running Status field in console showing "Not Running".

You can now run Mesibo using following command (recommended):
```bash
$ sudo docker run -v /certs:/etc/letsencrypt/live/mesibo.example.com --net=host \
		-d mesibo/mesibo <APP_TOKEN>
```

You need to specify the APP_TOKEN which you noted in Step-3 above. For example,

![App token Mesibo Console](/images/on-premise/token.jpg)

For example,

```
$ sudo docker run -v /certs:/etc/letsencrypt/live/mesibo.example.com --net=host \
		-d mesibo/mesibo iqmr059ms2yopxhc2sayx05ba2l5cralockbicqjsvv1nsqxs0x1yclfv0qvyg1q 
```

On sucessfull launch of Mesibo, the output log should look like below:

```
E3108-082633-480 (1): Mesibo Build: Aug 29 2019 15:39:08
E3108-082633-506 (1): PID: 1
E3108-082633-775 (1): Local IP Address: 172.17.0.2
E3108-082634-580 (10): *** onp_message: On-Premise not enabled - login to Mesibo console to enable it
E3108-082634-639 (10): Generating TLS certificate for 192.168.0.107
I3108-082635-167: Starting mesibo

```

You can view logs using

```
$ sudo docker logs <CONTAINER_ID>
```

You can find the CONTAINER_ID using `docker ps` command as shown below:
	
```
$ sudo docker ps
```
```
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS               NAMES
7508d3d78992        mesibo/mesibo  "/mesibo/bin/mesibo …"   8 seconds ago       Up 7 seconds                            blissful_ramanujan
```

If your logs contain any errors indicating failure to start Mesibo, refer to the troubleshooting section.

Now, check `Running status` of your server from the 'Mesibo console → App Settings → On-Premise Hosting`. If successful, the `Running Status` field will contain your hostname instead of `Not running`.

![Enable On-Premise screenshot](/images/on-premise/config-running.jpg)

If your On-Premise server is setup properly continue with the next step, else refer to the troubleshooting section.

### Step 5 - Enable On-Premise
Now, it's time to enable On-Premise from the console. Once you enable On-Premise from the console, all your users will be notified to use your On-Premise server instead of Mesibo cloud. 

![Enable On-Premise screenshot](/images/on-premise/enable-on.jpg)

Turn on the On-Premise switch and your app users will start connecting to your On-Premise server immediately and you will start observing logs like following:

```
: login successful: root (uid xxxx) aid: (xxxx) 

```
Congratulations! You are now UP with Mesibo running on your own premise.

### Firewall Settings
Mesibo listens on TCP ports `80`, `443`, `513`, `5222`, `5228`, `4443`, and `5443`. Ensure that these ports are open on your Linux host so that your users can connect to your On-Premise server.

Depending on your Linux distribution, there are different ways to configure firewall, for example, to open port `5222', you'd issue the following command depending on your system:

- If your system has **`firewall-cmd`** (Red Hat Enterprise Linux, CentOS, Fedora, and variants) 

  ```
  $ sudo firewall-cmd --add-port 5222/tcp
  ```

- If your system has **`iptables`** (Red Hat Enterprise Linux, CentOS, Fedora, and variants) 

  ```
  $ sudo iptables -A INPUT -p tcp --dport 5222 -j ACCEPT

  ```

- If your system has **`ufw`** (Ubuntu, Debian, and variants)

  ```
  $ sudo ufw allow 5222/tcp
  ```

### Local Configuration
If you prefer not to share your database credentials with us and instead like to configure it locally, you can create a configuration file `/etc/mesibo/mesibo.conf` and configure it as follows:

```
dbhost=host
dbname=dbname
dbuser=dbusername
dbpass=dbpassword
```

> You still need to configure dummy values in the `console`.  Anything you configure in `/etc/mesibo/mesibo.conf` overrides the console configuration.

You will also need to pass an additional parameter `-v /etc/mesibo:/etc/mesibo` to docker so that mesibo can access the configuration file. 


## Deploying with a Cloud Service Provider
All major cloud service providers support running docker containers. To create an on-demand instance of Mesibo in the cloud, refer to respective documentation on running Docker containers :

- [AWS](https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/) 

- [Azure](https://azure.microsoft.com/en-us/services/container-instances/) 

- [Google Cloud](https://cloud.google.com/run/docs/deploying)

## Group Management
Since, the complete database is hosted on your server, you need to provision all group configuration. Mesibo will create two tables `groups` and `members` in the database. 

For example, you can add group to database like following:

```
mysql> insert into groups set aid='Your APP ID', name='Your Group Name', expiry='2021-01-01 00:00:00';

```

You can then add groups members using `gid` in group table and `uid` from the users table. 

## Dynamically Loadable Modules and Scripting
Mesibo On-Premise is designed by developers for developers. Its dynamically loadable module architecture lets you load your own code for deeper integration with your infrastructure, enabling you for unlimited creative possibilities. This makes Mesibo, the most compelling real-time communication platform existing today.

We will soon publish a new tutorial on this topic. 

## Troubleshooting & FAQ

For a more detailed FAQ section on On-Premise [refer](https://mesibo.com/documentation/faq/)

### I am currently using Mesibo Cloud services. If I enable On-Premise, how long will it take to switch connection to my server?
As soon as you enable On-Premise in the console, your users will start getting connected to your On-Premise server. Please ensure that you have made the necessary configuration in the console and the `Running Status` in console displays your hostname before you enable On-Premise.

### Can I switch from Mesibo On-Premise back to Mesibo Cloud Service ? 
Yes, if you would like your users to switch from On-Premise server to Mesibo cloud server, first disable On-Premise in the console and then stop your docker container which is running mesibo. Your users will now get connected to Mesibo Cloud. 

### I have enabled On-Premise and my server is running, how do I know if my app is connected to my server?

You can check the logs for your server using 
```
$ sudo docker logs CONTAINER ID 
```
When a user on your app logs-in, you'll get a login entry for that user. 

### What happens if I have enabled On-Premise and my server is not running?

There are two possibilities:

- If `Auto Fallback` is not enabled and your server is not running and you have enabled On-Premise, your users will not be able to connect and hence will fail to get your service.

- If `Auto Fallback` is enabled and your server is not running and you have enabled On-Premise, your users will switch to Mesibo cloud after a few retries. 

### What happens if a user is connected to my server and I disable On-Premise from the console?
If your server is running, your users will continue to connect to your On-Premise server since they do not know about you disabling On-Premise server. 

However, if you also stop your docker container running Mesibo, your users will be redirected to Mesibo cloud after a few retries and will connect there since you have disabled On-Premise. 

### How can I update my Mesibo On-Premise docker image?
Always ensure that you have the latest version of Mesibo Docker Image running. To update your image run:

```
$ sudo docker pull mesibo/mesibo
```

### How can I stop the docker container running Mesibo?
Get the CONTAINER_ID of the Mesibo docker container by using

```
$ sudo docker ps
```

Now, stop this container using CONTAINER_ID

```
$ sudo docker stop <CONTAINER_ID>
```

### Can I run Mesibo on a Cloud Service Provider?
All major cloud service providers support running docker containers. Please refer to respective documentation for different providers such as [AWS](https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/), [Azure](https://azure.microsoft.com/en-us/services/container-instances/), [Google Cloud](https://cloud.google.com/run/docs/deploying), etc. 

### I am confused between On-Premise Vs. Cloud offering, which one is better?
You can't go wrong with either model. While our Cloud service let you start immediately without installing anything, the On-Premise model offers ultimate flexibility, control of your data, loadable modules, interface with machine learning and AI tools and much more. The pricing is same, however, On-Premise model can work out more cost-effective as there are no charges other than per active user charges. On another hand, you pay for bandwidth and storage charges in our cloud offering.

### Getting Error: MySQL Connection Failed - Can't connect to MySQL server in server logs 
1. Ensure that database host, name and credentials are correctly configured in On-Premise configuration. 
2. Ensure that you have granted the necessary permissions to access your database from the docker container. 
3. Check your database host firewall configuration and ensure that, it is configured properly for allowing connections from docker container. 

### Getting Error: Unable to verify app token - network error in server logs
Check your firewall configuration and verify that it is configured to allow connections from your docker container. Then, restart your docker container.

