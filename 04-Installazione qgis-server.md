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

e che l’eseguibile funzioni:

```bash
/usr/lib/cgi-bin/qgis_mapserv.fcgi
```

Se l’output mostra la versione, QGIS Server è installato correttamente ✅

---

## 7️⃣ Configurazione Apache (prossimo passo)

Dopo l’installazione, bisogna configurare Apache per esporre i servizi di QGIS Server sul web.

---
