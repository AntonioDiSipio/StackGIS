## PostgreSQL su Debian 13
Pagina ufficiale [https://www.postgresql.org/](https://www.postgresql.org/)

PostgreSQL è un potente database relazionale open-source, molto utilizzato in ambito GIS grazie all'estensione **PostGIS**.

Su Debian 13 può essere installato facilmente tramite i repositoryufficiali.

Se necessario, è possibile aggiungere il repository ufficiale PostgreSQL (PGDG) per disporre di versioni più recenti rispetto a quelle incluse in Debian.

Una volta installato, il servizio si avvia automaticamente e può essere amministrato tramite l'utente di sistema `postgres`.

Per l'integrazione con **QGIS Server** è consigliato configurare i file **`.pg_service.conf`** e **`.pgpass`**, che consentono di gestire connessioni e credenziali in modo centralizzato e sicuro, evitando di inserire username e password direttamente nei progetti.

-----

Installazione
PostgreSQL è disponibile di default in tutte le versioni di Debian. Tuttavia, Debian crea uno "snapshot" di una versione specifica di PostgreSQL, che sarà poi supportata per tutta la durata di vita di quella versione. Il progetto PostgreSQL gestisce un repository Apt con tutte le versioni di PostgreSQL supportate.

Se la versione inclusa nella tua versione di Debian non è quella che desideri, puoi utilizzare il [PostgreSQL Apt Repository](https://wiki.postgresql.org/wiki/Apt). Questo repository si integrerà con i tuoi normali sistemi e con la gestione delle patch, e fornirà aggiornamenti automatici per tutte le versioni supportate di PostgreSQL per tutta la durata del supporto di PostgreSQL.
