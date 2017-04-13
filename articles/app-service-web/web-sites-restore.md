<properties 
    pageTitle="Ripristinare un'app di Azure" 
    description="Informazioni su come ripristinare l'app da un backup." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="restore-an-app-in-azure"></a>Ripristinare un'app di Azure

In questo articolo viene illustrato come ripristinare un'applicazione di [Servizio App Azure](../app-service/app-service-value-prop-what-is.md) che in precedenza è eseguito il backup (vedere [backup l'app in Azure](web-sites-backup.md)). È possibile ripristinare l'app con il database collegati (Database di SQL o MySQL) su richiesta in uno stato precedente o creare una nuova app basata su uno dei backup dell'applicazione originale. Creazione di una nuova app che viene eseguita in parallelo con la versione più recente che può essere utile per A / B-test.

Il ripristino da un backup è disponibile per le applicazioni in esecuzione nel livello **Standard** e **Premium** . Per informazioni su scalabilità l'app, vedere [scalare un'app di Azure](web-sites-scale.md). Livello **Premium** consente a un numero maggiore di backup giornalieri deve essere eseguita rispetto a livello **Standard** .

<a name="PreviousBackup"></a>
## <a name="restore-an-app-from-an-existing-backup"></a>Ripristinare un'app da un backup esistente

1. Scegliere **l'esecuzione di backup** per visualizzare e il **backup** e **l'Impostazioni** dell'app nel portale di Azure. Quindi fare clic su **Ripristina** nella barra dei comandi. 
    
    ![Scegliere Ripristina ora][ChooseRestoreNow]

3. In e il **ripristino** , selezionare l'origine backup. 

    ![](./media/web-sites-restore/021ChooseSource.png)
    
    L'opzione **backup App** Mostra tutti i backup esistenti dell'applicazione corrente ed è possibile selezionare uno. 
    L'opzione di **spazio di archiviazione** consente di selezionare qualsiasi file ZIP backup da qualsiasi account di archiviazione di Azure e contenitore nell'abbonamento esistente. 
    Se si sta tentando di ripristinare il backup di un'altra app, usare l'opzione di **spazio di archiviazione** .

4. **Ripristinare destinazione**, quindi specificare la destinazione per il ripristino di app.

    ![](./media/web-sites-restore/022ChooseDestination.png)
    
    >[AZURE.WARNING] Se si sceglie di **sovrascrittura**, verranno cancellati tutti i dati esistenti nell'applicazione corrente. Prima di scegliere **OK**, verificare che sia esattamente che si vuole eseguire.
    
    È possibile selezionare **App esistente** per ripristinare il backup di app a un'altra app nello stesso gruppo delle risorse. Prima di utilizzare questa opzione, è necessario avere già creato un'altra app nel gruppo di risorse con che rispecchiano configurazione del database a quello definito nel backup app. 
    
5. Fare clic su **OK**.

<a name="StorageAccount"></a>
## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Scaricare o si elimina una copia di backup da un account di archiviazione
    
1. Dalla pagina principale di **Sfoglia** blade del portale di Azure, selezionare **gli account di archiviazione**.
    
    Verrà visualizzato un elenco degli account di archiviazione esistente. 
    
2. Selezionare l'account di archiviazione che contiene il backup che si desidera scaricare o eliminare.
    
    Verrà visualizzato e il per l'account di archiviazione.

3. In e il accountn lo spazio di archiviazione, selezionare il contenitore desiderato
    
    ![Visualizza contenitori][ViewContainers]

4. Selezionare file di backup che si desidera scaricare o eliminare.

    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Fare clic su **Scarica** o **eliminare** a seconda che si vuole eseguire.  

<a name="OperationLogs"></a>
## <a name="monitor-a-restore-operation"></a>Monitorare un'operazione di ripristino
    
1. Per visualizzare i dettagli di esito positivo o negativo dell'operazione di ripristino app, passare a e il **Log di controllo** nel portale di Azure. 
    
    E il **log di controllo** consente di visualizzare tutte le operazioni, oltre a livello, stato, risorse e dettagli ora.
    
2. Scorrere verso il basso per individuare l'operazione di ripristino desiderato e fare clic per selezionarla.

E il dettagli verrà visualizzate le informazioni disponibili relativi all'operazione di ripristino.
    
## <a name="next-steps"></a>Passaggi successivi

È anche possibile eseguire il backup e ripristinare le applicazioni di servizio App utilizzando API REST (vedere [Usare resto eseguire il backup e ripristinare le applicazioni di servizio App](websites-csm-backup.md)).

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 
