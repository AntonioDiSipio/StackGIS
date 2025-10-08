# 🌍 StackGIS — Guida all’installazione e configurazione

Questa documentazione descrive l’installazione completa di una VPS Debian 13 configurata per ospitare **QGIS Server** e **Lizmap Web Client**.

Tutti i contenuti sono scritti in terza persona e non contengono riferimenti personali.

---

## 📚 Indice dei contenuti
1. [01 - Dati della macchina GIS](01-server-data.md)
2. [02 - Creazione utente amministrativo](02-creazione-utenti.md)
3. [03 - Installazione QGIS Server](03-Installazione-qgis-server.md)
4. [04 - Configurazione Apache](04-Installazione e configurazione di Apache.md)
5. [05 - Installazione Plugin QGIS Server](05-Installazione-Plugin-Qgis-Server.md)
6. [06 - Installazione PHP](06-Installazione-PHP.md)
7. [07 - Installazione PostgreSQL + PostGIS](07-Installazione-Postgresql-PostGIS.md)
8. [08 - Installazione Lizmap](08-Installazione-di-Lizmap.md)
9. [09 - Hardening VPS](09-hardening-VPS.md)
10. [10 - Certificati SSL](10-Installazione-Certificati-SSL.md)

---

## ⚙️ Stack tecnologico
| Componente | Versione consigliata | Note |
|-------------|----------------------|------|
| Debian | 13 (Trixie) | Sistema operativo |
| QGIS Server | >= 3.44 | Servizi cartografici |
| Apache | 2.4+ | Con modulo FCGI |
| PHP | 8.4 | Necessario per Lizmap |
| PostgreSQL | 17 | Con PostGIS |
| Lizmap | 3.9 | Web client GIS |

---

## 🔐 Sicurezza
La guida comprende configurazioni per UFW, Fail2ban e certificati SSL tramite Let’s Encrypt.

---

## 📄 Licenza
Rilasciata con licenza MIT per uso libero e modificabile.
