# 🌍 StackGIS / VPS / QGIS / QGIS Server / Lizmap

***StackGIS*** è un progetto dedicato all’installazione, configurazione e gestione di una VPS per l’erogazione di servizi GIS con **QGIS Server** e **Lizmap**. Questo repository raccoglie la documentazione tecnica necessaria per replicare e mantenere l’infrastruttura, facilitando sia la gestione sistemistica sia l’aggiornamento dei servizi cartografici.

---

## 🛠️ Tecnologie

<table align="center">
  <tr>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://www.debian.org/logos/openlogo-nd-100.png" height="70"/><br/>
      <b>Debian</b>
    </td>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://upload.wikimedia.org/wikipedia/commons/9/91/QGIS_logo_new.svg" height="70"/><br/>
      <b>QGIS</b>
    </td>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://upload.wikimedia.org/wikipedia/commons/7/7e/Apache_Feather_Logo.svg" height="70"/><br/>
      <b>Apache</b>
    </td>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://postgis.net/brand.svg" height="70"/><br/>
      <b>PostGIS</b>
    </td>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://upload.wikimedia.org/wikipedia/commons/c/c3/Python-logo-notext.svg" height="70"/><br/>
      <b>Python</b>
    </td>
    <td align="center" style="border: none; padding: 0 50px;">
      <img src="https://docs.lizmap.com/3.8/it/_static/logo.png" height="70"/><br/>
      <b>Lizmap</b>
    </td>
</tr>
</table>

---

## 📂 Contenuto

- 🖥️ [01 - Dati della macchina](01-server-data.md)  
  Dettagli tecnici e caratteristiche della VPS (sistema operativo, risorse, configurazione iniziale).

- 🔑 [02 - Creazione utenti](02-creazione-utenti.md)  
  Procedura di creazione e configurazione dell'utente `gisadmin` con privilegi di amministratore e accesso tramite chiave SSH.

- 🔒 [03 - Hardening VPS](03-hardening-VPS.md)  
  Configurazioni di sicurezza con **UFW**, **Fail2ban** e aggiornamenti automatici.

- 🗺️ [04 - Installazione QGIS Server](04-Installazione-qgis-server.md)  
  Guida passo-passo per installare e verificare **QGIS Server** su Debian 13.

- 🪶 [05 - Installazione e configurazione di Apache](05-Installazione%20e%20configurazione%20di%20Apache.md)  
  Configurazione di Apache per servire QGIS Server e i progetti cartografici.

- 🔌 [06 - Installazione Plugin QGIS Server](06-Installazione-Plugin-Qgis-Server.md)  
  Guida per installare e configurare plugin di QGIS Server (Lizmap, AtlasPrint, ecc.).

- 🐘 [07 - Installazione PostgreSQL/PostGIS](07-Installazione-Postgresql-PostGIS.md)  
  Installazione e configurazione del database PostgreSQL con estensione **PostGIS**.

- 🌐 [10 - Installazione Lizmap](10-Installazione-di-Lizmap.md)  
  Installazione di **Lizmap Web Client** su server Nginx/PHP per la pubblicazione cartografica.

---

## 🏗️ Architettura

```mermaid
flowchart TD
    A[VPS Debian 13] --> B[Apache HTTP Server]
    A --> C[PostgreSQL + PostGIS]
    B --> D[QGIS Server]
    D --> E[Plugin QGIS Server]
    B --> F[Lizmap Web Client]
    F --> G[Utenti Web]
```

---

## 🎯 Obiettivo

Questo repository funge da **documentazione tecnica** e da **promemoria** delle operazioni effettuate sulla VPS, utile per riprodurre o mantenere la configurazione in futuro.

👉 Obiettivo finale: **un’infrastruttura GIS completa**, sicura e documentata, pronta per la pubblicazione di servizi cartografici con **QGIS Server + Lizmap**.
