# 🪶 Installazione e Configurazione di Apache HTTP Server

[Apache HTTP Server](https://httpd.apache.org/), comunemente chiamato **Apache**, è un server web open source sviluppato e mantenuto dalla **Apache Software Foundation**.  
È progettato per gestire richieste HTTP provenienti dai client (ad esempio browser web), servendo pagine statiche o dinamiche e integrandosi con applicazioni come **QGIS Server** tramite moduli dedicati.  

La sua architettura modulare consente di aggiungere funzionalità come il rewriting degli URL, l’autenticazione e il proxying, offrendo al tempo stesso **sicurezza**, **efficienza** ed **estensibilità**.

Questa sezione illustra la configurazione minima necessaria per l’integrazione di **QGIS Server** con **Apache**, utile per servire progetti GIS e interfacce web come Lizmap.

---

## 1️⃣ Installazione di Apache

Installare Apache e il modulo `fcgid` con il comando:

```bash
sudo apt install apache2 libapache2-mod-fcgid
```

Al termine dell’installazione, aprendo un browser e digitando:

```
http://tuo-server/
```

dovrebbe comparire la pagina web predefinita di Apache.

<p align="center">
  <img src="img/apachedefaultpage.jpg" width="800">
</p>

⚠️ **Nota:**  
Se la pagina di default non appare, è possibile che il **firewall** blocchi la porta **80 (HTTP)**.  
Su sistemi con `ufw`, è sufficiente sbloccarla con:

```bash
sudo ufw allow 80/tcp
sudo ufw reload
```

Se si utilizza **HTTPS**, aprire anche la porta **443**:

```bash
sudo ufw allow 443/tcp
sudo ufw reload
```

---

## 2️⃣ Configurazione del Virtual Host per QGIS Server

Per rendere operativo **QGIS Server** tramite Apache, è necessario creare un **Virtual Host** dedicato.

La configurazione di base di Apache (`000-default.conf`) può essere lasciata invariata, mentre si aggiunge un nuovo file di configurazione.  

Creare il file **`qgis-server.conf`** in `/etc/apache2/sites-available/`:

```bash
sudo touch /etc/apache2/sites-available/qgis-server.conf
```

Inserire le seguenti impostazioni:

```bash
<VirtualHost 127.0.0.1:8080>
  ServerAdmin webmaster@localhost
  ServerName qgis-server.local

  # Apache logs (different than QGIS Server log)
  ErrorLog ${APACHE_LOG_DIR}/qgis-server.error.log
  CustomLog ${APACHE_LOG_DIR}/qgis-server.access.log combined

  # Longer timeout for WPS... default = 40
  FcgidIOTimeout 120

  FcgidInitialEnv LC_ALL "en_US.UTF-8"
  FcgidInitialEnv PYTHONIOENCODING "UTF-8"
  FcgidInitialEnv LANG "en_US.UTF-8"
  FcgidInitialEnv DISPLAY ":99"
  FcgidInitialEnv QGIS_SERVER_LIZMAP_REVEAL_SETTINGS True
  FcgidInitialEnv QGIS_PLUGINPATH "/usr/lib/qgis/plugins"

  # QGIS log
  FcgidInitialEnv QGIS_SERVER_LOG_STDERR 1
  FcgidInitialEnv QGIS_SERVER_LOG_LEVEL 0

  # if qgis-server is installed from packages in debian based distros this is usually /usr/lib/cgi-bin/
  # run "locate qgis_mapserv.fcgi" if you don't know where qgis_mapserv.fcgi is
  ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
  <Directory "/usr/lib/cgi-bin/">
    AllowOverride None
    Options +ExecCGI -MultiViews -SymLinksIfOwnerMatch
    Require all granted
  </Directory>

  # Needed for QGIS HelloServer plugin HTTP BASIC auth
  <IfModule mod_fcgid.c>
    RewriteEngine on
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
  </IfModule>

  <IfModule mod_fcgid.c>
    FcgidMaxRequestLen 26214400
    FcgidConnectTimeout 60
  </IfModule>
</VirtualHost>
```

---

## 3️⃣ Abilitazione del Virtual Host e dei moduli

Abilitare il Virtual Host e i moduli necessari:

```bash
sudo a2dissite 000-default.conf
sudo a2dissite default.ssl.conf
sudo a2enmod fcgid
sudo a2enmod rewrite
sudo a2ensite qgis-server.conf
sudo systemctl reload apache2
```

---

## 4️⃣ Abilitazione della porta 8080 su Apache

Dopo la configurazione, verificare che Apache risponda alla porta 8080:

```bash
curl "http://127.0.0.1:8080/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&REQUEST=GetCapabilities"
```

Se viene restituito un errore, la porta **8080** non è ancora abilitata.  
Aprire il file delle porte:

```bash
sudo nano /etc/apache2/ports.conf
```

Aggiungere la seguente riga sotto `Listen 80`:

```
Listen 8080
```

Salvare e ricaricare Apache:

```bash
sudo systemctl reload apache2
```

Verificare il funzionamento:

```bash
sudo curl "http://127.0.0.1:8080/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&REQUEST=GetCapabilities"
```

Se la risposta è simile a:

```
<ServerException>Project file error. For OWS services: please provide a SERVICE and a MAP parameter pointing to a valid QGIS project file</ServerException>
```

Significa che Apache e QGIS Server comunicano correttamente ✅

---

## 5️⃣ Installazione e Configurazione di Xvfb

**QGIS Server** richiede un server X attivo per alcune funzionalità, come la stampa.  
Poiché sui server non è consigliato installare un ambiente grafico completo, è possibile usare **xvfb** (X virtual framebuffer) per simulare un display virtuale.

---

### 5.1️⃣ Installazione di Xvfb

```bash
sudo apt install xvfb
```

---

### 5.2️⃣ Creazione del servizio systemd

Creare il file di servizio:

```bash
sudo nano /etc/systemd/system/xvfb.service
```

Inserire il seguente contenuto:

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

### 5.3️⃣ Abilitazione e avvio del servizio

```bash
sudo systemctl enable --now xvfb.service
systemctl status xvfb.service
```

---

### 5.4️⃣ Integrazione di Xvfb con Apache

Aggiungere la variabile `DISPLAY` alla configurazione di Apache:  

```bash
sudo nano /etc/apache2/sites-available/qgis-server.conf
```

Inserire all’interno del blocco:

```apache
FcgidInitialEnv DISPLAY ":99"
```

Riavviare Apache per applicare la configurazione:

```bash
sudo systemctl restart apache2
```
