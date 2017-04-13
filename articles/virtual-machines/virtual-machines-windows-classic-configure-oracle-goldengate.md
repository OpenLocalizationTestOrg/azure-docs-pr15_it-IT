<properties
    pageTitle="Configurazione Oracle GoldenGate in macchine virtuali | Microsoft Azure"
    description="Passaggio a un'esercitazione per la configurazione e l'implementazione di Oracle GoldenGate in macchine virtuali di Server Windows Azure per alto disponibilità e ripristino di emergenza."
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


#<a name="configuring-oracle-goldengate-for-azure"></a>Configurazione GoldenGate Oracle per Azure


In questa esercitazione viene illustrato come installare GoldenGate Oracle per ambiente macchine virtuali di Azure per disponibilità e ripristino. L'esercitazione è incentrata su [replica bidirezionale](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) per i database Oracle non diritti ed è necessario che entrambi i siti sono attivi.

Oracle GoldenGate supporta la distribuzione dei dati e integrazione dei dati. Consente di configurare una distribuzione dei dati e una soluzione di sincronizzazione di dati tramite la configurazione della replica Oracle Oracle e offre una soluzione di disponibilità flessibile. Oracle GoldenGate integra protezione di dati Oracle con le funzionalità di replica per abilitare le migrazioni e gli aggiornamenti di tempo di inattività zero e distribuzione di informazioni aziendali. Per informazioni dettagliate, vedere [Uso Oracle GoldenGate con protezione di dati Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contiene i seguenti componenti principali: estrarre dati pump, Replicat, tracce o estrarre file, punti di controllo, Manager e Raccoglitore. Per salvare la replica bidirezionale tra due siti, è necessario creare e iniziare a tutti i componenti su entrambi i siti. Per informazioni dettagliate sull'architettura GoldenGate Oracle, vedere [Guida GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

In questa esercitazione si presuppone che sia già knowledge teorico e pratico sulla disponibilità di Database Oracle e ripristino di emergenza concetti, nonché [GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Per ulteriori informazioni, visitare il [sito web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Inoltre, l'esercitazione si presuppone che sia già stata implementata i prerequisiti seguenti:

- Già stato rivisto la sezione disponibilità e considerazioni sul ripristino di emergenza nell'argomento con [le immagini della macchina virtuale Oracle - considerazioni varie](virtual-machines-windows-classic-oracle-considerations.md) . Si noti che Azure supporta le istanze di Database Oracle autonoma ma non Oracle reale cluster di applicazioni (Oracle diritti) attualmente.

- Il software Oracle GoldenGate aver scaricato dal sito web di [Download di Oracle](http://www.oracle.com/us/downloads/index.html) . È stato selezionato prodotto Pack Oracle fusione Middleware-integrazione dei dati. Quindi selezionate Oracle GoldenGate su Oracle v11.2.1 Media Pack per Microsoft Windows x64 (64 bit) per un database di 11g Oracle. Successivamente, scaricare V11.2.1.0.3 GoldenGate Oracle per Oracle 11g a 64 bit in Windows 2008 (64 bit).

- Due macchine () è stato creato in Azure utilizzando Oracle Enterprise Edition in Windows Server. Assicurarsi che le macchine virtuali siano nella [stessa servizio cloud](virtual-machines-linux-load-balance.md) e nella stessa [Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurarsi che accedono tra loro tramite l'indirizzo IP privato persistente.

- Impostate i nomi di macchina virtuale come "MachineGG1" per il sito e "MachineGG2" per il sito B il portale classica Azure.

- Si sono creato il database di test "TestGG1" nel sito e "TestGG2" nel sito B.

- Accedi a Windows server come un membro del gruppo Administrators o come membro del gruppo **ORA_DBA** .

In questa esercitazione, sarà necessario:

1. Configurazione del database nel sito a e B di sito  

    1. Eseguire il caricamento iniziale dei dati

2. Preparare il sito a e B di sito per la replica di database

3. Creare tutti gli oggetti necessari per supportare la replica DDL

4. Configurare Gestione GoldenGate nel sito A e B

5. Crea gruppo estrarre e dati aumenta processi sul sito a e B di sito

    1. Creare processi di estrazione e aumenta dati nel sito

    2. Creare una tabella di verifica GoldenGate del sito B

    3. Aggiungere REPLICAT nel sito B

    4. Creare processi di estrazione e dati aumenta del sito B

    5. Creare una tabella di verifica GoldenGate nel sito

    6. Aggiungere REPLICAT nel sito

    7. Aggiungere trandata nel sito a e B di sito

    8. Avvio dei processi di estrazione e aumenta dati nel sito

    9. Avvio dei processi di estrazione e aumenta dati nel sito B

    10. Avvia il processo di REPLICAT nel sito

    11. Avvia il processo di REPLICAT del sito B

6. Verificare che il processo di replica bidirezionale

>[AZURE.IMPORTANT] In questa esercitazione è stato il programma di installazione e verificato in base alla configurazione di software seguenti:
>
>|                        | **Un Database del sito**              | **Database dei siti B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Versione finale di Oracle**     | Versione 2 – Oracle11g (11.2.0.1) | Versione 2-Oracle11g (11.2.0.1) |
>| **Nome del computer**       | MachineGG1                       | MachineGG2                       |
>| **Sistema operativo**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Schema di replica** | SCOTT                            | SCOTT                            |

Per le versioni successive di Database Oracle e Oracle GoldenGate, potrebbe essere alcune modifiche aggiuntive che è necessario implementare. Per informazioni più aggiornate versione specifiche, vedere documentazione [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) e [Database Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) nel sito web Oracle. Ad esempio, per un database di origine di rilascio 11.2.0.4 e versioni successive, l'acquisizione di DDL viene eseguita dal server logmining in modo asincrono e richiede alcun trigger speciale, tabelle o altri oggetti di database deve essere installato. Gli aggiornamenti GoldenGate Oracle possono essere eseguiti senza interrompere le applicazioni utente. Una volta estratto in modalità integrata con un database di origine 11g Oracle precedente alla versione 11.2.0.4, è necessario l'utilizzo di un trigger DDL e gli oggetti di supporto. Per istruzioni dettagliate, vedere [installazione e configurazione Oracle GoldenGate per Database Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. Imposta database nel sito a e B di sito
In questa sezione viene illustrato come eseguire i prerequisiti di database nel sito e sito B. È necessario eseguire tutti i passaggi di questa sezione su entrambi i siti: il sito e sito B.

Prima, remote desktop per sito a e B sito tramite il portale classico Azure. Aprire un prompt dei comandi di Windows e creare una home directory per i file di configurazione Oracle GoldenGate:

    mkdir C:\OracleGG

Quindi, decomprimere e installare il software Oracle GoldenGate in questa cartella. Dopo questo passaggio, è possibile avviare GoldenGate Software comando interprete (GGSCI) eseguendo il comando seguente:

    C:\OracleGG\.\ggsci

È possibile utilizzare [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) per eseguire diversi comandi che consentono di configurare, controllare e monitorare GoldenGate Oracle.

Successivamente, eseguire il seguente comando per creare tutte le sottocartelle dal pacchetto di installazione:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Eseguire il seguente comando per chiudere il prompt dei comandi GGSCI:

    GGSCI (Hostname) 1> EXIT

Quindi, è necessario creare un utente del database, da utilizzare per i processi Oracle GoldenGate Manager, estrarre e replica. Si noti che è possibile creare singoli utenti per ogni processo o configurare un solo utente comune. In questa esercitazione si crea un solo utente, ossia come ggate. Quindi abbiamo concedere tale utente le autorizzazioni necessarie. Si noti che è necessario eseguire le operazioni seguenti nel sito a e b del sito.

Aprire il SQL\*più prompt del sito A e B di sito con privilegi di amministratore di database utilizzando **SYSDBA**, ad esempio:

    Enter username: / as sysdba

Ed eseguire:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Individuare quindi l'INIZIALIZZAZIONE\<DatabaseSID\>. ORA dei file in % ORACLE\_HOME %\\database cartella nel sito a e B di siti e aggiungere i parametri di database seguenti INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Per un elenco completo di tutti i comandi di Oracle GoldenGate GGSCI, vedere [informazioni di riferimento per Oracle GoldenGate per Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Eseguire il caricamento iniziale dei dati

È possibile eseguire il caricamento iniziale dei dati nel database seguendo diversi metodi. Ad esempio, è possibile utilizzare il [Caricamento diretto GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) o regolare utilità di importazione ed esportazione esportare i dati della tabella dal sito per il sito B.

Per illustrare il processo di replica GoldenGate Oracle, questa esercitazione illustrata la creazione di una tabella nel sito e sito B, utilizzare i comandi seguenti.

Prima di tutto, aprire la SQL\*più la finestra dei comandi ed eseguire il seguente comando per creare una tabella di magazzino nel database di siti e siti B:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Aggiungere un vincolo alla tabella appena creata nel sito A e B sito database:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Concedere quindi tutti i privilegi per la nuova tabella inventario ggate utente del sito A e b sito:

    grant all on scott.inventory to ggate;

Successivamente, creare e attivare un trigger di database, INVENTORY_CDR_TRG, nella tabella appena creata per assicurarsi che tutte le transazioni per la nuova tabella vengono registrate se l'utente non sia ggate. Eseguire questa operazione nel sito a e b del sito.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. preparare il sito a e B di sito per la replica di database
In questa sezione viene illustrato come preparare il sito a e B di sito per la replica di database. È necessario eseguire tutti i passaggi di questa sezione su entrambi i siti: il sito e sito B.

Prima, remote desktop per sito a e B sito tramite il portale classico Azure. Passare il database in modalità archivelog utilizzando il codice SQL * Plus prompt:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Abilitare quindi minima [la registrazione supplementare](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) come indicato di seguito:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Successivamente, preparare il database per il supporto della replica DDL (DDL):

    SQL> alter system set recyclebin=off scope=spfile;

Quindi, arresto e il riavvio del database:

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. creare tutti gli oggetti necessari per supportare la replica DDL
In questa sezione elenca gli script che è necessario utilizzare per creare tutti gli oggetti necessari per supportare la replica DDL. È necessario eseguire gli script indicati in questa sezione nel sito e sito B.

Aprire un prompt dei comandi di Windows e passare alla cartella GoldenGate Oracle, ad esempio c:\\OracleGG. Avviare SQL\*più prompt dei comandi con privilegi di amministratore di database, ad esempio utilizzando **SYSDBA** del sito A e b del sito.

Eseguire gli script seguenti:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Strumento GoldenGate Oracle richiede un account di accesso livello di tabella per il supporto DDL (DDL). Questo è il motivo Abilita registrazione livello di tabella tramite il comando Aggiungi TRANDATA supplementare. Aprire la finestra di interprete Oracle GoldenGate comando, accedere al database e quindi eseguire il comando Aggiungi TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. configurare Gestione GoldenGate nel sito A e B
Oracle GoldenGate Manager esegue diverse funzioni come avviare gli altri GoldenGate processi, gestione dei file di log di riepilogo e creazione di report.

È necessario configurare il processo di gestione GoldenGate Oracle nel sito e sito B. A tale scopo, eseguire i passaggi seguenti nel sito a e b del sito.

Finestre aperte la finestra dei comandi e avviare interprete Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Registra la sessione GGSCI in un database in modo che sia possibile eseguire i comandi che influiscono sul database:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Visualizzare lo stato e di ritardo (se necessario) per tutti i processi di gestione, estrarre e Replicat in un sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Visualizzare lo stato e di ritardo (se necessario) per tutti i processi di gestione, estrarre e Replicat in un sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Registra la sessione GGSCI in un database in modo che sia possibile eseguire i comandi che influiscono sul database:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Avviare il processo di gestione:

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. creare estrarre i processi di gruppo e dati aumenta del sito A e B di sito

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Creare processi di estrazione e aumenta dati nel sito

È necessario creare i processi di estrazione e dati aumenta il sito e sito B. desktop remoto sito a e B sito tramite il portale classico Azure. Aprire la finestra interprete comando GGSCI. Eseguire i comandi seguenti nel sito a:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le seguenti informazioni: GGSCI (MachineGG1) 18 > Modifica parametri ext1 estratto ext1 ID utente ggate, PASSWORD ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate tabella scott.inventory, GETBEFORECOLS (via aggiornamento KEYINCLUDING (prod_category, qty_in_stock, last_dml), via eliminare KEYINCLUDING (prod_category, qty_in_stock, last_dml));

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Creare una tabella di verifica GoldenGate del sito B

Successivamente, è necessario aggiungere una tabella di verifica nel sito B. A questo scopo è necessario aprire una finestra di interprete comando GoldenGate ed eseguire:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

E quindi aggiungere la tabella di verifica al database, in cui ggate il proprietario:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Aggiungere il nome della tabella punto di controllo per il file globali nel server di destinazione, ossia B sito in questo passaggio. Modificare il file globali nel sito b:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Aggiungere quindi il parametro CHECKPOINTTABLE al file globali esistente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Come ultimo passaggio, salvare e chiudere il file di parametro globali.


###<a name="add-replicat-on-site-b"></a>Aggiungere REPLICAT nel sito B
In questa sezione viene descritto come aggiungere un processo REPLICAT "REP2" nel sito B.

Comando Aggiungi REPLICAT consente di creare un gruppo di Replicat nel sito b:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Creare processi di estrazione e dati aumenta del sito B

In questa sezione viene descritto come creare un nuovo processo estratto "EXT2" e un nuovo processo pump dati "DPUMP2" nel sito b:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Creare una tabella di verifica GoldenGate nel sito

Aprire la finestra di Oracle GoldenGate comando interprete e creare una tabella di verifica:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

È necessario aggiungere il nome della tabella punto di controllo per il file globali nel sito A.

Aprire la finestra di Oracle GoldenGate comando interprete e modificare il file globali nel sito a:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Salvare e chiudere il file di parametro globali.

###<a name="add-replicat-on-site-a"></a>Aggiungere REPLICAT nel sito

In questa sezione viene descritto come aggiungere un processo REPLICAT "REP1" nel sito A.

Il comando seguente crea un rep1 gruppo Replicat con il nome di una traccia e checkpointtable associato.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Aprire il file di parametro con il comando Modifica parametri e quindi aggiungere le informazioni seguenti:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Aggiungere trandata nel sito a e B di sito

Attivare la registrazione supplementare a livello di tabella tramite il comando Aggiungi TRANDATA. Aprire la finestra di interprete Oracle GoldenGate comando, accedere al database e quindi eseguire il comando Aggiungi TRANDATA.

Desktop remoto per MachineGG1, aprire la interprete Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Desktop remoto per MachineGG2, aprire la interprete Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Informazioni sullo stato del livello tabella supplementare registrazione:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Aggiungere trandata nel sito a e B di sito

Attivare la registrazione supplementare a livello di tabella tramite il comando Aggiungi TRANDATA. Aprire la finestra di interprete Oracle GoldenGate comando, accedere al database e quindi eseguire il comando Aggiungi TRANDATA.

Desktop remoto per MachineGG1, aprire la interprete Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Desktop remoto per MachineGG2, aprire la interprete Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Avvio dei processi di estrazione e aumenta dati nel sito

Avviare ext1 di processo estratto a sito:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Avviare il dpump1 processo pump dati nel sito a:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Visualizzare le informazioni relative ext1 di gruppo estratto: GGSCI (MachineGG1) 32 > info estrarre di ext1 estrarre EXT1 ultima avviato 2013-11-25 08:03 ritardo verifica esecuzione stato 00:00:00 (aggiornato 00:00:02 fa) Log lettura verifica Oracle Ripeti registri 2013-11-25 08:03:18 Seqno 6, si 3230720 RBA 0.1074371 (1074371)

Visualizzare lo stato e di ritardo (se necessario) per tutti i processi di gestione, estrarre e Replicat in un sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Avvio dei processi di estrazione e aumenta dati nel sito B

Avviare ext2 di processo estratto nel sito b:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Avviare il dpump2 processo pump dati nel sito b:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Visualizzare lo stato e di ritardo (se necessario) per tutti i processi di gestione, estrarre e Replicat in un sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Avvia il processo di REPLICAT nel sito

In questa sezione viene descritto come avviare il processo di REPLICAT "REP1" nel sito A.

Avviare il processo di Replicat con sito:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Visualizzare lo stato di un gruppo di Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Avvia il processo di REPLICAT del sito B

In questa sezione viene descritto come avviare il processo di REPLICAT "REP2" nel sito B.

Avviare il processo di Replicat nel sito b:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Visualizzare lo stato di un gruppo di Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Verificare che il processo di replica bidirezionale

Per verificare la configurazione di Oracle GoldenGate, inserire una riga nel database nel sito a Desktop remoto sito risposte Apri backup SQL * Plus prompt dei comandi ed Esegui: SQL > Scegliere nome database $ v.

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Quindi, verificare se tale riga viene replicato nel sito B. A tale scopo, desktop remoto sito B. Apri backup finestra SQL Plus ed eseguire:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Inserire un nuovo record al sito b:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Desktop remoto per il sito e verificare se la replica ha avuto luogo:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Risorse aggiuntive
[Immagini di macchina virtuale Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)
