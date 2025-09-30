# QGIS Server

## Cosa è QGIS Server

QGIS Server è un’applicazione open source scritta in C++ che si integra con un server web (come Apache o Nginx) e implementa servizi cartografici compatibili con gli standard OGC (come WMS, WFS, WCS), con supporto anche per OGC API for Features (WFS3).

Utilizza le stesse librerie del client desktop QGIS, quindi il rendering cartografico, la simbologia e gli stili definiti nei progetti desktop possono essere riprodotti **così come sono** sul server.

QGIS Server supporta inoltre plugin in Python per estendere le sue funzionalità.

---

## A cosa serve / quali servizi offre

| Servizio / funzione | Descrizione |
|---------------------|-------------|
| **WMS (Web Map Service)** | Genera immagini (mappe) su richiesta secondo stili e layer definiti nel progetto QGIS. |
| **WFS (Web Feature Service)** | Permette l’accesso ai dati vettoriali (feature) per lettura, interrogazione e, se configurato, modifica. |
| **WCS (Web Coverage Service)** | Consente l’accesso a dati raster come coperture e immagini geospaziali. |
| **OGC API for Features / WFS3** | Interfaccia moderna RESTful per l’accesso ai feature geospaziali. |
| **Rendering avanzato / cartografia tematica** | Supporta simboli, etichette, regole di classifica e stili complessi, come in QGIS Desktop. |
| **Integrazione con progetti QGIS** | I progetti creati in QGIS Desktop possono essere pubblicati direttamente su QGIS Server senza conversioni. |
| **Estensibilità tramite plugin Python** | Aggiunta di funzionalità personalizzate, filtri e automazioni lato server. |

---

## Vantaggi / punti di forza

- Coerenza tra ambiente desktop e server: i progetti QGIS usati localmente possono essere serviti senza conversioni.  
- Aderenza agli standard OGC = interoperabilità con client GIS, applicazioni web e altri server.  
- Flessibilità e personalizzazione grazie a plugin e configurazioni.  
- Open source: senza costi di licenza, con possibilità di contributo e adattamento.  
- Implementazione di riferimento per lo standard **WMS 1.3**.


---


# 🗺️ Installazione di QGIS Server su Debian 13 (Trixie)

👉 Pagina ufficiale di QGIS Installers: [QGIS Server Guida/Manuale](https://qgis.org/resources/installation-guide/#linux)

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

## 8 Configurazione Apache (prossimo passo)

Dopo l’installazione, bisogna configurare Apache per esporre i servizi di QGIS Server sul web.

---
