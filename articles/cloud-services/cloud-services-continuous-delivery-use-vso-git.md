<properties
    pageTitle="Recapito continuo con fra e Visual Studio Team Services in Azure | Microsoft Azure"
    description="Informazioni su come configurare i progetti di team Visual Studio Team Services per l'utilizzo di fra a compilare e distribuire la caratteristica Web App in Azure App servizio o cloud services automaticamente."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Recapito continuo a Azure utilizzando Visual Studio Team Services e fra

È possibile utilizzare i progetti team Visual Studio Team Services per ospitare un repository fra per il codice sorgente automaticamente compilare e distribuire le applicazioni web Azure o servizi cloud ogni volta che si inserisce un commit archivio di.

È necessario Visual Studio 2013 e Azure SDK installato. Se si dispone già di Visual Studio 2013, scaricarlo facendo clic sul collegamento **Guida introduttiva gratuitamente** su [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [qui](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]È necessario un account di Visual Studio Team Services per completare l'esercitazione: È possibile [aprire un account di Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Per configurare un servizio cloud per compilare e distribuire in Azure utilizzando Visual Studio Team Services automaticamente, seguire questa procedura.

## <a name="1-create-a-git-repository"></a>1: creare un archivio fra

1. Se si dispone già di un account di Visual Studio Team Services, è possibile ottenerne uno [qui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando si crea un progetto team, è possibile scegliere fra come il controllo del codice sorgente. Seguire le istruzioni per connettere Visual Studio per il progetto.

2. In **Esplora risorse del Team**, selezionare il collegamento **duplicare questo archivio** .

    ![][3]

3. Specificare la posizione della copia locale e quindi fare clic sul pulsante **duplicato** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: creare un progetto ed eseguire il commit all'archivio

1. In **Team Explorer**, nella sezione **soluzioni** selezionare il collegamento **Nuovo** per creare un nuovo progetto nell'archivio locale.

    ![][4]

2. È possibile distribuire un'app web o un servizio cloud (applicazione Azure) eseguendo la procedura descritta in questa procedura dettagliata. Creare un nuovo progetto di servizio Cloud di Azure o un nuovo progetto MVC ASP.NET. Assicurarsi che il progetto è destinato a .NET Framework 4 o versioni successive. Se si sta creando un progetto di servizio cloud, aggiungere un ruolo web ASP.NET MVC e un ruolo di lavoro.
Se si desidera creare un'app web, scegliere il modello di progetto **Applicazione Web ASP.NET** e quindi scegliere **MVC**. Per ulteriori informazioni, vedere [creare un'app web ASP.NET in Azure App servizio](../app-service-web/web-sites-dotnet-get-started.md) .

3. Aprire il menu di scelta rapida per la soluzione e scegliere **il Commit**.

    ![][7]

4. Se si tratta la prima volta che sono state usate fra in Visual Studio Team Services, sarà necessario fornire informazioni per identificarsi in fra. Nell'area di **Modifiche in sospeso** di **Team Explorer**, immettere l'indirizzo di posta elettronica e il nome utente. Immettere un commento per il commit e quindi fare clic su **Commit** .

    ![][8]

5. Nota le opzioni per includere o escludere modifiche specifiche quando si archivia. Se vengono escluse le modifiche desiderate, selezionare **Includi tutti**.

6. Ora stata approvata le modifiche nella copia locale del repository. Quindi sincronizzare le modifiche con il server facendo clic sul collegamento di **sincronizzazione** .

## <a name="3-connect-the-project-to-azure"></a>3: connettere il progetto in Azure

1. Dopo aver creato un archivio di operazioni in Visual Studio Team Services con codice sorgente al suo interno, si è pronti a cui connettersi repository fra Azure.  Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selezionare l'app web o servizio cloud oppure crearne uno nuovo, scegliere l'icona in basso a sinistra e scegliendo **Un servizio Cloud** o **Web App** e quindi **Creare rapido**+.

    ![][9]

3. Per i servizi cloud, selezionare il collegamento di **configurare la pubblicazione con Visual Studio Team Services** . Per le applicazioni web, selezionare il collegamento di **configurare la distribuzione dal controllo origine** .

    ![][10]

2. Nella creazione guidata digitare il nome del proprio account di Visual Studio Team Services nella casella di testo e scegliere il collegamento **Autorizzare ora** . Potrebbe essere necessario accedere.

    ![][11]

3. Nella finestra dialogo a comparsa **Richiesta di connessione** scegliere **accetta** per autorizzare Azure per configurare il progetto team in Visual Studio Team Services.

    ![][12]

4. Dopo avere stabilito autorizzazione, è visualizzato un elenco a discesa che contiene i progetti di Visual Studio Team Services team.  Selezionare il nome del progetto team che è stato creato in precedenza e scegliere segno di spunta della procedura guidata.

    ![][13]

    La volta successiva che si inserisce un commit per l'archivio Visual Studio Team Services compilare e distribuire il progetto in Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: impostare un trigger la ricostruzione e ridistribuire il progetto

1. In Visual Studio, aprire un file e modificarlo. Ad esempio modificare il file `_Layout.cshtml` nelle viste\\cartella condivisa in un ruolo web MVC.

    ![][17]

2. Modificare il testo del piè di pagina per il sito e salvare il file.

    ![][18]

3. In **Esplora soluzioni**, aprire il menu di scelta rapida per il nodo della soluzione, nodo del progetto o il file che è stato modificato e quindi scegliere **il Commit**.

4. Digitare un commento e scegliere **il Commit**.

    ![][20]

5. Selezionare il collegamento di **sincronizzazione** .

    ![][38]

6. Selezionare il collegamento **Push** per inserire il commit all'archivio in Visual Studio Team Services. (Anche utilizzare il pulsante **Sincronizza** per copiare il commit all'archivio. La differenza è che **sincronizzazione** recupera anche le modifiche più recenti dal repository.)

    ![][39]

7. Fare clic sul pulsante **Home** per tornare alla home page del **Team Explorer** .

    ![][21]

8. Scegliere **Crea** per visualizzare le build in corso.

    ![][22]

    **Team Explorer** mostra che una compilazione se è stata attivata per il controllo.

    ![][23]

9. Per visualizzare un file di log dettagliate durante la compilazione, fare doppio clic sul nome della compilazione in corso.

10. Durante la compilazione è in corso, esaminare la definizione di compilazione creato quando si utilizza la procedura guidata per creare un collegamento a Azure.  Aprire il menu di scelta rapida per la definizione di compilazione e scegliere **Modifica definizione di compilazione**.

    ![][25]

11. Nella scheda **Trigger** , si vedrà che la definizione di compilazione sia impostata su cui basare ogni archiviazione, per impostazione predefinita. (Per un servizio cloud, Visual Studio Team Services consente di creare e lo distribuisce ramo master all'ambiente di gestione temporanea automaticamente. Comunque necessario eseguire un passaggio manuale per distribuire il sito di produzione. Per un'applicazione web che non dispone di ambiente di gestione temporanea, distribuisce ramo master direttamente al sito live.

    ![][26]

1. Nella scheda **grafici** , è possibile visualizzare che l'ambiente di distribuzione è impostato sul nome dell'app web o servizio cloud.

    ![][27]

1. Specificare i valori delle proprietà se si desidera che valori diversi da quelli predefiniti. Nella sezione **distribuzione** sono le proprietà per la pubblicazione di Azure e potrebbe essere anche necessario impostare i parametri di MSBuild. Ad esempio, in un'area progetto di servizio, per specificare una configurazione del servizio diverso da "Cloud", imposta i parametri di MSbuild `/p:TargetProfile=[YourProfile]` in *[YourProfile]* corrisponde a un file di configurazione del servizio con un nome simile ServiceConfiguration. Cscfg *YourProfile*.

    Nella tabella seguente mostra le opzioni disponibili nella sezione **distribuzione** :

  	|Proprietà|Valore predefinito|
  	|---|---|
  	|Consentire certificati non attendibili|Se è false, i certificati SSL devono essere firmati da un'autorità radice.|
  	|Consentire l'aggiornamento|Consente di distribuzione aggiornare una distribuzione esistente anziché crearne uno nuovo. Consente di mantenere l'indirizzo IP.|
  	|Non eliminare|Se true, non sovrascrivere una distribuzione non correlata esistente (aggiornamento consentito).|
  	|Percorso di impostazioni di distribuzione|Il percorso al file .pubxml per un'app web, relativo alla cartella radice del repo. Ignorati per i servizi cloud.|
  	|Ambiente di distribuzione di SharePoint|Lo stesso nome del servizio.|
  	|Ambiente di distribuzione di Azure|Il nome di servizio cloud o app web.|

1. A questo punto, la compilazione deve essere completata.

    ![][28]

1. Se si fa doppio clic sul nome di compilazione, Visual Studio viene visualizzato un **Riepilogo compilazione**, inclusi i risultati dei test di progetti di test di unità associato.

    ![][29]

1. Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), è possibile visualizzare la distribuzione associata nella scheda **distribuzioni** quando viene selezionato l'ambiente di gestione temporanea.

    ![][30]

1.  Passare all'URL del sito. Per un'app web scegliendo il pulsante **Sfoglia** nel portale. Per un servizio cloud, selezionare l'URL nella sezione **Informazioni di riepilogo rapido** della pagina **Dashboard** che mostra l'ambiente di gestione temporanea.

    Distribuzioni dall'integrazione continua per i servizi cloud pubblicate per l'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostando la proprietà di **Ambiente servizio Cloud alternativo** di **produzione**. Ecco dove l'URL del sito nella pagina dashboard del servizio cloud.

    ![][31]

    Verrà aperta una nuova scheda del browser per visualizzare il sito in esecuzione.

    ![][32]

1.  Se si apportano modifiche al progetto, si trigger compila più e verranno inseriti più distribuzioni. Il più recente è contrassegnato come attivo.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: ridistribuire una build precedente

Questo passaggio è facoltativo. Nel portale di classica Azure, scegliere una distribuzione precedente e **ridistribuire** per riavvolgere il sito per un controllo precedente. Si noti che verrà avviare una nuova compilazione TFS e creare una nuova voce nella cronologia di distribuzione.

![][34]

## <a name="6-change-the-production-deployment"></a>6: modificare la distribuzione di produzione

Quando si è pronti, è possibile convertire l'ambiente di gestione temporanea all'ambiente di produzione scegliendo **Scambia** nel portale di classica Azure. Ambiente di gestione temporanea appena distribuito è promossi a produzione e ambiente di produzione precedente, se presenti, diventa un ambiente di gestione temporanea. Distribuzione attiva potrebbe essere diversa per la produzione e ambienti di gestione temporanea, ma la cronologia di distribuzione di recente build corrisponde indipendentemente dall'ambiente.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: distribuire da una diramazione di lavoro.

Quando si utilizza fra, in genere apportare modifiche in una sezione di lavoro e integrare ramo master quando lo sviluppo raggiunge uno stato completato. Durante la fase di sviluppo di un progetto, si verrà desidera compilare e distribuire il ramo lavoro Azure.

1. In **Team Explorer**, fare clic sul pulsante **Home** e quindi fare clic sul pulsante **diramazioni** .

    ![][40]

2. Selezionare il collegamento **Nuovo ramo** .

    ![][41]

3. Immettere il nome del ramo, ad esempio "lavoro", quindi scegliere **Crea ramo**. In questo modo si crea una nuova sezione locale.

    ![][42]

4. Pubblicare la diramazione. Scegliere il nome del ramo in **diramazioni non pubblicate**e scegliere **pubblica**.

    ![][44]

6. Per impostazione predefinita, solo le modifiche al ramo master attivano una compilazione continua. Per configurare la compilazione continua per un ramo di lavoro, selezionare la pagina **si basa** in **Team Explorer**e scegliere **Modifica definizione di compilazione**.

7. Aprire la scheda **Impostazioni origine** . In **monitorate rami per l'integrazione continua e compilazione**, scegliere **fare clic qui per aggiungere una nuova riga**.

    ![][47]

8. Specificare la diramazione che è stato creato, ad esempio rif/punte/lavoro.

    ![][48]

9. Modificare il codice, aprire il menu di scelta rapida per il file modificato e quindi scegliere **il Commit**.

    ![][43]

10. Selezionare il collegamento **Non sincronizzata commit** e scegliere il pulsante **Sincronizza** o il collegamento **Push** per copiare le modifiche alla copia del ramo lavoro in Visual Studio Team Services.

    ![][45]

11. Passare alla visualizzazione **consente di creare** e individuare la compilazione solo se si ha attivata per il ramo di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri suggerimenti sull'utilizzo di operazioni con Visual Studio Team Services, vedere [sviluppare e condividere il codice fra utilizzando Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e per informazioni sull'utilizzo di un repository fra non gestito da Visual Studio Team Services per la pubblicazione in Azure, vedere [Distribuzione continuo al servizio App Azure](../app-service-web/app-service-continuous-deployment.md). Per ulteriori informazioni sui servizi di Team Visual Studio, vedere [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
