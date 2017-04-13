<properties
    pageTitle="Guida introduttiva di sincronizzazione di dati di database SQL"
    description="In questa esercitazione consente di iniziare a utilizzare la sincronizzazione di dati di SQL Azure (Preview)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Guida introduttiva di sincronizzazione di dati SQL Azure (Preview)
In questa esercitazione si concetti di sincronizzazione di dati di SQL Azure tramite il portale classica Azure.

In questa esercitazione si presuppone minima precedente esperienza con SQL Server e Database di SQL Azure. In questa esercitazione si crea un gruppo di sincronizzazione ibrido (SQL Server e le istanze di Database SQL) completamente configurato e la sincronizzazione in base alla pianificazione impostate.

> [AZURE.NOTE] La documentazione tecnica completa impostata per la sincronizzazione di dati SQL Azure, in precedenza disponibile su MSDN, è disponibile come file PDF. È consigliabile scaricarla [qui](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Passaggio 1: Connettersi al Database SQL Azure

1. Accedere al [portale classica](http://manage.windowsazure.com).

2. Fare clic su **Database SQL** nel riquadro sinistro.

3. Fare clic su **Sincronizza** nella parte inferiore della pagina. Quando si fa clic su sincronizzazione, viene visualizzato un elenco con i cambiamenti che è possibile aggiungere - **Nuovo gruppo di sincronizzazione** e **Nuovo agente di sincronizzazione**.

4. Per avviare la creazione guidata nuovo agente di sincronizzazione di dati SQL, fare clic su **Nuovo agente di sincronizzazione**.

5. Se non è stato aggiunto un agente prima, **fare clic su Scarica crea qui**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Passaggio 2: Aggiungere un agente Client
Questo passaggio è necessario solo se si passa a un database di SQL Server locale incluso nel proprio gruppo di sincronizzazione. Se il gruppo di sincronizzazione include solo le istanze di Database SQL, andare al passaggio 4.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Passaggio 2: installare il software necessario
Assicurarsi di avere installato il computer in cui si installa l'agente Client il seguente.

- **.NET framework 4.0**

 Installare .NET Framework 4.0 da [qui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 sistema tipi di CLR (x86)**

 Installare Microsoft SQL Server 2008 R2 SP1 sistema tipi CLR (x86) da [qui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Oggetti di gestione (x86) condivisa di Microsoft SQL Server 2008 R2 SP1**

 Installare Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) da [qui](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Passaggio 2b: installare un nuovo agente Client

Seguire le istruzioni di [installazione di un agente di Client (sincronizzazione di dati SQL)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) per installare l'agente.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Passaggio 2c: completare la procedura guidata nuovo agente di sincronizzazione di dati SQL

1.  Tornare alla creazione guidata nuovo agente di sincronizzazione di dati di SQL.
2.  Assegnare un nome significativo l'agente.
3.  Nell'elenco a discesa selezionare l' **area geografica** (centro dati) per ospitare questo agente.
4.  Nell'elenco a discesa selezionare l' **abbonamento** a ospitare questo agente.
5.  Fare clic sulla freccia destra.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Passaggio 3: Registrare un database SQL Server con l'agente Client

Dopo aver installato l'agente Client, registrare ogni database di SQL Server locale che si desidera includere in un gruppo di sincronizzazione con l'agente.
Per registrare un database con l'agente, seguire le istruzioni disponibili in [registrare un Database di SQL Server con un agente Client](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Passaggio 4: Creare un gruppo di sincronizzazione


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Passaggio 4: avviare la procedura guidata nuovo gruppo di sincronizzazione

1.  Tornare al [portale classica](http://manage.windowsazure.com).
2.  Fare clic su **database SQL**.
3.  Fare clic su **Aggiungi Sincronizza** nella parte inferiore della pagina, quindi selezionare nuovo gruppo di sincronizzazione dal cassetto della.

    ![Image2, consente](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Passaggio 4b: immettere le impostazioni di base


1.  Immettere un nome significativo per il gruppo di sincronizzazione.
2.  Nell'elenco a discesa selezionare l' **area geografica** (Data Center) per ospitare il gruppo di sincronizzazione.
3. Fare clic sulla freccia destra.

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Passaggio 4c: definire l'hub di sincronizzazione

1. Nell'elenco a discesa selezionare l'istanza di Database SQL che funge da hub di gruppo di sincronizzazione.
2. Immettere le credenziali per l'istanza di SQL Database - **HUB nome utente** e la **PASSWORD di HUB**.
3. Attendi la sincronizzazione di dati SQL confermare il nome utente e la PASSWORD. Verrà visualizzato un segno di spunta verde visualizzata a destra della PASSWORD quando le credenziali sono confermate.
4. Nell'elenco a discesa selezionare il criterio di **Risoluzione dei conflitti** .

 **Hub Wins** - sovrascrittura modifiche nella stessa apportano scritta la scrittura di database hub per i database di riferimento, fare riferimento a record del database. Livello funzionale, significa che la modifica prima scritta hub propagata agli altri database.


 **Client Wins** - le modifiche apportate all'hub vengono sovrascritti dalle modifiche nel database di riferimento. Livello funzionale, significa che l'ultima modifica scritta all'hub è quello di permanenza e distribuiti agli altri database.

5.  Fare clic sulla freccia destra.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Passaggio 4d: aggiunta di un database di riferimento


Ripetere questo passaggio per ogni database aggiuntivi che si desidera aggiungere al gruppo di sincronizzazione.

1. Nell'elenco a discesa selezionare il database da aggiungere.

    Database nella casella di riepilogo includono entrambi i database SQL Server che sono stati registrati con l'agente e le istanze di Database SQL.
2.  Immettere le credenziali per il database - **nome utente** e **PASSWORD**.
3.  Nell'elenco a discesa, selezionare la **Direzione di sincronizzazione** per il database.

    **Bidirezionale** - modifiche apportate al database di riferimento vengono scritte nel database di hub e modifiche al database hub vengono scritte nel database di riferimento.

    **Sincronizzazione dall'Hub** - il database riceve aggiornamenti dall'Hub. Modifiche non vengano inviate all'Hub.

    **Sincronizzazione con l'Hub** - il database invia gli aggiornamenti all'Hub. Le modifiche nell'Hub non vengono scritte al database.

4.  Per completare la creazione del gruppo di sincronizzazione, fare clic sul segno di spunta nell'angolo inferiore destro della procedura guidata. Attendere che la sincronizzazione di dati SQL confermare le credenziali. Un segno di spunta verde indica che le credenziali sono confermate.

5.  Fare clic sul segno di spunta una seconda volta. Si tornerà in questo modo alla pagina di **sincronizzazione** in database SQL. In questo gruppo di sincronizzazione viene elencato con altri gruppi di sincronizzazione e agenti.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Passaggio 5: Definire i dati da sincronizzare

Sincronizzazione di dati di SQL Azure consente di selezionare le tabelle e colonne da sincronizzare. Se si desidera filtrare una colonna in modo che solo le righe con valori specifici (ad esempio, l'età > = 65) sincronizzare, utilizzare il portale di sincronizzazione di dati di SQL Azure e documentazione di selezionare le tabelle, colonne e righe da sincronizzare per definire i dati da sincronizzare.

1.  Tornare al [portale classica](http://manage.windowsazure.com).
2.  Fare clic su **database SQL**.
3.  Fare clic sulla scheda **sincronizzazione** .
4.  Fare clic sul nome di questo gruppo di sincronizzazione.
5.  Fare clic sulla scheda **Regole di sincronizzazione** .
6.  Selezionare il database che si desidera fornire lo schema di gruppo di sincronizzazione.
7.  Fare clic sulla freccia destra.
8.  Fare clic su **Aggiorna SCHEMA**.
9.  Per ogni tabella nel database, selezionare le colonne da includere nella sincronizzazione.
    - Non è possibile selezionare le colonne con tipi di dati non supportate.
    - Se non sono selezionate alcun colonne in una tabella, la tabella non è incluso nel gruppo di sincronizzazione.
    - Per selezionare o deselezionare tutte le tabelle, fare clic su Seleziona nella parte inferiore dello schermo.
10. Fare clic su **Salva**, quindi attendere che il gruppo di sincronizzazione completare il provisioning.
11. Per tornare alla pagina di destinazione di sincronizzazione di dati, fare clic sulla freccia indietro nell'angolo superiore sinistro dello schermo (sopra il nome del gruppo sincronizzazione).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Passaggio 6: Configurare il gruppo di sincronizzazione

È sempre possibile sincronizzare un gruppo di sincronizzazione, fare clic su Sincronizza nella parte inferiore della pagina di destinazione di sincronizzazione di dati.
Per sincronizzare in base alla pianificazione, configurare il gruppo di sincronizzazione.

1.  Tornare al [portale classica](http://manage.windowsazure.com).
2.  Fare clic su **database SQL**.
3.  Fare clic sulla scheda **sincronizzazione** .
4.  Fare clic sul nome di questo gruppo di sincronizzazione.
5.  Fare clic sulla scheda **Configura** .
6.  **SINCRONIZZAZIONE AUTOMATICA**
    - Per configurare il gruppo di sincronizzazione per sincronizzare su una frequenza set, fare clic su **Sì**. È comunque possibile sincronizzare su richiesta facendo clic su Sincronizza.
    - Fare clic su **** per configurare il gruppo di sincronizzazione per sincronizzare solo quando si fa clic su Sincronizza.
7.  **FREQUENZA DI SINCRONIZZAZIONE**
    - Se la sincronizzazione automatica è attivata, impostare la frequenza di sincronizzazione. Specificare la frequenza deve essere compreso tra 1 mese e 5 minuti.
8.  Fare clic su **Salva**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Congratulazioni. È stato creato un gruppo di sincronizzazione che include un'istanza di Database SQL e un database SQL Server.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sui Database SQL e sincronizzazione di dati SQL, vedere:

* [Scaricare la documentazione tecnica di sincronizzazione di dati SQL completata](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Panoramica di Database SQL](sql-database-technical-overview.md)
* [Gestione del ciclo di vita del database](https://msdn.microsoft.com/library/jj907294.aspx)
 

 
