# 👤 Creazione Utente Amministrativo `gisadmin`

Questo documento descrive i passaggi per la creazione e configurazione dell’utente amministrativo **`gisadmin`** su una macchina **Debian 13**, utilizzata nell’ambito del progetto **StackGIS**.

---

## 1️⃣ Creazione dell’utente
```bash
sudo adduser gisadmin
```
- Impostare una **password temporanea**, che verrà successivamente disabilitata.  
- Inserire eventuali informazioni aggiuntive richieste (*facoltative*).  

---

## 2️⃣ Aggiunta ai sudoers
```bash
sudo usermod -aG sudo gisadmin
```
L’utente `gisadmin` ottiene così i privilegi di amministrazione del sistema.  

---

## 3️⃣ Configurazione chiavi SSH
Creare la directory `.ssh` nella home del nuovo utente:  
```bash
sudo mkdir -p /home/gisadmin/.ssh
sudo chmod 700 /home/gisadmin/.ssh
```

Copiare la chiave pubblica già presente nel sistema (ad esempio quella utilizzata in fase di accesso iniziale):  
```bash
sudo cp /home/admin/.ssh/authorized_keys /home/gisadmin/.ssh/
sudo chown -R gisadmin:gisadmin /home/gisadmin/.ssh
sudo chmod 600 /home/gisadmin/.ssh/authorized_keys
```

---

## 4️⃣ Test accesso SSH
Verificare da un client locale:  
```bash
ssh gisadmin@<server_ip>
```

---

## 5️⃣ Hardening SSH (disabilitazione accesso via password)
Accedere con l’utente `gisadmin` e modificare il file di configurazione SSH:  
```bash
sudo nano /etc/ssh/sshd_config
```

Impostare i seguenti parametri:  
```conf
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
KbdInteractiveAuthentication no
UsePAM no
ClientAliveInterval 120

# Consente l’accesso solo all’utente gisadmin
AllowUsers gisadmin
```

Per completezza, si riporta un esempio del file `/etc/ssh/sshd_config` con le modifiche applicate:  

```conf
# This is the sshd server system-wide configuration file. See
# sshd_config(5) for more information.

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

# Consente l’accesso solo all’utente gisadmin
AllowUsers gisadmin
```

Riavviare il servizio SSH per applicare le modifiche:  
```bash
sudo systemctl restart ssh
```

---

## 6️⃣ Rimozione utente predefinito
Dopo aver verificato che l’accesso con `gisadmin` avviene correttamente:  
```bash
sudo deluser --remove-home admin
```

---

## ✅ Risultato finale
Il sistema risulta configurato con un solo utente amministrativo (`gisadmin`) dotato di accesso SSH esclusivamente tramite chiavi:  
- 🔒 **Accesso tramite password disabilitato**  
- 👤 **Utente predefinito rimosso**  
- 🛡️ **Aumentato il livello di sicurezza della VPS**
