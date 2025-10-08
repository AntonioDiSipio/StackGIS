# 🧩 Installazione di PHP e dei pacchetti necessari

⚠️ **Avvertenza**  
È necessario installare almeno **PHP 7.4** o una versione superiore.  
Le estensioni richieste sono:  
- `dom`  
- `simplexml`  
- `pcre`  
- `session`  
- `tokenizer`  
- `spl`  

Queste estensioni risultano normalmente abilitate di default in un’installazione standard di PHP 7 o PHP 8.

---

## 1️⃣ Aggiornamento dei pacchetti e installazione delle dipendenze di base

Aggiornare la lista dei pacchetti e installare le dipendenze fondamentali:

```bash
sudo apt update # aggiorna la lista pacchetti
sudo apt install curl openssl libssl3
```

---

## 2️⃣ Installazione dei pacchetti PHP richiesti

Installare PHP 8.4 e i moduli necessari al funzionamento di **Lizmap Web Client** e **QGIS Server**:

```bash
sudo apt install php8.4-fpm php8.4-cli php8.4-bz2 php8.4-curl php8.4-gd php8.4-intl php8.4-mbstring php8.4-pgsql php8.4-sqlite3 php8.4-xml php8.4-ldap php8.4-redis
```

---

## 3️⃣ Abilitazione dei moduli Apache per PHP-FPM

Abilitare i moduli richiesti per l’integrazione tra **Apache** e **PHP-FPM**:

```bash
sudo a2enmod proxy_fcgi setenvif
sudo a2enconf php8.4-fpm
```

---

## 4️⃣ Riavvio del servizio Apache

Applicare le modifiche riavviando il servizio Apache:

```bash
sudo systemctl restart apache2
```

---

## ✅ Risultato finale

Dopo aver completato l’installazione:
- **PHP 8.4** risulta correttamente installato e configurato.  
- Tutte le estensioni necessarie per l’utilizzo con **Lizmap Web Client** e **QGIS Server** sono attive.  
- **Apache** è pronto per gestire richieste PHP tramite **PHP-FPM**, garantendo migliori prestazioni e isolamento dei processi.
