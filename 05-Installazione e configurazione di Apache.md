## Apache HTTP Server

[Apache HTTP Server](https://httpd.apache.org/), spesso chiamato semplicemente **Apache**, è un server web open source molto diffuso, sviluppato e mantenuto dalla Apache Software Foundation.  

Il suo scopo principale è gestire le richieste HTTP provenienti dai client (ad esempio i browser), servendo pagine web, risorse statiche (immagini, file CSS/JS) oppure instradando applicazioni web dinamiche tramite moduli.  

Progettato per essere **sicuro**, **efficiente** ed **estensibile**, Apache offre un’architettura modulare che consente di aggiungere funzionalità come il rewriting degli URL, l’autenticazione o l’utilizzo come proxy, adattandosi a diverse esigenze.

Installazione di Apache
---
Installiamo apache lanciando il comando:
```bash
sudo apt install apache2 libapache2-mod-fcgid
```
al termine del processo di installazione e aver avviato apache se apri un browser e digiti ```http://tuo-server/``` dovrebbe comparire la pagina web di default di apache

<p align="center">
  <img src="img/apachedefaultpage.jpg" width="200">
</p>

⚠️ **Nota bene:**  
Se la pagina di default di Apache non dovesse essere visibile, verifica che il **firewall** non stia bloccando la porta **80** (HTTP).  
Su sistemi con `ufw`, ad esempio, puoi aprire la porta con:  
 
```bash
sudo ufw allow 80/tcp
sudo ufw reload
```
  
Se invece usi HTTPS, ricordati di aprire anche la porta **443**:  
 
```bash
sudo ufw allow 443/tcp
sudo ufw reload
```

per far funzionare qgis-server su apache e quindi esporre i servizi, bisogna configurare un virtua host, nel lascio la configurazione di default di apache e creo un nuovo virtual host con la configurazione suggerita dalla guida, ma apportando alcune modifiche necessarie al mio caso per far funzionare la configurazione, quindi

creiamo subito il file gisserver.conf in /etc/apache2/sites-available/ con il comando
```bash
sudo nano /etc/apache2/sites-available/gisserver.conf
```
con le seguenti impostazioni
```bash
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  ServerName gisserver

  DocumentRoot /var/www/html

  # Apache logs (different than QGIS Server log)
  ErrorLog ${APACHE_LOG_DIR}/qgis.demo.error.log
  CustomLog ${APACHE_LOG_DIR}/qgis.demo.access.log combined

  # Longer timeout for WPS... default = 40
  FcgidIOTimeout 120

  FcgidInitialEnv LC_ALL "en_US.UTF-8"
  FcgidInitialEnv PYTHONIOENCODING UTF-8
  FcgidInitialEnv LANG "en_US.UTF-8"

  # QGIS log
  FcgidInitialEnv QGIS_SERVER_LOG_STDERR 1
  FcgidInitialEnv QGIS_SERVER_LOG_LEVEL 0

  # default QGIS project
  SetEnv QGIS_PROJECT_PATH /gisdata

  # QGIS_AUTH_DB_DIR_PATH must lead to a directory writeable by the Server's FCGI process user
  FcgidInitialEnv QGIS_AUTH_DB_DIR_PATH "/home/qgis/qgisserverdb/"
  FcgidInitialEnv QGIS_AUTH_PASSWORD_FILE "/home/qgis/qgisserverdb/qgis-auth.db"

  # Set pg access via pg_service file
  SetEnv PGSERVICEFILE /home/qgis/.pg_service.conf
  FcgidInitialEnv PGPASSFILE "/home/qgis/.pgpass"

  # if qgis-server is installed from packages in debian based distros this is usually /usr/lib/cgi-bin/
  # run "locate qgis_mapserv.fcgi" if you don't know where qgis_mapserv.fcgi is
  ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
  <Directory "/usr/lib/cgi-bin/">
    AllowOverride None
    Options +ExecCGI -MultiViews -SymLinksIfOwnerMatch
    Require all granted
  </Directory>

  <IfModule mod_fcgid.c>
  FcgidMaxRequestLen 26214400
  FcgidConnectTimeout 60
  </IfModule>

</VirtualHost>
```



