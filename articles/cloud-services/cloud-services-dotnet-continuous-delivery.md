<properties
    pageTitle="Recapito continuo per il cloud services con TFS in Azure | Microsoft Azure"
    description="Informazioni su come impostare le opzioni di distribuzione continua per applicazioni basate su cloud Azure. Esempi di codice per le istruzioni della riga di comando MSBuild e gli script di PowerShell."
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/30/2016"
    ms.author="tarcher"/>

# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Recapito continuo per i servizi Cloud in Azure

La procedura descritta in questo articolo viene illustrato come impostare le opzioni di distribuzione continua per applicazioni basate su cloud Azure. Questa procedura consente di creare automaticamente pacchetti e distribuire il pacchetto di Azure dopo ogni codice di controllo. Il processo di compilazione di pacchetto descritto in questo articolo corrisponde al contenuto il comando **pacchetto** in Visual Studio e la procedura di pubblicazione è equivalente al comando **pubblica** in Visual Studio.
L'articolo vengono illustrati i metodi utilizzati per creare un server di generazione con le istruzioni della riga di comando MSBuild e script di Windows PowerShell e anche di seguito viene illustrato come se si desidera configurare Visual Studio Team Foundation Server - Team Build definizioni di utilizzare i comandi di MSBuild e gli script di PowerShell. Il processo è personalizzabile per l'ambiente di sviluppo e ambienti di destinazione Azure.

È anche possibile utilizzare Visual Studio Team Services, una versione di TFS ospitato in Azure, per eseguire questa operazione più facilmente. Per ulteriori informazioni, vedere [Il recapito continua a Azure da utilizzando Visual Studio Team Services][].

Prima di iniziare, è consigliabile pubblicare l'applicazione da Visual Studio.
In questo modo che tutte le risorse siano disponibili e inizializzato quando si tenta di automatizzare il processo di pubblicazione.

## <a name="1-configure-the-build-server"></a>1: configurare il Server di generazione

Prima di creare un pacchetto di Azure tramite MSBuild, è necessario installare gli strumenti e software necessari sul server di compilazione.

Visual Studio non è necessario installare nel server della build. Se si desidera utilizzare il servizio Team Foundation Build per gestire il server di generazione, seguire le istruzioni nella documentazione del [Servizio di Team Foundation Build][] .

1.  Nel server di generazione, installare [.NET Framework 4.5.2][], che include MSBuild.
2.  Installare [Gli strumenti di creazione condivisa di Azure per .NET](https://azure.microsoft.com/develop/net/)più recente.
3.  Installare le [librerie di Azure per .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Copiare il file Microsoft.WebApplication.targets da un'installazione di Visual Studio nel server di compilazione.

    In un computer con installato Visual Studio, questo file si trova nella directory c:\\programma x86\\MSBuild\\Microsoft\\Visual Studio\\v14.0\\WebApplications. È necessario copiare nella stessa directory sul server di compilazione.
5.  Installare [Azure Tools per Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: creare un pacchetto usando i comandi di MSBuild

In questa sezione viene descritto come creare un comando di MSBuild che consente di creare un pacchetto di Azure. Eseguire questo passaggio nel server di compilazione per verificare che tutto sia configurato correttamente e che il comando MSBuild svolge la funzione desiderata. È possibile aggiungere la riga di comando per script di generazione esistenti nel server di compilazione oppure è possibile utilizzare la riga di comando in una definizione per creare, come descritto nella sezione successiva. Per ulteriori informazioni sui parametri della riga di comando e MSBuild, vedere [Informazioni di riferimento della riga di comando MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Se è installato Visual Studio nel server di generazione, individuare e scegliere **Visual Studio comando Prompt** nella cartella **Visual Studio Tools** di Windows.

    Se sul server di compilazione non è installato Visual Studio, aprire un prompt dei comandi e assicurarsi che sia accessibile nel percorso MSBuild.exe. MSBuild viene installato con .NET Framework nel percorso x:%Windir%\\Microsoft.NET\\Framework\\*versione*. Ad esempio, per aggiungere MSBuild.exe alla variabile di ambiente PATH dopo aver installato .NET Framework 4, digitare il comando seguente al prompt dei comandi:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  Al prompt dei comandi, passare alla cartella contenente il file di progetto Azure che si desidera creare.

3.  Eseguirlo tramite con l'opzione /target: pubblicare opzione come illustrato nell'esempio seguente:

        MSBuild /target:Publish

    Questa opzione può essere abbreviata in /t: pubblicare. Dopo aver installato il SDK di Azure, l'opzione /t:Publish in MSBuild non possono essere confuse con i comandi di pubblicazione disponibili in Visual Studio. /T: pubblicare opzione build solo i pacchetti di Azure. Non viene distribuito i pacchetti come comandi pubblica in Visual Studio.

    Facoltativamente, è possibile specificare il nome del progetto come parametro MSBuild. Se non viene specificato, viene utilizzata la directory corrente. Per ulteriori informazioni sulle opzioni della riga di comando di MSBuild, vedere [Informazioni di riferimento della riga di comando MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

4.  Individuare l'output. Per impostazione predefinita, questo comando crea una directory in relazione alla cartella radice per il progetto, ad esempio *ProjectDir*\\collocazione\\*configurazione*\\app.publish\\. Quando si creazione un progetto di Azure, viene generato due file, il file di pacchetto e il relativo file di configurazione:

    -   Project.cspkg
    -   ServiceConfiguration. Cscfg *TargetProfile*

    Per impostazione predefinita, ogni progetto Azure include un servizio configurazione (file cscfg) per locale Build (debug) e un altro per build cloud (produzione o gestione temporanea), ma è possibile aggiungere o rimuovere i file di configurazione del servizio in base alle esigenze. Quando si crea un pacchetto Visual Studio, verrà richiesto il file di configurazione del servizio per includere insieme al pacchetto.

5.  Specificare il file di configurazione del servizio. Quando si crea un pacchetto tramite MSBuild, il file di configurazione del servizio locale è incluso per impostazione predefinita. Per includere un file di configurazione di servizi diversa, impostare la proprietà TargetProfile del comando MSBuild, come illustrato nell'esempio seguente:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Specificare il percorso per l'output. Impostare il percorso utilizzando il /p:PublishDir =*Directory* \\ opzione, incluso il separatore di barra rovesciata finale, come illustrato nell'esempio seguente:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Dopo avere creato e testati una riga di comando MSBuild appropriata per la generazione dei progetti e combinarle in un pacchetto di Azure, è possibile aggiungere la riga di comando per gli script di compilazione. Se il server di generazione utilizza script personalizzati, questo processo dipenderà specifiche del processo di compilazione personalizzato. Se si utilizza TFS come un ambiente di generazione, è possibile seguire le istruzioni nel passaggio successivo per aggiungere la compilazione di Azure pacchetto per il processo di compilazione.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: creare un pacchetto tramite per Team Build

Se si dispone di Team Foundation Server (TFS) configurare come un controller di compilazione e il server di generazione impostato come un computer di compilazione TFS, quindi è facoltativamente possibile configurare una generazione automatica per il pacchetto di Azure. Per informazioni su come configurare e utilizzare Team Foundation server come un sistema di compilazione, vedere [scalabilità del sistema di compilazione][]. In particolare, la procedura seguente presuppone che sia stato configurato il server di generazione come descritto in [distribuire e configurare un server di generazione][], e che ha creato un progetto team, creare un progetto di servizio cloud nel progetto team.

Per configurare TFS per la creazione di pacchetti Azure, procedere come segue:

1.  In Visual Studio nel computer di sviluppo, nel menu Visualizza scegliere **Team Explorer**oppure Ctrl +\\, Ctrl + M. Nella finestra Explorer del Team, espandere il nodo **Build** o scegliere la pagina **consente di creare** e scegliere **Nuova definizione di compilazione**.

    ![Opzione nuova definizione di compilazione][0]

2.  Scegliere la scheda **Trigger** e specificare le condizioni desiderate per quando il pacchetto da compilare. Ad esempio, specificare **L'integrazione continua** per creare il pacchetto ogni volta che un controllo aggiuntivo controllo di origine.

3.  Scegliere la scheda **Impostazioni origine** e verificare che la cartella di progetto è elencata nella colonna **Cartella del codice sorgente** e lo stato **attivo**.

4.  Scegliere la scheda **Impostazioni predefinite compilazione** e controller di compilazione, verificare il nome del server di compilazione.  Inoltre, scegliere l'opzione **Copia output di compilazione il seguente elenco a discesa cartella** e specificare il percorso punto desiderato.

5.  Scegliere la scheda **processo** . Nella scheda grafici, scegliere il modello predefinito, in **Crea**, scegliere il progetto se non è già selezionata, espandere la sezione **Avanzate** nella sezione **creare** della griglia.

6.  Scegliere **Argomenti MSBuild**e impostare gli argomenti della riga di comando MSBuild appropriati, come descritto nel passaggio 2 precedente. Ad esempio, immettere **/t: pubblicare /p:PublishDir =\\\\myserver\\inserisce\\ ** per creare un pacchetto e copiare i file del pacchetto nella posizione \\ \\myserver\\inserisce\\:

    ![Argomenti MSBuild][2]

    **Nota:** Copiare i file in una condivisione pubblica, è facile manualmente la distribuzione dei pacchetti dal proprio computer di sviluppo.

5.  Verificare il successo dell'istruzione di generazione controllando una modifica al progetto o coda di una nuova compilazione. Per accodare su una nuova compilazione in Team Explorer rapida **Tutte le definizioni compilare** e quindi scegliere **Accoda nuova compilazione**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: pubblicare un pacchetto utilizzando uno Script di PowerShell

In questa sezione viene descritto come creare uno script di Windows PowerShell per la pubblicazione l'output di pacchetto app Cloud Azure utilizzando parametri facoltativi. Questo script può essere chiamato dopo la compilazione passaggio del processo di automazione di generazione personalizzata. Inoltre, può essere chiamato dalle attività del flusso di lavoro di modello di processo in Visual Studio per Team Build.

1.  Installare i [cmdlet di PowerShell Azure][] (v0.6.1 o versioni successive).
    Durante la fase di installazione cmdlet scegliere di installare come snap-in. Si noti che questa versione ufficiale supportata sostituisce la versione precedente grazie ai CodePlex, anche se le versioni precedenti sono state numerate 2.x.x.

2.  Avviare Azure PowerShell utilizzando il menu Start o pagina. Se si avvia in questo modo, i cmdlet di PowerShell Azure verranno caricati.

3.  Al prompt dei comandi PowerShell, verificare che i cmdlet di PowerShell vengano caricati immettendo il comando parziale `Get-Azure` e quindi premere il tasto Tab per il completamento.

    Se si preme il tasto Tab più volte, è necessario verificare diversi comandi PowerShell di Azure.

4.  Verificare che sia possibile connettersi al proprio abbonamento Azure importando le informazioni sull'abbonamento da file publishsettings.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    Immettere il comando

    `Get-AzureSubscription`

    Mostra le informazioni sull'abbonamento. Verificare che tutto sia corretto.

4.  Salvare il modello di script fornito alla fine di questo articolo per la cartella di script come c:\\script\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Nella sezione parametri dello script. Aggiungere o modificare i valori predefiniti. Questi valori possono essere ignorati sempre passando parametri espliciti.

6.  Verificare gli account di servizio e lo spazio di archiviazione cloud valido creati in abbonamento che possa essere la destinazione di script pubblica. Account di archiviazione (archiviazione blob) verrà utilizzato per caricare e archiviare temporaneamente il file di pacchetto e configurazione di distribuzione durante la creazione di distribuzione.

    -   Per creare un nuovo servizio cloud, è possibile chiamare lo script o usare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Verrà utilizzato il nome del servizio cloud come un prefisso in un nome di dominio completo e pertanto deve essere univoco.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Per creare un nuovo account di archiviazione, è possibile chiamare lo script o usare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Il nome dell'account di archiviazione da utilizzare come un prefisso in un nome di dominio completo e pertanto deve essere univoco. È possibile provare a utilizzare lo stesso nome del servizio cloud.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Chiamare lo script direttamente da Azure PowerShell o collegare questo script per l'automazione di compilazione host venga eseguita dopo la compilazione di pacchetto.

    >[AZURE.IMPORTANT] Lo script verrà sempre eliminare o sostituire le distribuzioni esistenti per impostazione predefinita se vengono rilevati. Questo è necessario attivare il recapito continuo da automazione nel punto in cui non è possibile chiedere all'utente.

    **Scenario di esempio 1:** distribuzione continua per l'ambiente di gestione temporanea di un servizio:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    In genere seguita dall'esecuzione verifica e scambio VIP dei test. Scambiare VIP può essere eseguita tramite il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) o utilizzando il cmdlet di distribuzione di spostamento.

    **Scenario di esempio 2:** distribuzione continua all'ambiente di produzione di un servizio test dedicato

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Desktop remoto:**

    Se Desktop remoto sia attivato nel progetto Azure sarà necessario eseguire ulteriori passaggi monouso per assicurarsi che il certificato di servizio Cloud corretto viene caricato in tutti i servizi cloud riservati da questo script.

    Individuare i valori di identificazione personale certificato previsti per i ruoli. I valori di identificazione personale sono visibili nella sezione dei certificati del file di configurazione cloud (ad esempio ServiceConfiguration.Cloud.cscfg). È inoltre visibile nella finestra di dialogo Configurazione Desktop remoto in Visual Studio quando si visualizza opzioni e visualizzare il certificato selezionato.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Caricare i certificati Desktop remoto come passaggio iniziali di configurazione utilizzando il seguente script cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Per esempio:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    In alternativa è possibile esportare il file di certificato PFX con la chiave privata e caricare i certificati in ogni servizio cloud di destinazione tramite il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    Vedere l'articolo seguente per altre informazioni: [] [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx].

    **Aggiornare la distribuzione di distribuzione e Elimina -\> nuova distribuzione**

    Lo script eseguirà per impostazione predefinita una distribuzione di aggiornamento ($enableDeploymentUpgrade = 1) quando non viene passato alcun parametro o il valore 1 viene passato in modo esplicito. Per singole istanze il vantaggio di prendere meno tempo rispetto a una distribuzione completa. Per le istanze che richiedono disponibilità che anche il vantaggio di uscita alcune istanze in esecuzione mentre altri vengono aggiornate (alcuni dominio aggiornamento) e l'indirizzo VIP non verranno eliminati.

    Distribuzione aggiornamento può essere disattivata nello script ($enableDeploymentUpgrade = 0) o passando *a enableDeploymentUpgrade 0* come parametro, che si modifica il comportamento di script prima di tutto eliminare qualsiasi distribuzione esistente e quindi si crea una nuova distribuzione.

    >[AZURE.IMPORTANT] Lo script verrà sempre eliminare o sostituire le distribuzioni esistenti per impostazione predefinita se vengono rilevati. È necessario abilitare recapito continuo da automazione nel punto in cui non è possibile utente/operatore richiesta.

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: pubblicare un pacchetto utilizzando per Team Build

Questo passaggio facoltativo si connette per Team Build per lo script creato nel passaggio 4, che consente di gestire la pubblicazione della build del pacchetto in Azure. Questa operazione comporta la modifica del modello di processo utilizzato per la definizione di compilazione in modo che possa essere eseguita un'attività pubblica alla fine del flusso di lavoro. Attività pubblica eseguirà il comando di PowerShell passando i parametri della compilazione. Output di MSBuild è destinato e pubblicare script verrà reindirizzato nell'output di generazione standard.

1.  Modificare la definizione di compilazione responsabile continuo distribuire.

2.  Selezionare la scheda **processo** .

3.  Seguire [queste istruzioni](http://msdn.microsoft.com/library/dd647551.aspx) per aggiungere un progetto di attività per il modello di processo di compilazione, scaricare il modello predefinito, aggiungerla al progetto e archiviarlo. Assegnare al modello di processo di creare un nuovo nome, ad esempio AzureBuildProcessTemplate.

3.  Tornare alla scheda **processo** e usare **Mostra dettagli** per visualizzare un elenco di modelli di processo di compilazione disponibile. Fare clic sul pulsante **Nuovo** e passare al progetto appena aggiunto e archivia. Individuare il modello appena creato e scegliere **OK**.

4.  Aprire il modello di processo selezionato per la modifica. È possibile aprire direttamente in Progettazione flussi di lavoro o nell'editor XML per l'uso con il codice XAML.

5.  Aggiungere il seguente elenco di argomenti nuovi come voci distinte nella scheda argomenti di progettazione flussi di lavoro. Tutti gli argomenti devono avere direzione = In e digitare = stringa. Questi verrà utilizzato per parametri del flusso dalla definizione di compilazione nel flusso di lavoro, quindi ottenere utilizzato per chiamare lo script pubblica.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![Elenco di argomenti][3]

    Il codice XAML corrispondente è simile alla seguente:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Aggiungere una nuova sequenza alla fine di eseguire l'agente:

    1.  Prima di tutto aggiungere un'attività di istruzione If per verificare la presenza di un file di script valido. Impostare la condizione per il valore seguente:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Quindi caso dell'istruzione If, aggiungere una nuova attività di sequenza. Impostare il nome visualizzato pubblicare' Start'

    3.  Con l'inizio pubblicare sequenza ancora selezionato, aggiungere l'elenco seguente di nuove variabili come voci distinte nella scheda variabili di progettazione flussi di lavoro. Tutte le variabili devono avere tipo variabile = stringa e obiettivi globali = inizio pubblica. Questi verrà utilizzato per parametri del flusso dalla definizione di compilazione nel flusso di lavoro, quindi ottenere utilizzato per chiamare lo script pubblica.

        -   SubscriptionDataFilePath di tipo stringa

        -   PublishScriptFilePath di tipo stringa

            ![Nuove variabili][4]

    4.  Se si utilizza TFS 2012 o versioni precedenti, aggiungere un'attività ConvertWorkspaceItem all'inizio della nuova sequenza. Se si utilizza TFS 2013 o versione successiva, aggiungere un'attività GetLocalPath all'inizio della nuova sequenza. Per un ConvertWorkspaceItem, impostare le proprietà come indicato di seguito: direzione = ServerToLocal, DisplayName = 'Converti pubblicare script nomefile', Input = 'PublishScriptLocation' risultato = 'PublishScriptFilePath' area di lavoro = 'area di lavoro. Per un'attività GetLocalPath, impostare la proprietà IncomingPath a 'PublishScriptLocation' e il risultato per 'PublishScriptFilePath'. Questa attività converte il percorso per lo script pubblica da TFS server percorsi non sicuri (se applicabile) per un percorso su disco locale standard.

    5.  Se si utilizza TFS 2012 o versioni precedenti, aggiungere un'altra attività ConvertWorkspaceItem alla fine della nuova sequenza. Direzione = ServerToLocal, DisplayName = 'Converti abbonamento nomefile', Input = 'SubscriptionDataFileLocation' risultato = 'SubscriptionDataFilePath' area di lavoro = 'area di lavoro. Se si utilizza TFS 2013 o versione successiva, aggiungere un altro GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' e risultato = 'SubscriptionDataFilePath'.

    6.  Aggiungere un'attività InvokeProcess alla fine della nuova sequenza.
        Questa attività chiama PowerShell.exe con gli argomenti passati per la definizione di compilazione.

        1.  Gli argomenti = Format ("-File""{0}" "- nome {1} - storageAccountName {2} - packageLocation""{3 \" "- cloudConfigLocation""{4 \" "- subscriptionDataFile""{5 \" "- selectedSubscription {6}-ambiente""{7 \" "", PublishScriptFilePath, nome, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, ambiente)

        2.  DisplayName = Execute pubblicare script

        3.  FileName = "PowerShell" (includere le virgolette)

        4.  OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Nella sezione **Gestisci Output Standard** casella di testo di InvokeProcess, impostare il valore di testo a "dati". Questa è una variabile di memorizzazione dei dati di output standard.

    8.  Aggiungere un'attività WriteBuildMessage sotto la sezione **Gestisci Output Standard** . Impostare la priorità = 'BuildMessageImportance' e il messaggio = 'dati'. In questo modo l'output standard dello script verrà visualizzato scritto in output di compilazione.

    9.  Nella sezione **Gestisci Output errore** casella di testo di InvokeProcess, impostare il valore di testo a "dati". Questa è una variabile di memorizzazione dei dati di errore standard.

    10. Aggiungere un'attività WriteBuildError immediatamente di sotto della sezione **Gestire Output di errore** . Impostare il messaggio = 'dati'. In questo modo che verranno scritte errori standard dello script per l'output di errore di compilazione.

    11. Correggere gli errori, indicati da punti esclamativi blu. Passare il mouse su esclamativi per ottenere un suggerimento sull'errore. Salvare il flusso di lavoro per cancellare gli errori.

    Nella finestra di progettazione, il risultato finale delle attività del flusso di lavoro pubblica aspetto simile al seguente:

    ![Attività flusso di lavoro][5]

    In XAML, il risultato finale delle attività del flusso di lavoro pubblica aspetto simile al seguente:

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>


7.  Salvare il flusso di lavoro di compilazione processo modello e archivia il file.

8.  Modificare la definizione di compilazione (chiuderlo se è già aperto) e selezionare il pulsante **Nuovo** se non viene ancora visualizzato il nuovo modello nell'elenco dei modelli di processo.

9.  Impostare il parametro valori di proprietà nella sezione varie come indicato di seguito:

    1.  CloudConfigLocation =' c:\\inserisce\\app.publish\\ServiceConfiguration.Cloud.cscfg' *questo valore dipende dal: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = ' c:\\inserisce\\app.publish\\ContactManager.Azure.cspkg' *questo valore dipende dal: ($PublishDir)($ProjectName) cspkg*

    3.  PublishScriptLocation = ' c:\\script\\WindowsAzure\\PublishCloudService.ps1'

    4.  Nome = 'mycloudservicename' *usare il nome del servizio cloud appropriato*

    5.  Ambiente = 'Gestione temporanea'

    6.  StorageAccountName = 'mystorageaccountname' *usare il nome di account di archiviazione appropriato*

    7.  SubscriptionDataFileLocation = ' c:\\script\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![Valori di proprietà di parametro][6]

10. Salvare le modifiche alla definizione di compilazione.

11. Accodare una compilazione per eseguire Crea pacchetto e pubblica. Se si dispone di un trigger impostato su integrazione continua, si eseguirà questo comportamento in ogni controllo.

### <a name="publishcloudserviceps1-script-template"></a>Modello di script PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Passaggi successivi

Per abilitare il debug remoto quando si usa recapito continuo, vedere [attivare il debug quando si usa recapito continuo per la pubblicazione in Azure remoto](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Recapito continuo a Azure tramite Visual Studio Team Services]: cloud-services-continuous-delivery-use-vso.md  
  [Servizio di Team Foundation Build]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Scalabilità del sistema di compilazione]: https://msdn.microsoft.com/library/dd793166.aspx
  [Distribuire e configurare un server di generazione]: https://msdn.microsoft.com/library/ms181712.aspx
  [Cmdlet di PowerShell Azure]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
