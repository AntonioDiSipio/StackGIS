# 🗃️ Installazione PostgreSQL e PostGIS

Installare PostgreSQL 17 e PostGIS:

```bash
sudo apt install postgresql-17 postgresql-17-postgis-3 postgresql-17-postgis-3-scripts
```

Creare utente e database per Lizmap:

```bash
sudo -u postgres psql
CREATE ROLE lizmapadmin WITH LOGIN PASSWORD 'strongpassword';
CREATE DATABASE lizmap OWNER lizmapadmin;
\c lizmap
CREATE EXTENSION postgis;
```
