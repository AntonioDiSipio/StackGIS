# 🔒 Hardening di una VPS Debian 13

Questo documento descrive i passaggi essenziali per aumentare la sicurezza di una VPS basata su **Debian 13**, configurando un firewall con **UFW**, la protezione contro attacchi di forza bruta con **Fail2ban**, e abilitando gli **aggiornamenti automatici di sicurezza**.

---

## ✅ Prerequisiti completati
Prima di procedere, si presume che siano già stati eseguiti i seguenti passaggi fondamentali di sicurezza:

- Aggiornamento completo del sistema operativo:  
  ```bash
  sudo apt update && sudo apt upgrade
  ```
- Creazione di un utente non-root con privilegi sudo.  
- Disabilitazione dell’accesso SSH diretto come utente root.  
- Configurazione dell’accesso SSH solo tramite chiave pubblica.  

---

## 1️⃣ Configurazione del firewall con UFW

Installare **UFW** (Uncomplicated Firewall) se non già presente nel sistema:

```bash
sudo apt install ufw -y
```

Impostare le regole di default per bloccare tutte le connessioni in ingresso e consentire solo quelle in uscita:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Abilitare solo i servizi essenziali.  
Le regole seguenti consentono l’accesso SSH (porta 22), HTTP (porta 80) e HTTPS (porta 443).  
Se la porta SSH è stata modificata, sostituire `22` con la porta effettiva:

```bash
sudo ufw allow 22/tcp     # SSH
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS
```

Abilitare il firewall e verificarne lo stato:

```bash
sudo ufw enable
sudo ufw status verbose
```

Attivare il logging (facoltativo ma consigliato per audit e diagnosi):

```bash
sudo ufw logging on
```

---

## 2️⃣ Protezione SSH con Fail2ban

Installare **Fail2ban**, un servizio che monitora i log di autenticazione e blocca automaticamente gli indirizzi IP che tentano accessi ripetuti non autorizzati:

```bash
sudo apt install fail2ban -y
```

Copiare il file di configurazione predefinito in modo da poterlo personalizzare senza rischiare sovrascritture future:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Aprire il file di configurazione principale:

```bash
sudo nano /etc/fail2ban/jail.local
```

Configurare la sezione dedicata al servizio SSH, adattando la porta se differente da quella predefinita (22):

```bash
[sshd]
enabled  = true                # Attiva la jail per SSH
port     = 22                  # Monitora la porta SSH
filter   = sshd                # Usa il filtro predefinito per SSH
logpath  = /var/log/auth.log   # Percorso del log di autenticazione
maxretry = 5                   # Numero massimo di tentativi falliti
findtime = 600                 # Intervallo di tempo (in secondi)
bantime  = -1                  # Ban permanente (fino a rimozione manuale)
```

Abilitare e avviare il servizio:

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Verificare il corretto funzionamento e lo stato delle regole attive:

```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

> ℹ️ **Nota**: il file `jail.local` ha priorità rispetto a `jail.conf`.  
> In caso di aggiornamenti di sistema, `jail.conf` può essere sovrascritto, mentre `jail.local` rimane intatto.

---

## 3️⃣ Aggiornamenti automatici di sicurezza

Per mantenere la VPS aggiornata con le ultime patch di sicurezza, installare e configurare il pacchetto `unattended-upgrades`:

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

Durante la configurazione, selezionare l’opzione per abilitare gli aggiornamenti automatici.

---

## 🚀 VPS Sicura e Manutenuta

Dopo l’applicazione di queste configurazioni, la VPS risulta dotata di:

- 🔐 **Firewall UFW**, che filtra le connessioni in ingresso e consente solo i servizi essenziali.  
- 🛡️ **Fail2ban**, che protegge il servizio SSH da tentativi di accesso non autorizzati.  
- ⚙️ **Aggiornamenti automatici**, che assicurano l’applicazione tempestiva delle patch di sicurezza.  

Queste misure costituiscono la base del processo di **hardening** del sistema, fornendo un equilibrio tra sicurezza e semplicità di gestione.  
