# 🗺️ Installazione di QGIS Server su Debian 13

QGIS Server fornisce servizi OGC (WMS, WFS, WCS) e supporta plugin Python per estendere le funzionalità.

## Installazione

```bash
sudo mkdir -m755 -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
```

Creare `/etc/apt/sources.list.d/qgis.sources`:

```
Types: deb deb-src
URIs: https://qgis.org/debian
Suites: trixie
Architectures: amd64
Components: main
Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg
```

Aggiornare e installare:

```bash
sudo apt update
sudo apt install qgis-server python3-qgis
```

Verifica:

```bash
/usr/lib/cgi-bin/qgis_mapserv.fcgi --version
```
