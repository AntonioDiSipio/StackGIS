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
quindi ```sudo nano /etc/apache2/sites-available/gisserver.conf``` ed incollare questo valore 
```bash
FcgidInitialEnv QGIS_SERVER_LIZMAP_REVEAL_SETTINGS True
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
[07-Installazione-Postgresql+Postgis](07-Installazione-Postgresql-Postgis.md)

