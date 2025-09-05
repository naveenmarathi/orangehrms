🚀 OrangeHRM 4.0 on Ubuntu (Apache2 + MariaDB + PHP 7.1)

📋 Table of Contents
Prerequisites
1) Install Apache2, PHP 7.1 & tools
2) Tune PHP settings
3) Download OrangeHRM and place files
4) Fix permissions
5) Create Apache Virtual Host
6) Enable site & restart Apache
7) (Optional) Create the database
8) Finish setup in browser
One-shot script (optional)
Troubleshooting

✅ Prerequisites
* A fresh Ubuntu server (18.04/20.04) with sudo access
* A domain (e.g., example.com) or use your server’s public IP
* Internet access from the server

1) Install Apache2, PHP 7.1 & tools
   # Update package index
    sudo apt update

   # Install Apache
    sudo apt install -y apache2

   # Helper package to add PPAs
    sudo apt-get install -y software-properties-common

   # Add PHP PPA that still provides PHP 7.1
   sudo add-apt-repository -y ppa:ondrej/php

   # Refresh package lists (now includes PHP 7.1 from PPA)
   sudo apt update
  
   # Install PHP 7.1
   sudo apt install -y php7.1

   # Install required PHP extensions for OrangeHRM 4.0
    sudo apt install -y \
    php7.1 php7.1-common php7.1-mbstring php7.1-xmlrpc php7.1-soap php7.1-gd \
    php7.1-xml php7.1-intl php7.1-mysql php7.1-cli php7.1-mcrypt php7.1-ldap \
    php7.1-zip php7.1-curl

   # (Optional) Install MariaDB server if you don’t have it
    sudo apt install -y mariadb-server

   Why:
   * apt update refreshes package lists.
   * apache2 is the web server that will serve OrangeHRM.
   * software-properties-common lets us add the ondrej/php PPA to get PHP 7.1 (not in default repos).
   * The PHP extensions are features OrangeHRM needs (XML, MySQL, LDAP, mbstring, etc.).
   * mariadb-server is the database engine.
2) Tune PHP settings
Open the Apache PHP config file:
sudo vi /etc/php/7.1/apache2/php.ini
Set/add these values (search by / in vi or just scroll):
file_uploads = On
allow_url_fopen = On
memory_limit = 256M
upload_max_filesize = 100M
date.timezone = America/Chicago
Why:
•	Enable file uploads and remote file opening where needed.
•	Increase memory and upload limits so large pages/files don’t fail.
•	Set a proper timezone to avoid date/time issues.
________________________________________
3) Download OrangeHRM and place files
# Get the repo that contains OrangeHRM 4.0 zip
git clone https://github.com/naveenmarathi/orangehrms.git
cd orangehrms

# Move the application zip into the web root
sudo mv orangehrm-4.0.zip /var/www

# Go to web root and unzip
cd /var/www
sudo apt install -y unzip
sudo unzip orangehrm-4.0.zip

# Rename folder to a cleaner name
sudo mv orangehrm-4.0 orangehrm
Why:
•	We clone a helper repo that provides the orangehrm-4.0.zip.
•	/var/www is the default Apache document area.
•	unzip extracts the application.
•	Renaming to /var/www/orangehrm makes paths simple.
________________________________________
4) Fix permissions
# Give Apache (www-data) ownership of the app
sudo chown -R www-data:www-data /var/www/orangehrm/

# Allow read/execute for all, write for owner
sudo chmod -R 755 /var/www/orangehrm/
Why:
•	Apache runs as www-data and needs to read/write certain files.
•	755 is a safe default for web apps (owner write; others read/execute).
________________________________________
5) Create Apache Virtual Host
Create the site config:
sudo vi /etc/apache2/sites-available/orangehrm.conf
Paste:
<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/orangehrm
    ServerName DNS Name
    ServerAlias www.DNSNAME.com

    <Directory /var/www/orangehrm/>
        Options +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
What this does:
•	Points your domain to the OrangeHRM folder.
•	AllowOverride All lets .htaccess rules work (OrangeHRM needs this).
•	Logs go to the standard Apache log directory.
🔁 Replace example.com/www.example.com with your domain, or omit ServerName/ServerAlias and access by server IP.
________________________________________
6) Enable site & restart Apache
sudo a2ensite orangehrm.conf
sudo a2enmod rewrite
# (optional) disable default site to avoid conflicts
# sudo a2dissite 000-default.conf

# Check syntax before restart
sudo apachectl configtest

# Restart Apache
sudo systemctl restart apache2.service
Why:
•	a2ensite turns your new site on.
•	rewrite module is required for pretty URLs.
•	Restart applies changes.
________________________________________
7) (Optional) Create the database
If you didn’t already set up the DB:
sudo mysql -u root

-- inside MySQL/MariaDB shell:
CREATE DATABASE orangehrm CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'orangeuser'@'localhost' IDENTIFIED BY 'StrongPasswordHere';
GRANT ALL PRIVILEGES ON orangehrm.* TO 'orangeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Why:
•	Creates a database and a dedicated user for OrangeHRM.
________________________________________
8) Finish setup in browser
Open your site:
http://example.com
# or
http://SERVER_PUBLIC_IP
