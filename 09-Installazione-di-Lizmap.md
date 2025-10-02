# 09 - Installazione di Lizmap Web Client

Guida completa: [Requisiti necessari per Lizmap Web Client](https://docs.lizmap.com/3.9/it/install/pre_requirements.html)

## 1. Conoscenze necessarie
- Amministrazione di un server (Nginx/Apache, stack **LAMP**).  
- **PHP** (Lizmap è un’applicazione PHP e richiede un ambiente configurato).  
- Variabili d’ambiente, log di sistema e QGIS Server.  
- Gestione PostgreSQL e debug HTTP (cURL).  

## 2. Dati GIS e file QGS
- Archiviazione libera dei dati (es. `/srv/lizmap/data/`).  
- Definire `rootRepositories` o usare percorsi assoluti.  
- Nessun trasferimento automatico: caricare i file manualmente sul server.  

## 3. QGIS Server
- Usare **stessa versione** di QGIS Desktop e Server.  
- Consigliato **Py-QGIS-Server** (altrimenti configurare FCGI).  
- Impostare `QGIS_OPTIONS_PATH` e installare **XVFB** (per stampa PDF).  
- Verificare che l’URL di QGIS Server funzioni (XML atteso).  
- Proteggere l’host: QGIS Server deve essere accessibile solo da Lizmap.  

## 4. Plugin QGIS Server
- **Obbligatorio:** `Lizmap server`.  
- **Opzionali:** AtlasPrint, Cadastre, DataPlotly, WfsOutputExtension.  
- Installazione consigliata con `qgis-plugin-manager`.  
- Attenzione: non confondere con il plugin desktop Lizmap.  

## 5. Configurazione API Lizmap
- Con FCGI: URL API vuoto.  
- Con Py-QGIS-Server: configurare endpoint `/lizmap`.  
- Variabile d’ambiente: `QGIS_SERVER_LIZMAP_REVEAL_SETTINGS=True`.  
- Proteggere `http://your.qgis.server.url/lizmap/server.json`.  

## 6. PostgreSQL
- Possibili usi:  
  1. Archiviazione dati GIS.  
  2. Gestione utenti e azioni Lizmap.  
  3. Funzione `lizmap_search`.  
