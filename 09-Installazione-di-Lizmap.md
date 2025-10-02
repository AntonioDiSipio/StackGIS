# Requisiti per Lizmap Web Client

## Conoscenze richieste
- Amministrazione server (Nginx, Apache, variabili d’ambiente, log, cURL).  
- Gestione database PostgreSQL.  

## Dati GIS
- Archiviazione libera (es. `/srv/lizmap/data/`).  
- Trasferimento file manuale dal desktop al server.  

## QGIS Server
- Stessa versione di QGIS Desktop e Server.  
- Consigliato uso di **Py-QGIS-Server**.  
- Installare **XVFB** per stampa PDF.  
- Proteggere l’URL del server (solo accesso via Lizmap).  

## Plugin QGIS Server
- **Obbligatorio:** Lizmap server plugin.  
- **Opzionali:** AtlasPrint, Cadastre, DataPlotly, WfsOutputExtension.  
- Installabili con `qgis-plugin-manager`.  

## Amministrazione Lizmap
- Configurare endpoint API Lizmap (diverso per FCGI o Py-QGIS-Server).  
- Proteggere `/lizmap/server.json` da accessi esterni.  

## PostgreSQL
- Possibili usi:
  - Archiviazione dati GIS.  
  - Gestione utenti e azioni Lizmap.  
  - Funzione `lizmap_search`.
