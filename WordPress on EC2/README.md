# WordPress on EC2
Amazon Elastic Compute Cloud (Amazon EC2) provides on-demand, scalable computing capacity in the Amazon Web Services (AWS) Cloud. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. On a virtual server you can install an Apache web server to host your WordPress website.

**Tasks**
- [Step 1: Create a key pair](#step-1-create-a-key-pair)
- [Step 2: Create a security group](#step-2-create-a-security-group)
- [Step 3: Create & connect to instance](#step-3-create--connect-to-instance)
- [Step 4: Install packages](#step-4-install-packages)
- [Step 5: Create a database user and database for WordPress](#step-5-create-a-database-user-and-database-for-wordPress)
- [Step 6: Create and edit the wp-config.php file](#step-6-create-and-edit-the-wp-configphp-file)
- [Step 7: Install the WordPress files under the Apache document root](#step-7-Step 7-install-the-wordpress-files-under-the-apache-document-root)
- [Step 10: Enable & Test TLS](#step-6-enable--test-tls)

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
cp wordpress/wp-config-sample.php wordpress/wp-config.php
```

2. Edit the wp-config.php file with `nano` text editor and enter values for installation.

```
nano wordpress/wp-config.php
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

