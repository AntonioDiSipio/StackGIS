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
sudo su # solo se non sei già root
sudo apt update # aggiorna la lista pacchetti
sudo apt install curl openssl libssl3
```

---

## Installare i pacchetti PHP richiesti

```bash
sudo apt install php8.4-fpm php8.4-cli php8.4-bz2 php8.4-curl \
php8.4-gd php8.4-intl php8.4-mbstring php8.4-pgsql \
php8.4-sqlite3 php8.4-xml php8.4-ldap php8.4-redis
```
