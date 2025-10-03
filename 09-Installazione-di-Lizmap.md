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
```bash
FcgidInitialEnv QGIS_SERVER_LIZMAP_REVEAL_SETTINGS True
```
