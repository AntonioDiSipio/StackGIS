# Installazione pacchetti necessari

⚠️ **Avvertimento**  
È necessario installare almeno **PHP 7.4** o superiore.  
Le estensioni richieste sono:  
- `dom`  
- `simplexml`  
- `pcre`  
- `session`  
- `tokenizer`  
- `spl`  

Queste estensioni sono generalmente abilitate di default in una normale installazione PHP 7/8.

---

## Aggiornare i pacchetti e installare dipendenze di base

```bash
sudo apt update # aggiorna la lista pacchetti
sudo apt install curl openssl libssl3
```

---

## Installare i pacchetti PHP richiesti

```bash
sudo apt install php8.2-fpm php8.2-cli php8.2-bz2 php8.2-curl php8.2-gd php8.2-intl php8.2-mbstring php8.2-pgsql php8.2-sqlite3 php8.2-xml php8.2-ldap php8.2-redis
```
