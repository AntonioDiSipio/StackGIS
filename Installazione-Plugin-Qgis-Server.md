## Installare Plugin per qgis server
Per impostazione predefinita, sui sistemi basati su Debian, QGIS Server cercherà i plugin situati in /usr/lib/qgis/plugins. Il valore predefinito viene visualizzato all’avvio di QGIS Server, nei log. È possibile impostare un percorso personalizzato definendo la variabile d’ambiente ```QGIS_PLUGINPATH``` nella configurazione del server web.
I plugin possono essere installati sia manualmente che utilizzando un gestore di plugin python

Manualmente con uno ZIP
-
Ad esempio, per installare il plugin HelloWorld per testare il server, utilizzando una cartella specifica, devi prima creare una cartella per contenere i plugin del server.
Questa sarà specificata nella configurazione dell’host virtuale e passata al server attraverso una variabile d’ambiente.
Io volgio installare i miei plugin in ```/usr/share/qgis/python/plugins```
```bash
cd /var/www/qgis-server/plugins
wget https://github.com/elpaso/qgis-helloserver/archive/master.zip
unzip master.zip
mv qgis-helloserver-master HelloServer
```

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
sudo pip3 install qgis-plugin-manager --break-system-packages
```
```
cd /usr/lib/qgis/plugins
```

Devi dire a qgis-plugin-manager quali repository usare (quello ufficiale di QGIS, o uno personalizzato).
Quindi fai:
```
sudo qgis-plugin-manager init
```
```
sudo qgis-plugin-manager update
```

Installa un plugin di prova (es. Lizmap):
```
sudo qgis-plugin-manager install 'Lizmap server'
```
