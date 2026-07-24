# 🚀 ISP Billing System — Ubuntu Installation Guide

Complete **A to Z Ubuntu installation guide** for the ISP Billing System.

This guide covers:

* Apache Web Server
* PHP
* MySQL Database
* Apache `mod_rewrite`
* ISP Billing Repository
* Database Setup
* Apache Virtual Host
* Permissions
* Troubleshooting

---

## 📋 Prerequisites

Before starting the installation, make sure your Ubuntu server has:

* Ubuntu Server 20.04 / 22.04 / 24.04
* PHP 7.4 or higher
* MySQL or MariaDB
* Apache2 Web Server
* Git
* Root or Sudo access

---

# 🛠️ Step 1 — Update Ubuntu System

Update the system packages:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade -y
```

Install basic required packages:

```bash
sudo apt install -y git curl wget unzip
```

---

# 🌐 Step 2 — Install Apache2

Install Apache Web Server:

```bash
sudo apt install apache2 -y
```

Start Apache:

```bash
sudo systemctl start apache2
```

Enable Apache on system boot:

```bash
sudo systemctl enable apache2
```

Check Apache status:

```bash
sudo systemctl status apache2
```

You can test Apache by opening:

```text
http://YOUR_SERVER_IP
```

---

# 🔁 Step 3 — Enable Apache mod_rewrite

Enable the Apache rewrite module:

```bash
sudo a2enmod rewrite
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

Verify Apache configuration:

```bash
sudo apache2ctl configtest
```

Expected output:

```text
Syntax OK
```

---

# 🐘 Step 4 — Install PHP

Install PHP and required packages:

```bash
sudo apt install -y \
php \
php-cli \
php-common \
php-mysql \
php-pdo \
php-curl \
php-mbstring \
php-xml \
php-zip \
php-gd
```

Verify PHP installation:

```bash
php -v
```

Example:

```text
PHP 8.x.x (cli)
```

Check installed PHP modules:

```bash
php -m
```

---

## ⚙️ Recommended PHP Configuration

Find the PHP version:

```bash
php -v
```

Open the Apache PHP configuration:

```bash
sudo nano /etc/php/*/apache2/php.ini
```

Recommended settings:

```ini
memory_limit = 512M
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 300
date.timezone = Asia/Dhaka
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

# 🗄️ Step 5 — Install MySQL Server

Install MySQL:

```bash
sudo apt install mysql-server -y
```

Start MySQL:

```bash
sudo systemctl start mysql
```

Enable MySQL at system boot:

```bash
sudo systemctl enable mysql
```

Check MySQL status:

```bash
sudo systemctl status mysql
```

---

## 🔐 Secure MySQL Installation

Run:

```bash
sudo mysql_secure_installation
```

Follow the instructions shown on the screen.

---

# 📥 Step 6 — Clone ISP Billing Repository

Go to the Apache web directory:

```bash
cd /var/www/html
```

Clone the repository:

```bash
sudo git clone https://github.com/Sohag1190/ISP-Billing.git
```

Check the project:

```bash
ls -la /var/www/html/ISP-Billing
```

---

# 🗃️ Step 7 — Create MySQL Database

Login to MySQL:

```bash
sudo mysql -u root -p
```

Create the database:

```sql
CREATE DATABASE hk_isp_billing
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

Verify the database:

```sql
SHOW DATABASES;
```

Exit MySQL:

```sql
EXIT;
```

---

# 📦 Step 8 — Import Database Schema

Import the database schema:

```bash
sudo mysql -u root -p hk_isp_billing \
< /var/www/html/ISP-Billing/database/schema.sql
```

Enter your MySQL password when prompted.

---

## 🌱 Optional — Import Sample Data

If the repository contains `seeds.sql`, import it:

```bash
sudo mysql -u root -p hk_isp_billing \
< /var/www/html/ISP-Billing/database/seeds.sql
```

---

## 🔍 Verify Database Tables

Login to the database:

```bash
sudo mysql -u root -p hk_isp_billing
```

Show tables:

```sql
SHOW TABLES;
```

Exit:

```sql
EXIT;
```

---

# ⚙️ Step 9 — Configure Database Connection

Open the database configuration file:

```bash
sudo nano /var/www/html/ISP-Billing/config/database.php
```

Update the database credentials:

```php
<?php

define('DB_HOST', 'localhost');

define('DB_NAME', 'hk_isp_billing');

define('DB_USER', 'root');

define('DB_PASS', 'your_password_here');
```

If your MySQL root user does not have a password:

```php
define('DB_PASS', '');
```

> ⚠️ For production environments, it is strongly recommended to create a separate MySQL user instead of using the `root` user.

---

# 🔐 Step 10 — Set Proper File Permissions

Set Apache as the project owner:

```bash
sudo chown -R www-data:www-data \
/var/www/html/ISP-Billing
```

Set standard permissions:

```bash
sudo chmod -R 755 \
/var/www/html/ISP-Billing
```

If the application requires writable directories, use:

```bash
sudo chmod -R 775 \
/var/www/html/ISP-Billing/storage
```

If the `storage` directory does not exist, skip this command.

---

# 🌍 Step 11 — Configure Apache Virtual Host

Creating a Virtual Host is recommended for production deployments.

Create a new Apache configuration:

```bash
sudo nano /etc/apache2/sites-available/isp-billing.conf
```

Add:

```apache
<VirtualHost *:80>

    ServerName localhost

    DocumentRoot /var/www/html/ISP-Billing/public

    <Directory /var/www/html/ISP-Billing/public>

        Options Indexes FollowSymLinks

        AllowOverride All

        Require all granted

    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/isp-billing-error.log

    CustomLog ${APACHE_LOG_DIR}/isp-billing-access.log combined

</VirtualHost>
```

Save and exit:

```text
CTRL + X
Y
ENTER
```

---

# ✅ Step 12 — Enable ISP Billing Virtual Host

Enable the site:

```bash
sudo a2ensite isp-billing.conf
```

Optional: disable the default Apache site:

```bash
sudo a2dissite 000-default.conf
```

Test Apache configuration:

```bash
sudo apache2ctl configtest
```

Expected output:

```text
Syntax OK
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

# 🌐 Step 13 — Access the ISP Billing System

## Option 1 — Without Virtual Host

Open:

```text
http://YOUR_SERVER_IP/ISP-Billing/
```

Or locally:

```text
http://localhost/ISP-Billing/
```

---

## Option 2 — With Apache Virtual Host

Open:

```text
http://localhost/
```

If you are using a real domain:

```text
http://billing.yourdomain.com
```

---

# 🔒 Optional — Production HTTPS Setup

For a production domain, install Certbot:

```bash
sudo apt install certbot python3-certbot-apache -y
```

Generate an SSL certificate:

```bash
sudo certbot --apache \
-d billing.example.com
```

Test automatic certificate renewal:

```bash
sudo certbot renew --dry-run
```

---

# 🔧 Troubleshooting

## ❌ 404 Not Found

Enable `mod_rewrite`:

```bash
sudo a2enmod rewrite
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

Check the Apache DocumentRoot:

```text
/var/www/html/ISP-Billing/public
```

Make sure the directory exists:

```bash
ls -la /var/www/html/ISP-Billing/public
```

---

## ❌ Database Connection Error

Check the database configuration:

```bash
sudo nano \
/var/www/html/ISP-Billing/config/database.php
```

Verify:

```php
DB_HOST
DB_NAME
DB_USER
DB_PASS
```

Test MySQL login:

```bash
mysql -u root -p hk_isp_billing
```

Check database:

```sql
SHOW TABLES;
```

---

## ❌ Permission Denied

Run:

```bash
sudo chown -R www-data:www-data \
/var/www/html/ISP-Billing
```

Then:

```bash
sudo chmod -R 755 \
/var/www/html/ISP-Billing
```

---

## ❌ PHP Errors

Check Apache error logs:

```bash
sudo tail -f /var/log/apache2/error.log
```

Check ISP Billing-specific logs:

```bash
sudo tail -f \
/var/log/apache2/isp-billing-error.log
```

---

## ❌ Apache Configuration Error

Run:

```bash
sudo apache2ctl configtest
```

Expected:

```text
Syntax OK
```

Then restart:

```bash
sudo systemctl restart apache2
```

---

# 🔐 Recommended Production Security

For a production ISP billing system:

* Use a strong database password
* Avoid using the MySQL `root` user
* Enable HTTPS/SSL
* Keep Ubuntu updated
* Configure a firewall
* Do not expose MySQL port `3306` publicly
* Create regular database backups
* Protect configuration files
* Use SSH key authentication
* Restrict admin access where possible

---

# 🧱 Basic Firewall Configuration

Install UFW:

```bash
sudo apt install ufw -y
```

Allow SSH:

```bash
sudo ufw allow 22/tcp
```

Allow HTTP:

```bash
sudo ufw allow 80/tcp
```

Allow HTTPS:

```bash
sudo ufw allow 443/tcp
```

Enable the firewall:

```bash
sudo ufw enable
```

Check status:

```bash
sudo ufw status
```

---

# 💾 Database Backup

Create a database backup:

```bash
mysqldump -u root -p hk_isp_billing \
> hk_isp_billing_backup.sql
```

Restore the database:

```bash
mysql -u root -p hk_isp_billing \
< hk_isp_billing_backup.sql
```

---

# 🔄 Update the Application

Go to the project directory:

```bash
cd /var/www/html/ISP-Billing
```

Pull the latest code:

```bash
sudo git pull origin main
```

Fix permissions:

```bash
sudo chown -R www-data:www-data \
/var/www/html/ISP-Billing
```

Restart Apache:

```bash
sudo systemctl restart apache2
```

---

# 🧪 Final Installation Checklist

* [ ] Ubuntu updated
* [ ] Apache2 installed
* [ ] Apache service running
* [ ] `mod_rewrite` enabled
* [ ] PHP installed
* [ ] Required PHP extensions installed
* [ ] MySQL installed
* [ ] MySQL secured
* [ ] ISP Billing repository cloned
* [ ] Database created
* [ ] Database schema imported
* [ ] Sample data imported if required
* [ ] Database configuration completed
* [ ] File permissions configured
* [ ] Apache Virtual Host configured
* [ ] Apache configuration tested
* [ ] Application accessed successfully
* [ ] Firewall configured
* [ ] Database backup created

---

# ⚡ Quick Installation Commands

```bash
sudo apt update && sudo apt upgrade -y

sudo apt install -y \
apache2 \
mysql-server \
php \
php-cli \
php-common \
php-mysql \
php-pdo \
php-curl \
php-mbstring \
php-xml \
php-zip \
php-gd \
git \
curl \
wget \
unzip

sudo a2enmod rewrite

sudo systemctl enable apache2
sudo systemctl enable mysql

sudo systemctl restart apache2

cd /var/www/html

sudo git clone \
https://github.com/Sohag1190/ISP-Billing.git

sudo chown -R www-data:www-data \
/var/www/html/ISP-Billing

sudo chmod -R 755 \
/var/www/html/ISP-Billing
```

---

# 🎉 Installation Complete

Your **ISP Billing System** should now be ready to run on Ubuntu.

Access the application using:

```text
http://localhost/ISP-Billing/
```

Or:

```text
http://YOUR_SERVER_IP/ISP-Billing/
```

---

## 👨‍💻 Author

**Md. Abu Musa**

System Network Engineer & Administrator

* MikroTik
* Cisco
* Juniper
* BGP & Routing
* ISP Infrastructure
* Ubuntu & Linux
* Docker
* Cacti
* LibreNMS
* Web Development
* ISP Automation

---

## ⭐ Support

If this project is useful, please consider giving the repository a ⭐ Star.

For bugs, issues, or feature requests, please open a GitHub Issue.

---

## 📜 License

Please review the original repository license before using, modifying, or redistributing this project.
