# 🗺️ Installazione di QGIS Server su Debian 13 (Trixie)

## 🔍 Introduzione

**QGIS Server** è un’applicazione open source scritta in C++ che si integra con un server web (come Apache o Nginx) e fornisce servizi cartografici conformi agli standard OGC (WMS, WFS, WCS), con supporto anche per **OGC API for Features (WFS3)**.  
Utilizza le stesse librerie del client desktop **QGIS**, permettendo di riprodurre fedelmente rendering, simbologie e stili dei progetti desktop direttamente sul server.  
È inoltre estendibile tramite **plugin Python**, che consentono di aggiungere funzioni personalizzate lato server.

---

## 🧭 Servizi offerti

| Servizio / funzione | Descrizione |
|---------------------|-------------|
| **WMS (Web Map Service)** | Genera mappe rasterizzate su richiesta, rispettando layer e stili del progetto QGIS. |
| **WFS (Web Feature Service)** | Consente l’accesso ai dati vettoriali per lettura e interrogazione (e modifica, se configurato). |
| **WCS (Web Coverage Service)** | Fornisce dati raster geospaziali e coperture. |
| **OGC API for Features / WFS3** | Interfaccia RESTful moderna per la gestione di feature geografiche. |
| **Rendering avanzato / cartografia tematica** | Supporta simboli, etichette, regole e stili complessi identici a QGIS Desktop. |
| **Integrazione con progetti QGIS** | Permette di pubblicare direttamente progetti QGIS Desktop senza conversioni. |
| **Estensibilità tramite plugin Python** | Consente di ampliare le funzionalità e automatizzare processi lato server. |

---

## ⚙️ Punti di forza

- Coerenza tra ambiente desktop e server.  
- Conformità agli standard **OGC**, garantendo interoperabilità con altri sistemi GIS.  
- Ampia possibilità di personalizzazione tramite **plugin**.  
- Software **open source**, libero da licenze proprietarie.  
- Riferimento ufficiale per lo standard **WMS 1.3**.

---

## 📖 Riferimenti ufficiali

Documentazione e pacchetti aggiornati sono disponibili alla pagina ufficiale:  
👉 [QGIS Server - Installation Guide](https://qgis.org/resources/installation-guide/#linux)

---

## 🔧 1️⃣ Preparazione

Su **Debian 13** non è necessario installare pacchetti preliminari come `gnupg` o `software-properties-common`, in quanto già inclusi.  
Pertanto, questo passaggio può essere saltato.

---

## 🔑 2️⃣ Aggiunta delle chiavi del repository QGIS

```bash
sudo mkdir -m755 -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
```

> ℹ️ La directory `/etc/apt/keyrings` è normalmente presente in Debian 12+ (apt ≥ 2.4.0); in caso contrario, viene creata automaticamente.

---

## 📦 3️⃣ Creazione del repository QGIS

Creare il file di configurazione per i repository:

```bash
sudo nano /etc/apt/sources.list.d/qgis.sources
```

Incollare il seguente contenuto:

```text
Types: deb deb-src
URIs: https://qgis.org/debian
Suites: trixie
Architectures: amd64
Components: main
Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg
```

---

## 🔁 4️⃣ Aggiornamento pacchetti

Aggiornare la lista dei pacchetti disponibili:

```bash
sudo apt update
```

---

## 🧩 5️⃣ Installazione di QGIS Server

Installare **QGIS Server** e, se necessario, i moduli Python aggiuntivi:

```bash
sudo apt install qgis-server --no-install-recommends --no-install-suggests
# se si desidera installare i plugin Python per il server
sudo apt install python3-qgis
```

---

## 🧪 6️⃣ Verifica dell’installazione

Verificare la versione installata di QGIS Server:

```bash
/usr/lib/cgi-bin/qgis_mapserv.fcgi --version
```

Esempio di output previsto:

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

Verificare inoltre che l’eseguibile risponda correttamente:

```bash
/usr/lib/cgi-bin/qgis_mapserv.fcgi
```

Esempio di risposta:

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

Se l’output riporta la versione e i moduli caricati, **QGIS Server è installato correttamente** ✅

---

## 🌐 7️⃣ Configurazione Apache (passo successivo)

Completata l’installazione, il passaggio seguente consiste nella **configurazione di Apache** per pubblicare i servizi QGIS Server sul web.  
Le istruzioni sono riportate nel file dedicato all’installazione e configurazione di **Apache HTTP Server**.
