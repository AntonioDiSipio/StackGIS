# Creazione Utenti

Questa pagina documenta i passaggi effettuati per la creazione e configurazione dell’utente amministrativo **`gisadmin`** sulla VPS Debian 13.

---

## 1. Creazione dell’utente
```bash
sudo adduser gisadmin
```
- Impostare una password temporanea (che sarà poi disabilitata).  
- Inserire eventuali informazioni aggiuntive richieste (opzionali).  

---

## 2. Aggiunta ai sudoers
```bash
sudo usermod -aG sudo gisadmin
```
In questo modo l’utente `gisadmin` ha privilegi di amministratore.  

---

## 3. Configurazione chiavi SSH
Creare la cartella `.ssh` nella home del nuovo utente:  
```bash
sudo mkdir -p /home/gisadmin/.ssh
sudo chmod 700 /home/gisadmin/.ssh
```

Copiare la chiave pubblica (già configurata per l’utente `admin`) in `authorized_keys`:  
```bash
sudo cp /home/admin/.ssh/authorized_keys /home/gisadmin/.ssh/
sudo chown -R gisadmin:gisadmin /home/gisadmin/.ssh
sudo chmod 600 /home/gisadmin/.ssh/authorized_keys
```

---

## 4. Test accesso SSH
Da locale verificare:  
```bash
ssh gisadmin@<server_ip>
```

---

## 5. Rimozione accesso password
Modificare il file di configurazione SSH:  
```bash
sudo nano /etc/ssh/sshd_config
```

Impostare i parametri principali:  

```
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

Riavviare il servizio:  
```bash
sudo systemctl restart ssh
```

---

## 6. Rimozione utente `admin`
Dopo aver verificato che `gisadmin` accede correttamente con i privilegi `sudo`, rimuovere l’utente di default:  
```bash
sudo deluser --remove-home admin
```

---

✅ **Risultato**: il sistema ora utilizza esclusivamente l’utente **`gisadmin`** con accesso SSH tramite chiavi, eliminando l’utente predefinito `admin` e bloccando l’accesso tramite password.
