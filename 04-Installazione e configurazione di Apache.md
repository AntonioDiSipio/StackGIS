# Apache HTTP Server

[Apache HTTP Server](https://httpd.apache.org/), spesso chiamato semplicemente **Apache**, è un server web open source molto diffuso, sviluppato e mantenuto dalla Apache Software Foundation.  

Il suo scopo principale è gestire le richieste HTTP provenienti dai client (ad esempio i browser), servendo pagine web, risorse statiche (immagini, file CSS/JS) oppure instradando applicazioni web dinamiche tramite moduli.  

Progettato per essere **sicuro**, **efficiente** ed **estensibile**, Apache offre un’architettura modulare che consente di aggiungere funzionalità come il rewriting degli URL, l’autenticazione o l’utilizzo come proxy, adattandosi a diverse esigenze.

Facciamo una configurazione minima pronta per far funzionare qgis-server e il nostro sito con apache:
--

---

## 🔹 Installazione di Apache

Installiamo Apache lanciando il comando:

```bash
sudo apt install apache2
```

Al termine del processo di installazione e dopo aver avviato Apache, se apri un browser e digiti:

```
http://tuo-server/
```

dovrebbe comparire la pagina web di default di Apache.

<p align="center">
  <img src="img/apachedefaultpage.jpg" width="800">
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

---

## 🔹 Configurazione Virtual Host per QGIS Server

Per far funzionare QGIS Server su Apache e quindi esporre i servizi, bisogna configurare un Virtual Host.  

Lascio la configurazione di default di Apache (`000-default.conf`) e creo un nuovo Virtual Host con la configurazione suggerita dalla guida, ma apportando alcune modifiche necessarie al mio caso.  

Creiamo subito il file **`gisserver.conf`** in `/etc/apache2/sites-available/` con il comando:

```bash
sudo nano /etc/apache2/sites-available/disipio.conf
```

Con le seguenti impostazioni:

```apache
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  ServerName disipio.io

  DocumentRoot /var/www/html

  # Apache logs (diversi dai log di QGIS Server)
  ErrorLog ${APACHE_LOG_DIR}/gisserver.error.log
  CustomLog ${APACHE_LOG_DIR}/gisserver.access.log combined

  # Timeout più lungo per WPS... default = 40
  FcgidIOTimeout 120

  FcgidInitialEnv LC_ALL "en_US.UTF-8"
  FcgidInitialEnv PYTHONIOENCODING UTF-8
  FcgidInitialEnv LANG "en_US.UTF-8"

  # QGIS log
  FcgidInitialEnv QGIS_SERVER_LOG_STDERR 1
  FcgidInitialEnv QGIS_SERVER_LOG_LEVEL 0

  # cartella contenente i progetti QGIS
  SetEnv QGIS_PROJECT_PATH /gisserver/

  # QGIS_AUTH_DB_DIR_PATH deve puntare a una cartella scrivibile dall’utente FCGI (www-data)
  FcgidInitialEnv QGIS_AUTH_DB_DIR_PATH "/gisserver/qgisserverdb/"
  FcgidInitialEnv QGIS_AUTH_PASSWORD_FILE "/gisserver/qgisserverdb/qgis-auth.db"

  # Configurazione per accesso PostgreSQL via pg_service
  SetEnv PGSERVICEFILE /gisserver/.pg_service.conf
  FcgidInitialEnv PGPASSFILE "/gisserver/.pgpass"

  # Dove si trova qgis_mapserv.fcgi
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

---

## 🔹 Creazione cartelle necessarie

Per comodità metterò i dati nella cartella root del server. Questo ha una duplice finalità:  
1. accorciare l’URL della chiamata MAP  
2. bypassare le restrizioni di un’eventuale installazione nella cartella utente.  

Creiamo ora le cartelle che ospiteranno i progetti, i registri di QGIS Server e il database di autenticazione:

```bash
# cartella log dedicata a QGIS Server
sudo mkdir -p /var/log/qgis/
```

```bash
# cartella database di autenticazione
sudo mkdir -p /gisserver/qgisserverdb
```

```bash
# file database autenticazione (se non già presente)
sudo touch /gisserver/qgisserverdb/qgis-auth.db
```

```bash
# cartella progetti QGIS
sudo mkdir -p /gisserver/
```

---

## 🔹 Abilitazione Virtual Host e moduli

Ora possiamo abilitare l’host virtuale e il modulo `fcgid`, se non è già stato fatto:

```bash
sudo a2dissite 000-default.conf
sudo a2enmod fcgid
sudo a2enmod rewrite
sudo a2ensite disipio.conf
```

---

## 🔹 Riavvio e verifica di Apache

Dopo aver abilitato moduli o configurazioni, riavvia Apache per applicare le modifiche:

```bash
sudo systemctl restart apache2
```

Puoi verificarne lo stato con:

```bash
systemctl status apache2
```

Se il servizio è attivo, vedrai **active (running)** evidenziato in verde.  

👉 Per ricaricare la configurazione senza interrompere Apache (utile in produzione):

```bash
sudo systemctl reload apache2
```

In caso di problemi, controlla:  

```bash
sudo apache2ctl configtest
sudo tail -f /var/log/apache2/error.log
```

---

## 🔹 Progetto di esempio

Sul sito è disponibile un file zip contenente un esempio di progetto QGIS con i confini amministrativi delle Regioni italiane:  

👉 [Regioni Italiane](RegioniISTAT.zip)

Installiamo un gestore di pacchetti zip con:
```bash
sudo apt-get install unzip
```
per poter testare le funzionalità di QGIS Server.
Su GitHub i file binari vanno scaricati usando il link “Download raw” (cioè il contenuto effettivo, non la pagina). L’URL giusto deve passare per ```raw.githubusercontent.com```.
scarichiamo il file zip con 
```bash
sudo wget https://raw.githubusercontent.com/AntonioDiSipio/StackGIS/main/RegioniISTAT.zip
```
ed estraiamolo
```bash
sudo unzip RegioniISTAT.zip
```
rimuoviamo il file zip per non creare dati superflui
```bash
sudo rm -r RegioniISTAT.zip
```

---

## 🔹 Test finale

Se tutto è andato a buon fine, al seguente link dovresti vedere le **capabilities** del progetto pubblicato:

```
http://disipio.io/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetCapabilities&MAP=/gisserver/datigis/RegioniISTAT/RegioniISTAT.qgz
```

---

## 🔹 Xvfb

QGIS Server ha bisogno di un server X funzionante per essere pienamente utilizzabile, in particolare per la stampa.  
Sui server si raccomanda di non installare un server grafico, quindi si può usare **xvfb** per avere un ambiente X virtuale.  

---

### 1️⃣ Installazione di xvfb

```bash
sudo apt install xvfb
```

---

### 2️⃣ Configurazione del servizio

Crea il file di servizio `/etc/systemd/system/xvfb.service`
```bash
sudo nano /etc/systemd/system/xvfb.service
```
incollando questo contenuto:
```ini
[Unit]
Description=X Virtual Frame Buffer Service
After=network.target

[Service]
ExecStart=/usr/bin/Xvfb :99 -screen 0 1024x768x24 -ac +extension GLX +render -noreset

[Install]
WantedBy=multi-user.target
```

---

### 3️⃣ Abilitazione e avvio del servizio

```bash
sudo systemctl enable --now xvfb.service
systemctl status xvfb.service
```

---

### 4️⃣ Integrazione con Apache

Aggiungi alla configurazione Fcgid in Apache il parametro:
```bash
sudo nano /etc/apache2/mods-available/fcgid.conf
```
ed incolla questo script
```apache
FcgidInitialEnv DISPLAY ":99"
```

Poi riavvia Apache affinché la nuova configurazione venga presa in carico:

```bash
sudo systemctl restart apache2
```
