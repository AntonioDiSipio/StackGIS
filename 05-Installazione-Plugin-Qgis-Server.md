# 📘 Installazione di Plugin su QGIS Server

Per impostazione predefinita, sui sistemi basati su Debian, **QGIS Server** carica i plugin dalla directory:  

```
/usr/lib/qgis/plugins
```

Questo percorso viene mostrato nei log all’avvio del servizio.  

Se si desidera utilizzare una directory diversa, è necessario impostare la variabile d’ambiente `QGIS_PLUGINPATH` nella configurazione del server web (Apache/FastCGI).  

I plugin possono essere installati in due modi:
1. **Manuale**, scaricando un archivio ZIP e copiandolo nella cartella dei plugin.  
2. **Automatico**, utilizzando lo strumento `qgis-plugin-manager`, più adatto per plugin ufficiali disponibili su PyPI o nei repository QGIS.  

---

## 1️⃣ Installazione manuale tramite archivio ZIP

Esempio con il plugin **HelloWorld**, utile per verificare la corretta configurazione del server:  

1. Accedere alla directory dei plugin. In questo esempio si utilizza la posizione predefinita `/usr/lib/qgis/plugins`.  
2. Scaricare il plugin **HelloWorld** da GitHub.  
3. Decomprimere e rinominare la cartella in modo che QGIS Server la riconosca correttamente.  

```bash
cd /usr/lib/qgis/plugins
sudo wget https://github.com/elpaso/qgis-helloserver/archive/master.zip
sudo unzip master.zip
sudo mv qgis-helloserver-master HelloServer
```

Questo metodo è utile quando un plugin non è disponibile nel repository ufficiale dei plugin QGIS, ma è distribuito tramite GitHub o come pacchetto ZIP.  

---

## 2️⃣ Installazione e configurazione dei plugin con `qgis-plugin-manager`

### 2.1️⃣ Installazione di pip
`pip` è il gestore pacchetti di Python, necessario per scaricare e aggiornare librerie e strumenti Python da **PyPI**.  
Senza `pip` non è possibile installare `qgis-plugin-manager`.  

```bash
sudo apt update
sudo apt install python3-pip
```

---

### 2.2️⃣ Installazione di qgis-plugin-manager
Debian adotta la protezione **PEP 668**, che impedisce l’installazione di pacchetti Python globali con `pip` per evitare conflitti con quelli gestiti da `apt`.  
Per aggirare questa restrizione, si utilizza l’opzione `--break-system-packages`:  

```bash
sudo pip3 install qgis-plugin-manager --break-system-packages
```

Questo comando installa **qgis-plugin-manager**, uno strumento CLI che consente di gestire i plugin di QGIS Server direttamente dai repository ufficiali QGIS.  

---

### 2.3️⃣ Inizializzazione e aggiornamento del gestore dei plugin
```bash
# Spostarsi nella cartella dei plugin
cd /usr/lib/qgis/plugins
# Inizializzare i repository (ufficiali o personalizzati)
sudo qgis-plugin-manager init
# Aggiornare l’indice dei plugin disponibili in base alla versione di QGIS installata
sudo qgis-plugin-manager update
```

Questo passaggio è essenziale: senza l’aggiornamento, il gestore non conosce l’elenco dei plugin installabili.  

---

### 2.4️⃣ Installazione dei plugin desiderati

Esempi di installazione di alcuni plugin comuni:  

```bash
sudo qgis-plugin-manager install 'Lizmap server'
sudo qgis-plugin-manager install 'atlasprint'
sudo qgis-plugin-manager install 'Data Plotly'
sudo qgis-plugin-manager install 'wfsOutputExtension'
```

**Descrizione dei principali plugin:**
- `Lizmap server`: pubblicazione di progetti QGIS sul web tramite Lizmap.  
- `atlasprint`: generazione di atlanti per la stampa lato server.  
- `Data Plotly`: creazione di grafici interattivi con Plotly.  
- `wfsOutputExtension`: estensione delle funzionalità WFS di QGIS Server.  

Altri comandi utili:
- Ricerca di un plugin specifico:  
  ```bash
  qgis-plugin-manager search lizmap
  ```
- Elenco dei plugin installati:  
  ```bash
  qgis-plugin-manager list
  ```

---

## 3️⃣ Abilitazione dell’autenticazione HTTP Basic

Alcuni plugin, come **HelloWorld**, richiedono la lettura dell’header `Authorization`.  
Per impostazione predefinita, Apache non inoltra questo header ai processi FastCGI.  
Per abilitare il passaggio, è necessario aggiungere una specifica configurazione.

Modificare il file di configurazione del Virtual Host:

```bash
sudo nano /etc/apache2/sites-available/qgis-server.conf
```

Aggiungere il seguente blocco:

```apache
# Needed for QGIS HelloServer plugin HTTP BASIC auth
<IfModule mod_fcgid.c>
    RewriteEngine on
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
</IfModule>
```

Il modulo `mod_rewrite` deve essere abilitato per far funzionare questa configurazione.  

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## ✅ Risultato finale

Dopo aver completato questi passaggi:
- **QGIS Server** può caricare plugin dalla directory predefinita o da un percorso personalizzato.  
- I plugin possono essere installati sia manualmente che tramite `qgis-plugin-manager`.  
- **Apache** è configurato correttamente per supportare l’autenticazione e i plugin server-side.  
- Plugin come **Lizmap**, **AtlasPrint**, **Data Plotly** e **HelloWorld** risultano pienamente operativi.  
