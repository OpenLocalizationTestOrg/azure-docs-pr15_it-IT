<properties
    pageTitle="Configurazione della protezione di dati Oracle in macchine virtuali | Microsoft Azure"
    description="Passaggio a un'esercitazione per la configurazione e l'implementazione di protezione di dati Oracle in macchine virtuali Azure per alta disponibilità e ripristino di emergenza."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />

#<a name="configuring-oracle-data-guard-for-azure"></a>Configurazione di protezione di dati Oracle per Azure


In questa esercitazione viene illustrato come configurare e implementare funzionalità di protezione di dati Oracle di ambiente macchine virtuali di Azure per disponibilità e il ripristino di emergenza. L'esercitazione è incentrata su replica unidirezionale per i database Oracle non diritti.

Protezione di dati Oracle supporta la protezione dei dati e il ripristino di emergenza per Database Oracle. È un semplice, prestazioni elevate, soluzione caduta per il ripristino di emergenza, la protezione dei dati e disponibilità per l'intero database Oracle.

In questa esercitazione si presuppone che sia già teorico e pratico knowledge sui concetti di emergenza e disponibilità Database Oracle. Per informazioni, vedere il [sito web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) e anche [i concetti di protezione dati Oracle e Guida di amministrazione](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

Inoltre, l'esercitazione si presuppone che sia già stata implementata i prerequisiti seguenti:

- Già stato rivisto la sezione disponibilità e considerazioni sul ripristino di emergenza nell'argomento con [le immagini della macchina virtuale Oracle - considerazioni varie](virtual-machines-windows-classic-oracle-considerations.md) . Azure supporta attualmente istanze di Database Oracle autonome ma non Oracle Real cluster di applicazioni (Oracle diritti).


- Due macchine () è stato creato in Azure utilizzando la stessa piattaforma fornita immagine Oracle Enterprise Edition. Assicurarsi che le macchine virtuali sono nella [stessa servizio cloud](virtual-machines-windows-load-balance.md) e nella stessa rete virtuale per assicurarsi che accedono tra loro tramite l'indirizzo IP privato persistente. Inoltre, è consigliabile per posizionare le macchine virtuali in stesso [disponibilità impostato](virtual-machines-windows-manage-availability.md) per consentire di Azure per inserirli in domini separati guasto ed eseguire l'aggiornamento di domini. Protezione di dati Oracle è disponibile solo con Database Oracle versione Enterprise. Ogni computer devono contenere almeno 2 GB di memoria e 5 GB di spazio su disco. Per informazioni più aggiornate su piattaforma fornita le dimensioni di macchine Virtuali, vedere [Le dimensioni di macchina virtuale per Azure](virtual-machines-windows-sizes.md). Se è necessario volume disco aggiuntivo per le macchine virtuali, è possibile allegare dischi aggiuntivi. Per informazioni, vedere [come collegare un disco di dati a una macchina virtuale](virtual-machines-windows-classic-attach-disk.md).



- Impostate i nomi di macchina virtuale come "Computer1" per le principali macchine Virtuali "Computer2" per la macchina virtuale standby al portale di classica Azure.

- Impostate la variabile di ambiente **ORACLE_HOME** in modo che puntino allo stesso percorso di installazione radice oracle principale e standby macchine virtuali, ad esempio `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Accedi a Windows server come un membro del gruppo **Administrators** o come membro del gruppo **ORA_DBA** .

In questa esercitazione, sarà necessario:

Implementare l'ambiente di database in standby fisica

1. Creare un database principale

2. Preparare il database primario per la creazione di database in standby

    1. Attivare la registrazione forzata

    2. Creare un file di password

    3. Configurare un file di log standby Ripeti

    4. Abilitare l'archiviazione

    5. Impostare i parametri di inizializzazione database primario

Creare un database in standby fisico

1. Preparare un file di parametro inizializzazione per database standby

2. Configurare la comunicare ascoltatore e tnsnames per supportare il database nel computer principale e standby

    1. Configurare listener.ora su entrambi i server per contenere le voci per entrambi i database

    2. Per mettere in attesa voci per i database principale e di standby, configurare tnsnames nelle macchine virtuali principale e di standby. 

    3. Iniziare a comunicare ascoltatore e controllare tnsping in entrambe le macchine virtuali a entrambi i servizi.

3. Avviare l'istanza standby nello stato nomount

4. Utilizzare RMAN da duplicare il database e creare un database in standby

5. Avviare il database in standby fisico in modalità di recupero gestiti

6. Verificare il database in standby fisico

> [AZURE.IMPORTANT] In questa esercitazione è stata impostata e verificata in base alla configurazione hardware e software seguente:
>
>|                      | **Database primario**                      | **Database in standby**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versione finale di Oracle**   | Versione Enterprise Oracle11g (11.2.0.4.0) | Versione Enterprise Oracle11g (11.2.0.4.0) |
>| **Nome del computer**     | Computer1                                  | Computer2                                  |
>| **Sistema operativo** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | TEST                                      | TEST\_STBY                                |
>| **Memoria**           | Min 2 GB                                  | Min 2 GB                                  |
>| **Spazio su disco**       | Min 5 GB                                  | Min 5 GB                                  |

Per le versioni successive di Database Oracle e protezione di dati Oracle, potrebbe essere alcune modifiche aggiuntive che è necessario implementare. Per informazioni specifiche della versione più aggiornate, vedere [Protezione dei dati](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [Database Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) documentazione nel sito web Oracle.

##<a name="implement-the-physical-standby-database-environment"></a>Implementare l'ambiente di database in standby fisica
### <a name="1-create-a-primary-database"></a>1. creare un database principale

- Creare un database primario "TEST" nella macchina virtuale principale. Per informazioni, vedere Creazione e configurazione di un Database Oracle.
- Per visualizzare il nome del database, connettersi al database come utente sistema con ruolo SYSDBA in SQL * Plus prompt dei comandi ed eseguire l'istruzione seguente:

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- Eseguire una query, i nomi dei file di database dalla visualizzazione di sistema dba_data_files:

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. preparare il database primario per la creazione di database in standby

Prima di creare un database in standby, è consigliabile assicurarsi che il database primario sia configurato correttamente. Di seguito è un elenco dei passaggi che è necessario eseguire:

1. Attivare la registrazione forzata
2. Creare un file di password
3. Configurare un file di log standby Ripeti
4. Abilitare l'archiviazione
5. Impostare i parametri di inizializzazione database primario

#### <a name="enable-forced-logging"></a>Attivare la registrazione forzata

Per implementare un Database in questa modalità, è necessario abilitare viene imposta la registrazione del database principale. Questa opzione garantisce che anche se viene eseguita un'operazione 'NoLogging.', forzare la registrazione avrà la priorità e tutte le operazioni sono registrate nei registri Ripeti. Di conseguenza, occorre assicurarsi che tutti gli elementi del database principale ha eseguito l'accesso e la replica di standby include tutte le operazioni nel database principale. Per attivare la registrazione di forza, eseguire l'istruzione alter database:

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Creare un file di password

Per poter spedire e applicare i registri archiviati dal server primario per il server Standby, la password di sistema deve essere identica nel server primario e standby. Questo è il motivo per cui creare un file di password per il database principale e copiarla nel server di Standby.

>[AZURE.IMPORTANT] Quando si usano Database Oracle 12c, esiste un nuovo utente, **SYSDG**, che è possibile utilizzare per amministrare la protezione di dati Oracle. Per ulteriori informazioni, vedere [modifiche ai database Oracle 12 c Release](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

Inoltre, assicurarsi che la ORACLE\_ambiente HOME è già definita in Computer1. In caso contrario, definirla come una variabile di ambiente utilizzando la finestra di dialogo variabili. Per accedere a questa finestra di dialogo, avviare l'utilità di **sistema** facendo doppio clic sull'icona sistema nel **Pannello di controllo**; quindi fare clic sulla scheda **Avanzate** e scegliere **Le variabili di ambiente**. Per impostare le variabili di ambiente, fare clic sul pulsante **Nuovo** in **Variabili di sistema**. Dopo aver impostato le variabili di ambiente, chiudere il prompt dei comandi di Windows esistente e aprire un altro.

Eseguire l'istruzione seguente per passare a Oracle\_directory Home, ad esempio c:\\OracleDatabase\\prodotto\\11.2.0\\dbhome\_1\\database.

    cd %ORACLE_HOME%\database

Creare un file di password tramite l'utilità di creazione di file di password, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). Nella stessa Windows prompt dei Computer1, eseguire il comando seguente impostando il valore di password la password di **sistema**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Questo comando crea un file di password, denominato come PWDTEST.ora in ORACLE\_HOME\\directory del database. È necessario copiare il file in % ORACLE\_HOME %\\database directory in Computer2 manualmente.

#### <a name="configure-a-standby-redo-log"></a>Configurare un file di log standby Ripeti

Quindi, è necessario configurare un file di Log Ripeti Standby in modo che il principale riceva correttamente il ripristino quando diventa standby. Pre-crearli qui consente inoltre i registri di ripristino standby deve essere creato automaticamente nel server di riserva. È importante configurare Standby Ripeti log (SRL) con le stesse dimensioni come ripristinare la versione online registri. Le dimensioni dei file di log Ripeti standby corrente devono corrispondere esattamente le dimensioni dei file di log online Ripristina database primaria corrente.

Eseguire l'istruzione seguente in SQL\*più prompt dei comandi di Computer1. Il file di log $ v è una visualizzazione di sistema che contiene le informazioni sui file di log Ripeti.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Si noti che 52428800 50 megabyte.

Quindi, in SQL\*Plus finestra, eseguire le seguenti istruzioni per aggiungere un nuovo gruppo di file di log standby Ripristina un database in standby e specificare un numero che identifica il gruppo utilizzando la clausola GROUP. Utilizzare numeri gruppo inserirvi amministrazione Ripeti standby gruppi file di log più semplici:

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Successivamente, eseguire la visualizzazione di sistema seguente per visualizzare informazioni su Ripristina file di log. Questa operazione consente di verificare anche che sono stati creati i gruppi di file di log Ripeti standby:

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Abilitare l'archiviazione

Quindi abilitare l'archiviazione eseguendo le istruzioni seguenti per spostare il database primario in modalità ARCHIVELOG e attivare l'archiviazione automatica. È possibile abilitare la modalità log archivio montaggio del database e quindi eseguire il comando archivelog.

Prima di tutto, eseguire l'accesso come sysdba. Nella finestra prompt dei comandi di Windows eseguire:

    sqlplus /nolog

    connect / as sysdba

Quindi, arrestare il database SQL\*più prompt dei comandi:

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Eseguire il comando avvio di installare il database. In questo modo che Oracle associa l'istanza del database specificato.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Eseguire quindi:

    SQL> alter database archivelog;
    Database altered.

Quindi, eseguire l'operazione di modifica istruzione database con la clausola aperta per rendere disponibile per l'uso normale il database:

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Impostare i parametri di inizializzazione database primario

Per configurare la protezione dei dati, è necessario creare e configurare i parametri standby in una normale pfile (file di testo inizializzazione parametro) prima. Quando il pfile è pronta, è necessario convertirla in un file di parametri server (SPFILE).

È possibile controllare l'ambiente di protezione dei dati utilizzando i parametri nell'INIZIALIZZAZIONE. File ORA. Quando si segue questa esercitazione, è necessario aggiornare il database primario Inizializzazione. ORA in modo che non può contenere entrambi i ruoli: principale o Standby.

    SQL> create pfile from spfile;
    File created.

È necessario modificare pfile per aggiungere i parametri standby. A tale scopo, aprire la INITTEST. ORA di file nella posizione del % ORACLE\_HOME %\\database. Successivamente, aggiungere le istruzioni seguenti al file INITTEST.ora. Convenzione di denominazione per l'INIZIALIZZAZIONE. ORA è Inizializzazione\<YourDatabaseName\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


Il blocco di istruzioni precedenti include tre elementi di impostazione importanti:
-   **LOG_ARCHIVE_CONFIG...:** È possibile definire gli ID univoci database utilizzando questa istruzione.
-   **LOG_ARCHIVE_DEST_1...:** Definire il percorso della cartella archivio locale utilizzando questa istruzione. È consigliabile creare una nuova directory per esigenze di archiviazione del database e specificare il percorso dell'archivio locale utilizzando l'istruzione in modo esplicito anziché Oracle predefinito cartella % ORACLE_HOME%\database\archive.
-   LOG_ARCHIVE_DEST_2 **... LGWR ASYNC...:** è possibile definire un processo di scrittura asincrono log (LGWR) per raccogliere dati Ripeti transazione e trasmettere a destinazioni standby. In questo caso, il DB_UNIQUE_NAME specifica un nome univoco per il database al server di riserva di destinazione.

Quando il nuovo file di parametro è pronto, è necessario creare il spfile.

Chiusura prima di tutto il database:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Successivamente, comando avvio nomount come indicato di seguito:

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

A questo punto, creare un spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Quindi, chiusura il database:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Quindi, utilizzare il comando di avvio per avviare un'istanza:

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Creare un database in standby fisico
Questa sezione vengono illustrati i passaggi da eseguire in Computer2 per preparare il database in standby fisico.

È necessario innanzitutto desktop remoto per Computer2 tramite il portale classico Azure.

Quindi, nel Server di riserva (Computer2), creare tutte le cartelle per il database in standby, ad esempio c:\\\<YourLocalFolder\>\\TEST. Mentre si segue questa esercitazione, assicurarsi che la struttura delle cartelle corrisponda la struttura delle cartelle su Machine1 per mantenere tutti i file necessari, ad esempio un file controlfile, file di dati, redologfiles, udump, bdump e cdump. Inoltre, definire ORACLE\_HOME e ORACLE\_BASE variabili in Computer2. In caso contrario, definiti come una variabile di ambiente utilizzando la finestra di dialogo variabili. Per accedere a questa finestra di dialogo, avviare l'utilità di **sistema** facendo doppio clic sull'icona sistema nel **Pannello di controllo**; quindi fare clic sulla scheda **Avanzate** e scegliere **Le variabili di ambiente**. Per impostare le variabili di ambiente, fare clic sul pulsante **Nuovo** in **Variabili di sistema**. Dopo aver impostato le variabili di ambiente, è necessario chiudere il prompt dei comandi di Windows esistente e aprire un altro per visualizzare le modifiche.

Successivamente, procedere come segue:

1. Preparare un file di parametro inizializzazione per database standby

2. Configurare la comunicare ascoltatore e tnsnames per supportare il database nel computer principale e standby

    1. Configurare listener.ora su entrambi i server per contenere le voci per entrambi i database

    2. Configurare tnsnames nelle macchine virtuali principale e di standby per contenere le voci per i database principale e di standby

    3. Iniziare a comunicare ascoltatore e controllare tnsping in entrambe le macchine virtuali a entrambi i servizi.

3. Avviare l'istanza standby nello stato nomount

4. Utilizzare RMAN da duplicare il database e creare un database in standby

5. Avviare il database in standby fisico in modalità di recupero gestiti

6. Verificare il database in standby fisico

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. preparare un file di parametro inizializzazione per database standby

In questa sezione viene illustrato come preparare un file di parametro inizializzazione per il database in standby. A tale scopo, copiare innanzitutto il INITTEST. ORA file dal computer 1 a Computer2 manualmente. Dovrebbe essere possibile visualizzare il INITTEST. ORA dei file in % ORACLE\_HOME %\\cartella del database in entrambi i computer. Quindi, modificare il file INITTEST.ora in Computer2 impostare per il ruolo standby come specificato di seguito:

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


Il blocco di istruzioni precedenti include due elementi di impostazione importanti:

-   **\*. LOG_ARCHIVE_DEST_1:** è necessario creare manualmente la cartella c:\OracleDatabase\TEST_STBY\archives in Computer2.
-   **\*. LOG_ARCHIVE_DEST_2:** questa operazione è facoltativa. A tale come potrebbe essere necessario quando la macchina principale è in manutenzione e il computer standby diventa database primario.

Quindi, è necessario avviare l'istanza standby. Nel server di database in standby, immettere il comando seguente al prompt dei comandi di Windows per creare un'istanza di Oracle mediante la creazione di un servizio Windows:

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Il comando **Oradim** crea un'istanza di Oracle ma non viene avviato. È possibile trovare nella c\\OracleDatabase\\prodotto\\11.2.0\\dbhome\_1\\directory BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurare la comunicare ascoltatore e tnsnames per supportare il database nel computer principale e standby
Prima di creare un database in standby, è necessario assicurarsi che i database principali e di standby nella configurazione possono comunicare con loro. A questo scopo è necessario configurare comunicare ascoltatore sia TNSNames manualmente o mediante l'utilità di configurazione della rete NETCA. Si tratta di un'attività obbligatoria quando si usa l'utilità di gestione di ripristino (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configurare listener.ora su entrambi i server per contenere le voci per entrambi i database

Desktop remoto Computer1 e modificare il file listener.ora come specificato di seguito. Quando si modifica il file listener.ora, è sempre assicurarsi che l'apertura e una parentesi di chiusura allineati nella stessa colonna. È possibile trovare il file listener.ora nella cartella seguente: c:\\OracleDatabase\\prodotto\\11.2.0\\dbhome\_1\\rete\\amministratore\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Desktop remoto o successiva COMPUTER2 e modifica di listener.ora file come indicato di seguito: # listener.ora File di configurazione della rete: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurare tnsnames nelle macchine virtuali principale e di standby per contenere le voci per i database principale e di standby

Desktop remoto Computer1 e modificare il file tnsnames come specificato di seguito. È possibile trovare il file tnsnames nella cartella seguente: c:\\OracleDatabase\\prodotto\\11.2.0\\dbhome\_1\\rete\\amministratore\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Desktop remoto COMPUTER2 e modifica la tnsnames file come indicato di seguito:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Iniziare a comunicare ascoltatore e controllare tnsping in entrambe le macchine virtuali a entrambi i servizi.

Aprire un nuovo prompt dei comandi di Windows in macchine virtuali principale e standby ed eseguire le istruzioni seguenti:

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Avviare l'istanza standby nello stato nomount
Per configurare l'ambiente per supportare il database in standby del computer standby virtuale (Computer2).

Prima di tutto, copiare il file di password dal computer principale (Computer1) nel computer in standby (Computer2) manualmente. Questa operazione è necessaria la password di **sistema** deve essere identica su entrambi i computer.

Quindi aprire il prompt dei comandi di Windows in COMPUTER2 e impostare le variabili di ambiente in modo che punti al database Standby come indicato di seguito:

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Successivamente, avviare il database Standby nello stato nomount e generare un spfile.

Avviare il database:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Utilizzare RMAN da duplicare il database e creare un database in standby
È possibile utilizzare l'utilità di gestione di ripristino (RMAN) per eseguire una copia di backup del database primario per creare il database in standby fisico.

Desktop remoto per la macchina virtuale standby (Computer2) ed eseguire l'utilità RMAN specificando una connessione completa stringa per entrambi la destinazione (database primario, Computer1) e ausiliario (standby database, COMPUTER2) istanze.

>[AZURE.IMPORTANT] Non utilizzare l'autenticazione del sistema operativo poiché non è presente alcun database del server standby ancora.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Avviare il database in standby fisico in modalità di recupero gestiti
In questa esercitazione viene illustrato come creare un database in standby fisico. Per informazioni sulla creazione di un database in standby logico, vedere la documentazione Oracle.

Aprire il SQL\*Plus prompt dei comandi e abilitare la protezione dei dati nel server (Computer2) o standby macchina virtuale come indicato di seguito:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Quando si apre il database in standby in modalità di **installazione** , il log di archiviazione spedizione continua e il processo di ripristino gestito continua log l'applicazione di database in standby. In questo modo che il database in standby sia aggiornato con il database primario. Si noti che il database in standby non può essere accessibile per la creazione di report durante questo periodo.

Quando si apre il database in standby in modalità di **Sola lettura** , nell'archivio la distribuzione dei log continua. Ma si arresta il processo di ripristino gestito. In questo modo il database in standby essere sempre più aggiornate fino a quando non viene riattivato il processo di ripristino gestito. È possibile accedere al database standby ai fini della creazione di report durante questo periodo ma dati potrebbero non riflettere le modifiche più recenti.

In generale, è consigliabile mantenere il database in standby in modalità di **installazione** per mantenere aggiornati i dati nel database di standby se si verifica un errore del database primario. Tuttavia, è possibile mantenere il database in standby in modalità di **Sola lettura** per la gestione di report in base ai requisiti dell'applicazione. La procedura seguente viene illustrato come attivare la protezione dei dati in modalità di sola lettura tramite SQL\*Plus:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Verificare il database in standby fisico
In questa sezione viene illustrato come verificare la configurazione di disponibilità come amministratore.

Aprire il SQL\*Plus finestra prompt dei comandi e controllo archiviate Ripeti log in Standby Virtual Machine (Computer2):

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Aprire il SQL * Plus registri di finestra e passare al prompt dei comandi sul computer principale (Computer1):

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Controllare il Registro di ripristino archivio in Standby Virtual Machine (Computer2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Cercare qualsiasi distanza in Standby Virtual Machine (Computer2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Un altro metodo di verifica potrebbe essere il controllo al database in standby e quindi verificare se è possibile failback al database primario. Per attivare il database in standby come database primario, utilizzare le istruzioni seguenti:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Se non è stata attivata flashback al database principale originale, è consigliabile eliminare il database principale originale e ricreare come un database in standby.

Si consiglia di abilitare flashback database del server primario e i database in standby. In caso di errore caso, il database primario possibile lampeggiato indietro per il tempo prima il failover e rapidamente convertito in un database in standby.

##<a name="additional-resources"></a>Risorse aggiuntive
[Immagini di macchina virtuale Oracle per Azure](virtual-machines-windows-classic-oracle-images.md)
