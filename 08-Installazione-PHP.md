Installare i pacchetti necessari
Avvertimento

You must install at least the 7.4 version of PHP. The dom, simplexml, pcre, session, tokenizer and spl extensions are required (they are generally turned on in a standard PHP 7/8 installation)
```bash
sudo su # only necessary if you are not logged in as root
apt update # update packages list
sudo apt install curl openssl libssl3
```



Install these PHP packages:
```bash
sudo apt install php8.4-fpm php8.4-cli php8.4-bz2 php8.4-curl \
php8.4-gd php8.4-intl php8.4-mbstring php8.4-pgsql \
php8.4-sqlite3 php8.4-xml php8.4-ldap php8.4-redis
```
