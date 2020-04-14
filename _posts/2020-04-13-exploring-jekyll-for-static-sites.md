---
layout: post
title: Exploring Jekyll for Static Sites
date: 2020-04-13 23:28
summary: How to host multiple static websites using Jekyll and Apache. 
categories: jekyll pixyll
---

I have several years of experience hosting multiple [Wordpress](https://wordpress.org/) websites on a personal [LAMP](https://stackoverflow.com/questions/10060285/what-is-a-lamp-stack) stack server I administer from my home (I recenlty migrated this workload to GCP, see blog post [Migrating Wordpress to GCP](https://richardbright.me/jekyll/pixyll/2020/04/12/Migrating-Wordpress-to-GCP/)). For readers unfamiliar with Wordpress, it is a great option for hosting [dynamic websites](https://en.wikipedia.org/wiki/Dynamic_web_page) for a business, a personal blog, or an enterprise content manageemnt system. Wordpress is an open source platform and widley popular for it's quick and easy setup, easy administration, and abundance of functional options (plug-ins). Figures posted by [W3Tech](https://w3techs.com/technologies/details/cm-wordpress) show Wordpress powering over 35% of all websites on the internet. 

<strong>Static vs. Dynamic Website Architecture</strong>
![Static vs. Dynamic Website Architecture](https://richardbright.me/images/staticdynamic.png){:class="img-responsive"}

The figure above from [ApacheBooster](https://www.apachebooster.com/) visualizes the difference between a static and dynamic website architecture, Scheme A shows how web calls are served back to the requestor directly whereas Scheme B shows the additional layers/dependencies when a web call is made to a dynaminc site. Each of those layers does result in additional latency, which is the time it takes before you receive a responce back from the server. 

So why [Jekyll](https://jekyllrb.com/), I was looking to move some of the content I host using Wordpress to a more lightweight platform that is responsive and easy to install and post updates. Additionally, unlike Wordpress, there is no need to update themes, plug-ins, and databases - so idelally less risk of your site getting compromised, and if it does, it's easy enough to regenerate your blog and/or site and redeploy the build files to your hosting server. It's pretty simple.

I'm not advocating for static sites over dynamic sites or vise versa, both have use cases where one would be more desiarable over the other. For the remainder of this article I will walk you though how to setup a virtual hosting server where you can host one or many sites concurrenlty. 

<span style="color: #000000;"><strong>Server Specs:</strong> small box I have tucked away in my closet</span>
<ul>
<li>cpu: Intel(R) Core(TM) i7-4790 CPU @ 3.60GHz (8 cores)</li>
<li>memory: 15GiB</li>
<li>storage: 2TB</li>
<li>CentOS 7</li>
<li>Apache Web Server</li>
</ul>

<strong>Step 1:</strong> you will need to install the Fedora EPEL repository to get started  
<pre>
~]$ sudo yum update -y
~]$ sudo yum install epel-release -y
</pre> 

<strong>Step 2:</strong> next you'll need to install Ruby
<pre>
~]$ sudo yum install ruby -y
</pre>

<strong>Step 3:</strong> now you will need to install Jekyll using [gem](https://jekyllrb.com/docs/ruby-101/#gems). You do not need to run this with  sudo provliges 
<pre>
~]$ gem install jekyll 
</pre>

<strong>Step 4:</strong> now check you installed Ruby and Jekyll
<pre>
~]$ ruby -v
~]$ jekyll -v
</pre>

<strong>Step 5:</strong> use gem to install the Ruby [Bundler](https://rubygems.org/gems/bundler)
<pre>
~]$ gem install bundler
</pre>

Before we generate a Jekyll package we first need to discuss where it should be created. Do you install it locally on your Linux machine and/or MacBook Pro, or how about a remote server in your house or in the cloud? Honestly, either is fine, but there is implications for how to preview the site. The less confusing strategy is to install Jekyll locally on your machine so you can view the site using http://localhost:4000/ and/or http://127.0.0.1:4000/. Jekyll has a <code>serve</code> feature that will instantiate an ephemoral version of the site using port 4000.  

If you install the Jekyll source code on a remote server, which is not running a graphical user interface (GUI), then you will have to use the <code>--host</code> flag. The flag will allow you to view your site using a class 3 IP address (beginning with 192.168.6.73). Simply type-in the IP address followed by the port number in your browser. Please note I installed the source code on a personal remote server. 

Okay, let's keep going.  

<strong>Step 6:</strong> create a folder for your site and install Jekyll
<pre>
~]$ mkdir mysite ; cd mysite
mysite]$ jekyll new myblog
</pre> 

You will need to check your firewall setting if you're running on a remote server - Jekyll will render the ephemeral site on port 4000. You can use the following commands to check and update your local firewall, if you're runing CentOS. 
<pre>
~]$ sudo systemctl status firewalld ##determine if the firewwall is enabled
~]$ sudo firewall-cmd --list-ports ##list ports 
Output: 10000/tcp
~]$ sudo firewall-cmd --permanent --add-port=4000/tcp ##open port 4000
Output: 10000/tcp 4000/tcp
</pre>

For quick testing, you can always turn off the local firewall all together. 
<pre>
~]$ sudo systemctl stop firewalld 
</pre>

<strong>Step 7:</strong> change directories and view site using IP address of host server (I'm using an internal IP address)  
<pre>
~]$ cd myblog
myblog]$ jekyll server --host 192.168.6.73
</pre> 

<strong>Picture of new Jekyll install</strong>
![New Jekyll Homesreen](https://richardbright.me/images/jekyll.png){:class="img-responsive"}


<strong>Step 8:</strong> now I need to push the build to the apache web server directory. If you are planning to run multiple sites you will want to create subdirectories in <code>/var/www/html</code>. For example, <code>/var/www/html/site1</code>, <code>/var/www/html/site2</code>, etc... If running a single web site, then deploy the package to <code>/var/www/html</code>. 
<pre>
myblog]$ jekyll build --destination /var/www/html/site1
</pre>

<strong>Step 9:</strong> create apache configuration file
<pre>
~]$ sudo su - ##change to root user 
 # vi /etc/httpd/conf.d/site1.conf
-----
paste in the following:
#NameVirtualHost *:80
<Directory /var/www/html/site1>
    AllowOverride All
    Require all granted
</Directory>

<VirtualHost *:80>
    DocumentRoot "/var/www/html/site1"
    ServerName http://site1.com
    ServerAlias site1.com
    ServerAdmin root@site1.com
    ErrorLog "/var/log/httpd/error_log_site1.com"
    CustomLog "/var/log/httpd/access_log_site1.com" combined
RewriteEngine on
RewriteCond %{SERVER_NAME} =www.site1.com [OR]
RewriteCond %{SERVER_NAME} =site1.com
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>

 # exit
~]$ sudo systemctl reload httpd ##restart apache server to load update 
</pre>


Before you proceed to the next step you will need to ensure port forwarding is enabled on your router, if you're using a home-based server.


<strong>Step 10:</strong> update DNS setting with IP address

Access your domain registration dashboard and update the "A Record"(e.g., GoDaddy, Bluehost and HostGator).
Name: yourdomain.com
Value: ip-address

Updates can take anywhere from 1 minute to 20 minutes to propagate domain controllers. You can check the record of the site with the nslookup utility.
<pre>gcp]$ nslookup sitename.com
##or you can watch for the update
~]$ watch -d "nslookup sitename.com"
##wait for the DNS to update before updating your ssl/tls cert</pre>
 
<strong>Step 11:<strong> apply TLS encryption using LetsEncrypt
<pre>
sudo certbot --apache
...
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator apache, Installer apache
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org
Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: site1.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): 1

Cert not yet due for renewal

You have an existing certificate that has exactly the same domains or certificate name you requested and isn't close to expiry.
(ref: /etc/letsencrypt/renewal/sitename.com.conf)

What would you like to do?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: Attempt to reinstall this existing certificate
2: Renew & replace the cert (limit ~5 per 7 days)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 1

Keeping the existing certificate
Deploying Certificate to VirtualHost /etc/httpd/conf.d/site1-le-ssl.conf

Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2

Enhancement redirect was already set.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled
https://site1.com

You should test your configuration at:
https://www.ssllabs.com/ssltest/analyze.html?d=sitename.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at:
/etc/letsencrypt/live/site1.com/fullchain.pem
Your key file has been saved at:
/etc/letsencrypt/live/site1/privkey.pem
Your cert will expire on 2019-10-27. To obtain a new or tweaked
version of this certificate in the future, simply run certbot again
with the "certonly" option. To non-interactively renew *all* of
your certificates, run "certbot renew"
- If you like Certbot, please consider supporting our work by:

Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
Donating to EFF: https://eff.org/donate-le
</pre>


Congrats, now your site is running over TLS encryption! 

Now you should initiate <code>git</code> in the new root directory to ensure you have adequate version control for your source code. You'll want to log into your [github](https://github.com/) account and create a public or private repo. Ensure your ssh key is added so you can push your code from the terminal. 
<pre>
myblog]$ git config --global user.name "name"  
myblog]$ git config --global user.email "email address"
myblog]$ git init
myblog]$ git add .
myblog]$ git commit -m "message"
myblog]$ git remote add origin git@github.com:"username"/"site1"
myblog]$ git push -u origin master
</pre>

Now toggle back to your github account, you should see the source code for your site. 

That should do it, I hope you found this post informative and helpful to you on your journey. 


---
 
References:
<ul>
        <li><a href="https://wordpress.org/">https://wordpress.org/</a></li>
	<li><a href="https://stackoverflow.com/questions/10060285/what-is-a-lamp-stack">https://stackoverflow.com/questions/10060285/what-is-a-lamp-stack</a></li>
	<li><a href="https://richardbright.me/jekyll/pixyll/2020/04/12/Migrating-Wordpress-to-GCP/">https://richardbright.me/jekyll/pixyll/2020/04/12/Migrating-Wordpress-to-GCP/</a></li>
	<li><a href="https://en.wikipedia.org/wiki/Dynamic_web_page">https://en.wikipedia.org/wiki/Dynamic_web_page</a></li> 
	<li><a href="https://w3techs.com/technologies/details/cm-wordpress">https://w3techs.com/technologies/details/cm-wordpress</a></li>
	<li><a href="https://jekyllrb.com/">https://jekyllrb.com/</a></li>
	<li><a href="https://apachebooster.com/blog/cache-static-and-dynamic-content-for-website/staticdynamic/">https://apachebooster.com/blog/cache-static-and-dynamic-content-for-website/staticdynamic/</a></li> 
	<li><a href="https://jekyllrb.com/docs/ruby-101/#gems">https://jekyllrb.com/docs/ruby-101/#gems</a></li>
</ul>
