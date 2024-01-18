# XAMPP  localhost SSL Certificate Installation Guide
XAMPP Apache + MariaDB + PHP + Perl SSL Certificate installation guide on your laptop.
You have successfully installed XAMPP on this system! Now you can start using Apache, MariaDB, PHP and other components. 
XAMPP is meant only for development purposes. It has certain configuration settings that make it easy to develop locally but that are insecure if you want to have your installation accessible to others. Thus, this guide would help you enabling SSL certificate to localhost site so that you can use for development purposes.

# Scope 
This guide would give you overview of implementing SSL certificate for your localhost site.

# By the end of the article:

You will know what is the difference between Ports 80 and 443.
You will have a working self-signed SSL certificate on a specific custom domain within your localhost.
You will get familiar with some Apache configurations.
You will learn a bit about OpenSSLand last but not least, I hope you will enjoy the tutorial.

# Ports 80 and 443 — What’s the difference?
Let’s start XAMPP and turn on Apache.
Once it starts, you can notice two Ports: 80 and 443.
Port 80 is used for HTTP by default.
Port 443 is used for HTTPS.
If you go to a website that doesn’t have an SSL/TLS certificate, the request will go through HTTP, which means Port 80 is used and the connection is not secured. It’s good to keep in mind that Port 80 is used to access an unsecured website, while Port 443 is used for secure connections. We’ll use this information a bit later in the tutorial when we configure Apache.

# Software versions
XAMPP for Windows 8.0.30, 8.1.25 & 8.2.12.
XAMPP Software download link : https://www.apachefriends.org/download.html

# Configuration Files
You must be administrator on you laptop or PC with windows OS installed. After installing XAMPP software you would define your application site url or host in webserver. In general when XAMPP apache webserver runs it would connect to localhost at port 80 by default. http://localhost:80/ --> This would be default URL. However, your application name would somthing like myapplicationname.com then you would want URL to be http://myapplicationname.com. In order to achive this you would need to change the hosts file in windows folder.
The location of hosts configuration file is C:\windows\system32\drivers\etc\hosts. You can edit this file with notepad and add entry to it at last line something like 
**127.0.0.1 myapplicationname.com** save the file. Now if you browse your application url http://myapplicationname.com it would load index.html file of your application. **Note:** You would need to configure your application inside apache webserver in order to get application working in browser.

In order to make above application SSL enabled in local laptop following files would need to change at XAMPP webserver. 
XAMPP installation folder --> C:\XAMPP unless you've selected different path. C:\xampp\apache --> This is webserver application path. 
  - **bin** folder has webserver executable file **httpd.exe** and OpenSSL executable file **openssl.exe**.
  - **conf** folder has different configuration file required for apache webserver to run on your laptop.
  - **httpd.conf** This file has configuration details of your webserver. This file is present in c:\xampp\apache\conf\ folder. The important parameters you would be editing. 
     - **Listen** - Port number of your application listen client requests
     - **LoadModule ssl_module modules/mod_ssl.so** --> Remove # from front of this line
     - **Include conf/extra/httpd-ssl.conf** --> Remove # from front of this line
     - **ServerName myapplicationname.com:80** --> modify ServerName parameter and enter your applicaiton URL here.
        save and close this file.
- **httpd.ssl** This file has SSL related configurations used by webserver. This file is present in C:\xampp\apache\conf\extra folder. 
     -  **ServerName myapplicationname.com:443**  --> modify ServerName parameter and enter your applicaiton URL here. save and close this file.
- **httpd-vhosts.conf** This file has virtual hosts related configurations used by webserver. This file is present in C:\xampp\apache\conf\extra folder. We enable the 
    SSLEngine, and specify the location of the SSLCertificateFile and SSLCertificateKeyFile. You would need to modify ServerName and Server Alias parameters. The vaules 
    would be you application URL. You can use below tags modify parameters and paste it at the end of httpd-vhosts.conf file and save it.
   ```
           <VirtualHost *:80>
              ServerName myapplicationname.com
              ServerAlias myapplicationname.com
              DocumentRoot "c:/xampp/htdocs/"
              RewriteEngine on
              RewriteCond %{SERVER_NAME} =www.myapplicationname.com [OR]
              RewriteCond %{SERVER_NAME} =myapplicationname.com
              RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
          </VirtualHost>
          
          <VirtualHost *:443>
              ServerName myapplicationname.com
              ServerAlias myapplicationname.com
              DocumentRoot "c:/xampp/htdocs/"
              SSLEngine on
              SSLCertificateFile "conf/ssl.crt/server.crt"
              SSLCertificateKeyFile "conf/ssl.key/server.key"
          </VirtualHost>
     ```
  
    ## Create the SSL Certificate
  In order to create SSL Certificate you would need to create a conf file in **C:\xampp** folder.
  1. You can copy the below code and change the values of section [__subject__] portion especially commonName_default and DNS.1 parameters to your site url and save with name as **cert.conf**.
    
    ```
                  [ req ]
                  
                  default_bits        = 2048
                  default_keyfile     = server-key.pem
                  distinguished_name  = subject
                  req_extensions      = req_ext
                  x509_extensions     = x509_ext
                  string_mask         = utf8only
                  
                  [ subject ]
                  
                  countryName                 = Country Name (2 letter code)
                  countryName_default         = IN
                  
                  stateOrProvinceName         = State or Province Name (full name)
                  stateOrProvinceName_default = KA
                  
                  localityName                = Locality Name (eg, city)
                  localityName_default        = Bangalore
                  
                  organizationName            = Organization Name (eg, company)
                  organizationName_default    = myapplicationname.com
                  
                  commonName                  = Common Name (e.g. server FQDN or YOUR name)
                  commonName_default          = myapplicationname.com
                  
                  emailAddress                = Email Address
                  emailAddress_default        = mahesh_31@hotmail.com
                  
                  [ x509_ext ]
                  
                  subjectKeyIdentifier   = hash
                  authorityKeyIdentifier = keyid,issuer
                  
                  basicConstraints       = CA:FALSE
                  keyUsage               = digitalSignature, keyEncipherment
                  subjectAltName         = @alternate_names
                  nsComment              = "OpenSSL Generated Certificate"
                  
                  [ req_ext ]
                  
                  subjectKeyIdentifier = hash
                  
                  basicConstraints     = CA:FALSE
                  keyUsage             = digitalSignature, keyEncipherment
                  subjectAltName       = @alternate_names
                  nsComment            = "OpenSSL Generated Certificate"
                  
                  [ alternate_names ]
                  
                  DNS.1       = myapplicationname.com
    ```
  2. Create a ssl_cert.bat file in C:\xampp folder with below code and save it.

     ```
             @echo off
              set /p domain="Enter Domain: "
              set OPENSSL_CONF=./apache/conf/openssl.cnf
              
              if not exist .\%domain% mkdir .\%domain%
              
              .\apache\bin\openssl req -config cert.conf -new -sha256 -newkey rsa:2048 -nodes -keyout %domain%\server.key -x509 -days 3650 -out %domain%\server.crt
              
              echo.
              echo -----
              echo The certificate was provided.
              echo.
              pause
     ```
  3. You can run the above batch file by double cliking on it.
  5. This batch file would ask for Domain name you can enter your web site URL e.g. myapplicationname.com.

 <img width="661" alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/7ac51b98-a252-409a-96ea-13742c6fd052">

> **Warning**<br>
If your inserted a value in above screen doesn't match your domain i.e. site URL then SSL certificate will not work!
     
  7. It would create a folder in with URL name in C:\xampp\myapplicationname.com folder name. This folder would contain files displayed in below image.

     <img width="512" alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/bbb474e8-4d3e-4d0f-ae3f-61410b8c017c">

  8. Copy server.crt file into C:\xampp\apache\conf\ssl.crt\ folder. In case this folder not exists then create new folder with same name.
  9. Copy server.key file into C:\xampp\apache\conf\ssl.key\ folder. In case this folder not exists then create new folder with same name.

      
# Install the SSL certificate
Go to C:\xampp\apache\conf\ssl.crt folder and open the server.crt file by double clicking.
In the first step click the Install Certificate… button.

<img width="420" alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/f476d3a8-22a7-49c8-a2ae-69bc507f8dc4">

SSL certificate wizard installation
Then choose the Local Machine option.

<img width="420"  alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/cc3343b0-f3c8-4ba9-adf4-0720d63533ca">

SSL certificate wizard Local Machine option

Then choose Place all certificates in the following store.

<img width="420"  alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/e0ff5172-6608-4092-a2d4-c36849dcfe36">

SSL certificate place certificate in store

Then click the Browse… button and select Trusted Root Certification Authorities from the Select Certificate Store window.

<img width="420" alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/43b821e0-ea6c-43f2-bac1-2e362826800e">

SSL certificate Trusted Root Certification Authorities

Click OK, then Next.

Press Finish and the installation is complete.

All good, let’s restart Apache and then go to your https://myapplicationname.com.

The site now has the 🔒 in front of the URL which means the connection is now secured and it has a valid SSL certificate.

This is what the certificate looks like from the browser.

<img width="406" alt="image" src="https://github.com/maheshmrs/xampp-localhost-ssl-cert-installation/assets/17200170/ca6699e5-a912-49ff-9d2c-479c8edbe476">

# Redirect HTTP to HTTPS
One last thing.
If we access our website through http://myapplicationname.com, it would redirect us to the HTTPS version of website. This provision is already implemented to respective conf file using above code. If you follow all the above step-by-step guide then you would be able make your localhost website SSL enable.
Kindly post your comments in case you face issues with it.
