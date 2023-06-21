

**About LAMP**

LAMP stack is a group of open source software used to get web servers up and running. The acronym stands for Linux, Apache, MySQL, and PHP. Since the server is already running CentOS, the linux part is taken care of. Here is how to install the rest.



# LAMP

## Step 1 -- install Apache

Install Apache

```shell
$ sudo yum install httpd
```

Start Apache

```shell
$ sudo service httpd start
```

Find Server 's IP address

```shell
$ ifconfig eth0 | grep inet | awk '{ print $2 }'
```



## Step 2 -- install MySQL

As mentioned in the introduction, the Yum command to install MySQL in fact installs MariaDB. To install MySQL, we'll need to visit the MySQL community Yum Repository which provides packages for MySQL.

In a web browser, visit:

```shell
$ https://dev.mysql.com/downloads/repo/yum/
```



```shell
 $ wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

Once the rpm file is saved, we will verify the integrity of the download by running `md5sum` and comparing it with the corresponding MD5 value listed on the site:

```shell
$ md5sum mysql57-community-release-el7-9.noarch.rpm
```

Now that we've verified that the file wasn't corrupted or changed, we'll install the package:

```shell
$ rpm -ivh mysql57-community-release-el7-9.noarch.rpm
```

This adds two new MySQL yum repositories, and we can now use them to install MySQL server:

```shell
$ yum install mysql-server
```



```shell
$ sudo systemctl start mysqld
```



## Step 3 -- Configuring MySQL



```shell
$ sudo grep 'temporary password' /var/log/mysqld.log
$ 2017-02-14T03:15:45.333772Z 1 [Note] A temporary password is generated for root@localhost: .)*:mg,Ph3b(
```



```shell
$ sudo mysql_secure_installation 

Securing the MySQL server deployment.

Enter password for user root: 

The existing password for the user account root has expired. Please set a new password.

New password: 
```



## Step 4 -- Testing MySQL

We can verify our installation and get information about it by connecting with the `mysqladmin` tool, a client that lets you run administrative commands. Use the following command to connect to MySQL as **root** (`-u root`), prompt for a password (`-p`), and return the version.

```shell
$ mysqladmin -u root -p version

--------------------------------------------------------------------------------------

mysqladmin  Ver 8.42 Distrib 5.7.17, for Linux on x86_64
Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version		5.7.17
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/lib/mysql/mysql.sock
Uptime:			9 min 9 sec

Threads: 1  Questions: 14  Slow queries: 0  Opens: 113  Flush tables: 1  Open tables: 106  Queries per second avg: 0.025

```



## Step 5 -- Install PHP



```shell
sudo -y yum install php php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-snmp php-soap curl
```



```shell
sudo yum -y install  phpmyadmin
sudo systemctl restart httpd.service
```



```shell
sudo systemctl restart httpd.service
```



```shell
yum search php-v
```




```shell
sudo chkconfig httpd on
sudo chkconfig mysqld on
```













## Step 6 -- RESULTS: See PHP on your Server



To set this up, first create a new file:

```shell
sudo nano /var/www/html/info.php
```

Add in the following line:

```shell
<?php
phpinfo();
?>
```

Then Save and Exit.

Restart apache so that all of the changes take effect on your virtual server:

```shell
sudo service httpd restart
```

Finish up by visiting your php info page (make sure you replace the example ip address with your correct one): http://localhost/info.php













# Install WordPress on CentOS 7



Before you begin with this guide, there are a few steps that need to be completed first.

You will need a CentOS 7 server installed and configured with a non-root user that has `sudo` privileges. If you haven't done this yet, you can run through steps 1-4 in the [CentOS 7 initial server setup guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7) to create this account.

Additionally, you'll need to have a LAMP (Linux, Apache, MySQL, and PHP) stack installed on your CentOS 7 server. If you don't have these components already installed or configured, you can use this guide to learn [how to install LAMP on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7).

When you are finished with these steps, you can continue with the installation of WordPress.



## Step One — Create a MySQL Database and User for WordPress

The first step that we will take is in preparation. WordPress uses a relational database to manage information for the site and its users. We have MariaDB (a fork of MySQL) installed already, which can provide this functionality, but we need to make a database and a user for WordPress to work with.

To get started, log into MySQL's `root` (administrative) account by issuing this command:

```shell
mysql -u root -p
```

You will be prompted for the password that you set for the root account when you installed MySQL. Once that password is submitted, you will be given a MySQL command prompt.

First, we'll create a new database that WordPress can control. You can call this whatever you would like, but I will be calling it `wordpress` for this example.

```shell
CREATE DATABASE wordpress;
```

**Note:** Every MySQL statement or command must end in a semi-colon (`;`), so check to make sure that this is present if you are running into any issues.

Next, we are going to create a new MySQL user account that we will use exclusively to operate on WordPress's new database. Creating one-function databases and accounts is a good idea, as it allows for better control of permissions and other security needs.

I am going to call the new account `wordpressuser` and will assign it a password of `password`. You should definitely use a different username and password, as these examples are not very secure.

```shell
CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';
```

At this point, you have a database and user account that are each specifically made for WordPress. However, the user has no access to the database. We need to link the two components together by granting our user access to the database.

```shell
GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';
```

Now that the user has access to the database, we need to flush the privileges so that MySQL knows about the recent privilege changes that we've made:

```shell
FLUSH PRIVILEGES;
```

Once these commands have all been executed, we can exit out of the MySQL command prompt by typing:

```shell
exit
```

You should now be back to your regular SSH command prompt.







## Step Two — Install WordPress

Before we download WordPress, there is one PHP module that we need to install to ensure that it works properly. Without this module, WordPress will not be able to resize images to create thumbnails. We can get that package directly from CentOS's default repositories using `yum`:

```shell
sudo yum install php-gd
```

Now we need to restart Apache so that it recognizes the new module:

```shell
sudo service httpd restart
```

We are now ready to download and install WordPress from the project's website. Luckily, the WordPress team always links the most recent stable version of their software to the same URL, so we can get the most up-to-date version of WordPress by typing this:

```shell
cd ~
wget http://wordpress.org/latest.tar.gz
```

This will download a compressed archive file that contains all of the WordPress files that we need. We can extract the archived files to rebuild the WordPress directory with `tar`:

```shell
tar xzvf latest.tar.gz
```

You will now have a directory called `wordpress` in your home directory. We can finish the installation by transferring the unpacked files to Apache's document root, where it can be served to visitors of our website. We can transfer our WordPress files there with `rsync`, which will preserve the files' default permissions:

```shell
sudo rsync -avP ~/wordpress/ /var/www/html/
============================================
or
sudo cp -avr wordpress /var/www/html
sudo chmod -R 775 wordpress

```

`rysnc` will safely copy all of the contents from the directory you unpacked to the document root at `/var/www/html/`. However, we still need to add a folder for WordPress to store uploaded files. We can do that with the `mkdir` command:

```shell
mkdir /var/www/html/wp-content/uploads
```

Now we need to assign the correct ownership and permissions to our WordPress files and folders. This will increase security while still allowing WordPress to function as intended. To do this, we'll use `chown` to grant ownership to Apache's user and group:

```shell
sudo chown -R apache:apache /var/www/html/*
```

With this change, the web server will be able to create and modify WordPress files, and will also allow us to upload content to the server.



o 







## Step Three — Configure WordPress

Most of the configuration required to use WordPress will be completed through a web interface later on. However, we need to do some work from the command line to ensure that WordPress can connect to the MySQL database that we created for it.

Begin by moving into the Apache root directory where you installed WordPress:

```shell
cd /var/www/html
```

The main configuration file that WordPress relies on is called `wp-config.php`. A sample configuration file that mostly matches the settings we need is included by default. All we have to do is copy it to the default configuration file location, so that WordPress can recognize and use the file:

```shell
cp wp-config-sample.php wp-config.php
```

Now that we have a configuration file to work with, let's open it in a text editor:

```shell
nano wp-config.php
```

The only modifications we need to make to this file are to the parameters that hold our database information. We will need to find the section titled `MySQL settings` and change the `DB_NAME`, `DB_USER`, and `DB_PASSWORD` variables in order for WordPress to correctly connect and authenticate to the database that we created.

Fill in the values of these parameters with the information for the database that you created. It should look like this:

```shell
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wordpressuser');

/** MySQL database password */
define('DB_PASSWORD', 'password');
```

These are the only values that you need to change, so save and close the file when you are finished.



## Step Four — Complete Installation Through the Web Interface

Now that you have your files in place and your software is configured, you can complete the WordPress installation through the web interface. In your web browser, navigate to your server's domain name or public IP address:

```shell
http://server_domain_name_or_IP
```









```shell
> select user from mysql.user;





```





















































