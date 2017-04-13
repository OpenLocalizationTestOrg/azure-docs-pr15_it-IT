<properties
    pageTitle="Specifica di una versione Node"
    description="Informazioni su come specificare la versione di Node usato da Azure siti e servizi Cloud"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Specifica di una versione Node in un'applicazione di Azure

Durante l'hosting di un'applicazione di Node. js, può essere necessario verificare che l'applicazione utilizza una versione specifica di Node. Esistono diversi modi per eseguire questa operazione per le applicazioni ospitate in Azure.

##<a name="default-versions"></a>Versioni predefinite

Versioni Node fornite da Azure vengono continuamente aggiornate. Se non diversamente specificato, la versione predefinita specificato nel `WEBSITE_NODE_DEFAULT_VERSION` variabile di ambiente verrà utilizzata. Per ignorare questo valore predefinito, seguire i passaggi seguenti sezioni di questo articolo

> [AZURE.NOTE] Se si ospita l'applicazione in un servizio Cloud di Azure (ruolo web o lavoro) ed è la prima volta che è stato distribuito l'applicazione, Azure tenterà di usare la stessa versione di Node come è stato installato l'ambiente di sviluppo se corrispondente a una delle versioni predefiniti disponibili in Azure.

##<a name="versioning-with-packagejson"></a>Controllo delle versioni con package.json

È possibile specificare la versione di Node che verrà utilizzato da aggiungere il file **package.json** le operazioni seguenti:

    "engines":{"node":version}

Dove *versione* è il numero di versione specifici da utilizzare. È possibile può specificare più complesse condizioni per la versione, ad esempio:

    "engines":{"node": "0.6.22 || 0.8.x"}

Poiché 0.6.22 non è una delle versioni di nell'ambiente di hosting, la versione più recente di 0,8 serie che è disponibile sarà utilizzato - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Siti Web di controllo delle versioni con le impostazioni dell'App
Se si ospita l'applicazione in un sito Web, è possibile impostare la variabile di ambiente **WEBSITE_NODE_DEFAULT_VERSION** per la versione desiderata. 

##<a name="versioning-cloud-services-with-powershell"></a>Servizi Cloud di controllo delle versioni con PowerShell

Se si dispone dell'applicazione in un servizio Cloud e l'applicazione tramite PowerShell di Azure, è possibile ignorare la versione di Node predefinita utilizzando il cmdlet di PowerShell **Set-AzureServiceProjectRole** . Per esempio:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Nota che i parametri nell'istruzione precedente sono maiuscole e minuscole.  È possibile verificare che la versione corretta di Node è stata selezionata verificando proprietà **motori** in **package.json del ruolo**.

È possibile anche utilizzare **Get-AzureServiceProjectRoleRuntime** per recuperare un elenco delle versioni Node disponibile per le applicazioni ospitate come un servizio Cloud.  Verificare sempre la versione di Node dipende il progetto è presente nell'elenco.

##<a name="using-a-custom-version-with-azure-websites"></a>Tramite una versione personalizzata di siti Web di Azure

Azure offre diverse versioni di predefinito di Node, è consigliabile usare una versione non disponibili per impostazione predefinita. Se l'applicazione è ospitata come un sito Web di Azure, è possibile eseguire utilizzando il file **iisnode.yml** . Fasi del processo di usando una versione personalizzata di Node con un sito Web Azure istruzioni dettagliate per la procedura seguente:

1. Creare una nuova directory e quindi si crea un file di **server.js** all'interno della directory. Il file **server.js** deve contenere le operazioni seguenti:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Verrà visualizzata la versione Node usata durante l'esplorazione del sito Web.

2. Creare un nuovo sito Web e prendere nota del nome del sito. Ad esempio, di seguito viene utilizzato [Azure strumenti della riga di comando] per creare un nuovo sito Web Azure denominato **percorso**e quindi attivare un archivio di operazioni per il sito Web.

        azure site create mywebsite --git

3. Creare una nuova directory denominata **bin** come elemento figlio della directory che contiene il file **server.js** .

4. Scaricare la versione di **node.exe** (la versione di Windows) che si desidera utilizzare con l'applicazione. Ad esempio, di seguito viene utilizzato **voltare** scaricare versione 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Salvare il file **node.exe** nella cartella **bin** creata in precedenza.

5. Creare un file **iisnode.yml** nella stessa directory come file **server.js** e quindi aggiungere il contenuto seguente al file **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Questo percorso è in **node.exe** all'interno del progetto sarà possibile individuare il file dopo aver pubblicato l'applicazione al sito Web di Azure.

6. Pubblicare l'applicazione. Ad esempio, poiché creato un nuovo sito Web con il parametro - fra in precedenza, i comandi seguenti verranno aggiungere personale archivio locale fra i file dell'applicazione e quindi inviarli all'archivio sito Web:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Dopo l'applicazione è pubblicato, aprire il sito Web in un browser. Verrà visualizzato un messaggio che indica "Ciao da Azure versione nodo in esecuzione: v0.8.1".

##<a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come specificare la versione di Node utilizzate dall'applicazione, informazioni su come [lavorare con i moduli], [compilare e distribuire un sito Web Node]e [come usare gli strumenti della riga di comando di Azure per Mac e Linux].

Per ulteriori informazioni, vedere il [Centro per sviluppatori Node](/develop/nodejs/).

[Come usare gli strumenti della riga di comando di Azure per Mac e Linux]: xplat-cli-install.md
[Azure strumenti della riga di comando]: xplat-cli-install.md
[lavorare con i moduli]: nodejs-use-node-modules-azure-apps.md
[compilare e distribuire un sito Web Node]: web-sites-nodejs-develop-deploy-mac.md
