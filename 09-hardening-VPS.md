# 🔒 Hardening VPS

Per migliorare la sicurezza, configurare UFW e Fail2ban.

```bash
sudo apt install ufw fail2ban -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22,80,443/tcp
sudo ufw enable
```

Configurazione Fail2ban in `/etc/fail2ban/jail.local` per proteggere SSH.
