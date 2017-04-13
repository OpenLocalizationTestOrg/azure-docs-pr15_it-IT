<properties
    pageTitle="Riferimenti per sviluppatori di funzioni Azure | Microsoft Azure"
    description="Individuare i concetti di Azure funzioni e i componenti comuni a tutte le lingue e associazioni."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Riferimenti per sviluppatori di funzioni Azure

Funzioni di Azure condividono alcuni concetti tecnici e dei componenti, indipendentemente dalla lingua o di rilegatura. Prima di passare in apprendere dettagli specifici per una determinata lingua o associazione, assicurarsi di leggere in questo argomento si applica a tutti gli elementi.

In questo articolo si presuppone che già letti [Panoramica delle funzioni di Azure](functions-overview.md) e conoscono [i concetti WebJobs SDK, ad esempio trigger, le associazioni e il runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funzioni di Azure si basa su SDK WebJobs. 


## <a name="function-code"></a>Codice di funzione

Una *funzione* è il concetto principale nelle funzioni di Azure. Scrivere codice per una funzione in una lingua di propria scelta e salvare i file di codice e un file di configurazione nella stessa cartella. Configurazione di JSON e il file è denominato `function.json`. Ognuno di essi è un'esperienza leggermente diversa, ottimizzata per funzionano in modo ottimale per quella lingua diverse lingue sono supportate. 

Il `function.json` file contiene configurazione specifica per una funzione, incluse le associazioni. Il runtime legge il file per determinare quali eventi generare all'esterno della, i dati da includere quando si chiama la funzione e dove è possibile inviare dati passato dalla funzione stessa. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

È possibile impedire che il runtime esegue la funzione impostando la `disabled` la proprietà `true`.

Il `bindings` proprietà è possibile impostare i trigger e le associazioni. Ogni associazione condivide alcune impostazioni comuni e alcune impostazioni specifiche di un determinato tipo di associazione. Ogni binding richiede le seguenti impostazioni:

|Proprietà|Valori/tipi|Commenti|
|---|-----|------|
|`type`|stringa|Tipo di associazione. Ad esempio `queueTrigger`.
|`direction`|'in', 'out'| Indica se l'associazione per ricevere dati alla funzione o l'invio di dati dalla funzione.
| `name` | stringa | Il nome che verrà utilizzato per i dati associati nella funzione. Per c# trattarsi il nome di un argomento; per JavaScript sia il tasto in un elenco di chiave/valore.

## <a name="function-app"></a>Funzione app

Un'app di funzione è costituita da una o più funzioni singoli gestiti dal servizio di App Azure. Tutte le funzioni in un'app di funzione condividono lo stesso piano prezzo, distribuzione continua e la versione di runtime. Funzioni scritte in più lingue possono condividere la stessa applicazione di funzione. Considerato come un modo per organizzare e gestire collettivamente le funzioni di un'app di funzione. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (script host e host web)

Il runtime o script host, è l'host WebJobs SDK sottostante che è in attesa per gli eventi, raccoglie e invia dati e infine viene eseguito il codice. 

Per facilitare i trigger di HTTP, è inoltre disponibile un host web che è progettato per essere davanti all'host di script in scenari di produzione. In questo modo per isolare l'host di script dal traffico front-end gestito dall'host web.

## <a name="folder-structure"></a>Struttura delle cartelle

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quando la configurazione di un progetto per la distribuzione di funzioni in un'app di funzione nel servizio App Azure, è possibile considerare la struttura delle cartelle e il codice del sito. È possibile utilizzare gli strumenti esistenti come integrazione continua e distribuzione o script di distribuzione personalizzata per eseguire la distribuzione di installazione del pacchetto di tempo o codice transpilation.

>[AZURE.NOTE] Il `wwwroot` cartella qui si trova nel punto in cui i file verranno ottenere distribuiti in. Tuttavia, non è necessario includere tale cartella nei file di cui si distribuisce, farebbe con `wwwroot\wwwroot`. Se, tuttavia, il `host.json` le cartelle di file e funzione devono essere direttamente nella radice del si distribuisce.

## <a id="fileupdate"></a>Come aggiornare i file app di funzione

In editor incorporato nel portale di Azure consente di aggiornare il file *function.json* e il file di codice per una funzione. Per caricare o aggiornare altri file, ad esempio *package.json* o *project.json* o dipendenze, è necessario usare altri metodi di distribuzione.

Funzione App sono progettate App del servizio, in modo che tutte le [Opzioni di distribuzione disponibili a standard web apps](../app-service-web/web-sites-deploy.md) sono disponibili per le applicazioni di funzione anche. Ecco alcuni metodi per caricare o aggiornare file app funzione. 

#### <a name="to-use-app-service-editor"></a>Per utilizzare l'Editor del servizio di App

1. Nel portale di funzioni di Azure, fare clic su **Impostazioni app funzione**.

2. Nella sezione **Impostazioni avanzate** fare clic su **Vai alle impostazioni del servizio di App**.

3. Fare clic su **App servizio Editor** nel Menu App di spostamento in **Strumenti di sviluppo**.

4.  Fare clic su **Vai**.

    Dopo il caricamento dell'Editor di servizio App, si noterà le cartelle di file e funzione *host.json* in *wwwroot*. 

5. Aprire file per modificarli, o trascinare dal computer di sviluppo per caricare i file.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Per utilizzare endpoint servizi (Kudu) dell'applicazione di funzione

1. Passare a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **Console di Debug > CMD**.

3. Passare a `D:\home\site\wwwroot\` aggiornare *host.json* o `D:\home\site\wwwroot\<function_name>` per aggiornare i file di una funzione.

4. Trascinare un file da caricare nella cartella appropriata nella griglia di file. Esistono due aree nella griglia di file in cui è possibile eliminare un file. Per i file *con estensione zip* , viene visualizzata una finestra con l'etichetta "Trascinare qui per caricare e decomprimere il file.." Per altri tipi di file, rilasciare nella griglia di file ma all'esterno della casella "unzip".

#### <a name="to-use-ftp"></a>Per utilizzare FTP

1. Seguire le istruzioni riportate [di seguito](../app-service-web/web-sites-deploy.md#ftp) per ottenere FTP configurato.

2. Quando si è connessi al sito app funzione, copiare un file aggiornato *host.json* `/site/wwwroot` o copiare i file funzione `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Per utilizzare la distribuzione di continua

Seguire le istruzioni nella sezione [distribuzione continua per le funzioni di Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Esecuzione in parallelo

Quando si verificano più velocemente di quanto è possibile elaborarlo un runtime multithread funzione più eventi di attivazione, il runtime può richiamare la funzione più volte in parallelo.  Se un'app di funzione utilizza il [Piano di servizio dinamico](functions-scale.md#dynamic-service-plan), l'app funzione Impossibile scalabilità automaticamente.  Ogni istanza di app funzione se l'app viene eseguito il piano di servizio dinamico o un normale [Piano di servizio App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), può elaborare le chiamate di funzione simultanee in parallelo utilizzando più thread.  Il numero massimo di chiamate di funzione simultanee in ogni istanza di app funzione varia in base alla dimensione della memoria dell'app funzione. 

## <a name="azure-functions-pulse"></a>Funzioni Azure Pulse  

Pulse è un flusso di eventi live che mostra la frequenza di esecuzione la funzione, nonché successi e gli errori. È inoltre possibile monitorare il tempo medio di esecuzione. Verranno aggiunte altre funzionalità e le personalizzazioni a tale nel tempo. È possibile accedere alla pagina **Pulse** dalla scheda **monitoraggio** .

## <a name="repositories"></a>Archivi

Il codice per le funzioni di Azure è Apri origine e archiviati in archivi GitHub:

* [Azure funzioni runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portale funzioni Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modelli di funzioni Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Estensioni Azure SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Associazioni

Ecco una tabella di tutte le associazioni supportate.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Segnalazione dei problemi

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti:

* [Riferimenti per sviluppatori di Azure funzioni c#](functions-reference-csharp.md)
* [Riferimenti per sviluppatori di Azure funzioni F #](functions-reference-fsharp.md)
* [Azure NodeJS funzioni di riferimento per sviluppatori](functions-reference-node.md)
* [Associazioni e azure trigger di funzioni](functions-triggers-bindings.md)
* [Funzioni azure: viaggio di](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) nel blog del team servizio App Azure. Cronologia delle modalità di sviluppo di funzioni di Azure.





