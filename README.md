# Update package lists
sudo apt-get update

# Install required dependencies
sudo apt -y install software-properties-common apache2 unzip openssh-server mysql-server curl certbot python3-certbot-apache

# Add PHP repository and install PHP 7.4 and required extensions
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt -y install php7.4 php7.4-cli php7.4-common php7.4-mysql php7.4-zip php7.4-gd php7.4-mbstring php7.4-curl php7.4-xml php7.4-bcmath php7.4-soap php7.4-opcache libapache2-mod-php7.4

# Enable Apache mods and restart services
sudo a2enmod rewrite php7.4
sudo systemctl restart apache2

# Set PHP 7.4 as default
sudo update-alternatives --set php /usr/bin/php7.4

# Apache configurations and restart
sudo systemctl enable apache2
sudo systemctl restart apache2

# Allow Apache through firewall
sudo ufw allow 'Apache Full'

# Set up SugarCRM directory
cd /var/www/html/
sudo mkdir sugar
sudo chown -R www-data:www-data sugar
sudo chmod -R 755 sugar

# Create Apache config for SugarCRM and enable site
cd /etc/apache2/sites-available/
sudo nano sugar14new.dev-techloyce.com.conf  # Edit your site config as needed
sudo a2ensite sugar14new.dev-techloyce.com.conf

# Change directory to SugarCRM folder, unzip and set permissions
cd /var/www/html/sugar/
sudo mv /path/to/SugarEnt-14.0.1.zip public/
sudo chown -R www-data:www-data /var/www/html/sugar
sudo chmod -R 755 /var/www/html/sugar

# Restart Apache after changes
sudo systemctl restart apache2

# Set up SSL with Certbot
sudo certbot --apache -d sugar14new.dev-techloyce.com

# Restart services after SSL setup
sudo systemctl restart apache2

# Install Elasticsearch (optional)
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch

# Final checks and permissions for SugarCRM
sudo chown -R www-data:www-data /var/www/html/sugar/public/
sudo chmod 644 /var/www/html/sugar/public/.user.ini

# Reboot the system if necessary
sudo reboot
