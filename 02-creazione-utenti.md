# 👤 Creazione Utente Amministrativo `gisadmin`

Questa pagina documenta i passaggi effettuati per la creazione e configurazione dell’utente amministrativo **`gisadmin`** sulla VPS **Debian 13**.

---

## 1️⃣ Creazione dell’utente
```bash
sudo adduser gisadmin
```
- Impostare una **password temporanea** (che sarà poi disabilitata).  
- Inserire eventuali informazioni aggiuntive richieste (*opzionali*).  

---

## 2️⃣ Aggiunta ai sudoers
```bash
sudo usermod -aG sudo gisadmin
```
👉 L’utente `gisadmin` acquisisce così privilegi di amministratore.  

---

## 3️⃣ Configurazione chiavi SSH
Creare la cartella `.ssh` nella home del nuovo utente:  
```bash
sudo mkdir -p /home/gisadmin/.ssh
sudo chmod 700 /home/gisadmin/.ssh
```

Copiare la chiave pubblica già configurata per `admin`:  
```bash
sudo cp /home/admin/.ssh/authorized_keys /home/gisadmin/.ssh/
sudo chown -R gisadmin:gisadmin /home/gisadmin/.ssh
sudo chmod 600 /home/gisadmin/.ssh/authorized_keys
```

---

## 4️⃣ Test accesso SSH
Da locale:  
```bash
ssh gisadmin@<server_ip>
```

---

## 5️⃣ Hardening SSH (disabilitare password)
Modificare il file di configurazione:  
```bash
sudo nano /etc/ssh/sshd_config
```

Assicurarsi di impostare almeno:  
```conf
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
KbdInteractiveAuthentication no
UsePAM no
ClientAliveInterval 120

# permette solo a gisadmin di collegarsi tramite SSH
AllowUsers gisadmin
```

Per completezza si riporta il contenuto del file `/etc/ssh/sshd_config` con le modifiche applicate:

```conf
# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

Include /etc/ssh/sshd_config.d/*.conf

#Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

# Authentication
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
KbdInteractiveAuthentication no
UsePAM no

# Session
ClientAliveInterval 120

# permette solo a gisadmin di collegarsi tramite SSH
AllowUsers gisadmin
```


🔄 Riavviare il servizio:  
```bash
sudo systemctl restart ssh
```

---

## 6️⃣ Rimozione utente `admin`
Dopo aver verificato l’accesso di `gisadmin`:  
```bash
sudo deluser --remove-home admin
```


---

## ✅ Risultato
Il sistema ora utilizza **esclusivamente** l’utente `gisadmin` con accesso SSH tramite chiavi:  
- 🔒 Accesso **password disabilitato**  
- 👤 Utente predefinito `admin` rimosso  
- 🛡️ Maggiore sicurezza del server  
