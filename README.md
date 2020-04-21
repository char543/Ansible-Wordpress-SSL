# Ansible-Wordpress-SSL
Ansible playbook to deploy wordpress on a hardened SSL server.

I didn't want this to be complex or out of reach to anyone not familiar with ansible so i wanted to keep this playbook in a single file and as 'one liner' as possible.

For now it is tested on ubuntu 18.04 but may work on other variants.

## What does it do?
It updates and upgrades the operating system, installs all required packages for operation, installs and securely configures apache web server (directory listing / indexing off, enforcing ciphers and forcing redirection via https, sets secure headers and does its best to obscure info given out by the server and a bunch of others). It installs and configures WordPress and even sets up the salt values in the config file for you in one click. 


### Steps

Before you start, Set cloudflare ssl to 'off' (or turn off your other cdn / domain based ssl). This playbook assumes a fresh server, it may work otherwise based on existing configuration but it's reccomended to begin with a fresh one.

You may want to adjust the variables at the top of the playbook:

  vars:
    username: nonroot # (name of non root user for server setup and operation)
    mysql_user: dbuser # (name of database user)
    mysql_pass: pass # (mysql user password)
    mysql_db: wpdb # (wordpress database name)
    mysql_root_pass: root # (mysql root password)
    email: your-email@site.com # (your email address)
    domain: yourdomain.com # (your site domain)

#### 1) Install Ansible with this command
sudo apt update && sudo apt install software-properties-common && sudo apt install python-apt -y && sudo apt-add-repository --yes --update ppa:ansible/ansible && sudo apt install ansible -y

#### 2) Run playbook
sudo ansible-playbook site.yml

#### 3) Generate wildcard certificates (*.mydomain.com) for our domain 
certbot certonly --manual -d *.{{ domain }} -d {{ domain }} --agree-tos --no-bootstrap --manual-public-ip-logging-ok --expand --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory

#### 4) Harden Apache
Add this block of text, uncommented, to apache ssl conf inside vhost block. Normally located @ /etc/apache2/sites-available/***mydomain.com-le-ssl.conf***

######     APACHE 2 SECURE CONFIG                                                                                     

SSLCipherSuite EECDH+AESGCM:EDH+AESGCM  
SSLProtocol -all +TLSv1.3 +TLSv1.2  
SSLOpenSSLConfCmd Curves X25519:secp521r1:secp384r1:prime256v1  
SSLHonorCipherOrder On  
Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"  
Header always set X-Frame-Options DENY  
Header always set X-Content-Type-Options nosniff  
SSLCompression off  
SSLUseStapling on  
SSLSessionTickets Off  


##### SSL stapling cache line must be outside of virtualhost block  

SSLStaplingCache "shmcb:logs/stapling-cache(150000)"


#### 5) Restart Apache
sudo service apache2 restart 


########################################################################################################################

That's it! You can set cloudflare (or other) SSL back to 'on' and re-enable proxies etc.

Below is a list of free services i used to audit the sites after deployment.

###### qualys ssl labs checker
###### enable dnssec
###### upguard
###### mozilla observatory


