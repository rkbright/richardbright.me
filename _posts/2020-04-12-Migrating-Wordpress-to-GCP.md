---
layout:     post
title:      Migrating Wordpress to GCP
date:     2020-04-12
summary:  Summary of how to move your Wordpress workload to a GCP virtual machine. The article does not show you how to set up an instance in GCP but it does walk you through the process of installing a LAMP stack, how to sync your files, and how to install SSL certs using LetsEncrypt. Happy reading everyone! 
permalink: /migrating-wordpress-to-gcp/
tags:
  - wordpress
  - gcp
  - apache
  - linux
---

![Picture by Fikret tozak @tozakfikret](https://richardbright.me/images/wordpress-cover-pic.jpg){:class="img-responsive"}

I recently migrated my shared hosting WordPress platform from a dedicated server I was administering in the basement of my home (my personal on prem server farm) to Google Cloud Platform (GCP). Why GCP? I have several years of experience in Amazon Web Services (AWS) and could have easily replicated the process using their specialized services, but my decision to align with GCP was driven by my interest in exploring other cloud platforms as a means to broaden my experience. The<a href="https://cloud.google.com/free/#always-free" target="_blank" rel="noopener"> $<span style="text-decoration: underline;">300 account credit</span></a> also contributed in the decision making process! Thanks Google!

With infrastructure, storage and networking out of the way, I then focused on the migration strategy. I researched several blogs and professional service companies to determine the best strategy (hmm, I really mean the easiest strategy). I mostly found solutions needing plug ins, and, in many cases, a subscription for premium services. Not that there is anything wrong with premium services, I think it's great that open source software is helping drive innovation, collaboration and prosperity, but for my use case, I was looking for a cheap (meaning free) and easy way to migrate my shared hosting platform to GCP.

<strong>NOTE:</strong> The <span style="text-decoration: underline;"><a href="https://wordpress.org/" target="_blank" rel="noopener">WordPress.or</a><a href="https://wordpress.org/" target="_blank" rel="noopener">g</a></span> website has real helpful resources to reference to better understand common issues and questions. I strongly recommend anyone working with WordPress to create an account. There are hundreds of posts and threads you can review; you can even start your own topic(s) and post questions. Thanks <span style="text-decoration: underline;"><a href="https://wordpress.org/support/users/gappiah/" target="_blank" rel="noopener">@gappiah</a> </span>for your help!

In the end, I went with what I know, the command line. The process I outline may not work for the broadest of use cases or be the most efficient (please go easy on me), but it got the job done!  All of the websites I support are now running 100% in GCP!

You will need to create a GCP account to take advantage of the $300 credit. If you're reading this a few months/years from now (8/2019) - YES, Google credited new customers with $300!  You have to enter your credit card information, but don't worry, charges will not accrue until you exhaust the credited amount. Once you have an account, you will need to create a <span style="text-decoration: underline;"><a href="https://cloud.google.com/gcp/getting-started/" target="_blank" rel="noopener">GCP project</a></span>. A project forms the basis for creating, enabling and using all GCP services including managing APIs, enabling billing, adding and removing collaborators, and managing permissions for GCP resources.<br>
<br><strong>NOTE:</strong> Google has a marketplace WordPress offering with a baked in <span style="text-decoration: underline;"><a href="https://en.wikipedia.org/wiki/LAMP_(software_bundle)" target="_blank" rel="noopener">LAMP stack</a></span> (Linux, Apache, MySQL | MariaDB, &amp; PHP); however, I chose to start from a fresh image and configure everything myself - basically replicating my existing server configurations.  This may also be a good time to consider upgrading to PHP 7.* once you've migrated: 1) your server-side configurations, 2) ported your platform,  and 3) tested functionality on the older PHP version. You can run the following commands in the PHP section below if you're running an older version. You can test PHP version compatibility with your site(s) themes and plugins with tools such as <a href="https://wpengine.com/solution-center/php-compatibility-checker/" target="_blank" rel="noopener"><strong><span style="text-decoration: underline;">PHP Compatibility Checker</span></strong></a>. You can run through multiple iterations and test each PHP 7.* version to determine how upgrading to a newer version will affect your site(s).

<strong>Installing the LAMP Stack</strong>

<strong>Linux: </strong>Choose a distribution to run your hosting platform (e.g., refer to my "<span style="text-decoration: underline;"><a href="https://operationitops.com/what-is-linux-anyway-a-quick-overview-of-distributions/" target="_blank" rel="noopener">What is Linux anyway? A quick overview of distributions.</a></span>") Post. Two of the most common operating systems for hosting WordPress is Ubuntu and/or CentOS - but generally, any Linux server-based distribution should work. Just be sure to select a distribution that has a large community of support.

<strong>Apache:</strong>
<pre>##install apache
~]$ sudo yum install httpd ## install apache
~]$ sudo systemctl start httpd.service
~]$ sudo systemctl enable httpd.service ## start on boot</pre>
<strong>MariaDB:</strong>
<pre>##install mariadb
~]$ sudo yum install mariadb-server mariadb
~]$ sudo systemctl start mariadb
~]$ sudo mysql_secure_installation ##remove some dangerous defaults and lock down access
##
#Set root password? [Y/n] Y
#New password: <strong>Enter your password here</strong>
#Re-enter new password: <strong>repeat your password</strong>d
#Remove anonymous users? [Y/n] Y
#Disallow root login remotely? [Y/n] Y
#Remove test database and access to it? [Y/n] Y
#Reload privilege tables now? [Y/n] Y
##
~]$ sudo systemctl enable mariadb.service ## start on boot</pre>
<strong>PHP:</strong>
<pre><span style="color: #000000;">##Install php 7.*
~]$ sudo yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm</span>
~]$ sudo yum -y install epel-release yum-utils
~]$ php -v ##get version for next command
~]$ sudo yum-config-manager --disable remi-php## ##only needed if you're running an older version
~]$ sudo yum-config-manager --enable remi-php73## install php 7
~]$ sudo yum -y install php php-cli php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json
~]$ php -v ## check version
~]$ sudo systemctl restart httpd.service ## restart apache server</pre>
<strong><span style="color: #000000;">Other Considerations:</span></strong>
<pre>## Configure firewalld - open local ports for http(80) &amp; https(443) traffic
~]$ sudo firewall-cmd --permanent --zone=public --add-service=http 
~]$ sudo firewall-cmd --permanent --zone=public --add-service=https
~]$ sudo firewall-cmd --reload

## lock down ssh configuration
~]$ sudo vi /etc/ssh/sshd_config
## add line at the bottom of file
...
AllowUsers name1 name2 name3 ## will lock down who can ssh into the system
## also check that the following options
...
PermitRootLogin no
PasswordAuthentication no
GSSAPIAuthentication yes ##for secure ssh-based authentication

</pre>
<span style="color: #000000;">Okay, with LAMP installed, we are ready to begin the process of migrating your hosting platform to GCP. Below details my hosting platform configuration to help you better understand the source environment. </span>

<span style="color: #000000;"><strong>Source Environment:</strong> </span>
<ul>
 	<li style="list-style-type: none;">
<ul>
 	<li>Internet Service Provider (ISP): Verizon Static IP - was not on residential DHCP account</li>
 	<li>Did not use a Verizon provided router; I had CAT5 installed from the modem to the router. Verizon may give you a hard time....just be persistent, they will get it done. I had this done for the residential line as well. Resulted in much better throughput when uploading/downloading local files within your LAN.</li>
 	<li>Router: <a href="https://www.netgear.com/landings/nighthawk/" target="_blank" rel="noopener">Nighthawk® X6S Tri-Band WiFi Router With MU-MIMO</a></li>
 	<li>Server Specs
<ul>
 	<li>cpu: Intel(R) Core(TM) i7-4790 CPU @ 3.60GHz (8 cores)</li>
 	<li>memory: 15GiB</li>
 	<li>storage: 2TB</li>
</ul>
</li>
</ul>
</li>
</ul>
<b>Additional Prerequisite Tasks</b>

Log into your site and run all updates and make sure you're on the latest version of WordPress. Please note WordPress version 5.2.2 is not compatible with PHP 5.4, you will need to upgrade to PHP 5.6 for your site to render correctly. You will need access to your host terminal to complete the remaining tasks. The steps outlined below assumes only one (1) site is being migrated. If you run multiple sites, you can either repeat the steps per site or apply the tar file and database backup to all sites and databases -- this approach may take a while to transfer all of the data from source to host.

<strong>Access Your Current Hosting Terminal</strong>

Make backups of all source files and databases in the unfortunate (but all likely) event you have to roll back. You will absolutely be relieved if you did and terrified if you didn't.
<pre>~]$ sudo cp -rpv /var/www/html /home/user/backup/
~]$ mysqldump -u root -p --all-databases &gt; wp-databases.sql
    Enter password:</pre>
<b>Create a Working Directory to Collocate and Save Files</b>
<pre>~]$ mkdir gcp
~]$ cd gcp</pre>
<strong>Create a tar File of (or all) WordPress site(s)</strong>
<pre>gcp]$ sudo tar -cvzf sitename.tar /var/www/html/sitename
##use ..html/ if copying all sites 
##then update file permissions
gcp]$ sudo chown user.user sitename.tar</pre>
<strong>Get Database Information and Create Database File</strong>
<pre>gcp]$ cat /var/www/html/sitename/wp-config.php | grep DB
...
define('DB_NAME', 'database_name');
define('DB_USER', 'database_user');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'localhost');
define('DB_CHARSET', 'utf8');
define('DB_COLLATE', '');
...
##generate database file for one or all databases
##for all databases add the --all-databases option in place of the database_name
gcp]$ mysqldump -u root -p database_name &gt; database_name-date.sql</pre>
<strong>Copy Your Apache Configuration File</strong>
<pre>gcp]$ sudo cp -rpv /etc/httpd/conf.d/sitename.conf .</pre>
<strong>NOTE: </strong>You should now have three files in your gcp working directory
<pre>gcp]$ ls
...
sitename.conf database_name-date.sql sitename.tar</pre>
The next step assumes you already propagated your public keys between the source and target environments. If you're using a laptop as an intermediary between the environments, you can pull the files down from the source and then push to your target. A direct connection is likely the quickest and most efficient path and the one I recommend.

<strong>Sync Your Files</strong>
<pre>##pull method from target
##ssh into gcp server
~]$ mkdir gcp
~]$ cd gcp 
gcp]$ rsync -avzh user@source-ip-address:/home/user/gcp/sitename.tar .
##repeat for all files or use ..gcp/* to copy all files</pre>
<pre>##push method from source
##ssh into source server
##be sure to create the gcp working directory in the target server before running
gcp]$ rsync -avzh sitename.tar user@target-ip-address:/home/user/gcp
##repeat for all files or . to copy all files</pre>

<strong>Untar Files into the Default root Folder for the Web Server </strong>
<pre>gcp]$ sudo tar -xvf sitename.tar -C /var/www/html/
##change ownership to apache user and group
gcp]$ sudo chown -Rv apache.apache /var/www/html/sitename
##if more than one site use path /var/www/htlm/</pre>
<strong>Create Database, User and Load Database File(s)</strong>
<pre>create database - use the same database name, user account and password from source
gcp]$ mysql -u root -p
Enter password:
MariaDB [(none)]&gt;CREATE DATABASE database_name;
MariaDB [(none)]&gt;GRANT ALL PRIVILEGES on database_name.* to 'database_user'@'localhost' identified by 'password';
MariaDB [(none)]&gt;FLUSH PRIVILEGES;
MariaDB [(none)]&gt;EXIT;
##load the data file
gcp]$ cat database_name-date.sql | mysql -u root -p database_name</pre>

<strong>Move apache configuration file </strong>
<pre>gcp]$ sudo mv sitename.conf /etc/httpd/conf.d/
##change ownership to root
gcp]$ sudo chown root.root /etc/httpd/conf.d/sitename.conf</pre>
<strong>Update SELinux Setting so apache can Write Updates</strong>
<pre>gcp]$ sudo chcon -t httpd_sys_rw_content_t /var/www/html/sitename -R</pre>
<strong>Update DNS Setting with New IP Address</strong>

Access your domain registration dashboard and update the "A Record"(e.g., GoDaddy, Bluehost and HostGator).
Name: yourdomain.com
Value: ip-address

Updates can take anywhere from 1 minute to 20 minutes to propagate domain controllers. You can check the record of the site with the nslookup utility.
<pre>gcp]$ nslookup sitename.com
##or you can watch for the update 
gcp]$ watch -d "nslookup sitename.com"
##wait for the DNS to update before updating your ssl/tls cert</pre>
<strong>Update Your Certs if Using SSL/TLS</strong>
<pre>I use <a href="https://letsencrypt.org/" target="_blank" rel="noopener">Lets Encrypt</a> for CA
gcp]$ sudo certbot --apache
...
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator apache, Installer apache
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: sitename.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): <strong>1</strong>

Cert not yet due for renewal

You have an existing certificate that has exactly the same domains or certificate name you requested and isn't close to expiry.
(ref: /etc/letsencrypt/renewal/sitename.com.conf)

What would you like to do?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: Attempt to reinstall this existing certificate
2: Renew &amp; replace the cert (limit ~5 per 7 days)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): <strong>1</strong>

Keeping the existing certificate
Deploying Certificate to VirtualHost /etc/httpd/conf.d/wordpress3-le-ssl.conf

Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): <strong>2</strong>

Enhancement redirect was already set.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled
https://sitename.com

You should test your configuration at:
https://www.ssllabs.com/ssltest/analyze.html?d=sitename.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at:
/etc/letsencrypt/live/sitename.com/fullchain.pem
Your key file has been saved at:
/etc/letsencrypt/live/sitename/privkey.pem
Your cert will expire on 2019-10-27. To obtain a new or tweaked
version of this certificate in the future, simply run certbot again
with the "certonly" option. To non-interactively renew *all* of
your certificates, run "certbot renew"
- If you like Certbot, please consider supporting our work by:

Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
Donating to EFF: https://eff.org/donate-le
</pre>
<strong>OPTIONAL: Setup a cron Job to Auto Renew the Cert Every 90 Days</strong>
<pre>##create folder to capture logs
~]$ mkdir -p crojobs/sslUpdateLog ##cron also captures data in /var/log/cron
~]# vi /usr/bin/sslUpdate.sh
...
##paste below into file
#!/bin/bash

# Define a timestamp function
today=`date '+%Y_%m_%d__%H_%M_%S'`

certbot renew &gt; /home/user/crojobs/sslUpdateLog/sslUpdateLog_$today.txt</pre>

<strong>Update crontab</strong>
<pre>~]$ sudo crontab -e
...
##add entry into crontab
55 11 * 1/3 * /usr/bin/sslDaily.sh #update ssl certs</pre>

Okay, all your websites should be pointed to your new hosting platform in GCP (or platform of choice). I hope you found this article useful and informative in helping you to successfully migrate your hosting platform. Please feel free to send me a comment through my contact page if you have any questions.

<b>References:</b>
<ul>
 	<li><a href="https://cloud.google.com/free/#always-free">https://cloud.google.com/free/#always-free</a></li>
 	<li><a href="https://wordpress.org/support/users/gappiah/">https://wordpress.org</a></li>
 	<li> <a href="https://wordpress.org/support/users/gappiah/">https://wordpress.org/support/users/gappiah/</a></li>
 	<li><a href="https://en.wikipedia.org/wiki/LAMP_(software_bundle)">https://en.wikipedia.org/wiki/LAMP_(software_bundle)</a></li>
 	<li><a href="https://cloud.google.com/gcp/getting-started/" target="_blank" rel="noopener">https://cloud.google.com/gcp/getting-started/</a></li>
 	<li><a href="https://wpengine.com/solution-center/php-compatibility-checker/" target="_blank" rel="noopener">https://wpengine.com/solution-center/php-compatibility-checker/</a></li>
 	<li><a href="https://letsencrypt.org/" target="_blank" rel="noopener">https://letsencrypt.org/</a></li>
	 <li>Picture by Fikret tozak @tozakfikret</li>
</ul>

---
