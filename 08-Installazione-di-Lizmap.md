# 🌐 Installazione di Lizmap Web Client

Guida ufficiale: [Requisiti necessari](https://docs.lizmap.com/3.9/it/install/pre_requirements.html)

---

## 🔧 Requisiti
- Server web (Apache/Nginx) con **PHP** (stack LAMP).  
- Conoscenze di base: variabili d’ambiente, log, cURL, PostgreSQL.  

### Dati GIS
- Archiviazione consigliata, ad esempio:  
  ```
  /srv/lizmap/data/
  ```  
- È possibile definire `rootRepositories` o usare percorsi assoluti.  

### QGIS Server
- Stessa versione di **QGIS Desktop** e **QGIS Server**.  
- Si consiglia **Py-QGIS-Server** (in alternativa FCGI).  
- Installare **XVFB** per il supporto alla stampa PDF.  
- Proteggere l’URL del server QGIS permettendo l’accesso solo da Lizmap.  

### Plugin QGIS Server
- **Obbligatorio:** `Lizmap server`  
- **Opzionali:** `AtlasPrint`, `Cadastre`, `DataPlotly`, `WfsOutputExtension`  

### API Lizmap
- FCGI: URL API vuoto  
- Py-QGIS-Server: endpoint `/lizmap`  
- Variabile necessaria:  
  ```
  QGIS_SERVER_LIZMAP_REVEAL_SETTINGS=True
  ```

### PostgreSQL
- Archiviazione dei dati GIS.  
- Gestione utenti e azioni Lizmap.  
- Indice di ricerca `lizmap_search`.  

---

## 1️⃣ Configurazione ambiente server

Per motivi di sicurezza, per abilitare l’API sul lato server QGIS, impostare la variabile d’ambiente:  
```bash
FcgidInitialEnv QGIS_SERVER_LIZMAP_REVEAL_SETTINGS True
```

Nel file di configurazione di Apache:  
```bash
sudo nano /etc/apache2/sites-available/qgis-server.conf
```

Aggiungere la riga sopra, quindi ricaricare il servizio Apache:  
```bash
sudo systemctl reload apache2
```

---

## 2️⃣ Impostazioni locali

Configurare la codifica e la localizzazione del server:

```bash
sudo locale-gen it_IT.UTF-8
sudo dpkg-reconfigure locales
```

Impostare il fuso orario (utile per i log):

```bash
sudo dpkg-reconfigure tzdata
```

⚠️ Non è necessario installare `ntp` e `ntpdate` poiché obsoleti; il servizio `systemd-timesyncd` è già incluso e sufficiente.  
Per verificare la sincronizzazione oraria:  

```bash
timedatectl status
```

Uscita prevista:
```
System clock synchronized: yes
NTP service: active
```

---

## 3️⃣ Creazione directory di installazione

Creare la cartella di destinazione per Lizmap:  
```bash
sudo mkdir -p /lizmapserver
sudo chown -R www-data:www-data /lizmapserver/
```

Impostare alcune variabili d’ambiente per comodità:
```bash
VERSION=3.9.0
LOCATION=/lizmapserver/
```

---

## 4️⃣ Installazione di Lizmap Web Client

```bash
cd $LOCATION
sudo wget https://github.com/3liz/lizmap-web-client/releases/download/$VERSION/lizmap-web-client-$VERSION.zip
sudo unzip lizmap-web-client-$VERSION.zip
sudo ln -s /lizmapserver/lizmap-web-client-3.9.2/lizmap/www/ /var/www/lizmap
sudo rm lizmap-web-client-$VERSION.zip
```

---

## 5️⃣ Configurazione del database

Lizmap necessita di un database per i propri dati e per accedere ai layer PostGIS.  

Creare `profiles.ini.php` copiando il file di esempio:
```bash
cd /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config
sudo cp profiles.ini.php.dist profiles.ini.php
cd ../../..
```

Installare il supporto PostgreSQL per PHP:  
```bash
sudo apt install php8.4-pgsql
sudo service php8.4-fpm restart
```

Configurare la connessione al database in:
```bash
sudo nano /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config/profiles.ini.php
```

E inserire:
```bash
[jdb:jauth]
driver=pgsql
host=localhost
port=5432
database=lizmap
user=lizmapadmin
password=tuapassword
search_path=public

[jdb:lizlog]
driver=pgsql
host=localhost
port=5432
database=lizmap
user=lizmapadmin
password=tuapassword
search_path=public
```

---

## 6️⃣ Permessi e configurazioni aggiuntive

Impostare i permessi per Apache/Nginx:
```bash
cd /lizmapserver/lizmap-web-client-3.9.2
sudo lizmap/install/set_rights.sh www-data www-data
```

Creare e copiare i file di configurazione:
```bash
cd /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config/
sudo cp lizmapConfig.ini.php.dist lizmapConfig.ini.php
sudo cp localconfig.ini.php.dist localconfig.ini.php
cd ../../..
```

### Modifica di `localconfig.ini.php`
Abilitare FCGI:
```bash
[qgisWrapper]
allowFcgi=on
```

### Modifica di `lizmapConfig.ini.php`
Se QGIS Server è configurato su una porta diversa da 80:
```bash
[services]
wmsServerURL="http://127.0.0.1:8080/cgi-bin/qgis_mapserv.fcgi"
```

Se Lizmap è installato in sottocartella:
```bash
[general]
basePath="/lizmap"
```

---

## 7️⃣ Configurazione Apache per Lizmap

Creare o modificare il Virtual Host:  
```bash
sudo nano /etc/apache2/sites-available/disipio.conf
```

E inserire la seguente configurazione (senza HTTPS):

```apache
<VirtualHost *:80>
    ServerName disipio.io
    ServerAlias www.disipio.io 
    DocumentRoot /var/www/disipio

    ErrorLog ${APACHE_LOG_DIR}/disipio_error.log
    CustomLog ${APACHE_LOG_DIR}/disipio_access.log combined

    # =========================
    # HOMEPAGE
    # =========================
    <Directory /var/www/disipio>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
        DirectoryIndex index.html index.php
    </Directory>

    # =========================
    # LIZMAP (in sottocartella /lizmap)
    # =========================
    Alias /lizmap /lizmapserver/lizmap-web-client-3.9.2/lizmap/www

    <Directory /lizmapserver/lizmap-web-client-3.9.2/lizmap/www>
        Options FollowSymLinks
        AllowOverride All
        Require all granted

        # URL puliti (necessari per Lizmap)
        RewriteEngine On
        RewriteBase /lizmap/
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^(.*)$ index.php [QSA,L]

        # Gestione PHP-FPM
        <FilesMatch "\.php$">
            SetHandler "proxy:unix:/run/php/php8.4-fpm.sock|fcgi://localhost/"
        </FilesMatch>
    </Directory>

    # Variabile per QGIS Server
    SetEnv LIZMAP_WMS_SERVICES "http://127.0.0.1:8080/cgi-bin/qgis_mapserv.fcgi"
</VirtualHost>
```

Abilitare il sito e ricaricare Apache:
```bash
sudo a2ensite disipio.conf
sudo systemctl reload apache2
```

---

## 8️⃣ Installazione finale

Eseguire lo script di installazione:
```bash
sudo php lizmap/install/installer.php
```

---

## ✅ Risultato finale

Dopo aver completato tutti i passaggi:
- Lizmap Web Client è installato e accessibile.  
- L’integrazione con QGIS Server e PostgreSQL è attiva.  
- Apache è configurato correttamente con PHP-FPM e il VirtualHost `disipio.conf`.  
- Tutti i permessi e le variabili d’ambiente sono impostati correttamente.
