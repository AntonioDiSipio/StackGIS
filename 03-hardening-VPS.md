# 🔒 Hardening VPS Debian 13 (Contabo)

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
enabled  = true
port     = 22
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5
bantime  = 3600
findtime = 600
```



### Esempio di configurazione `jail.local` completa

```ini
# File jail.local
# Configurazione Fail2ban su Debian 13

[INCLUDES]

#before = paths-distro.conf
before = paths-debian.conf

[DEFAULT]
bantime  = 10m
findtime = 10m
maxretry = 5
backend  = auto
usedns   = warn
logencoding = auto
enabled = false
mode = normal
filter = %(__name__)s[mode=%(mode)s]
destemail = root@localhost
sender = root@<fq-hostname>
mta = sendmail
protocol = tcp
chain = <known/chain>
port = 0:65535
fail2ban_agent = Fail2Ban/%(fail2ban_version)s
banaction = iptables-multiport
banaction_allports = iptables-allports
action = %(banaction)s[port="%(port)s", protocol="%(protocol)s", chain="%(chain)s"]

[sshd]
enabled  = true
port     = 22
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5
bantime  = 3600
findtime = 600

# Altri jail disponibili (apache, nginx, dovecot, postfix, ecc.)
# Si possono attivare modificando "enabled = true" e adattando logpath/port
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
