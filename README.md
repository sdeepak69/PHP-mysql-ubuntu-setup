# PHP-mysql-ubuntu-setup
All command for install php and mysql on ubuntu server


# For More info or Source : https://websiteforstudents.com/install-phpmyadmin-latest-version-on-ubuntu-16-04-18-04-with-apache2-mariadb-and-php-7-2/ 

# For Mod rewrite enable : https://www.digitalocean.com/community/tutorials/how-to-rewrite-urls-with-mod_rewrite-for-apache-on-ubuntu-16-04

Install PhpMyAdmin Latest Version On Ubuntu 16.04 / 18.04 With Apache2, MariaDB And PHP 7.2
Want to manually install the latest versions of phpMyAdmin on Ubuntu? This brief tutorial shows students and new users how to download the latest version of phpMyAdmin package and manually install and configure it on Ubuntu 16.04 /18.04 with Apache2, MariaDB and PHP 7.2 or PHP 7.3 support…

phpMyAdmin packages are available via Ubuntu default repositories… However, the version that comes with Ubuntu might not necessarily be the latest.. In order to get the latest version, you may have to manually download the archived package from its website and install it and that’s what this brief tutorial is going to show you….

For those who don’t know, phpMyAdmin is a free software tool written in PHP, intended to handle the administration of MySQL over the Web…. phpMyAdmin supports a wide range of operations on MySQL and MariaDB….When you’re ready to manually install phpMyAdmin, follow the steps below:

# Step 1: Install Apache2 HTTP Server
phpMyAdmin needs a web server to function.. A popular open source web server is Apache2.. Run the commands below to install it on Ubuntu…

<pre> sudo apt update
sudo apt install apache2 </pre>
After installing Apache2, the commands below can be used to stop, start and enable Apache2 service to always start up with the server boots…

<pre> sudo systemctl stop apache2.service
sudo systemctl start apache2.service
sudo systemctl enable apache2.service </pre>
Now that Apache2 is installed…. to test whether the web server is working, open your browser and browse to the URL below…

http://localhost

Apache2 Test Page

If you see the page above, then Apache2 is successfully installed…
# Step 2: Install MariaDB Database Server
Since we’re going to be managing MariaDB databases via phpMyAdmin, run the commands below to install MariaDB database server on Ubuntu…

<pre> sudo apt-get install mariadb-server mariadb-client </pre>
For Mysql 
<pre> sudo apt-get install mysql-server</pre>

After installing MariaDB, the commands below can be used to stop, start and enable MariaDB service to always start up when the server boots…

Run these on Ubuntu 16.04 LTS

<pre> sudo systemctl stop mysql.service
sudo systemctl start mysql.service
sudo systemctl enable mysql.service </pre>

Run these on Ubuntu 18.10 and 18.04 LTS

<pre>sudo systemctl stop mariadb.service
sudo systemctl start mariadb.service
sudo systemctl enable mariadb.service</pre>

Next, run the commands below to secure the database server with a root password if you were not prompted to do so during the installation…

<pre>sudo mysql_secure_installation</pre>

When prompted, answer the questions below by following the guide.

Enter current password for root (enter for none): Just press the Enter
Set root password? [Y/n]: Y
New password: Enter password
Re-enter new password: Repeat password
Remove anonymous users? [Y/n]: Y
Disallow root login remotely? [Y/n]: Y
Remove test database and access to it? [Y/n]:  Y
Reload privilege tables now? [Y/n]:  Y
Now that MariaDB is installed, to test whether the database server was successfully installed, run the commands below…

<pre>sudo mysql -u root -p</pre>

type the root password when prompted…

mariadb welcome

If you see a similar screen as shown above, then the server was successfully installed…

# Step 3: Install PHP Script
In order to get phpMyAdmin working, you’ll need to install PHP and related modules…

However, PHP 7.2 may not be available in Ubuntu default repositories… To run PHP 7.2 on Ubuntu 16.04 and previous, you may need to run the commands below:

<pre> sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php </pre>
Then update and upgrade to PHP 7.2

<pre> sudo apt update</pre> 

Then run the commands below to install

<pre>sudo apt-get install php7.2 php-tcpdf php7.2-cgi php7.2-mysqli php-pear php7.2-mbstring php7.2-gettext libapache2-mod-php7.2 php7.2-common php-phpseclib php7.2-mysql</pre>

After installing the above PHP required modules, go and download PHP latest version… You can get it from the link below:

https://www.phpmyadmin.net/downloads/

When you find the version you want, run the commands below to download it.. replacing the package link.. Next, move the extracted files and create a new phpmyadmin directory…

<pre>cd /tmp
wget https://files.phpmyadmin.net/phpMyAdmin/4.8.5/phpMyAdmin-4.8.5-english.tar.gz
tar xvzf phpMyAdmin-4.8.5-english.tar.gz
sudo mv phpMyAdmin-4.8.5-english /usr/share/phpmyadmin</pre>
After that, create these directory and adjust their permissions to support Apache2…

<pre>sudo mkdir -p /var/lib/phpmyadmin/tmp
sudo mkdir /etc/phpmyadmin/
sudo chown -R www-data:www-data /var/lib/phpmyadmin</pre>

When you’re done, copy phpMyAdmin sample config file and create a new default config file using the commands below:

<pre>sudo cp /usr/share/phpmyadmin/config.sample.inc.php /usr/share/phpmyadmin/config.inc.php</pre>

After that edit the line in the file using the secret passphrase… can be anything… You can visit the website below to generate blowfish key using its key generator.

# http://www.passwordtool.hu/blowfish-password-hash-generator

<pre>sudo nano /usr/share/phpmyadmin/config.inc.php</pre>

Then edit the highlighted line:
<pre>
<?php
/* vim: set expandtab sw=4 ts=4 sts=4: */
/**
* phpMyAdmin sample configuration, you can use it as base for
* manual configuration. For easier setup you can use setup/

$cfg['blowfish_secret'] = '$2a$07$ln5gsWXQazAkMsKI691mxOVmyDOhmOmS/j8NLyfHAVB/lDKZb24fe'; /* $

/**
Scroll down the file and add a temp directory config line as shown below:

/**
 * End of servers configuration
 */
 * Directories for saving/loading files from server
 */
$cfg['UploadDir'] = '';
$cfg['SaveDir'] = '';
$cfg['TempDir'] = '/var/lib/phpmyadmin/tmp';

/**
</pre>

Save the file and exit

When you’re done… run the commands below to great phpMyAdmin Apache2 configuration file…

<pre>sudo nano /etc/apache2/conf-enabled/phpmyadmin.conf</pre>

Then copy and paste the lines below into the file and save…

<pre>Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php

    <IfModule mod_php5.c>
        <IfModule mod_mime.c>
            AddType application/x-httpd-php .php
        </IfModule>
        <FilesMatch ".+\.php$">
            SetHandler application/x-httpd-php
        </FilesMatch>

        php_value include_path .
        php_admin_value upload_tmp_dir /var/lib/phpmyadmin/tmp
        php_admin_value open_basedir /usr/share/phpmyadmin/:/etc/phpmyadmin/:/var/lib/phpmyadmin/:/usr/share/php/php-gettext/:/usr/share/php/php-php-gettext/:/usr/share/javascript/:/usr/share/php/tcpdf/:/usr/share/doc/phpmyadmin/:/usr/share/php/phpseclib/
        php_admin_value mbstring.func_overload 0
    </IfModule>
    <IfModule mod_php.c>
        <IfModule mod_mime.c>
            AddType application/x-httpd-php .php
        </IfModule>
        <FilesMatch ".+\.php$">
            SetHandler application/x-httpd-php
        </FilesMatch>

        php_value include_path .
        php_admin_value upload_tmp_dir /var/lib/phpmyadmin/tmp
        php_admin_value open_basedir /usr/share/phpmyadmin/:/etc/phpmyadmin/:/var/lib/phpmyadmin/:/usr/share/php/php-gettext/:/usr/share/php/php-php-gettext/:/usr/share/javascript/:/usr/share/php/tcpdf/:/usr/share/doc/phpmyadmin/:/usr/share/php/phpseclib/
        php_admin_value mbstring.func_overload 0
    </IfModule>

</Directory>

# Authorize for setup
<Directory /usr/share/phpmyadmin/setup>
    <IfModule mod_authz_core.c>
        <IfModule mod_authn_file.c>
            AuthType Basic
            AuthName "phpMyAdmin Setup"
            AuthUserFile /etc/phpmyadmin/htpasswd.setup
        </IfModule>
        Require valid-user
    </IfModule>
</Directory>

# Disallow web access to directories that don't need it
<Directory /usr/share/phpmyadmin/templates>
    Require all denied
</Directory>
<Directory /usr/share/phpmyadmin/libraries>
    Require all denied
</Directory>
<Directory /usr/share/phpmyadmin/setup/lib>
    Require all denied
</Directory>
</pre>

Save the file and exit

Restart Apache2 by running the commands below:

<pre>sudo systemctl restart apache2</pre>

After that, open your web browser and browse to the server hostname or IP address followed by phpmyadmin

http://example.com/phpmyadmin

You should see phpMyAdmin logon page…

phpMyAdmin Ubuntu Manual install
You won’t be able to logon with MariaDB root account…

When you attempt to logon using MariaDB root account it will fail… That’s because MariaDB and MySQL have switch their authentication method to auth_socket

The auth_socket plugin authenticates users that connect from the localhost through the Unix socket file… which prevents users from connecting with password… So, you won’t be able to connect via phpMyAdmin…

When you attempt to logon, you see the error “#1698 – Access denied for user ‘root’@’localhost’”

To fix that, run the commands below:

sudo mysql -u root

That should get you into the database server. After that, run the commands below to disable plugin authentication for the root user

use mysql;
update user set plugin='' where User='root';
flush privileges;
exit
Restart and run the commands below to set a new password.

sudo systemctl restart mariadb.service

Now try again to logon… this time it should work!

phpMyAdmin Nginx

Congratulations! You have successfully installed phpMyAdmin with Apache2, MariaDB and PHP 7.2 support…
