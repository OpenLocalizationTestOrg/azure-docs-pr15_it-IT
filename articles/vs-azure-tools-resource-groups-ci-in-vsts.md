<properties
    pageTitle="Integrazione continua VS Team Services utilizzando i progetti di gruppo di risorse Azure | Microsoft Azure"
    description="Descrive come configurare l'integrazione continua in Visual Studio Team Services utilizzando progetti di distribuzione di Azure gruppo di risorse in Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integrazione continua in Visual Studio Team Services utilizzando i progetti di distribuzione di Azure gruppo di risorse

Per distribuire un modello di Azure, è necessario eseguire attività per passare tra le varie fasi: copia di compilazione, Test, in Azure (denominato anche "Prova") e distribuire modello.  Esistono diversi modi per eseguire questa operazione in Visual Studio Team Services (VS Team Services). Entrambi i metodi forniscono gli stessi risultati, quindi scegliere quello più appropriato per il flusso di lavoro.

-   Aggiungere un unico passaggio alla definizione di compilazione che esegue lo script di PowerShell incluse nel progetto di distribuzione di gruppo di risorse Azure (Distribuisci AzureResourceGroup.ps1). Lo script consente di copiare gli elementi e quindi distribuisce il modello.
-   Aggiungere che più servizi di Team VS istruzioni di generazione, ognuno di essi eseguire un'operazione secondario.

In questo articolo viene illustrato come utilizzare la prima opzione (utilizzare una definizione di compilazione per eseguire lo script di PowerShell). Uno dei vantaggi di questa opzione è che lo script utilizzato dagli sviluppatori di Visual Studio è lo stesso script utilizzato da VS Team Services. Questa procedura si presuppone che si dispone già di un progetto di distribuzione di Visual Studio archiviato in Visual Studio Team Services.

## <a name="copy-artifacts-to-azure"></a>Copia gli elementi da Azure 

Indipendentemente dallo scenario, se si dispone di tutti gli elementi necessari per la distribuzione dei modelli, sarà necessario concedere l'accesso Manager delle risorse di Azure ad essi. Questi elementi è possono includere file quali:

-   Modelli nidificati
-   Script di configurazione e DSC
-   File binari dell'applicazione

### <a name="nested-templates-and-configuration-scripts"></a>Modelli nidificati e gli script di configurazione
Quando si utilizzano i modelli forniti da Visual Studio o integrato con frammenti di Visual Studio, lo script di PowerShell fasi non solo gli elementi, anche Parametrizza URI per le risorse per distribuzioni diverse. Lo script quindi copia gli elementi in un contenitore protetto in Azure, crea un token SA per tale contenitore e quindi passa tali informazioni alla distribuzione dei modelli. Vedere [creare un modello di distribuzione](https://msdn.microsoft.com/library/azure/dn790564.aspx) per informazioni sui modelli di annidate.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurare la distribuzione continua VS Team Services

Per chiamare lo script di PowerShell VS Team Services, è necessario aggiornare la definizione di compilazione. In breve, la procedura è: 

1.  Modificare la definizione di compilazione.
1.  Consente di impostare autorizzazioni Azure VS Team Services.
1.  Aggiungere un passaggio di compilazione di Azure PowerShell che fa riferimento lo script di PowerShell nel progetto di distribuzione di Azure gruppo di risorse.
1.  Impostare il valore del parametro *- ArtifactsStagingDirectory* per l'uso con un progetto generato in Visual Studio Team Services.

### <a name="detailed-walkthrough"></a>Descrizione dettagliata

La procedura seguente consentono all'utente i passaggi necessari per configurare la distribuzione continua VS Team Services 

1.  Modificare la definizione di compilazione VS Team Services e aggiungere un passaggio di compilazione di Azure PowerShell. Scegliere la definizione di compilazione nella categoria **definizioni di compilazione** e quindi scegliere **Modifica** collegamento.

    ![][0]

1.  Aggiungere una nuova azione di compilazione di **Azure PowerShell** per la definizione di compilazione e quindi scegliere **Aggiungi creare passaggio...** pulsante.

    ![][1]

1.  Scegliere la categoria di **attività di distribuzione** , selezionare l'attività di **PowerShell Azure** e quindi scegliere il pulsante **Aggiungi** .

    ![][2]

1.  Scegliere il passaggio di compilazione di **PowerShell Azure** e quindi compilare i valori.

    1.  Se si dispone già di un endpoint del servizio di Azure aggiunto a VS Team Services, scegliere la sottoscrizione nella casella di riepilogo a discesa **Abbonamento Azure** e passa quindi alla sezione successiva. 

        Se non si dispone di un endpoint del servizio di Azure VS Team Services, sarà necessario aggiungerne uno. In questa sezione vengono illustrate il processo. Se l'account Azure utilizza un account Microsoft (ad esempio Hotmail), è necessario eseguire le operazioni seguenti per ottenere un principale del servizio di autenticazione.

    1.  Scegliere casella di riepilogo a discesa il collegamento **Gestisci** accanto all' **Abbonamento Azure** .

        ![][3]

    1. Scegliere **Azure** nella casella di riepilogo a discesa **Nuovo Endpoint del servizio** .

        ![][4]

    1.  Nella finestra di dialogo **Aggiungi Azure abbonamento** selezionare l'opzione **Principale del servizio** .

        ![][5]

    1.  Aggiungere le informazioni sull'abbonamento Azure nella finestra di dialogo **Aggiungi sottoscrizione Azure** . È necessario specificare gli elementi seguenti:
        -   Id abbonamento
        -   Nome dell'abbonamento
        -   Id principale del servizio
        -   Chiave principale del servizio
        -   Id tenant

    1.  Aggiungere un nome desiderato nella casella nome **sottoscrizione** . Questo valore verrà visualizzato in un secondo momento nell'elenco VS Team Services a discesa **Abbonamento Azure** . 

    1.  Se non si conosce l'ID di abbonamento Azure, è possibile utilizzare uno dei comandi seguenti per scoprire come.
        
        Per gli script di PowerShell, usare:

        `Get-AzureRmSubscription`

        Per CLI Azure, usare:

        `azure account show`
    

    1.  Per ottenere un ID principale del servizio, chiave principale del servizio e ID Tenant, seguire la procedura descritta nel [portale di applicazione di creazione di Active Directory e principale del servizio](resource-group-create-service-principal-portal.md) o [l'autenticazione di un'identità di servizio Gestione risorse Azure](resource-group-authenticate-service-principal.md).

    1.  Sommare i valori ID principale del servizio, chiave principale del servizio e ID Tenant nella finestra di dialogo **Aggiungi sottoscrizione Azure** e quindi scegliere il pulsante **OK** .

        Ora è un'entità di servizio valido da utilizzare per eseguire lo script di PowerShell Azure.

1.  Modificare la definizione di compilazione e scegliere il passaggio di compilazione di **Azure PowerShell** . Nella casella di riepilogo a discesa **Abbonamento Azure** , selezionare l'abbonamento. (Se l'abbonamento non è disponibile, scegliere il pulsante **Aggiorna** avanti il collegamento **Gestisci** .) 

    ![][8]

1.  Specificare un percorso per lo script di PowerShell Distribuisci AzureResourceGroup.ps1. A tale scopo, fare clic sul pulsante i puntini di sospensione (...) accanto alla casella **Percorso Script** , passare allo script di PowerShell Distribuisci AzureResourceGroup.ps1 nella cartella **script** del progetto, selezionarlo e fare clic sul pulsante **OK** . 

    ![][9]

1. Dopo aver selezionato lo script, aggiornare il percorso per lo script in modo che viene eseguita da Build.StagingDirectory (la stessa directory che *ArtifactsLocation* è impostato su). È possibile eseguire questa operazione mediante l'aggiunta di "$(Build.StagingDirectory)/"all'inizio del percorso degli script.

    ![][10]

1.  Nella casella **Argomenti Script** immettere i parametri seguenti (in una sola riga). Quando si esegue lo script in Visual Studio, è possibile vedere come VS utilizza i parametri nella finestra di **Output** . È possibile usare come punto di partenza per impostare i valori dei parametri nel passaggio compilazione.

  	| Parametro | Descrizione|
  	|---|---|
  	| -ResourceGroupLocation           | Il valore di geografico posizione in cui si trova, ad esempio **eastus** o **' US orientale '**al gruppo di risorse. (Aggiungi tra virgolette singole se è presente uno spazio nel nome). Per ulteriori informazioni, vedere [Le aree di Azure](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | Il nome del gruppo di risorse utilizzato per la distribuzione.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Questo parametro, se presente, specifica che gli elementi devono essere caricati in Azure dal sistema locale. È sufficiente impostare questa opzione se la distribuzione di modello richiede elementi aggiuntivi che si desidera organizzare utilizzando lo script di PowerShell (ad esempio gli script di configurazione o modelli nidificati).                                                                                                                                                                 |
  	| -StorageAccountName              | Il nome dell'account di archiviazione utilizzato per gli elementi secondario per la distribuzione. Questo parametro è obbligatorio solo se si sta copiando gli elementi in Azure. Questo account di archiviazione non verrà creato automaticamente per la distribuzione, deve essere già presente.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | Il nome del gruppo di risorse associato all'account di archiviazione. Questo parametro è obbligatorio solo se si sta copiando gli elementi in Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | Il percorso del file di modello nel progetto di distribuzione di Azure gruppo di risorse. Per una maggiore flessibilità, utilizzare un percorso per il parametro è relativo percorso dello script di PowerShell anziché un percorso assoluto.|
  	| -TemplateParametersFile          | Il percorso del file di parametri nel progetto di distribuzione di Azure gruppo di risorse. Per una maggiore flessibilità, utilizzare un percorso per il parametro è relativo percorso dello script di PowerShell anziché un percorso assoluto.|
  	| -ArtifactStagingDirectory        | Questo parametro consente di PowerShell script conosce la cartella da cui copiati i file binari del progetto. Questo valore sostituisce il valore predefinito utilizzato per lo script di PowerShell. Per utilizzare Visual Studio Team Services, impostare il valore: $(Build.StagingDirectory) - ArtifactStagingDirectory                                                                                                                                                                                              |

    Ecco un esempio di argomenti di script (riga interrotta per migliorare la leggibilità):

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Al termine, la casella **Argomenti Script** sarà simile al seguente.

    ![][11]

1.  Dopo aver aggiunto che tutti gli elementi necessari per Azure PowerShell creare passaggio, scegliere **coda** compilazione pulsante per generare il progetto. **Creare** schermata output dello script di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di gestione di risorse Azure](azure-resource-manager/resource-group-overview.md) per ulteriori informazioni sulla gestione di risorse di Azure e gruppi di risorse Azure, leggere.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
