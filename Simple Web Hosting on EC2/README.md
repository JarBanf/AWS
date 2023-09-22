# Simple Web Hosting on EC2
**Tasks**
- [Step 1: Create EC2 instance](#step-1-create-ec2-instance)
- [Step 2: Connect to EC2 instance](#step-2-connect-to-ec2-instance)
- [Step 3: Install Apache web server](#step-3-install-apache-web-server)
- [Step 4: Set file permissions](#step-4-set-file-permissions)
- [Step 5: Enable & Add index.html](#step-5-add-static-website)
- [Step 6: Enable & test TLS](#step-6-enable--test-tls)

### Step 1: Create EC2 instance
1. Open the Amazon `EC2` console.

<img width="860" alt="Select EC2" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1a%20select%20EC2.png?raw=true">
<br/>

2. From the EC2 console dashboard, in the Launch instance box, choose `Launch instance`.

<img width="600" alt="Launch instance" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1b%20launch%20instance.png?raw=true">
<br/>

3. Configure instance

3a. Name.

<img width="600" alt="Name" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1c%20name.png?raw=true">
<br/>

3b. Application and OS Images.

<img width="600" alt="Application and OS Images" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1d%20application%20and%20os%20images.png?raw=true">
<br/>

3c. Instance type.

<img width="600" alt="Instance type" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1e%20instance%20type.png?raw=true">
<br/>

3d. Key pair, choose `Create new key pair`.  

<img width="600" alt="Create new key pair" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1f%20create%20new%20key%20pair.png?raw=true">
<br/>

3e. Configure & `Create key pair`.  

<img width="600" alt="Configure & Create key pair" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1h%20create%20and%20download%20key%20pair.png?raw=true">
<br/>

3f. Network settings.  

<img width="600" alt="Network settings" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1i%20network%20settings.png?raw=true">
<br/>

3g. Configure storage.  

<img width="600" alt="Configure storage" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1j%20configure%20storage.png?raw=true">
<br/>

3h. Summary & `Launch instance`.  

<img width="300" alt="Summary & Launch instance" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1k%20summary%20and%20launch%20instance.png?raw=true">
<br/>

4. Instance is running.

<img width="1000" alt="Instance is running" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/1l%20instance%20running.png?raw=true">
<br/>

### Step 2: Connect to EC2 instance
1. Open the instance ancd click `Connect`.

<img width="1000" alt="Connect to instance" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/2a%20connect%20to%20instance.png?raw=true">
<br/>

2. Select `EC2 Instance Connect` and click `Connect`.

<img width="600" alt="EC2 Instance Connect" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/2b%20ec2%20instance%20connect.png?raw=true">
<br/>

3. New tab appears and now connected to instance.

<img width="500" alt="Connected to instance" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/2c%20connected%20to%20instance%20.png?raw=true">
<br/>

### Step 3: Install Apache web server
1. Perform quick update to make sure all software packages are up to date.

`sudo yum update -y`

<img width="600" alt="Quick software update" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/3a%20update%20software%20packages.png?raw=true">
<br/>

2. Install Apache web server.

`sudo yum install -y httpd`

<img width="600" alt="Install Apache web server" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/3b%20install%20apache%20web%20server.png?raw=true">
<br/>

3. Start  Apache web server.

`sudo systemctl start httpd`

4. Configure the Apache web server to start at each system boot.

`sudo systemctl enable httpd`

5. Verify that web server is on.

`systemctl status httpd`

<img width="900" alt="Verify web server is on" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/3c%20start%20enable%20verify%20web%20server%20.png?raw=true">
<br/>

6. Test web server.
   
6a. Grab the Public IPv4 address for Instance (My Web Server) using the Amazon EC2 console.

<img width="1000" alt="Grab Public IPv4 address" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/3d%20grab%20public%20ip4%20address.png?raw=true">
<br/>

6b. Open new browser tab and insert the Public IPv4 address of the instance.

<img width="1000" alt="Test web server" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/3e%20test%20web%20server.png?raw=true">
<br/>

### Step 4: Set file permissions
Apache httpd serves files that are kept in a directory called the Apache document root. The Amazon Linux Apache document root is `/var/www/html`, which by default is owned by root. To allow the `ec2-user` account to manipulate files in this directory, you must modify the ownership and permissions of the directory.
1. Add user to the `apache` group.

`sudo usermod -a -G apache ec2-user`

2. Log out and then log back in to pick up the new group, and then verify membership.

2a. Log out. 

`exit`

2b. Reconnect back to instance.

2c. Verify membership. 

`group`

<img width="700" alt="Verify membership" src="https://github.com/JarBanf/AWS-Projects/blob/main/Simple%20Web%20Hosting%20on%20EC2/screenshots/4a%20verify%20membership.png?raw=true">
<br/>

3. Change the ownership of `/var/www` and its contents to the `apache` group.

`sudo chown -R ec2-user:apache /var/www`

4. To add group write permissions and to set the group ID on future subdirectories, change the directory permissions of `/var/www` and its subdirectories.

`sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;`

Now, `ec2-user` (and any future members of the `apache` group) can add, delete, and edit files in the Apache document root, enabling you to add content, such as a static website.

### Step 5: Add static website
### Step 6: Enable & test TLS


**......IN THE MAKING :)**
