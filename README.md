![How to install LEMP](https://github.com/LaravelDevBose/LEMP-install-and-laravel-setup-A-to-Z/blob/main/035575f2985fe451d86e717d73691e533a1a00545d7230900ed786341dc3c882.jpg)


# LEMP-Install
* Update All file <code> sudo apt-get update </code>
* Install Nginx <code>sudo apt-get install nginx</code>
* Install Mysql Server <code> sudo apt-get install mysql-server </code>
* Set Secure Mysql install<code> sudo mysql_secure_installation</code><br>
Next it will ask you if you want to change the existing password for root user. If you just set a secure password in the last step then you can also skip this question by pressing any key (other than ‘Y’). If you left the password blank or set up a silly password (like ‘password’ or ‘1234’) then go ahead and change your password now.
<br><br>
Next it will ask you to remove an anonymous user. Type Y to remove the anonymous users, this is a major security risk.
<br><br>
Now it will ask if you want to Disallow root login remotely? and you should also select Y for this.
<br><br>
Another prompt asks to Remove test database and access to it? This is a good idea to do, so type Y again for this.
<br><br>
It may also ask to reload privilege tables now? This is always a good idea, so type Y again.
<br><br>
The terminal will tell you All Done! With this announcement we now have a LEM stack, which really isn’t a think. But I thought you would like to know your progress as we just completed the M in LEMP with our MySQL instance. Now let’s finish it up and get the P.
<br><br>

* install php 7.4
<code> sudo apt install php7.4-fpm php7.4-common php7.4-mysql php7.4-xml php7.4-curl php7.4-gd php7.4-imagick php7.4-cli php7.4-imap php7.4-mbstring php7.4-opcache php7.4-zip
  </code>
* <code>sudo nano /etc/php/7.4/fpm/php.ini</code>
* Press <code>Ctrl+W</code> and search <code>cgi.fix_pathinfo=</code> and enter
* Un comment <code>cgi.fix_pathinfo=</code> and make <code>cgi.fix_pathinfo=0</code>
* <code>ctrl+x</code> and save
* Restart FPM <code>sudo systemctl restart php7.4-fpm</code>
<hr>

# Configure Nginx
* GO to <code>sudo nano /etc/nginx/sites-available/default</code>
* Add Code 
<code>
 server {
        listen 80 default_server;
        listen [::]:80 default_server;
 
        root /var/www/html/wegokart/public;
             
        index index.php index.html;

        server_name server ip or domain name;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }
                
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }}
</code>

* Check Status <code>sudo nginx -t</code>
* Reload Nginx <code>sudo systemctl reload nginx</code>
<hr>

# Config Mysql Server
* login on mysql: <code>mysql -u root -p'yourpassword'</code>
* Create User: <code>CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'user_password';</code>
* GRANT ALL PRIVILEGES: <code>GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost';</code>
* <code>FLUSH PRIVILEGES;</code>
* exit and login again with new user
* Create Database: <code>CREATE DATABASE dbname;</code>
* Show Databases <code>SHOW DATABASES;</code>
* update root password <code>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Current-Root-Password';
FLUSH PRIVILEGES;</code>
* show auth state of use <code>SELECT user,authentication_string,plugin,host FROM mysql.user;</code>
<hr>

# Generate ssh key on server
* Run <code>ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"</code>
* Copy key <code>cat ~/.ssh/id_rsa.pub</code>
* add key to your git
<hr>

# Install Composer 
* Go to <code>cd ~ </code>
* Install Composer <code>curl -sS https://getcomposer.org/installer | php</code>
* Move composer <code>sudo mv composer.phar /usr/local/bin/composer</code>
<hr>

# Project File Setup
* git clone your project git url
* composer install <code>composer install</code>
<hr>

## Laravel Permissions
* Owner Permission: <code>sudo chown -R :www-data /var/www/html/project_folder</code>
* Storage Permission: <code>sudo chmod -R 775 /var/www/html/project_folder/storage</code>
* Cache File Permission: <code>sudo chmod -R 775 /var/www/html/project_folder/bootstrap/cache</code>
<hr>

## Set Up and config .env file
* cp .env.example .env
* php artisan key:generate
* config your .env

## Creating server for each subdomain
* go to <code>cd /etc/nginx/sites-available</code>
* Run <code> sudo nano dashboard.example.com </code>
* Copy and paste the configurations given below

<code> 
  ## For dashboard.example.com subdomain
server {
        listen 80;
        listen [::]:80;
        root /var/www/dashboard.example.com;
        index index.html;
        server_name dashboard.example.com www.dashboard.example.com;
}
</code>

* Make a symbolic link

<code> sudo ln -s /etc/nginx/sites-available/dashboard.example.com /etc/nginx/sites-enabled/dashboard.example.com </code>

*  Testing and Reload Server
<code>
 sudo nginx -t
 sudo systemctl restart nginx
</code>

# SSL configuration

<code>
  
  server { listen 443 ssl default_server;

        root /var/www/html/medi;

        # Add index.php to the list if you are using PHP
        index index.php index.html;

        server_name example.xyz www.example.xyz;

        ssl_certificate /etc/ssl/medi_medisquare/example_xyz_chain.crt;
        ssl_certificate_key /etc/ssl/server.key;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }


        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}

server {
        listen 80;
        server_name example.xyz;                             
        return 301 https://www.$host$request_uri;
  }
  
</code>
