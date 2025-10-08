# 👤 Creazione utente amministrativo

Questa sezione descrive la creazione dell’utente amministrativo `gisadmin` sulla VPS Debian 13.

## Creazione e configurazione utente

```bash
sudo adduser gisadmin
sudo usermod -aG sudo gisadmin
```

Configurare l’accesso SSH:

```bash
sudo mkdir -p /home/gisadmin/.ssh
sudo cp /home/admin/.ssh/authorized_keys /home/gisadmin/.ssh/
sudo chown -R gisadmin:gisadmin /home/gisadmin/.ssh
sudo chmod 700 /home/gisadmin/.ssh
sudo chmod 600 /home/gisadmin/.ssh/authorized_keys
```

Aggiornare `/etc/ssh/sshd_config` per consentire solo accessi tramite chiave pubblica:

```conf
PubkeyAuthentication yes
PasswordAuthentication no
AllowUsers gisadmin
```

Riavviare SSH:

```bash
sudo systemctl restart ssh
```

Il server risulta ora accessibile esclusivamente tramite chiave SSH sicura.
