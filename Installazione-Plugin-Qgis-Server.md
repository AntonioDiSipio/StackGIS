## Installare Plugin per qgis server
Per impostazione predefinita, sui sistemi basati su Debian, QGIS Server cercherà i plugin situati in /usr/lib/qgis/plugins. Il valore predefinito viene visualizzato all’avvio di QGIS Server, nei log. È possibile impostare un percorso personalizzato definendo la variabile d’ambiente ```QGIS_PLUGINPATH``` nella configurazione del server web.
Installiamo ora i plugin dper qgis server, questo ci servirà successivamente per configurare lizmap

diamo il comando per installare pip
pip è il gestore di pacchetti di Python.
Serve per installare, aggiornare e rimuovere librerie e moduli scritti in Python da PyPI (Python Package Index)
```bash
sudo apt update
sudo apt install python3-pip
```
adesso lanciano il comando
```pip3 install qgis-plugin-manager``` per instllare qgis-plugin-manager
Debian potrebbe bloccare l'installazione perchè il sistema Python è “gestito esternamente” (PEP 668).
In pratica Debian vuole che non si installino pacchetti Python globalmente con pip, per non rompere i pacchetti gestiti da apt, allora forziamo pip sul sistema con
```
pip install --break-system-packages qgis-plugin-manager
```
