## Apache HTTP Server

[Apache HTTP Server](https://httpd.apache.org/), spesso chiamato semplicemente **Apache**, è un server web open source molto diffuso, sviluppato e mantenuto dalla Apache Software Foundation.  

Il suo scopo principale è gestire le richieste HTTP provenienti dai client (ad esempio i browser), servendo pagine web, risorse statiche (immagini, file CSS/JS) oppure instradando applicazioni web dinamiche tramite moduli.  

Progettato per essere **sicuro**, **efficiente** ed **estensibile**, Apache offre un’architettura modulare che consente di aggiungere funzionalità come il rewriting degli URL, l’autenticazione o l’utilizzo come proxy, adattandosi a diverse esigenze.

Installazione di Apache
---
Installiamo apache lanciando il comando:
```bash
sudo apt install apache2 libapache2-mod-fcgid
```
al termine del processo di installazione e aver avviato apache se apri un browser e digiti ```http://tuo-server/``` dovrebbe comparire la pagina web di default di apache

<p align="center">
  <img src="img/apachedefaultpage.jpg" width="200">
</p>

⚠️ **Nota bene:**  
Se la pagina di default di Apache non dovesse essere visibile, verifica che il **firewall** non stia bloccando la porta **80** (HTTP).  
Su sistemi con `ufw`, ad esempio, puoi aprire la porta con:  
 
```bash
sudo ufw allow 80/tcp
sudo ufw reload
```
  
Se invece usi HTTPS, ricordati di aprire anche la porta **443**:  
 
```bash
sudo ufw allow 443/tcp
sudo ufw reload
```



