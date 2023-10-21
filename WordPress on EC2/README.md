# WordPress on EC2
Amazon Elastic Compute Cloud (Amazon EC2) provides on-demand, scalable computing capacity in the Amazon Web Services (AWS) Cloud. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. On a virtual server you can install an Apache web server to host your WordPress website.

**Tasks**
- [Step 1: Create a key pair](#step-1-create-a-key-pair)
- [Step 2: Create a security group](#step-2-create-a-security-group)
- [Step 3: Create & connect to instance](#step-3-create--connect-to-instance)
- [Step 4: Install packages](#step-4-install-packages)
- [Step 5: Create a database user and database for WordPress](#step-5-create-a-database-user-and-database-for-wordPress)
- [Step 6: Create and edit the wp-config.php file](#step-6-create-and-edit-the-wp-configphp-file)
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
