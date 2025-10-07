# 09 - Installazione di Lizmap Web Client

Guida: [Requisiti necessari](https://docs.lizmap.com/3.9/it/install/pre_requirements.html)

## Requisiti
- Server web (Apache/Nginx) con **PHP** (stack LAMP).  
- Conoscenze base: variabili d’ambiente, log, cURL, PostgreSQL.  

## Dati GIS
- Archiviazione libera (es. `/srv/lizmap/data/`).  
- Definire `rootRepositories` o usare percorsi assoluti.  

## QGIS Server
- Stessa versione di QGIS Desktop/Server.  
- Consigliato **Py-QGIS-Server** (altrimenti FCGI).  
- Installare **XVFB** (stampa PDF).  
- Proteggere URL: accesso solo da Lizmap.  

## Plugin QGIS Server
- **Obbligatorio:** `Lizmap server`.  
- **Opzionali:** AtlasPrint, Cadastre, DataPlotly, WfsOutputExtension.  

## API Lizmap
- FCGI: URL API vuoto.  
- Py-QGIS-Server: endpoint `/lizmap`.  
- Variabile: `QGIS_SERVER_LIZMAP_REVEAL_SETTINGS=True`.  

## PostgreSQL
- Dati GIS.  
- Utenti e azioni Lizmap.  
- Ricerca `lizmap_search`.  

# Installazione
## Per motivi di sicurezza, per abilitare l’API sul lato server QGIS, è necessario abilitare la variabile d’ambiente
QGIS_SERVER_LIZMAP_REVEAL_SETTINGS con il valore impostato su True sul server QGIS.

# Apache FCGI example
quindi `sudo nano /etc/apache2/sites-available/qgis-server.conf` ed incollare questo valore 
```bash
FcgidInitialEnv QGIS_SERVER_LIZMAP_REVEAL_SETTINGS True
```
poi

```bash
sudo systemctl reload apache2
```

Configurazioni locali
Per semplicità, puoi configurare il server con la codifica UTF-8 di default.

# configura l'italiano
```bash
sudo locale-gen it_IT.UTF-8 #replace fr with your language
```
```bash
sudo dpkg-reconfigure locales
```
# definisci la timezone [useful for logs]
```bash
sudo dpkg-reconfigure tzdata
```
non è necessario lanciare questo comando sulla tua macchina non serviva installare ntp e ntpdate, perché sono pacchetti vecchi, ormai rimossi dai repository Debian/Ubuntu recenti, oggi il lavoro lo fa già systemd-simesyncd, che è incluso di default e molto più leggero, per i tuoi scopi (avere i log con data/ora corretta) è più che sufficiente.
```bash
sudo apt install ntp ntpdate
```
quindi per controllare
```bash
timedatectl status
```
Se vedi una riga tipo:
```bash
System clock synchronized: yes
NTP service: active
```

# Adesso passiamo all'installazione di php
[08-Installazione PHP](08-Installazione-PHP.md)

# Instllazione Postgresql + Postgis
adesso installiamo anche postgresql

[07-Installazione-Postgresql+Postgis](07-Installazione-Postgresql-PostGIS.md)

finiti i passaggi di instllazoine di php e postgresql installiamo lizmap.

creiamo la cartella dove verrà installato lizmap `sudo mkdir -p /lizmapserver` 

Impostiamo alcune variabili per facilitare le istruzioni. Impostiamo la versione e la posizione in cui verrà installato Lizmap. Adatta questi valori alle tue esigenze
```bash
VERSION=3.9.0 #metti la versione di tuo interesse
LOCATION=/lizmapserver/ #metti la location dove vuoi scaricare lizmap
```
cambia il proprietario di /lizmapserver in
```bash
sudo chown -R www-data:www-data /lizmapserver/
```
Quindi puoi installare il file zip:
```bash
cd $LOCATION
sudo wget https://github.com/3liz/lizmap-web-client/releases/download/$VERSION/lizmap-web-client-$VERSION.zip
# Unzip archive
sudo unzip lizmap-web-client-$VERSION.zip

# virtual link for http://localhost/lizmap/
sudo ln -s /lizmapserver/lizmap-web-client-3.9.2/lizmap/www/ /var/www/lizmap
# Remove archive
sudo rm lizmap-web-client-$VERSION.zip
```
Lizmap ha bisogno di un database per memorizzare i propri dati e per accedere ai dati utilizzati nei vostri progetti Qgis, con il suo strumento di editing.
Creare `profiles.ini.php` in `lizmap/var/config` copiando `profiles.ini.php.dist`.
```bash
cd /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config
sudo cp profiles.ini.php.dist profiles.ini.php
cd ../../..
```
Per la modifica dei livelli PostGIS in Web Client Lizmap, installare il supporto PostgreSQL per PHP. Nessun file di configurazione necessita di essere modificato per modificare i livelli PostgreSQL. Bisogna solo controllare che il server Lizmap possa accedere al database con le credenziali che sono memorizzate nel progetto QGIS (o con un file di servizio PostgreSQL).
```bash
sudo apt install php8.4-pgsql
```
```bash
sudo service php8.4-fpm restart
```
Per i log di Lizmap, gli utenti e i gruppi, possono essere memorizzati in SqLite o PostgreSQL. Per memorizzare queste informazioni in PostgreSQL, seguire queste istruzioni.

In una nuova copia di `sudo nano /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config/profiles.ini.php` devi inserire le informazioni per la connessione al database postgresql

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

Dai gli appropriati diritti alle cartelle e ai file
Impostare i diritti per Nginx/Apache, cosicché gli script PHP possano scrivere alcuni file temporanei o fare modifiche.
```bash
cd /lizmapserver/lizmap-web-client-3.9.2
```
```bash
sudo lizmap/install/set_rights.sh www-data www-data
```
Configurazione dell’installazione
Create `lizmapConfig.ini.php`, `localconfig.ini.php` e modificateli per impostare i parametri specifici della vostra installazione. Potete modificare lizmapConfig.ini.php per impostare l’url di qgis map server e altre cose.
```bash
cd /lizmapserver/lizmap-web-client-3.9.2/lizmap/var/config/
sudo cp lizmapConfig.ini.php.dist lizmapConfig.ini.php
sudo cp localconfig.ini.php.dist localconfig.ini.php
cd ../../..
```
su [qgiswrapper] modifica allowFcgi in on e decommenta la riga così:

```bash
[qgisWrapper]
; If off, FCGI is not allowed, only Py-QGIS-Server or QJazz
; https://docs.lizmap.com/current/en/install/py-qgis-server.html
; https://docs.3liz.org/py-qgis-server/
; https://github.com/3liz/qjazz
allowFcgi=on
```

Lanciare il programma di installazione

Dopo aver creato i file di configurazione, potete lanciare il programma di installazione
```bash
sudo php lizmap/install/installer.php
```

Primo test

Per testare lanciare: http://localhost/lizmap nel vostro browser.

Se ottieni un errore 500 - internal server error, riesegui:

```bash
cd /var/www/lizmap-web-client-3.9.0/
```

```bash
sudo lizmap/install/set_rights.sh www-data www-data
```

