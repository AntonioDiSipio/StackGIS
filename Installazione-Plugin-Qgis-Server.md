# Installare Plugin su QGIS Server

Per impostazione predefinita, sui sistemi basati su Debian, QGIS Server cercherà i plugin situati in `/usr/lib/qgis/plugins`.  
Il valore predefinito viene visualizzato all’avvio di QGIS Server, nei log.  
È possibile impostare un percorso personalizzato definendo la variabile d’ambiente `QGIS_PLUGINPATH` nella configurazione del server web.  

I plugin possono essere installati sia **manualmente** che utilizzando un **gestore di plugin Python**.

---

## Installazione manuale con uno ZIP

Ad esempio, per installare il plugin **HelloWorld** per testare il server, utilizzando una cartella specifica, devi prima creare una cartella per contenere i plugin del server.  
Questa sarà specificata nella configurazione dell’host virtuale e passata al server attraverso una variabile d’ambiente.  

Io voglio installare i miei plugin in `/usr/lib/qgis/plugins`:  

```bash
cd /usr/lib/qgis/plugins
wget https://github.com/elpaso/qgis-helloserver/archive/master.zip
unzip master.zip
mv qgis-helloserver-master HelloServer
```

---

## 🔧 Installazione e configurazione dei plugin QGIS Server con `qgis-plugin-manager`

### 1. Installare **pip**, il gestore pacchetti di Python  
`pip` è l’utility che permette di installare, aggiornare e rimuovere librerie Python da **PyPI**.  
Su Debian/Ubuntu:  

```bash
sudo apt update
sudo apt install python3-pip
```

---

### 2. Installare **qgis-plugin-manager**  
Su Debian, il sistema Python è “protetto” (PEP 668) e non permette di installare pacchetti globali con `pip` per evitare conflitti con `apt`.  
Per forzare l’installazione:  

```bash
sudo pip3 install qgis-plugin-manager --break-system-packages
```

---

### 3. Inizializzare e aggiornare il gestore dei plugin  
Spostati nella cartella dei plugin di QGIS Server:  

```bash
cd /usr/lib/qgis/plugins
```

Inizializza i repository dei plugin (ufficiali o personalizzati):  

```bash
sudo qgis-plugin-manager init
```

Aggiorna l’indice dei plugin disponibili:  

```bash
sudo qgis-plugin-manager update
```

---

### 4. Installare un plugin (es. Lizmap Server)  
Per installare Lizmap Server:  

```bash
sudo qgis-plugin-manager install "Lizmap server"
```

Puoi anche cercare i plugin disponibili con:  

```bash
qgis-plugin-manager search lizmap
```

---

### 5. Configurare Apache per QGIS Server e i plugin  

#### 🔹 Indicare a FastCGI dove si trovano i plugin  
SE necessario indicare a FastCGI dove si trovano i plugin, questo è necessario solo se si caambia la directory di default dei plugin di qgis-server /usr/lib/qgis/plugins

ad esempio nel file di configurazione di Apache si può ggiungere un percorso tipo questo:  

```apache
FcgidInitialEnv QGIS_PLUGINPATH "/var/www/qgis-server/plugins"
```

In questo modo QGIS Server sa dove cercare i plugin.  

#### 🔹 Abilitare autenticazione HTTP Basic  
Alcuni plugin (es. **HelloWorld**) richiedono di passare l’header `Authorization` a QGIS Server.  
Per permettere ad Apache + FastCGI di propagare l’header, aggiungi:  

```apache
# Needed for QGIS HelloServer plugin HTTP BASIC auth
<IfModule mod_fcgid.c>
    RewriteEngine on
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
</IfModule>
```

Questo assicura che QGIS Server riceva correttamente le credenziali di autenticazione.  

---

### 6. Riavvia Apache  
Per rendere effettive le modifiche:  

```bash
sudo systemctl restart apache2
```

---

✅ A questo punto i plugin per **QGIS Server** sono installati e pronti all’uso (compreso Lizmap).
