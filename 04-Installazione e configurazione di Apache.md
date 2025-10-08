# 🌐 Installazione e configurazione di Apache

Apache HTTP Server gestisce le richieste web e funge da interfaccia per QGIS Server.

## Installazione

```bash
sudo apt install apache2 libapache2-mod-fcgid
```

Creare `/etc/apache2/sites-available/qgis-server.conf` e inserire la configurazione del virtual host su porta 8080, mantenendo i percorsi:

```apache
<VirtualHost 127.0.0.1:8080>
  ServerAdmin webmaster@localhost
  ServerName qgis-server.local
  ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
  <Directory "/usr/lib/cgi-bin/">
    AllowOverride None
    Options +ExecCGI -MultiViews -SymLinksIfOwnerMatch
    Require all granted
  </Directory>
</VirtualHost>
```

Abilitare e riavviare:

```bash
sudo a2enmod fcgid rewrite
sudo a2ensite qgis-server.conf
sudo systemctl reload apache2
```
