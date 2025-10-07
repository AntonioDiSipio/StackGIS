# 🔒 Guida Installazione Certbot (HTTPS con Let’s Encrypt)

## 📋 Requisiti
- Sistema operativo: **Debian 13 / Trixie**
- Server web: **Apache2**
- Utente con privilegi `sudo`
- Dominio correttamente configurato (es. `disipio.io`, `www.disipio.io`)

---

## ⚙️ 1️⃣ Aggiornamento pacchetti

Aggiorna l’indice dei pacchetti e installa `snapd` (necessario per gestire i pacchetti Snap):

```bash
sudo apt update
sudo apt install snapd
```

Durante l’installazione verranno aggiunti i servizi di sistema necessari:

- `snapd.service`
- `snapd.socket`
- `snapd.seeded.service`

---

## 📦 2️⃣ Installazione di Certbot tramite Snap

Una volta installato `snapd`, installa **Certbot** in modalità classica:

```bash
sudo snap install --classic certbot
```

Crea il link simbolico in `/usr/bin` (per richiamarlo più facilmente da terminale):

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

---

## 🔐 3️⃣ Richiesta del certificato HTTPS

Esegui la procedura guidata:

```bash
sudo certbot --apache
```

Durante l’esecuzione:
1. Inserisci il tuo indirizzo email (es. `antonio.disipio@gmail.com`)
2. Accetta i **Termini di Servizio**
3. (Facoltativo) Consenti la condivisione dell’email con la **EFF**
4. Seleziona i domini per cui attivare HTTPS (es. `www.disipio.io`)

---

## 🧾 4️⃣ Esempio di output di installazione riuscita

```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/www.disipio.io/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/www.disipio.io/privkey.pem
This certificate expires on 2026-01-05.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Successfully deployed certificate for www.disipio.io to /etc/apache2/sites-available/disipio-le-ssl.conf
Added an HTTP->HTTPS rewrite in addition to other RewriteRules.
Congratulations! You have successfully enabled HTTPS on https://www.disipio.io
```

---

## 🔁 5️⃣ Estendere il certificato anche al dominio principale

Se vuoi che il certificato copra **sia `disipio.io` che `www.disipio.io`**, esegui:

```bash
sudo certbot --apache -d disipio.io -d www.disipio.io
```

---

## 🌍 6️⃣ Configurare il redirect automatico

Apri il file generato da Certbot:

```bash
sudo nano /etc/apache2/sites-available/disipio-le-ssl.conf
```

E aggiungi queste direttive all’interno del blocco `<VirtualHost *:443>`:

```apache
ServerName disipio.io
ServerAlias www.disipio.io

# Redirigi tutto verso https://disipio.io
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.disipio\.io$ [NC]
RewriteRule ^(.*)$ https://disipio.io/$1 [L,R=301]
```

Poi salva ed esci.

---

## 🔄 7️⃣ Ricarica Apache

```bash
sudo systemctl reload apache2
```

---

## 🧪 8️⃣ Test finale

Verifica che:
- 🔹 `https://disipio.io` → funzioni con il lucchetto verde  
- 🔹 `https://disipio.io/lizmap` → apra Lizmap in HTTPS  
- 🔹 `https://www.disipio.io` → rediriga automaticamente a `https://disipio.io`

---

## 🔁 9️⃣ Rinnovo automatico

Certbot imposta un **cron job automatico**.  
Puoi testarlo manualmente con:

```bash
sudo certbot renew --dry-run
```

Se non vengono mostrati errori, il rinnovo automatico è attivo.

---

## 🧰 10️⃣ File principali generati

| Percorso | Descrizione |
|-----------|--------------|
| `/etc/letsencrypt/live/www.disipio.io/fullchain.pem` | Certificato completo |
| `/etc/letsencrypt/live/www.disipio.io/privkey.pem` | Chiave privata |
| `/etc/apache2/sites-available/disipio-le-ssl.conf` | Configurazione HTTPS automatica |
| `/var/log/letsencrypt/letsencrypt.log` | Log delle operazioni Certbot |

---

## ✅ Risultato finale

- HTTPS attivo su `https://disipio.io` e `https://disipio.io/lizmap`  
- Certificato Let’s Encrypt valido per 90 giorni e rinnovato automaticamente  
- Redirect automatico da HTTP e da `www.` verso il dominio principale




