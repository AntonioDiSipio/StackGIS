qgis-server è installato seguendo la guida sulla pagina ufficiale

https://docs.qgis.org/3.40/it/docs/server_manual/index.html

--------

Nel mio caso ho installato ho seguito la procedura di installaizone su sistemi Debian
La pagina ufficiale ci rimanda al seguente pagina per installare QGIS Server su un sistema basato su Debian sono forniti in`QGIS installers page <https://qgis.org/resources/installation-guide/#linux>. Dovresti installare l’ultima versione a lungo termine.

**saltiamo questo comando perchè in Debian 13 non è necessario**

```
sudo apt install gnupg software-properties-common
```
e passiamo direttamente all'installazione delle chiavi per installare qgis server

```
sudo mkdir -m755 -p /etc/apt/keyrings  # not needed since apt version 2.4.0 like Debian 12 and Ubuntu 22 or newer
sudo wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
```
aggiungiamo i repo per l'ultima versione stabile di qgis nel file `/etc/apt/sources.list.d/qgis.sources`

quindi creiamo il file con questo comando

```
sudo nano /etc/apt/sources.list.d/qgis.sources
```

e incolliamoci dentro queste righe

```
Types: deb deb-src
URIs: https://qgis.org/debian
Suites: trixie
Architectures: amd64
Components: main
Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg
```

adesso aggiorniamo il sistema dando

```
sudo apt update
```

ed installiamo qgis-server con 

```
sudo apt install qgis-server
```

finita la procedura di installazione testiamo qgis server con il comando  

```
/usr/lib/cgi-bin/qgis_mapserv.fcgi
```
