Step 1: Install apache2

	→ sudo apt update
	→ sudo apt upgrade
	→ sudo apt-get install apache2




Step 2: Install mysql server

	→ sudo apt-get install mysql-server
  
Set the root passwort to something you’ll remember

  	→ sudo mysql_secure_installation
	
  	Use existing password? 		ENTER
  	Remove anonymous users? 	Y
  	Disallow root login remotely? 	Y
  	Remove test database? 		Y
  	Reload privileges?		Y

Create wordpress database
	
	→ sudo mysql -u root -p
	
Enter the password you created

	→ CREATE DATABASE wordpress;
	→ GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
	→ FLUSH PRIVILEGES;
	→ EXIT



Step 3: Install PHP stack

	→ sudo apt-get install software-properties-common
	→ sudo add-apt-repository ppa:ondrej/php
	→ sudo apt update
	→ sudo apt install php7.2 libapache2-mod-php7.2 php7.2-common php7.2-mbstring php7.2-xmlrpc php7.2-soap php7.2-gd php7.2-xml php7.2-intl php7.2-mysql php7.2-cli php7.2-zip php7.2-curl



Step 4: Configure php

	→ sudo nano /etc/php/7.2/apache2/php.ini
	
Add the following to the beginning of php.ini under the [PHP] heading

	file_uploads = On
	allow_url_fopen = On
	memory_limit = 256M
	upload_max_filesize = 100M
	max_execution_time = 360
	date.timezone = America/Chicago
	
Save and exit
	
	→ ctrl + O
	→ ENTER
	→ ctrl + X

Restart apache

	→ sudo systemctl restart apache2



Step 5: Create wordpress.conf file 
 
	→ cd /etc/apache2/sites-available/
	→ sudo cp 000-default.conf wordpress.conf 
	→ sudo nano wordpress.conf

Make sure your wordpress.conf looks something like this

	<VirtualHost *:80>

		ServerAdmin admin@example.com
		DocumentRoot /var/www/html/wordpress/
		ServerName example.com
		ServerName www.example.com

		<Directory /var/www/html/wordpress/>
			Options +FollowSymlinks
			AllowOverride All
			Require all granted
		</Directory>

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

	</VirtualHost>

Save and exit

	→ ctrl + O
	→ ENTER
	→ ctrl + X
	
	

Step 6: Enable rewrite and wordpress.conf

	→ sudo a2ensite wordpress.conf
	→ sudo a2dissite 000-default.conf
	→ sudo a2enmod rewrite
	→ sudo systemctl restart apache2


Step 7: Download and extract Wordpress into your root

Navigate to your root directory

     	→ cd /var/www/html
	→ sudo curl -O https://wordpress.org/latest.tar.gz 
	→ sudo tar xzvf latest.tar.gz




Step 8: Configure Wordpress

	→ sudo mkdir wordpress/wp-content/upgrade
	→ sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
	→ sudo nano wordpress/wp-config.php

Your MYSQL section should look something like this

	/** The name of the database for WordPress */
	define( 'DB_NAME', 'wordpress' );

	/** MySQL database username */
	define( 'DB_USER', 'wordpressuser' );

	/** MySQL database password */
	define( 'DB_PASSWORD', 'password' );

	/** MySQL hostname */
	define( 'DB_HOST', 'localhost' );

	/** Database Charset to use in creating database tables. */
	define( 'DB_CHARSET', 'utf8' );

	/** The Database Collate type. Don't change this if in doubt. */
	define( 'DB_COLLATE', '' );



Step 9: Update permissions

	→ sudo chown -R www-data:www-data /var/www/html/wordpress
	→ sudo find /var/www/html/wordpress/ -type d -exec chmod 750 {} \;
	→ sudo find /var/www/html/wordpress/ -type f -exec chmod 640 {} \;



Step 10: Generate and input secret key
	
	→ curl -s https://api.wordpress.org/secret-key/1.1/salt/
	
Copy the output

	→ sudo nano /var/www/html/wordpress/wp-config.php

Replace the following with your copied key

	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');

Save and exit

	→ ctrl + O
	→ ENTER
	→ ctrl + X
