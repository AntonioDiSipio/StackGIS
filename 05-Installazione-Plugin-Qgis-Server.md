# 📘 Installare Plugin su QGIS Server

Per impostazione predefinita, sui sistemi basati su Debian, **QGIS Server** carica i plugin dalla cartella:  
```
/usr/lib/qgis/plugins
```
Questo percorso viene mostrato nei log all’avvio di QGIS Server.  

➡️ Se vuoi usare un percorso diverso, devi impostare la variabile d’ambiente `QGIS_PLUGINPATH` nella configurazione del server web (Apache/FastCGI).  

I plugin possono essere installati in due modi:  
1. **Manuale** (scaricando un archivio ZIP e copiandolo nella cartella dei plugin).  
2. **Automatico** con `qgis-plugin-manager` (più comodo per plugin ufficiali da PyPI).  

---

## 🛠 Installazione manuale con uno ZIP

Esempio con il plugin **HelloWorld** (utile per testare la configurazione del server):  

1. Mi sposto nella cartella dei plugin. In questo esempio uso quella di default `/usr/lib/qgis/plugins`.  
2. Scarico il plugin HelloWorld da GitHub.  
3. Lo decomprimo e rinomino la cartella, in modo che QGIS Server lo riconosca correttamente.  

```bash
cd /usr/lib/qgis/plugins
sudo wget https://github.com/elpaso/qgis-helloserver/archive/master.zip
sudo unzip master.zip
sudo mv qgis-helloserver-master HelloServer
```

👉 Questo metodo è utile quando un plugin non è disponibile sul repository ufficiale dei plugin QGIS, ma solo su GitHub o come ZIP rilasciato dallo sviluppatore.  

---

## 🔧 Installazione e configurazione dei plugin con `qgis-plugin-manager`

### 1. Installare **pip**
- `pip` è il gestore pacchetti Python.  
- Serve per scaricare, installare e aggiornare librerie e tool Python da **PyPI**.  
- Senza `pip` non è possibile installare `qgis-plugin-manager`.  

```bash
sudo apt update
sudo apt install python3-pip
```

---

### 2. Installare **qgis-plugin-manager**
- Debian protegge Python con **PEP 668**, che impedisce di installare pacchetti globali con `pip` per non rompere pacchetti gestiti da `apt`.  
- Per bypassare questa restrizione, uso l’opzione `--break-system-packages`.  

```bash
sudo pip3 install qgis-plugin-manager --break-system-packages
```

👉 Con questo comando installi `qgis-plugin-manager`, un tool CLI che gestisce i plugin di QGIS Server direttamente dai repository ufficiali QGIS.  

---

### 3. Inizializzare e aggiornare il gestore dei plugin
```bash
# Mi sposto nella cartella dei plugin
cd /usr/lib/qgis/plugins
# Inizializzo i repository dei plugin (ufficiale QGIS o personalizzati)
sudo qgis-plugin-manager init
# Aggiorno l’indice dei plugin disponibili, scaricando la lista compatibile con la versione di QGIS installata
sudo qgis-plugin-manager update
```

👉 Questo step è fondamentale: senza aggiornamento, non puoi installare plugin perché il gestore non conosce la lista disponibile.  

---

### 4. Installare plugin (es. Lizmap, AtlasPrint, DataPlotly, ecc.)
Ora posso installare i plugin desiderati.  
Esempio:  

```bash
sudo qgis-plugin-manager install 'Lizmap server'
sudo qgis-plugin-manager install 'atlasprint'
sudo qgis-plugin-manager install 'Data Plotly'
sudo qgis-plugin-manager install 'wfsOutputExtension'
```

- `Lizmap server`: plugin per pubblicare progetti QGIS sul web tramite Lizmap.  
- `atlasprint`: plugin per la generazione di atlanti in stampa server-side.  
- `Data Plotly`: plugin per grafici interattivi con Plotly.  
- `wfsOutputExtension`: plugin per estendere l’output WFS di QGIS Server.  

Puoi anche:  
- Cercare un plugin specifico:  
  ```bash
  qgis-plugin-manager search lizmap
  ```
- Elencare quelli già installati:  
  ```bash
  qgis-plugin-manager list
  ```
---

### 🔹 Abilitare autenticazione HTTP Basic
- Alcuni plugin (es. HelloWorld) hanno bisogno di leggere l’header `Authorization`.  
- Di default Apache non passa questo header ai processi FastCGI.  
- Per abilitare il passaggio, aggiungi questa configurazione:  

```bash
sudo nano /etc/apache2/sistes-available/qgis-server.conf
```

```apache
# Needed for QGIS HelloServer plugin HTTP BASIC auth
<IfModule mod_fcgid.c>
    RewriteEngine on
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
</IfModule>
```

👉 **Nota:** questo richiede che il modulo `mod_rewrite` sia attivato.  
Puoi abilitarlo con:  

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

---

## ✅ Conclusione
Dopo aver seguito questi passaggi:  
- QGIS Server può caricare plugin dalla directory predefinita o personalizzata.  
- I plugin possono essere installati manualmente o con `qgis-plugin-manager`.  
- Apache è configurato per supportare correttamente i plugin, incluse le autenticazioni.  
- Plugin come **Lizmap**, **AtlasPrint** o **HelloWorld** funzionano correttamente.  
