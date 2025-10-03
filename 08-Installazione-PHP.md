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
apt install php8.2-fpm php8.2-cli php8.2-bz2 php8.2-curl php8.2-gd php8.2-intl php8.2-mbstring php8.2-pgsql php8.2-sqlite3 php8.2-xml php8.2-ldap php8.2-redis
```
