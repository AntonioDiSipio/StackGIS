# 🔒 Hardening VPS

Questa guida riassume i passaggi principali per mettere in sicurezza una VPS Debian 13 usando **UFW** e **Fail2ban**.

---

## ✅ Prerequisiti (già fatti)
- Aggiornato il sistema (`apt update && apt upgrade`).
- Creato utente non-root con sudo.
- Disabilitato login SSH come root.
- Abilitato accesso SSH solo con chiavi.

---

## 1. Configurare UFW (firewall)

Installa UFW (se non presente):
```bash
sudo apt install ufw -y
```

Imposta le policy di default:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Permetti solo i servizi necessari (adatta la porta SSH se diversa da 22):
```bash
sudo ufw allow 22/tcp     # SSH
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS
```

Abilita e verifica:
```bash
sudo ufw enable
sudo ufw status verbose
```

Attiva logging (opzionale):
```bash
sudo ufw logging on
```

---

## 2. Configurare Fail2ban

Installa:
```bash
sudo apt install fail2ban -y
```

Copia configurazione di base:
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Modifica `jail.local`:
```bash
sudo nano /etc/fail2ban/jail.local
```

Configura la sezione SSH (porta adattata se modificata):
```
[sshd]
enabled  = true                # attiva la jail per SSH
port     = 22                  # monitora la porta SSH (22 di default)
filter   = sshd                # usa il filtro predefinito "sshd"
logpath  = /var/log/auth.log   # legge i tentativi di accesso falliti da questo log
maxretry = 5                   # massimo 5 tentativi falliti...
findtime = 600                 # ...nell ^`^yarco di 10 minuti
bantime  = -1                  # ban permanente (finch   non sblocchi manualmente)
```

Abilita e avvia:
```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Verifica lo stato:
```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

> ℹ️ **Nota**: se esiste `jail.local`, Fail2ban lo usa al posto di `jail.conf`.  
> Il file `jail.conf` resta solo come modello e può essere sovrascritto dagli aggiornamenti.


---

## 3. Aggiornamenti automatici

Installa e configura:
```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

---

## 🚀 VPS Sicura

Con queste configurazioni hai:
- **Firewall UFW** che permette solo ciò che serve.
- **Fail2ban** per proteggere da brute-force SSH.
- **Aggiornamenti automatici** per patch di sicurezza.
