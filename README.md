# 🌍 StackGIS / VPS / Qgis / Qgiserver / Lizmap

Repository di configurazione e gestione della VPS utilizzata per il progetto **StackGIS**.

---

## 🛠️ Tecnologie

<div style="display: flex; justify-content: center; align-items: center; gap: 40px;">
  <div style="text-align: center;">
    <img src="https://www.debian.org/logos/openlogo-nd-100.png" height="70"/><br/>
    <b>Debian</b>
  </div>
  <div style="text-align: center;">
    <img src="https://upload.wikimedia.org/wikipedia/commons/9/91/QGIS_logo_new.svg" height="70"/><br/>
    <b>QGIS</b>
  </div>
  <div style="text-align: center;">
    <img src="https://upload.wikimedia.org/wikipedia/commons/7/7e/Apache_Feather_Logo.svg" height="70"/><br/>
    <b>Apache</b>
  </div>
  <div style="text-align: center;">
    <img src="https://upload.wikimedia.org/wikipedia/commons/c/c3/Python-logo-notext.svg" height="70"/><br/>
    <b>Python</b>
  </div>
  <div style="text-align: center;">
    <img src="https://docs.lizmap.com/3.8/it/_static/logo.png" height="70"/><br/>
    <b>Lizmap</b>
  </div>
</div>


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

---

## 🎯 Obiettivo

Questo repository funge da **documentazione tecnica** e da **promemoria** delle operazioni effettuate sulla VPS, utile per riprodurre o mantenere la configurazione in futuro.
