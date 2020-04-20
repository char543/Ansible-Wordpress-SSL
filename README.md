# Ansible-Wordpress-SSL
Ansible playbook to deploy wordpress on a hardened SSL server.

I didn't want this to be complex or out of reach to anyone not familiar with ansible so i wanted to keep this playbook in a single file.

For now it is tested on ubuntu 18.04 but may work on other variants.

## What does it do?

### Steps

Before you start, Set cloudflare ssl to 'off' (or turn off your other cdn / domain based ssl). This playbook assumes a fresh server, it may work otherwise based on existing configuration but it's reccomendedto begin with a fresh on.

##### 1) Install Ansible with this command
sudo apt update && sudo apt install software-properties-common && sudo apt install python-apt -y && sudo apt-add-repository --yes --update ppa:ansible/ansible && sudo apt install ansible -y

##### 2) Run playbook
sudo ansible-playbook site.yml

##### 3) Generate wildcard certificates (*.mydomain.com) for our domain 
certbot certonly --manual -d *.{{ domain }} -d {{ domain }} --agree-tos --no-bootstrap --manual-public-ip-logging-ok --expand --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory

##### 4) Harden Apache
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


#####     SSLStaplingCache "shmcb:logs/stapling-cache(150000)"

SSL stapling cache line above must be outside of virtualhost block

Need to run sudo service apache2 restart 

########################################################################################################################
###### qualys ssl labs checker
###### enable dnssec
###### upguard
###### mozilla observatory

Set cloudflare ssl to 'on'
