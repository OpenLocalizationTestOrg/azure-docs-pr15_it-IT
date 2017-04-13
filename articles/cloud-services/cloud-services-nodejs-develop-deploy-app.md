<properties
    pageTitle="Guida introduttiva di Node | Microsoft Azure"
    description="Informazioni su come creare una semplice applicazione web Node e distribuirlo a un servizio cloud Azure."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Compilare e distribuire un'applicazione di Node a un servizio Cloud di Azure

> [AZURE.SELECTOR]
- [Node](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

In questa esercitazione viene illustrato come creare una semplice applicazione Node in esecuzione in un servizio Cloud di Azure. Servizi cloud sono gli elementi costitutivi di applicazioni cloud scalable in Azure. Consentono la separazione e la gestione indipendente e scalabilità dei componenti front-end e back-end dell'applicazione.  Servizi cloud forniscono una macchina virtuale dedicata efficace per l'hosting affidabile ogni ruolo.

Per ulteriori informazioni sui servizi Cloud e illustrate le differenze rispetto a siti Web di Azure e macchine virtuali, vedere [siti Web di Azure, servizi Cloud e macchine virtuali di confronto].

>[AZURE.TIP] Ricerca di un sito Web semplice? Se il proprio scenario richiede solo un semplice sito Web front-end, è preferibile [un'app web leggera]. È possibile aggiornare facilmente in un servizio Cloud, come l'applicazione web che si sviluppa e alle proprie esigenze.

Seguendo questa esercitazione si creerà una semplice applicazione web ospitata all'interno di un ruolo web. Verrà utilizzare emulatore di calcolo per testare l'applicazione in locale, quindi distribuirlo mediante strumenti della riga di comando di PowerShell.

L'applicazione è semplice "hello world":

![Visualizzare la pagina web Hello World un web browser][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Prerequisiti

> [AZURE.NOTE] In questa esercitazione utilizza PowerShell di Azure, che richiede Windows.

- Installare e configurare [Azure Powershell].
- Scaricare e installare [Azure SDK per .NET 2.7]. Nel programma di installazione installa, selezionare:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Creare un progetto di servizio Cloud di Azure

Effettuare le operazioni seguenti per creare un nuovo progetto di servizio Cloud di Azure, insieme a supporto temporaneo Node base:

1. Esegui **di Windows PowerShell** come amministratore; dal **Menu Start** o **Schermata Start**, eseguire una ricerca per **Windows PowerShell**.

2. [PowerShell di connettersi] al proprio abbonamento.

3. Immettere il seguente cmdlet di PowerShell per creare per creare il progetto:

        New-AzureServiceProject helloworld

    ![Il risultato del comando helloworld AzureService nuovo][The result of the New-AzureService helloworld command]

    Il cmdlet **New-AzureServiceProject** genera una struttura di base per un'applicazione di Node in un servizio Cloud di pubblicazione. Contiene file di configurazione necessari per la pubblicazione su Azure. Il cmdlet di modificare anche la cartella di lavoro all'elenco per il servizio.

    Il cmdlet crea i file seguenti:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef**: specifici di Azure file necessari per la pubblicazione dell'applicazione. Per ulteriori informazioni, vedere [Panoramica della creazione di un servizio ospitato per Azure].

    -   **deploymentSettings.json**: memorizza le impostazioni locali vengono utilizzate i cmdlet di distribuzione di Azure PowerShell.

4.  Immettere il comando seguente per aggiungere un nuovo ruolo web:

        Add-AzureNodeWebRole

    ![L'output del comando Aggiungi AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    Il cmdlet **Aggiungi AzureNodeWebRole** crea un'applicazione di Node base. Viene inoltre modificata i file **.csfg** e **.csdef** per aggiungere voci di configurazione per il nuovo ruolo.

    > [AZURE.NOTE] Se non si specifica un nome di ruolo, viene utilizzato un nome predefinito. È possibile fornire un nome come primo parametro cmdlet:`Add-AzureNodeWebRole MyRole`

L'app Node sia definita nel file **server.js**, nella directory per il ruolo di web (**WebRole1** per impostazione predefinita). Ecco il codice:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Questo codice corrisponde essenzialmente esempio "Hello World" nel sito Web [nodejs.org] tranne che utilizza il numero di porta assegnato dall'ambiente basato su cloud.

## <a name="deploy-the-application-to-azure"></a>Distribuire un'applicazione di Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Scaricare le impostazioni di pubblicazione Azure

Per distribuire l'applicazione in Azure, è innanzitutto necessario scaricare le impostazioni di pubblicazione per l'abbonamento Azure.

1.  Eseguire il seguente cmdlet di PowerShell Azure:

        Get-AzurePublishSettingsFile

    Verrà utilizzato il browser per passare alla pagina di download pubblica impostazioni. Potrebbe essere richiesto di accedere con un Account Microsoft. In caso affermativo, utilizzare l'account associato all'abbonamento Azure.

    Salvare il profilo scaricato in un percorso di file che è possibile accedere facilmente.

2.  Eseguire le seguenti cmdlet per importare il profilo di pubblicazione che è stato scaricato:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Dopo aver importato le impostazioni di pubblicazione, è consigliabile l'eliminazione di file scaricati publishsettings perché contiene informazioni che potrebbero consentire a un utente accedere all'account.

### <a name="publish-the-application"></a>Pubblicare l'applicazione

Per pubblicare, eseguire i comandi seguenti:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **Nome -** specifica il nome per la distribuzione. Deve trattarsi di un nome univoco, in caso contrario non viene eseguito il processo di pubblicazione. Il comando **Get-Date** puntine una stringa di data/ora che è necessario rendere il nome univoco.

- **-Percorso** specifica data center ospitato in dell'applicazione. Per visualizzare un elenco di Data Center disponibili, utilizzare il cmdlet **Get-AzureLocation** .

- **-Avvio** verrà visualizzata una finestra del browser e si sposta il servizio di hosting dopo il completamento di distribuzione.

Dopo avere stabilito la pubblicazione, verrà visualizzata una risposta simile al seguente:

![L'output del comando Pubblica AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> È possibile richiedere alcuni minuti per l'applicazione per la distribuzione e diventano disponibili durante la pubblicazione iniziale.

Una volta completata la distribuzione, una finestra del browser aprire e passare al servizio cloud.

![Una finestra del browser che visualizza la pagina world hello; l'URL indica che la pagina è ospitata in Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

L'applicazione è in esecuzione in Azure.

Il cmdlet **AzureServiceProject pubblica** effettua le seguenti operazioni:

1.  Crea un pacchetto per la distribuzione. Il pacchetto contiene tutti i file nella cartella dell'applicazione.

2.  Crea un nuovo **account di archiviazione** se non esiste. L'account di archiviazione Azure viene utilizzato per archiviare il pacchetto dell'applicazione durante la distribuzione. Al termine dell'installazione, è possibile eliminare l'account di archiviazione.

3.  Se non ne esiste già, viene creato un nuovo **servizio cloud** . Un **servizio cloud** è il contenitore in cui l'applicazione è ospitata quando viene distribuito in Azure. Per ulteriori informazioni, vedere [Panoramica della creazione di un servizio ospitato per Azure].

4.  Pubblica il pacchetto di distribuzione di Azure.


## <a name="stopping-and-deleting-your-application"></a>Arresto ed eliminazione dell'applicazione

Dopo aver distribuito l'applicazione, è consigliabile disattivare in modo da evitare costi aggiuntivi. Fatture Azure web istanze del ruolo orarie del tempo di server utilizzato. Ora del server viene utilizzata una volta distribuita l'applicazione, anche se le istanze non sono in esecuzione e sono in stato di interruzione.

1.  Nella finestra di Windows PowerShell, interrompere la distribuzione di servizio creata nella sezione precedente con il seguente cmdlet:

        Stop-AzureService

    Interruzione del servizio può richiedere diversi minuti. Quando viene arrestato, si riceve un messaggio che indica che è stata interrotta.

    ![Lo stato del comando Interrompi AzureService][The status of the Stop-AzureService command]

2.  Per eliminare il servizio, chiamare il seguente cmdlet:

        Remove-AzureService

    Quando richiesto, immettere **Y** per eliminare il servizio.

    Eliminare il servizio può richiedere diversi minuti. Dopo aver eliminato il servizio viene visualizzato un messaggio che indica che il servizio è stato eliminato.

    ![Lo stato del comando Rimuovi AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Eliminare il servizio non eliminare l'account di archiviazione creati quando il servizio è stato pubblicato inizialmente e si continuerà per la fatturazione dello spazio di archiviazione utilizzato. Se nessun altro elemento Usa lo spazio di archiviazione, si desidera eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori Node].

<!-- URL List -->

[Confronto di siti Web, servizi Cloud e macchine virtuali di Azure]: ../app-service-web/choose-web-site-cloud-service-vm.md
[utilizzo di una semplice web app]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Powershell Azure]: ../powershell-install-configure.md
[Azure SDK per .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Connettere PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Panoramica della creazione di un servizio di hosting per Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centro per sviluppatori Node]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
