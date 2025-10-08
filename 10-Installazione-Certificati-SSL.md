# 🔒 Installazione di Certificati SSL con Let’s Encrypt (Certbot)

## 📋 Requisiti
- Sistema operativo: **Debian 13 / Trixie**
- Server web: **Apache2**
- Utente con privilegi `sudo`
- Dominio correttamente configurato (esempio: `example.it`, `www.example.it`)

---

## ⚙️ 1️⃣ Aggiornamento pacchetti

Aggiornare l’indice dei pacchetti e installare **snapd**, necessario per la gestione dei pacchetti Snap:

```bash
sudo apt update
sudo apt install snapd
```

Durante l’installazione verranno aggiunti automaticamente i seguenti servizi di sistema:

- `snapd.service`
- `snapd.socket`
- `snapd.seeded.service`

---

## 📦 2️⃣ Installazione di Certbot tramite Snap

Dopo l’installazione di `snapd`, installare **Certbot** in modalità classica:

```bash
sudo snap install --classic certbot
```

Creare un link simbolico in `/usr/bin` per facilitarne l’esecuzione:

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

---

## 🔐 3️⃣ Richiesta automatica del certificato HTTPS

Eseguire la procedura guidata:

```bash
sudo certbot --apache
```

Durante l’esecuzione:
1. Inserire un indirizzo email valido per ricevere notifiche sui certificati.
2. Accettare i **Termini di Servizio**.
3. (Facoltativo) Acconsentire alla condivisione dell’email con la **EFF**.
4. Selezionare i domini per cui attivare HTTPS (es. `example.it`, `www.example.it`).

Certbot configurerà automaticamente Apache per:
- creare i file `.conf` necessari in `/etc/apache2/sites-available/`
- attivare il redirect automatico da HTTP a HTTPS
- applicare il certificato SSL
- impostare il rinnovo automatico

---

## 🧾 4️⃣ Esempio di output di installazione riuscita

```
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/example.it/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/example.it/privkey.pem
This certificate expires on 2026-01-05.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Successfully deployed certificate for example.it to /etc/apache2/sites-available/example-le-ssl.conf
Added an HTTP->HTTPS rewrite in addition to other RewriteRules.
Congratulations! You have successfully enabled HTTPS on https://example.it
```

---

## 🔁 5️⃣ Estensione del certificato a più domini

Per includere sia il dominio principale che il sottodominio `www`, eseguire:

```bash
sudo certbot --apache -d example.it -d www.example.it
```

---

## 🔄 6️⃣ Verifica e ricarica di Apache

Certbot riavvia o ricarica automaticamente Apache.  
Per verificarne lo stato:

```bash
sudo systemctl status apache2
```

In alternativa, è possibile forzare manualmente un reload:

```bash
sudo systemctl reload apache2
```

---

## 🧪 7️⃣ Test finale

Verificare che:
- 🔹 `https://example.it` → funzioni correttamente e mostri il lucchetto verde
- 🔹 `https://example.it/lizmap` → apra Lizmap in HTTPS
- 🔹 `http://example.it` → venga rediretto automaticamente a `https://example.it`

---

## 🔁 8️⃣ Rinnovo automatico del certificato

Certbot configura automaticamente un **cron job** per il rinnovo dei certificati Let’s Encrypt.  
È possibile testare il processo di rinnovo con:

```bash
sudo certbot renew --dry-run
```

Se non vengono mostrati errori, il rinnovo automatico è correttamente funzionante.

---

## 🧰 9️⃣ File principali generati

| Percorso | Descrizione |
|-----------|-------------|
| `/etc/letsencrypt/live/example.it/fullchain.pem` | Certificato completo |
| `/etc/letsencrypt/live/example.it/privkey.pem` | Chiave privata |
| `/etc/apache2/sites-available/example-le-ssl.conf` | Configurazione HTTPS generata da Certbot |
| `/var/log/letsencrypt/letsencrypt.log` | Log delle operazioni di Certbot |

---

## ✅ Risultato finale

Dopo la procedura:
- HTTPS è attivo su `https://example.it` e `https://example.it/lizmap`.
- Apache è configurato automaticamente da Certbot, senza necessità di modifiche manuali.
- Il certificato Let’s Encrypt è valido per 90 giorni e si rinnova in modo automatico.
- Tutti i redirect HTTP → HTTPS vengono gestiti direttamente da Certbot.
