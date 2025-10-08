# 🐘 Installazione di PostgreSQL e PostGIS su Debian 13

## 🔍 Introduzione

Pagina ufficiale: [https://www.postgresql.org/](https://www.postgresql.org/)

**PostgreSQL** è un potente database relazionale open-source, ampiamente utilizzato in ambito **GIS** grazie all’estensione **PostGIS**, che introduce il supporto nativo ai dati geografici.  

Su **Debian 13 (Trixie)** può essere installato direttamente dai repository ufficiali.  
Se necessario, è possibile aggiungere il repository **PGDG** (PostgreSQL Global Development Group) per disporre di versioni più recenti rispetto a quelle incluse di default in Debian.  

Una volta installato, il servizio si avvia automaticamente e può essere amministrato tramite l’utente di sistema `postgres`.  

Per l’integrazione con **QGIS Server** è consigliato configurare i file **`.pg_service.conf`** e **`.pgpass`**, che consentono di memorizzare connessioni e credenziali in modo centralizzato e sicuro, evitando di includere username e password direttamente nei progetti QGIS.

---

## 1️⃣ Installazione di PostgreSQL

Installare **PostgreSQL** utilizzando il comando:

```bash
sudo apt install postgresql-17
```

Questo comando installerà la versione predefinita disponibile su Debian Trixie, attualmente **PostgreSQL 17**.

---

## 2️⃣ Aggiunta dell’estensione spaziale PostGIS

Installare i pacchetti relativi a **PostGIS** e agli script associati:

```bash
sudo apt install postgresql-17-postgis-3 postgresql-17-postgis-3-scripts
```

Accedere alla console di PostgreSQL come utente `postgres`:

```bash
sudo -u postgres psql
```

Abilitare le estensioni spaziali eseguendo i comandi SQL seguenti:

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

---

## 3️⃣ Configurazione dell’ambiente Apache per i file di servizio PostgreSQL

Per garantire la connessione sicura tra **QGIS Server** e i database PostgreSQL, è consigliato specificare la posizione dei file di servizio e delle credenziali all’interno della configurazione di Apache.  

Aprire il file di configurazione del Virtual Host del server QGIS:

```bash
sudo nano /etc/apache2/sites-available/qgis-server.conf
```

Aggiungere le seguenti righe, indicando la posizione in cui verranno salvati i file `.pg_service.conf` e `.pgpass`:

```bash
SetEnv PGSERVICEFILE /gisserver/.pg_service.conf
FcgidInitialEnv PGPASSFILE "/gisserver/.pgpass"
```

Salvare e chiudere il file, quindi riavviare Apache se necessario:

```bash
sudo systemctl restart apache2
```

---

## 4️⃣ Creazione di un database e di un utente dedicato per Lizmap

Accedere alla shell PostgreSQL come utente `postgres`:

```bash
sudo -u postgres psql
```

Eseguire i seguenti comandi SQL per creare l’utente e il database:

```bash
-- 1. Crea l’utente lizmapadmin con password
CREATE ROLE lizmapadmin WITH LOGIN PASSWORD 'la_tua_password';

-- 2. Assegna privilegi di superuser e permessi avanzati
ALTER ROLE lizmapadmin WITH SUPERUSER CREATEDB CREATEROLE REPLICATION;

-- 3. Crea il database lizmap e imposta lizmapadmin come proprietario
CREATE DATABASE lizmap OWNER lizmapadmin;

-- 4. (Opzionale ma consigliato) abilita PostGIS
\c lizmap
CREATE EXTENSION postgis;
```

---

## ✅ Risultato finale

Dopo l’esecuzione di questi passaggi:
- **PostgreSQL 17** e **PostGIS 3** risultano installati e funzionanti.  
- L’estensione spaziale è attiva e pronta per essere utilizzata da **QGIS Server** e **Lizmap Web Client**.  
- La configurazione di Apache è predisposta per gestire le connessioni in modo sicuro tramite i file di servizio `.pg_service.conf` e `.pgpass`.  
- È stato creato un database dedicato a Lizmap con un utente amministrativo dedicato.  
