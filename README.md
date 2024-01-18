# xampp-localhost-ssl-cert-installation-guide
XAMPP Apache + MariaDB + PHP + Perl SSL Certificate installation guide on your laptop.
You have successfully installed XAMPP on this system! Now you can start using Apache, MariaDB, PHP and other components. 
XAMPP is meant only for development purposes. It has certain configuration settings that make it easy to develop locally but that are insecure if you want to have your installation accessible to others.

Start the XAMPP Control Panel to check the server status.
# Scope 
This guide would give you overview of implementing SSL certificate for your localhost site.
# Software versions
XAMPP for Windows 8.0.30, 8.1.25 & 8.2.12
XAMPP Software download link : https://www.apachefriends.org/download.html
# Configuration Files
You must be administrator on you laptop or PC with windows OS installed. After installing XAMPP software you would define your application site url. In general when XAMPP apache webserver runs it would connect to localhost at port 80 by default. http://localhost:80/ --> This would be default URL. However, your application name would somthing like myapplicationname.com then you would want URL to be http://myapplicationname.com. In order to achive this you would need to change the hosts file in windows folder.
The location of hosts configuration file is C:\windows\system32\drivers\etc\hosts. You can edit this file with notepad and add entry to it at last line something like 
127.0.0.1 myapplicationname.com save the file.
