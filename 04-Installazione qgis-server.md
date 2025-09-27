# 🗺️ Installazione di QGIS Server su Debian 13 (Trixie)

👉 Guida ufficiale: [QGIS Server Guida/Manuale](https://docs.qgis.org/3.40/it/docs/server_manual/index.html)

---

## 1️⃣ Preparazione

Su **Debian 13** non serve installare pacchetti aggiuntivi come `gnupg` o `software-properties-common`, quindi **saltiamo questo passaggio**.

---

## 2️⃣ Aggiunta delle chiavi

Scarichiamo la chiave del repository QGIS:

```bash
sudo mkdir -m755 -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
```

> ℹ️ La cartella `/etc/apt/keyrings` è già presente in Debian 12+ (apt ≥ 2.4.0), quindi eventualmente non serve crearla manualmente.

---

## 3️⃣ Creazione del repository QGIS

Creiamo il file dei repository:

```bash
sudo nano /etc/apt/sources.list.d/qgis.sources
```

Incolliamo al suo interno:

```text
Types: deb deb-src
URIs: https://qgis.org/debian
Suites: trixie
Architectures: amd64
Components: main
Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg
```

---

## 4️⃣ Aggiornamento pacchetti

Aggiorniamo la lista dei pacchetti:

```bash
sudo apt update
```

---

## 5️⃣ Installazione di QGIS Server

Installiamo QGIS Server:

```bash
sudo apt install qgis-server
```

---

## 6️⃣ Test dell’installazione
Verifichiamo la versione di qgis installate dando:
```bash
/usr/lib/cgi-bin/qgis_mapserv.fcgi --version
```

questo dovrebbe essere il risultato:
```
QGIS 3.44.3-Solothurn 'Solothurn' (1d1d67e9edd)
QGIS code revision 1d1d67e9edd
Qt version 5.15.15
Python version 3.13.5
GDAL/OGR version 3.10.3
PROJ version 9.6.0
EPSG Registry database version v12.004 (2025-03-02)
GEOS version 3.13.1-CAPI-1.19.2
SQLite version 3.46.1
OS Debian GNU/Linux 13 (trixie)
```

e che l’eseguibile funzioni:

```
/usr/lib/cgi-bin/qgis_mapserv.fcgi
```

questo dovrebbe essere il risultato:
```
Application path not initialized
Application path not initialized
Application path not initialized
Application path not initialized
QStandardPaths: XDG_RUNTIME_DIR not set, defaulting to '/tmp/runtime-gisadmin'
Warning 1: Unable to find driver ECW to unload from GDAL_SKIP environment variable.
Warning 1: Unable to find driver ECW to unload from GDAL_SKIP environment variable.
Warning 1: Unable to find driver JP2ECW to unload from GDAL_SKIP environment variable.
"Loading native module /usr/lib/qgis/server/liblandingpage.so"
"Loading native module /usr/lib/qgis/server/libwcs.so"
"Loading native module /usr/lib/qgis/server/libwfs.so"
"Loading native module /usr/lib/qgis/server/libwfs3.so"
"Loading native module /usr/lib/qgis/server/libwms.so"
"Loading native module /usr/lib/qgis/server/libwmts.so"
Content-Length: 0
Location: http:/index.json
Server:  QGIS FCGI server - QGIS version 3.44.3-Solothurn
Status:  302
```


Se l’output mostra la versione, QGIS Server è installato correttamente ✅

---

## 7️⃣ Configurazione Apache (prossimo passo)

Dopo l’installazione, bisogna configurare Apache per esporre i servizi di QGIS Server sul web.

---
