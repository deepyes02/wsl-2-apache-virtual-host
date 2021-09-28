# APACHE Web Server on WSL-2 / Ubuntu #

## Configure Apache2 Web Server with Virtual Hosts ##
Linux commands and configurations, starting with the freshly installed apache server and php that shows up on localhost.

## Requirements ##
1. Ubuntu in WSL-2 (Development Env)
2. Fresh Apache2 Installation.
3. PHP 7^ installed and ready

## Codes ##
1. Document Directory should look like this
```txt
/var/www/
├── html
│   └── index.php
├── deepesh.test
│   └── public_html
│   	└── index.html
├── shisir.test
│   └── public_html
│   	└── index.html
```
2. You can achieve this my a one liner in wsl
```bash
# make directories
sudo mkdir -p /var/www/deepesh.test/public_html
sudo mkdir -p /var/www/shisir.test/public_html
# make html files and code a little
touch /var/www/deepesh.test/public_html/index.html
touch /var/www/shisir.test/public_html/index.html
```

3. Then code some html in each
```html
<html>
	<head>
		<title>deepesh.test</title>
	</head>
	<body>
		<h2>Hello World from deepesh.test</h2>
	</body>
</html>
```
4. do the same with shisit.test
```bash
/var/www/shisir.test/public_html/index.html
```
5. Also make sure there's something in /var/www/html/index.php (index.html) which shows up when you type 'localhost' in the browser. A simple __'Hello World from localhost'__ would do. 

## Starting Virtual Host ##
Virtual Hosting is a real life configuration, since web applications are served through such methods in production. Hence learning this is useful. On windows, a simple application like XAMPP and/or laragon is enough to configure, however, in production environment, all these things need to be done with hand. So I hope this is a great hands on experience. 

6. Provide ownership and execution/read/write access to 'www-data' instead of 'root' or 'your-user-name' to fix apache permission issues to domains.
```bash
sudo chown -R www-data: /var/www/deepesh.test
sudo chown -R www-data: /var/www/shisit.test
sudo chown -R www-data: /var/www/html
```

7. Enable your user to edit apache files (Optional but recommended)
```bash
# This will enable you to edit configuration files inside the apache2 server without permission issues in vs code and other editors. 
sudo chown -R deepesh /etc/apache2/
```

8. Create a Virtual Host configuration file in /etc/apache2/sites-available/ directory
```bash
#remember to create the file ending with '.conf' extension followed by previously defined domain name
cd /etc/apache2/sites-available
touch deepesh.test.conf
nano deepesh.test.conf  			#	I'm using vs code remote extension
# code deepesh.test.conf
# or vim deepesh.test.conf
# nano vim code all are good as long as they work
```

9. Fill the file with the following code
```conf
<VirtualHost *:80>
    ServerName deepesh.test
    ServerAlias www.deepesh.test
    ServerAdmin webmaster@deepesh.test
    DocumentRoot /var/www/deepesh.test/public_html

    <Directory /var/www/deepesh.test/public_html>
        Options -Indexes +FollowSymLinks
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/deepesh.test-error.log
    CustomLog ${APACHE_LOG_DIR}/deepesh.test-access.log combined
</VirtualHost>
```

10. Enable the site. There are two ways to do it.
```bash
# 1. Create a symlink manually
sudo ln -s /etc/apache2/sites-available/domain1.com.conf /etc/apache2/sites-enabled/

# 2. Use a2ensite site script provided by apache
sudo a2ensite deepesh.test

## Result
#	deepesh@LAP-DEEPESH:/etc/apache2$ sudo a2ensite deepesh.test
#	Enabling site deepesh.test.
#	To activate the new configuration, you need to run:
#	sudo service apache2 reload
```

11. Test your configuration for any errors
```bash
sudo apachectl configtest
## Outputs
# Syntax OK
## if the syntax OK does not return there is some mistake. Check your code again 
```

12. Restart the Apache server and open the link in the browser
```bash
sudo service apache2 restart
```

13. Check if you can access the server through curl
```bash
curl deepesh.test
# should output the following: 
#	<!DOCTYPE html>
#	<html>
#        <head>
#                <title>deepesh.test</title>
#        </head>
#        <body>
#                <h2>Hello world from deepesh.test</h2>
#        </body>
#	</html>
```

14. Update the host name and ip on windows host file. Open powershell or cmd
```powershell
#powershell
# check wsl ip
wsl hostname -I
# Output
# 172.20.81.56
```

12. Open windows host file
C:\Windows\System32\drivers\etc\hosts with notepad or other editors
```powershell
# powershell
notepad hosts
```

13. In the hosts file, assuming our wsl ip is '172.20.81.56', enter the following
__C:\Windows\System32\drivers\etc\hosts__

```txt
172.20.58.21		deepesh.test		#wsl magic
```
*make sure the above line is not commented with '#'.*

### Windows Host File Example ##
![Windows Host File](/personal-folder/host.png "host file")
14. Enter wsl and restart apache just for fun
```bash
# bash
sudo service apache2 restart
```

15. Visit 'deepesh.test' on your windows browser

### Virtual Host Running Successfully ###
![Chrome running virtual Host](/personal-folder/browser.png "browser running domain")


by - *Deepesh Dhakal*