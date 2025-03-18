

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
sudo find /var/www/html/sugar/ -type d -exec chmod 755 {} \;
sudo find /var/www/html/sugar/ -type f -exec chmod 644 {} \;
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















The **403 Forbidden** error for your CSS file in HestiaCP (running Nginx/Apache) indicates that the file permissions, ownership, or web server configuration might be blocking access. Here’s how to fix it:

---

## **Step 1: Verify File Permissions and Ownership**
Run the following commands to make sure files have the correct permissions:

```bash
chown -R sugar:www-data /home/sugar/web/newsugar13.dev-techloyce.com/public_html
find /home/sugar/web/newsugar13.dev-techloyce.com/public_html/cache -type d -exec chmod 755 {} \;
find /home/sugar/web/newsugar13.dev-techloyce.com/public_html/cache -type f -exec chmod 644 {} \;
```
If `cache/themes/RacerX/css/style.css` is missing, try regenerating it (see **Step 3**).

---

## **Step 2: Check Apache & Nginx Configuration**
HestiaCP uses **Nginx as a reverse proxy** in front of Apache.  
You need to check both configurations.

### **For Nginx:**
Open your Nginx configuration file for the domain:
```bash
nano /etc/nginx/conf.d/newsugar13.dev-techloyce.com.conf
```
Ensure this is present:
```nginx
location / {
    try_files $uri $uri/ /index.php?$query_string;
}

location ~* \.(css|js|jpg|png|gif|ico|woff|woff2|ttf|svg|eot)$ {
    expires max;
    log_not_found off;
}
```
Save (`CTRL+X`, then `Y`, then `Enter`) and restart Nginx:
```bash
systemctl restart nginx
```

### **For Apache:**
Check if `.htaccess` is blocking access:
```bash
nano /home/sugar/web/newsugar13.dev-techloyce.com/public_html/.htaccess
```
Ensure you **don't** have `Deny from all` inside the `cache/` directory.

If needed, add:
```apache
<Directory "/home/sugar/web/newsugar13.dev-techloyce.com/public_html/cache">
    AllowOverride All
    Require all granted
</Directory>
```
Restart Apache:
```bash
systemctl restart apache2
```

---

## **Step 3: Regenerate Cache**
If the CSS file is missing or corrupted, clear and regenerate the SugarCRM cache:

```bash
cd /home/sugar/web/newsugar13.dev-techloyce.com/public_html
rm -rf cache/*
php -f repair.php
php -f quick_repair_and_rebuild.php
chmod -R 777 cache upload
```

---

## **Step 4: Check Server Logs**
If the error persists, check the logs for more details:

### **For Nginx:**
```bash
tail -f /var/log/nginx/error.log
```

### **For Apache:**
```bash
tail -f /var/log/apache2/error.log
```

Look for **403 errors** and post the relevant log lines here.

---

## **Step 5: Disable SELinux (If Enabled)**
If your server has SELinux enabled, it may block file access. Check and disable it:

```bash
sestatus
setenforce 0  # Temporarily disable SELinux
```

---

### **Final Step: Restart Services**
Restart both Apache and Nginx after making changes:

```bash
systemctl restart nginx
systemctl restart apache2
```

---

Try these steps and let me know the results! 🚀
