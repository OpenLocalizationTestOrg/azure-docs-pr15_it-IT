<properties
   pageTitle="Il controllo in SQL Azure Data Warehouse | Microsoft Azure"
   description="Iniziare a utilizzare il controllo in Warehouse di dati di SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Il controllo in SQL Azure Data Warehouse

> [AZURE.SELECTOR]
- [Il controllo](sql-data-warehouse-auditing-overview.md)
- [Individuazione](sql-data-warehouse-security-threat-detection.md)

Il controllo SQL Data Warehouse consente di record del registro degli eventi del database a un controllo nell'account di archiviazione Azure. Il controllo consente di mantenere la conformità alle normative, comprendere l'attività di database e approfondire differenze e alterazioni possono indicare problematiche aziendali o sospetti violazioni della protezione. Il controllo SQL Data Warehouse si integra anche con Microsoft Power BI per il drill-down dei report e analisi.

Strumenti di controllo Abilita e agevolare la conformità agli standard di conformità ma non garantire la conformità. Per ulteriori informazioni sulle applicazioni Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro protezione di Azure</a>.

+ [Nozioni fondamentali sulla verifica database]
+ [Impostare il controllo per il database]
+ [Analizzare i log di controllo e report]

##<a id="subheading-1"></a>Nozioni fondamentali sul controllo Database Warehouse dati di SQL Azure


Il controllo dei database di SQL Data Warehouse consente di:

- **Mantieni** un audit trail di eventi selezionati. È possibile definire le categorie di funzioni del database da controllare.
- **Report** sulle attività di database. È possibile utilizzare report preconfigurato e un dashboard per iniziare rapidamente attività e la notifica degli eventi.
- Report di **analisi** . È possibile trovare eventi sospetti, insolito attività e le tendenze.

È possibile configurare il controllo per le categorie di eventi seguenti:

**Normale** e **SQL parametri** per il quale i raccolto registri di controllo sono classificati come  

- **Accesso ai dati**
- **Modifiche dello schema (Data Definition)**
- **Modifiche ai dati (DML)**
- **Account, ruoli e autorizzazioni (raccolta connessioni dati)**
- **Stored Procedure**, **accesso** e **la gestione delle transazioni**.

Per ogni categoria di eventi, controllo di **successo** e le operazioni **non riuscite** sono configurati separatamente.

Per ulteriori informazioni sulle attività e gli eventi controllati, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Riferimento di formato Log di controllo (download di file doc)</a>.

Log di controllo sono archiviati nell'account di archiviazione Azure. È possibile definire un periodo di conservazione dei log di controllo.

Un criterio può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo server predefinito verrà applicate a tutti i database in un server che non dispone di un specifica override database criterio definito.

Prima di impostazione up controllare controllo controllo se si utilizza un ["per i Client"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Impostare il controllo per il database

1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

2. passare a e il configurazione del database SQL Data Warehouse / SQL Server che si desidera controllare. Fare clic sul pulsante **Impostazioni** nella parte superiore e quindi, in e l'impostazione e selezionare **verifica**.

    ![][1]

3. In e il controllo configurazione, deselezionare la casella di controllo **Ereditano le impostazioni di controllo dal Server** . In questo modo è possibile specificare le impostazioni per un determinato database.

    ![][2]

4. Successivamente, abilitare il controllo facendo clic **sul** pulsante.

    ![][3]

5. In e il controllo configurazione, selezionare **Dettagli di archiviazione** per aprire e di spazio di archiviazione il log di controllo. Selezionare l'account di archiviazione Azure in cui verranno salvati registri e il periodo di conservazione. **Suggerimento:** Utilizzare lo stesso account di archiviazione per tutti i database controllati per sfruttare al meglio i modelli di report preconfigurato.

    ![][4]

6. Fare clic su **OK** per salvare la configurazione di dettagli dello spazio di archiviazione.


7. In **Registrazione, eventi**, fare clic su **esito positivo** o **negativo** per registrare tutti gli eventi o scegliere categorie singolo evento.


8. Se si sta configurando controllo per un database, potrebbe essere necessario modificare la stringa di connessione del client per assicurarsi che il controllo dei dati è acquisito in modo corretto. Controllare l'argomento [Modificare FDQN Server nella stringa di connessione](sql-data-warehouse-auditing-downlevel-clients.md) per le connessioni client di livello inferiore.

9. Fare clic su **OK**.


##<a id="subheading-3">Analizzare i log di controllo e report</a>

Log di controllo sono aggregati in un insieme di archivio tabelle con un prefisso **SQLDBAuditLogs** nell'account di archiviazione Azure che scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento, ad esempio <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Esplora archivi Azure</a>.

Modello di report del dashboard preconfigurate è disponibile come un <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">foglio di calcolo di Excel scaricabile</a> per analizzare rapidamente i dati del registro. Per utilizzare il modello sul log di controllo, è necessario disporre di Excel 2013 o versione successiva e Power Query, è possibile scaricare <a href="http://www.microsoft.com/download/details.aspx?id=39379">qui</a>.

Il modello contiene i dati di esempio fittizio ed è possibile impostare Power Query per importare i log di controllo direttamente dall'account di archiviazione Azure.

Per informazioni più dettagliate sull'uso con il modello di report, leggere la <a href="http://go.microsoft.com/fwlink/?LinkId=506731">Procedura (download doc)</a>.

![][5]


##<a id="subheading-4">Procedure consigliate per l'utilizzo di produzione</a>
La descrizione di questa sezione fa riferimento a schermate sopra. <a href="https://portal.azure.com" target="_blank">Portale di Azure</a> o <a href= "https://manage.windowsazure.com/" target="_bank">Classica portale classica Azure</a> possono essere utilizzati.


##<a id="subheading-5"></a>Rigenerazione della chiave di spazio di archiviazione

In produzione è soggetto a aggiornare periodicamente le chiavi di spazio di archiviazione. Quando aggiorna le chiavi è necessario salvare nuovamente il criterio. Il processo è il seguente:.


1. Impostare il **Tasto lo spazio di archiviazione** da *principale* per *secondario* e **salvare**in e il controllo configurazione (descritto in precedenza in configurare il controllo sezione).
![][4]
2. Visitare il blade configurazione dello spazio di archiviazione e **rigenerare** la *Chiave primaria di Access*.

3. Passare al controllo e configurazione, passare **Tasto lo spazio di archiviazione** da *secondaria* a *primaria* , quindi premere **Salva**.

4. Tornare dell'archiviazione dell'interfaccia utente e **rigenerare** il *Tasto secondario* (per la preparazione di ciclo di aggiornamento di chiavi successivo.

##<a id="subheading-6"></a>Automazione
Esistono diverse dei cmdlet di PowerShell che è possibile utilizzare per configurare il controllo nel Database di SQL Azure. Per accedere ai cmdlet di controllo è necessario eseguire PowerShell in modalità di gestione di risorse Azure.

> [AZURE.NOTE] Il modulo di [Gestione delle risorse Azure](https://msdn.microsoft.com/library/dn654592.aspx) è attualmente in anteprima. Non può fornire le stesse capacità di gestione del modulo di Azure.

Quando attiva la modalità di gestione risorse di Azure, eseguire `Get-Command *AzureSql*` per elencare i cmdlet disponibili.


<!--Anchors-->
[Nozioni fondamentali sulla verifica database]: #subheading-1
[Impostare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e report]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
