# 🌐 Installazione di Lizmap Web Client

Installazione e configurazione di Lizmap in `/lizmapserver/`.

```bash
sudo mkdir -p /lizmapserver/
VERSION=3.9.0
LOCATION=/lizmapserver/
cd $LOCATION
sudo wget https://github.com/3liz/lizmap-web-client/releases/download/$VERSION/lizmap-web-client-$VERSION.zip
sudo unzip lizmap-web-client-$VERSION.zip
sudo ln -s /lizmapserver/lizmap-web-client-$VERSION/lizmap/www/ /var/www/lizmap
sudo chown -R www-data:www-data /lizmapserver/
```

Configurare `profiles.ini.php` e `lizmapConfig.ini.php` con credenziali PostgreSQL.
