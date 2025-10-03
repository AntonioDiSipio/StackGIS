## PostgreSQL su Debian 13
Pagina ufficiale [https://www.postgresql.org/](https://www.postgresql.org/)

PostgreSQL è un potente database relazionale open-source, molto utilizzato in ambito GIS grazie all'estensione **PostGIS**.

Su Debian 13 può essere installato facilmente tramite i repositoryufficiali.

Se necessario, è possibile aggiungere il repository ufficiale PostgreSQL (PGDG) per disporre di versioni più recenti rispetto a quelle incluse in Debian.

Una volta installato, il servizio si avvia automaticamente e può essere amministrato tramite l'utente di sistema `postgres`.

Per l'integrazione con **QGIS Server** è consigliato configurare i file **`.pg_service.conf`** e **`.pgpass`**, che consentono di gestire connessioni e credenziali in modo centralizzato e sicuro, evitando di inserire username e password direttamente nei progetti.

-----

## Installazione

Installiamo postgresql dando il comando
```bash
apt install postgresql-17
```
questo installerà la versione di default di postgresql, ovvero ad oggi su Debian trixie la 17.

----

# Amministrare postgresql

Entrare nel database con
```bash
sudo -u postgres psql
```

-----

# Aggiungere l'estensione spaziale Postgis

```bash
sudo apt install postgresql-17-postgis-3 postgresql-17-postgis-3-scripts
```
entrare nel database
```bash
sudo -u postgres psql
```
ed abilitare Postgis

```bash
CREATE EXTENSION postgis;
-- enabling raster support
CREATE EXTENSION postgis_raster;
-- enabling advanced 3d support
CREATE EXTENSION postgis_sfcgal;
-- enabling SQL/MM Net Topology
CREATE EXTENSION postgis_topology;
-- using US census data for geocoding and standardization
CREATE EXTENSION address_standardizer;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
-- need to upgrade the postgis packaged extensions do 
SELECT postgis_extensions_upgrade();
```

modificare il file gisserver.conf per aggiungere la cartella dove vengono salvati i file **`.pg_service.conf`** e **`.pgpass`**
```bash
sudo nano /etc/apache2/sites-available/gisserver.conf
```
ed aggiungiamo il percorso dove verranno salvati i due file
```bash
SetEnv PGSERVICEFILE /gisserver/.pg_service.conf
FcgidInitialEnv PGPASSFILE "/gisserver/.pgpass"
```


