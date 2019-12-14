# Install WordPress on Ubuntu 16.04

In this tutorial you will find all of the required steps to install the latest version of WordPress on your Ubuntu 16.04 server.  We will install and configure supporting systems including Apache2, MySQL, and PHP 7.0 within this tutorial.  There is no need to have a functioning LAMP stack prior to following this tutorial.

## Prerequisites

* Ubuntu 16.04 server (you can download here http://releases.ubuntu.com/16.04/)

* Full SSH root access or a user with sudo privileges



## Step 1 - Connect to your server

Connect to your Ubuntu server with your credentials :

```
ssh user@ipaddress -p portnumber
```


## Step 2 - Install Apache2 (if you already have Apache2 installed, you can skip this step)


Install Apache2:

```
sudo apt-get install apache2
```

* Enter root user password, then enter y to continue

Start Apache: 

```
sudo systemctl start apache2
```

Enable Apache2 to start up automatically when the server boots:

```
sudo systemctl enable apache2
```

Check to see if the Apache server is running:

```
sudo systemctl status apache2
```

*If Apache is running, you should see **Active: active (running)***

* Enter q to exit Apache

You can also check that Apache is running by entering your server’s ip address into a web browser http://your_ip_address.  You should see an Apache welcome page if the install was successful.

Now that you have ensured Apache is running, you no longer need the Apache default page.  You can remove the index.html file from your root folder using the following command:

```
sudo rm /var/www/html/index.html 
```

## Step 3 - Install MySQL (You can skip this step if you already have MySQL installed)

Install MySQL:

```
sudo apt-get install mysql-server
```

* Enter y to continue.  You will be asked to set a password for the MySQL root user.  The root user has more privileges than other users.  It is important that you do not leave this field blank. 

I recommend that you run the following command to increase the security of your database:

```
sudo mysql_secure_installation
```

* enter root password
* You will be asked if you would like to enable VALIDATE PASSWORD PLUGIN.  Enter Y for yes, N for no.  If you chose to enable, you will be given some options to set security parameters for user passwords.  You can chose 0 (8 characters minimum), 1 (8 characters minimum, including numbers, capital letters, and special characters), or 2 (8 characters minimum, including numbers, capital letters, special characters, and dictionary checks).  You can read more about MySQL’s validate password policy here https://dev.MySQL.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy.
* Change the password for root? : N (we already set this).
* Remove anonymous users? : Y
* Disallow root login remotely? : Select N if you want remote access to the root account, choose Y if not.
* Remove test database and access to it? : Y
* Reload privilege tables now? : Y

Now that your database is set up, you can enter the following commands to start MySQL and enable it to start automatically when the server boots:

```
sudo systemctl start mysql
```

```
sudo systemctl enable mysql
```

## Step 4 -  Install PHP (if you already have php installed, you can skip this step)

Install PHP by running the following command.  This will also install some helper systems that allow PHP to connect with Apache and MySQL.

```
sudo apt-get install php7.0 libApache2-mod-php7.0 php7.0-mysql php7.0-curl php7.0-mbstring php7.0-gd php7.0-xml php7.0-xmlrpc php7.0-intl php7.0-soap php7.0-zip
```
* Enter y to continue

Next, restart Apache:

```
sudo systemctl restart apache2
```

## Step 5 - Create a WordPress Database 

Log in to MySQL as the root user:

```
sudo mysql -u root -p
```

* Enter root user password

Create a database:

```
CREATE DATABASE databasename;
```

Create a new user:

```
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
```

Grant the new user access to the database:

```
GRANT ALL PRIVILEGES ON databasename.* TO 'wordpressuser'@'localhost';
```

Check that the database was successfully created:

```
SHOW DATABASES;
```

*You should see your new database in this list.*

Exit the MySQL shell:

```
FLUSH PRIVILEGES;
```

```
EXIT;
```

Restart Apache and MySQL:

```
sudo systemctl restart apache2
```

```
sudo systemctl restart mysql
```

## Step 6 - Download WordPress

Navigate to your servers root directory, which is /var/www/html by default:

``` 
cd /var/www/html
```

Download the latest version of WordPress:

```
sudo wget -c http://wordpress.org/latest.tar.gz
```

Extract the file:

```
sudo tar -xzvf latest.tar.gz
```

*You should see the WordPress folder if you enter 'ls' into the command line*

Correct permissions for WordPress with the following commands:

```
sudo chown -R www-data:www-data /var/www/html/wordpress/
```
```
sudo chmod -R 755 /var/www/html/wordpress/
```

## Step 7 - Configure Apache2

Next we need to alter the default Apache2 configuration file

Open the configuration file:

```
sudo nano /etc/Apache2/sites-available/000-default.conf
```

Add the following text to the configuration file with your own information.  Do not remove any of the other text already in the file.

```
<Directory /var/www/html/wordpress/>
    AllowOverride All
</Directory>
```

* Save and exit the file

Enable the rewrite module:

```
sudo a2enmod rewrite
```

Restart Apache:
```
sudo systemctl restart apache2
```

## Step 8 - Configure WordPress

Rename the sample configuration file:

```
cd /var/www/html/wordpress
```

```
sudo mv wp-config-sample.php wp-config.php
```

Open the WordPress configuration file:

```
sudo nano /var/www/html/wordpress/wp-config.php
```

Enter your MySQL details in the following portion of the file:

```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wordpressuser');

/** MySQL database password */
define('DB_PASSWORD', 'user_password_here');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
```

* Save and exit the file.

Move WordPress files to the root directory:

```
cd /var/www/html
```

```
sudo rsync -av wordpress/* /var/www/html/
```


## Step 9 - Complete WordPress Installation in the Browser

Navigate to your ip address in the web browser, and you should see the WordPress setup page.  

Choose your language

Set up your account details for your WordPress site

Login, and there you are!  You have successfully created a new WordPress site.

