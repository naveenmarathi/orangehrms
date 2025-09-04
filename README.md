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

apt update refreshes package lists.

apache2 is the web server that will serve OrangeHRM.

software-properties-common lets us add the ondrej/php PPA to get PHP 7.1 (not in default repos).

The PHP extensions are features OrangeHRM needs (XML, MySQL, LDAP, mbstring, etc.).

mariadb-server is the database engine.
