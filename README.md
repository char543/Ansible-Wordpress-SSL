# ansible
Ansible playbook to deploy wordpress on a hardened SSL server.

I didn't want this to be complex or out of reach to anyone not familiar with ansible so i wanted to keep this playbook in a single file.

For now it is tested on ubuntu 18.04 but may work on other variants.

#######

# Set cloudflare ssl to 'off'

# 1) sudo apt update && sudo apt install software-properties-common && sudo apt install python-apt -y && sudo apt-add-repository --yes --update ppa:ansible/ansible && sudo apt install ansible -y

# 2) run playbook

# 3) certbot certonly --manual -d *.{{ domain }} -d {{ domain }} --agree-tos --no-bootstrap --manual-public-ip-logging-ok --expand --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory

# # Set cloudflare ssl to 'on'

########################################################################################################################
####     APACHE 2 SECURE CONFIG                                                                                     ####
########################################################################################################################

#     SSLCipherSuite EECDH+AESGCM:EDH+AESGCM
#     SSLProtocol -all +TLSv1.3 +TLSv1.2
#     SSLOpenSSLConfCmd Curves X25519:secp521r1:secp384r1:prime256v1
#     SSLHonorCipherOrder On
#     Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
#     Header always set X-Frame-Options DENY
#     Header always set X-Content-Type-Options nosniff
#     SSLCompression off
#     SSLUseStapling on
#     SSLSessionTickets Off


# Add above block of text, uncommented, to apache ssl conf inside vhost block


#     SSLStaplingCache "shmcb:logs/stapling-cache(150000)"


# ssl stapling cache line above must be outside of virtualhost block

# need to run sudo service apache2 restart 

########################################################################################################################
# qualys ssl labs checker
# enable dnssec
# upguard
# mozilla observatory

#########
