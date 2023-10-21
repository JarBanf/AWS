# WordPress on EC2
Amazon Elastic Compute Cloud (Amazon EC2) provides on-demand, scalable computing capacity in the Amazon Web Services (AWS) Cloud. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. On a virtual server you can install an Apache web server to host your WordPress website.

**Tasks**
- [Step 1: Create a key pair](#step-1-create-a-key-pair)
- [Step 2: Create a security group](#step-2-create-a-security-group)
- [Step 3: Create & connect to instance](#step-3-create--connect-to-instance)
- [Step 4: Install packages](#step-4-install-packages)
- [Step 5: Create a database user and database for WordPress](#step-5-create-a-database-user-and-database-for-wordPress)
- [Step 6: Create and edit the wp-config.php file](#step-6-create-and-edit-the-wp-configphp-file)
- [Step 7: Install the WordPress files under the Apache document root](#step-7-install-the-wordpress-files-under-the-apache-document-root)
- [Step 8: Allow WordPress to use permalinks](#step-8-allow-wordpress-to-use-permalinks)
- [Step 9: Install the PHP graphics drawing library](#step-9-install-the-php-graphics-drawing-library)
- [Step 10: Fix file permissions for the Apache web server](#step-10-fix-file-permissions-for-the-Apache-web-server)
- [Step 11: Test web server](#step-11-test-web-server)
- [Step 12: Enable & Test TLS](#step-12-enable--test-tls)

### Step 1: Create a key pair
### Step 2: Create a security group
### Step 3: Create & connect to instance
### Step 4: Install packages
1. Perform quick update to make sure all software packages are up to date.

```
sudo dnf update -y
```

2. Install the latest versions of Apache web server and PHP packages.

```
sudo dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel -y
```

3. Download the latest WordPress installation package

```
sudo wget https://wordpress.org/latest.tar.gz
```

4. Unzip and unarchive the installation package. The installation folder is unzipped to a folder called `wordpress`.

```
sudo tar -xzf latest.tar.gz
```

5. Ensure that the `httpd` and database services start at every system boot.

```
sudo systemctl enable httpd && sudo systemctl enable mariadb
```

### Step 5: Create a database user and database for WordPress
WordPress installation needs to store information, such as blog posts and user comments, in a database. This procedure helps you create your blog's database and a user that is authorized to read and save information to it.

1. Secure the database server.

The default installation of the MariaDB server has several features that are great for testing and development, but they should be disabled or removed for production servers. The mysql_secure_installation command walks you through the process of setting a root password and removing the insecure features from your installation.

1a. Start the MariaDB server.

```
sudo systemctl start mariadb httpd
```

1b. Run `mysql_secure_installation`.

```
sudo mysql_secure_installation
```

1c. When prompted, type the current root password. By default, the root account does not have a password set. Press `Enter`.

1d. Type `Y` to set a password, and type a secure password twice.

1e. Type `Y` to remove the anonymous user accounts.

1f. Type `Y` to disable the remote root login.

1g. Type `Y` to remove the test database.

1h. Type `Y` to reload the privilege tables and save your changes.

2. Log in to the database server as the root user. Enter your database root password when prompted.

```
mysql -u root -p
```

3. Create a user and password for your MySQL database. WordPress installation uses these values to communicate with MySQL database.

```
CREATE USER 'jar-ban'@'localhost' IDENTIFIED BY 'My_Strong_Password';
```

4. Create a database.

```
CREATE DATABASE `wordpress-db`;
```

5. Grant full privileges for database to the WordPress user.

```
GRANT ALL PRIVILEGES ON `wordpress-db`.* TO "jar-ban"@"localhost";
```

6. Flush the database privileges to pick up all changes.

```
FLUSH PRIVILEGES;
```

7. Exit the mysql client.

```
exit
```

### Step 6: Create and edit the wp-config.php file
The WordPress installation folder contains a sample configuration file called `wp-config-sample.php`. In this procedure, I copy this file and edit it to fit my specific configuration.

1. Copy the `wp-config-sample.php` file to a file called `wp-config.php`. This creates a new configuration file and keeps the original sample file intact as a backup.

```
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
```

2. Edit the wp-config.php file with `nano` text editor and enter values for installation.

```
sudo nano wordpress/wp-config.php
```

2a. Find the line that defines `DB_NAME` and change `database_name_here` to the database name that I created in step 5.

```
define('DB_NAME', 'wordpress-db');
```

2b. Find the line that defines `DB_USER` and change `username_here` to the database user that I created in step 5.

```
define('DB_USER', 'jar-ban');
```

2c. Find the line that defines `DB_PASSWORD` and change `password_here` to the password that I created in step 5.

```
define('DB_PASSWORD', 'My_Strong_Password');
```

2d. Find the section called `Authentication Unique Keys and Salts`. These `KEY` and `SALT` values provide a layer of encryption to the browser cookies that WordPress users store on their local machines. Basically, adding long, random values here makes your site more secure. Visit `https://api.wordpress.org/secret-key/1.1/salt/` to randomly generate a set of key values that you can copy and paste into your `wp-config.php` file.

```
define('AUTH_KEY',         'NelDy%-UlL?KCn2h]|+f9&oT5g%]=&-IIs858k|kna7~|+-h#2h^S`#Q&2=-Ne~S');
define('SECURE_AUTH_KEY',  'U /0#e,v@d0}@$5L[mA3 O>VtXV95|;6MCZStB+z|SfXV$+gIQp-%hA=7tF{Ty0s');
define('LOGGED_IN_KEY',    ';Z2@[J+#IwUwJqZ-[y?AF>*kA}{&P](S03<YO9#ZR4A7%s9s_n&L9x=9FsNH2Tfe');
define('NONCE_KEY',        '--X|7b ak7|7Q ]TmLf1bX%8 13$K2dfk;lb}-c;p@W.G~Mj-v`C9H dm;VRz@vW');
define('AUTH_SALT',        '}`2e+tOW j~XNn<4${P ^J|fiN|pV7KMf4{/YeSUKLF;C_,4Anw3m#xLdt;1(py4');
define('SECURE_AUTH_SALT', 'M_L;T$fAi|&.s8YONi5yjT2b+KtAfx~|#+t)c`Q89$d)Q%3azRd$ l/f sRg]|HE');
define('LOGGED_IN_SALT',   '-QjGu+nJkHsGZ|tHF>Ft90`XQNT;L,7Eq-qaB<kCKUYkuR9r~5Q^3)j|dD`WkJ9H');
define('NONCE_SALT',       '=%5+;nlqk3p v(o@g)OifIU`&:tn/,G|$ne ghaTKmg!:_LgjJ3/J9>M h!Kz-tf');
```

2e. Save the file and exit text editor.

### Step 7: Install the WordPress files under the Apache document root
Now that I've unzipped the installation folder, created a MySQL database and user, and customized the WordPress configuration file, I'm ready to copy the installation files to the web server document root so I can run the installation script that completes the installation.

1. Copy the contents of the wordpress installation directory (but not the directory itself) to the Apache document root.

```
sudo cp -r wordpress/* /var/www/html/
```

### Step 8: Allow WordPress to use permalinks.
WordPress permalinks need to use Apache .htaccess files to work properly, but this is not enabled by default on Amazon Linux. This procedure allows all overrides in the Apache document root.

1. Open the httpd.conf file with `nano` text editor.

```
sudo nano /etc/httpd/conf/httpd.conf
```

2. Find the section that starts with `<Directory "/var/www/html">`.

```
<Directory "/var/www/html">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>
```

3. Change the `AllowOverride None` line in the above section to read `AllowOverride All`.

```
AllowOverride All
```

4. Save the file and exit text editor.

### Step 9: Install the PHP graphics drawing library.
The GD library for PHP enables you to modify images. Install this library if you need to crop the header image for your blog.

1. Install the PHP graphics drawing library.

```
sudo dnf install php-gd
```

### Step 10: Fix file permissions for the Apache web server.
Some of the available features in WordPress require write access to the Apache document root (such as uploading media though the Administration screens).

1. Add `ec2-user` to the `apache` group.

```
sudo usermod -a -G apache ec2-user
```

2. Log out and then log back in again to pick up the new group.

```
exit
```

3. Verify membership in the `apache` group.

```
groups
```

4. Grant file ownership of `/var/www` and its contents to the `apache` user.

```
sudo chown -R apache /var/www
```

5. Grant group ownership of `/var/www` and its contents to the `apache` group.

```
sudo chgrp -R apache /var/www
```

6. Change the directory permissions of `/var/www` and its subdirectories to add group write permissions and to set the group ID on future subdirectories.

```
sudo chmod 2775 /var/www
```
```
find /var/www -type d -exec sudo chmod 2775 {} \;
```

7. Change the file permissions of /var/www and its subdirectories.

```
find /var/www -type f -exec sudo chmod 0644 {} \;
```

8. Restart the Apache web server to pick up the new group and permissions.

```
sudo systemctl restart httpd
```

### Step 11: Test web server.
1. In a web browser, type the URL of the WordPress blog. WordPress installation script will appear.

### Step 12: Enable & Test TLS
Secure Sockets Layer/Transport Layer Security (SSL/TLS) creates an encrypted channel between a web server and web client that protects data in transit from being eavesdropped on.

1. Add TLS support by installing the Apache module `mod_ssl`.

```
sudo dnf install openssl mod_ssl
```

2. After entering the following command, you will be taken to a prompt where you can enter information about your site.

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/private/apache-selfsigned.key -out /etc/pki/tls/certs/apache-selfsigned.crt
```

3. Restart Apache.

```
sudo systemctl restart httpd
```

4. Test TLS by entering the IP address or fully qualified domain name of the EC2 instance into a browser URL bar with the prefix `https://`.

Because this is a learning project I will not follow the process to obtain a trusted CA-signed certificate. A trusted CA-signed certificate not only encrypts, but also publicly authenticates you as the owner of the site.
