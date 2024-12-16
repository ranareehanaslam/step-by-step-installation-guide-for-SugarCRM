

### 1. Update package lists:
```bash
sudo apt-get update
```

### 2. Install required dependencies:
```bash
sudo apt -y install software-properties-common apache2 unzip openssh-server mysql-server curl certbot python3-certbot-apache
```

### 3. Add PHP repository and install PHP 7.4 and required extensions:
```bash
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt -y install php7.4 php7.4-cli php7.4-common php7.4-mysql php7.4-zip php7.4-gd php7.4-mbstring php7.4-curl php7.4-xml php7.4-bcmath php7.4-soap php7.4-opcache libapache2-mod-php7.4
```

### 4. Enable required Apache modules:
To enable the Apache modules that are necessary for your environment, run the following commands:

```bash
sudo a2enmod mpm_event
sudo a2enmod actions
sudo a2enmod auth_digest
sudo a2enmod authz_groupfile
sudo a2enmod cgi
sudo a2enmod expires
sudo a2enmod headers
sudo a2enmod mpm_prefork
sudo a2enmod php7.4
sudo a2enmod proxy
sudo a2enmod proxy_fcgi
sudo a2enmod proxy_http
sudo a2enmod rewrite
sudo a2enmod socache_shmcb
sudo a2enmod ssl
```

### 5. Restart Apache after enabling the modules:
```bash
sudo systemctl restart apache2
```

### 6. Set PHP 7.4 as default:
```bash
sudo update-alternatives --set php /usr/bin/php7.4
```

### 7. Apache configurations and restart:
```bash
sudo systemctl enable apache2
sudo systemctl restart apache2
```

### 8. Allow Apache through firewall:
```bash
sudo ufw allow 'Apache Full'
```

### 9. Set up SugarCRM directory:
```bash
cd /var/www/html/
sudo mkdir sugar
sudo chown -R www-data:www-data sugar
sudo chmod -R 755 sugar
```

### 10. Create Apache config for SugarCRM and enable site:
```bash
cd /etc/apache2/sites-available/
sudo nano sugar14new.sugarcrm.com.conf  # Edit your site config as needed
sudo a2ensite sugar14new.sugarcrm.com.conf
```

### 11. Change directory to SugarCRM folder, unzip and set permissions:
```bash
cd /var/www/html/sugar/
sudo mv /path/to/SugarEnt-14.0.1.zip public/
sudo chown -R www-data:www-data /var/www/html/sugar
sudo chmod -R 755 /var/www/html/sugar
```

### 12. Restart Apache after changes:
```bash
sudo systemctl restart apache2
```

### 13. Set up SSL with Certbot:
```bash
sudo certbot --apache -d sugar14new.sugarcrm.com
```

### 14. Restart services after SSL setup:
```bash
sudo systemctl restart apache2
```

### 15. Install Elasticsearch (optional):
```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```

### 16. Final checks and permissions for SugarCRM:
```bash
sudo chown -R www-data:www-data /var/www/html/sugar/public/
sudo chmod 644 /var/www/html/sugar/public/.user.ini
```

### 17. Reboot the system if necessary:
```bash
sudo reboot
```

---

