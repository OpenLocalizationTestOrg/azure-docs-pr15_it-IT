<properties
   pageTitle="Creare un modello di distribuzione app logica | Microsoft Azure"
   description="Informazioni su come creare un modello di distribuzione logica app e usarlo per la gestione del rilascio"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Creare un modello di distribuzione app logica

Dopo la creazione di un'app di logica, è consigliabile creare un modello di Manager delle risorse di Azure. In questo modo, è possibile distribuire facilmente l'app logica a qualsiasi ambiente o un gruppo di risorse in cui può essere necessario. Per un'introduzione ai modelli di Manager delle risorse, assicurarsi di vedere gli articoli sulla [creazione di modelli di gestione risorse di Azure](../resource-group-authoring-templates.md) e [distribuzione di risorse con i modelli di gestione risorse di Azure](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modello di distribuzione app logica

Un'app logica sono previsti tre componenti di base:

* **Risorsa app logica**. Questa risorsa contiene informazioni sugli elementi come prezzi piano, luogo e la definizione del flusso di lavoro.
* **Definizione del flusso di lavoro**. Si tratta di ciò che si vede nella visualizzazione codice. Include la definizione dei passaggi del flusso di e come eseguire il motore. Si tratta di `definition` proprietà della risorsa app logica.
* **Connessioni**. Si tratta di risorse distinte che sicuro archiviare metadati relativi a tutte le connessioni di connettore, ad esempio una stringa di connessione e un token di accesso. Si fa riferimento a questi elementi in un'app di logica nel `parameters` sezione della risorsa app logica.

È possibile visualizzare tutte le parti per le app logica esistente usando uno strumento come [Esplora risorse Azure](http://resources.azure.com).

Per creare un modello per un'app di logica da utilizzare con le distribuzioni di gruppo di risorse, è necessario definire le risorse e impostare i parametri in base alle esigenze. Ad esempio, se si distribuisce in un ambiente di produzione, i test e lo sviluppo, sarà probabilmente da usare stringhe di connessione diverse a un database SQL in ogni ambiente. In alternativa, è possibile distribuire entro diversi abbonamenti o gruppi di risorse.  

## <a name="create-a-logic-app-deployment-template"></a>Creare un modello di distribuzione app logica

Il modo più semplice per disporre di un modello di distribuzione app logica valido consiste nell'utilizzare [Visual Studio Tools per App logica](./app-service-logic-deploy-from-vs.md).  Gli strumenti di Visual Studio generano un modello di distribuzione valido che può essere utilizzato in qualsiasi percorso o l'abbonamento.

Alcuni altri strumenti possono fornire supporto durante la creazione di un modello di distribuzione app logica. È possibile creare manualmente, vale a dire utilizzando le risorse già descritte di seguito per creare i parametri in base alle esigenze. È inoltre possibile utilizzare un modulo di PowerShell [creatore di modelli di app logica](https://github.com/jeffhollan/LogicAppTemplateCreator) . In questo modulo Apri origine viene valutata l'app logica e tutte le connessioni che utilizza e quindi genera risorse modello con i parametri necessari per la distribuzione. Ad esempio, se si dispone di un'app di logica che riceve un messaggio da una coda di Azure servizio Bus e consente di aggiungere dati a un database SQL Azure, lo strumento verrà mantenere tutte le regole orchestrazione e i parametri per le stringhe di connessione SQL e Bus di servizio in modo che possa essere impostati in fase di distribuzione.

>[AZURE.NOTE] Connessioni devono essere dello stesso gruppo di risorse app logica.

### <a name="install-the-logic-app-template-powershell-module"></a>Installare il modulo di PowerShell modello app logica

Il modo più semplice per installare il modulo consiste nell'utilizzare la [Raccolta di PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)tramite il comando `Install-Module -Name LogicAppTemplate`.  

È anche possibile installare il modulo PowerShell manualmente:

1. Scaricare la versione più recente del [creatore di modelli di app logica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Estrarre la cartella nella cartella del modulo PowerShell (in genere `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Per il modulo per l'uso con qualsiasi accesso tenant e abbonamento token, è consigliabile usarlo con lo strumento di riga di comando [ARMClient](https://github.com/projectkudu/ARMClient) .  In questo [post di blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) , ARMClient vengono illustrate in modo più dettagliato.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generare un modello di app logica tramite PowerShell

Dopo l'installazione di PowerShell, è possibile generare un modello utilizzando il comando seguente:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`ID abbonamento Azure. Questa riga prima di tutto Ottiene un accesso token tramite ARMClient, quindi invia tramite allo script di PowerShell e quindi crea il modello in un file JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Aggiungere parametri a un modello di app logica

Dopo aver creato il modello di app logica, è possibile continuare aggiungere o modificare i parametri che potrebbero essere necessari. Ad esempio, se la definizione include un ID risorsa per una funzione di Azure o annidata del flusso di lavoro che si prevede di utilizzare una distribuzione singola, è possibile aggiungere altre risorse per il modello e impostare i parametri ID in base alle esigenze. Lo stesso si applica a tutti i riferimenti a personalizzato API o Swagger endpoint che si prevede di distribuire con ogni gruppo di risorse.

## <a name="deploy-a-logic-app-template"></a>Distribuire un modello di app logica

È possibile distribuire il modello utilizzando qualsiasi numero di strumenti, tra cui PowerShell, API REST, gestione del rilascio di Visual Studio e la distribuzione di modello portale Azure. Vedere l'articolo sulla [distribuzione di risorse con i modelli di gestione risorse di Azure](../resource-group-template-deploy.md) per ulteriori informazioni. Inoltre, è consigliabile creare un [file di parametri](../resource-group-template-deploy.md#parameter-file) per la memorizzazione di valori per il parametro.

### <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Dopo la distribuzione, l'app logica funziona to-end con parametri validi. Tuttavia, connessioni OAuth ancora saranno necessario essere autorizzati per generare un token di accesso valido. È possibile eseguire questa operazione, aprire l'app logica nella finestra di progettazione e quindi autorizzare le connessioni. In alternativa, se si vuole automatizzare, è possibile utilizzare uno script per consenso per ogni connessione OAuth. Esiste uno script di esempio in GitHub [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) nel progetto.

## <a name="visual-studio-release-management"></a>Gestione del rilascio di Visual Studio

Uno scenario comune per la distribuzione e gestione di un ambiente consiste nell'usare uno strumento come Visual Studio la gestione del rilascio, con un modello di distribuzione app logica. Visual Studio Team Services include un'attività di [Gruppo di risorse Azure distribuire](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) che è possibile aggiungere a qualsiasi compilazione o pipeline di rilascio. È necessario disporre di un [servizio principale](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) per l'autorizzazione per la distribuzione e quindi è possibile generare la definizione di rilascio.

1. In Gestione del rilascio, per creare una nuova definizione, selezionare **vuota** per iniziare con una definizione vuota.

    ![Creare una definizione di nuova e vuota][1]   

1. Scegliere tutte le risorse che necessarie per l'oggetto. Probabilmente sarà il modello di app logica generato manualmente o come parte del processo di compilazione.
1. Aggiungere un'attività di **Azure risorse gruppo di distribuzione** .
1. Configurazione con un [servizio principale](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e fare riferimento ai file di modello e i parametri del modello.
1. Continuare a compilare le fasi del processo di rilascio per qualsiasi altro ambiente, test automatizzato o i responsabili approvazione in base alle esigenze.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
