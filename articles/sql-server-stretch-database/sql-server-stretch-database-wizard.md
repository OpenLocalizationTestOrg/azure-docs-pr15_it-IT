<properties
    pageTitle="Iniziare eseguendo il Database di abilitare per zoom guidata | Microsoft Azure"
    description="Informazioni su come configurare un database per Database zoom eseguendo il Database di abilitare per procedura guidata di zoom."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Iniziare eseguendo il Database di abilitare per procedura guidata di zoom

Per configurare un database per Database zoom, eseguire il Database di abilitare per procedura guidata di zoom.  In questo argomento descrive le informazioni che è necessario immettere e le opzioni che è necessario effettuare la procedura guidata.

Per ulteriori informazioni sul Database di zoom, vedere [Database zoom](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] In seguito, se si disabilita il Database di zoom, tenere presente che la disattivazione di Database di zoom per una tabella o per un database non comporta l'eliminazione dell'oggetto remoto. Se si desidera eliminare la tabella remota o il database remoto, è necessario rilasciarla tramite il portale di gestione Azure. Gli oggetti remoti continuano a sostenere costi Azure finché non viene eliminato manualmente. 

## <a name="launch-the-wizard"></a>Avviare la procedura guidata

1.  In Esplora oggetti di SQL Server Management Studio selezionare il database in cui si desidera attivare zoom.

2.  Destra\-fare clic su e selezionare **le attività**e quindi selezionare **Zoom**e quindi selezionare **Attiva** per avviare la procedura guidata.

## <a name="Intro"></a>Introduzione
Esaminare lo scopo della procedura guidata e i prerequisiti.

Prerequisiti importanti sono i seguenti:

-   È necessario essere un amministratore per modificare le impostazioni del database.
-   È necessario disporre di un abbonamento a Microsoft Azure.
-   SQL Server deve essere in grado di comunicare con il server Azure remoto.

![Pagina di introduzione della creazione guidata Database di zoom][StretchWizardImage1]

## <a name="Tables"></a>Selezionare le tabelle
Selezionare le tabelle che si desidera abilitare per zoom.

Tabelle con numerose righe vengono visualizzate nella parte superiore dell'elenco. Prima che la procedura guidata viene visualizzato l'elenco di tabelle, li analizza per i tipi di dati che non è attualmente supportati dal Database di zoom.

![Pagina selezionare le tabelle della creazione guidata Database di zoom][StretchWizardImage2]

|Colonna|Descrizione|
|----------|---------------|
|(nessun titolo)|Selezionare la casella di controllo in questa colonna per abilitare la tabella selezionata per zoom.|
|**Nome**|Specifica il nome della colonna della tabella.|
|(nessun titolo)|Un simbolo nella colonna può rappresentare un messaggio di avviso e non\'t impedire l'attivazione della tabella selezionata per zoom. Può rappresentare anche un problema di blocco che impedisce l'attivazione della tabella selezionata per zoom \- , ad esempio, in quanto la tabella utilizza un tipo di dati non supportate. Passare il mouse sul simbolo per visualizzare altre informazioni in una descrizione comando. Per ulteriori informazioni, vedere [limitazioni per i Database di zoom](sql-server-stretch-database-limitations.md).|
|**Viene estesa**|Indica se la tabella è già attivata per zoom.|
|**Eseguire la migrazione**|È possibile eseguire la migrazione di un'intera tabella (**Aggiorna**) o è possibile specificare un filtro in una colonna esistente nella tabella. Se si desidera utilizzare una funzione di filtro diverso per selezionare le righe per eseguire la migrazione, eseguire l'istruzione ALTER TABLE per specificare la funzione filter dopo chiudere la procedura guidata. Per ulteriori informazioni sulla funzione filter, vedere [Selezionare le righe per eseguire la migrazione utilizzando una funzione di filtro](sql-server-stretch-database-predicate-function.md). Per ulteriori informazioni su come applicare la funzione, vedere [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Righe**|Specifica il numero di righe nella tabella.|
|**Dimensione (KB)**|Indica le dimensioni della tabella in KB.|

## <a name="Filter"></a>È possibile fornire un filtro di riga

Se si desidera fornire una funzione di filtro per selezionare le righe per eseguire la migrazione, eseguire le operazioni seguenti nella pagina **Selezione tabelle** .

1.  Nell'elenco **Selezionare le tabelle che si desidera allontanare** fare clic su **Intera tabella** nella riga relativa alla tabella. Verrà visualizzata la finestra di dialogo **Selezionare le righe per estendere** .

    ![Definire una funzione di filtro][StretchWizardImage2a]

2.  Nella finestra di dialogo **Selezionare le righe per estendere** selezionare **Le righe selezionate**.

3.  Nel **campo nome**specificare un nome per la funzione filtro.

4.  Per la clausola **Where** , selezionare una colonna dalla tabella, selezionare un operatore e specificare un valore.

5. Fare clic su **verifica** per verificare la funzione. Se la funzione restituisce i risultati della tabella -, ovvero se sono presenti righe che soddisfano la condizione - per eseguire la migrazione di test segnala **esito positivo**.

    >   [AZURE.NOTE] La casella di testo che consente di visualizzare la query del filtro è di sola lettura. Non è possibile modificare la query nella casella di testo.

6.  Fare clic su Fine per tornare alla pagina **Selezionare le tabelle** .

La funzione filter viene creata in SQL Server solo dopo aver completato la procedura guidata. Nel frattempo, è possibile tornare alla pagina **selezionare tabelle** per modificare o rinominare la funzione filtro.

![Selezionare pagina tabelle dopo la definizione di una funzione di filtro][StretchWizardImage2b]

Se si desidera utilizzare un tipo diverso della funzione di filtro per selezionare le righe per eseguire la migrazione, eseguire una delle operazioni seguenti.  

-   Chiudere la procedura guidata ed eseguire l'istruzione ALTER TABLE per attivare zoom per la tabella e per specificare una funzione di filtro. Per ulteriori informazioni, vedere [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-table.md).  

-   Eseguire l'istruzione ALTER TABLE per specificare una funzione di filtro dopo l'uscita la procedura guidata. Per i passaggi necessari, vedere [aggiungere una funzione di filtro dopo aver eseguito la procedura guidata](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurare la distribuzione di Azure

1.  Accedere a Microsoft Azure con un account Microsoft.

    ![Accedere a Azure - Creazione guidata Database di zoom][StretchWizardImage3]

2.  Selezionare l'abbonamento di Azure esistente da utilizzare per il Database di zoom.

3.  Selezionare un'area di Azure.
    -   Se si crea un nuovo server, il server viene creato in quest'area.  
    -   Se si dispone di server esistenti nell'area selezionata, la procedura guidata elenca i quando si sceglie **server esistente**.

    Per limitare la latenza, selezionare l'area geografica di Azure in cui si trova SQL Server. Per ulteriori informazioni sulle aree, vedere [Le aree di Azure](https://azure.microsoft.com/regions/).

4.  Specificare se si desidera utilizzare un server esistente oppure creare un nuovo server Azure.

    Se il servizio Active Directory in SQL Server è federata con Azure Active Directory, è possibile utilizzare facoltativamente un account di servizio federata per SQL Server per comunicare con il server Azure remoto. Per ulteriori informazioni sui requisiti per questa opzione, vedere [Modificare DATABASE impostare le opzioni (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Crea nuovo server**

        1.  Creare un account di accesso e una password per l'amministratore del server.

        2.  Facoltativamente, è possibile usare un account di servizio federata per SQL Server per comunicare con il server remoto Azure.

        ![Creare nuovi server Azure - Creazione guidata Database di zoom][StretchWizardImage4]

    -   **Server esistente**

        1.  Selezionare il server Azure esistente.

        2.  Selezionare il metodo di autenticazione.

            -   Se si seleziona **l'Autenticazione di SQL Server**, specificare l'account di amministratore e la password.

            -   Selezionare **Autenticazione integrata di Active Directory** per usare un account di servizio federata per SQL Server per comunicare con il server Azure remoto. Se il server selezionato non è integrato con Azure Active Directory, questa opzione non viene visualizzata.

        ![Selezionare server Azure esistente - Creazione guidata Database di zoom][StretchWizardImage5]

## <a name="Credentials"></a>Proteggere le credenziali
È necessario disporre di una chiave di schema del database per proteggere le credenziali zoom Database viene utilizzato per la connessione al database remoto.  

Se esiste già una chiave di schema del database, immettere la password per renderla.  

![Proteggere le credenziali pagina della creazione guidata Database di zoom][StretchWizardImage6b]

Se il database non ha una chiave master esistente, immettere una password complessa per creare una chiave di schema del database.  

![Proteggere le credenziali pagina della creazione guidata Database di zoom][StretchWizardImage6]

Per ulteriori informazioni sulla chiave di schema del database, vedere [creare chiave MASTER (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [creare una chiave di schema del Database](https://msdn.microsoft.com/library/aa337551.aspx). Per altre informazioni sulle credenziali che la procedura guidata Crea, vedere [Creare DATABASE nell'ambito delle CREDENZIALI (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selezionare indirizzo IP
Utilizzare l'intervallo di indirizzi IP di subnet (scelta consigliata) o l'indirizzo IP pubblico di SQL Server per creare una regola in Azure che consente di SQL Server comunicare con il server Azure remoto.

L'indirizzo IP o gli indirizzi che forniscono in questa pagina indicano il server Azure per consentire i dati in arrivo, query e le operazioni di gestione avviate da SQL Server per passano attraverso il firewall Azure. La procedura guidata non modifica le impostazioni di firewall in SQL Server.

![Selezionare pagina indirizzo IP della creazione guidata Database di zoom][StretchWizardImage7]

## <a name="Summary"></a>Riepilogo
Rivedere i valori immessi dall'utente e le opzioni selezionate nella procedura guidata e i costi stimati in Azure. Scegliere **Fine** per attivare zoom.

![Pagina di riepilogo della creazione guidata Database di zoom][StretchWizardImage8]

## <a name="Results"></a>Risultati
Esaminare i risultati.

Per controllare lo stato della migrazione dei dati, vedere [Monitor e risolvere i problemi di migrazione dei dati (Database di zoom)](sql-server-stretch-database-monitor.md).

![Pagina dei risultati della creazione guidata Database di zoom][StretchWizardImage9]

## <a name="KnownIssues"></a>La procedura guidata di risoluzione dei problemi
**La creazione guidata Database di zoom non è riuscita.**
Se Database zoom non è ancora abilitata a livello di server e si esegue la procedura guidata senza il sistema di autorizzazioni di amministratore per abilitarla, la procedura guidata. Chiedere all'amministratore di sistema per attivare zoom Database nell'istanza del server locale e quindi eseguire di nuovo la procedura guidata. Per ulteriori informazioni, vedere [prerequisiti: autorizzazioni per abilitare il Database di zoom nel server](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Passaggi successivi
Abilitare le altre tabelle per Database di zoom. Controllare la migrazione dei dati e gestire zoom\-abilitato database e le tabelle.

-   [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-table.md) per abilitare le altre tabelle.

-   [Monitor e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md) per controllare lo stato della migrazione dei dati.

-   [Interrompere e riprendere Database zoom](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di Database di zoom](sql-server-stretch-database-manage.md)

-   [Backup database abilitato zoom](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Vedere anche

[Abilitare il Database di zoom per un database](sql-server-stretch-database-enable-database.md)

[Abilitare il Database di zoom per una tabella](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
