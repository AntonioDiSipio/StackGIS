# 🔐 Installazione certificati SSL con Let’s Encrypt

Installare Certbot:

```bash
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --apache -d example.org -d www.example.org
sudo systemctl reload apache2
```

Verificare HTTPS e rinnovo automatico:

```bash
sudo certbot renew --dry-run
```
