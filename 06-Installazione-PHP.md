# 🐘 Installazione PHP

Installazione di PHP 8.4 e moduli richiesti per Lizmap Web Client.

```bash
sudo apt install php8.4-fpm php8.4-cli php8.4-bz2 php8.4-curl php8.4-gd php8.4-intl php8.4-mbstring php8.4-pgsql php8.4-sqlite3 php8.4-xml php8.4-ldap php8.4-redis
sudo a2enmod proxy_fcgi setenvif
sudo a2enconf php8.4-fpm
sudo systemctl restart apache2
```
