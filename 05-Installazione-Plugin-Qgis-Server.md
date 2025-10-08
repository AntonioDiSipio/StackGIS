# 📘 Installazione plugin QGIS Server

QGIS Server supporta plugin Python installabili manualmente o tramite `qgis-plugin-manager`.

## Installazione automatica

```bash
sudo apt install python3-pip
sudo pip3 install qgis-plugin-manager --break-system-packages
sudo qgis-plugin-manager update
sudo qgis-plugin-manager install 'Lizmap server'
```

## Configurazione Apache

Aggiungere la variabile per l’autenticazione nel file `/etc/apache2/sites-available/qgis-server.conf`:

```apache
<IfModule mod_fcgid.c>
  RewriteEngine on
  RewriteCond %{HTTP:Authorization} .
  RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
</IfModule>
```
