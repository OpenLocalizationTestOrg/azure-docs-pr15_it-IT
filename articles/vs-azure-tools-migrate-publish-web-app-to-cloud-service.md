<properties
   pageTitle="Come eseguire la migrazione e pubblicare un'applicazione Web in un servizio Cloud Azure da Visual Studio | Microsoft Azure"
   description="Informazioni su come eseguire la migrazione e pubblicare l'applicazione web in un servizio cloud Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Procedura: eseguire la migrazione e pubblicare un'applicazione Web in un servizio Cloud Azure da Visual Studio

Per sfruttare i vantaggi offerti dai servizi hosting e scalabilità di Azure, è consigliabile eseguire la migrazione e pubblicare l'applicazione web in un servizio cloud Azure. È possibile eseguire un'applicazione web Azure modifiche minime all'applicazione esistente.

>[AZURE.NOTE] In questo argomento è sulla distribuzione ai servizi cloud, non ai siti web. Per informazioni sulla distribuzione ai siti web, vedere [distribuire un'app web nel servizio App Azure](./app-service-web/web-sites-deploy.md).

Per un elenco di modelli specifici che sono supportati per c# e Visual Basic, vedere la sezione **Modelli di progetto supportati** più avanti in questo argomento.

Attivare l'applicazione web per Azure da Visual Studio. L'illustrazione seguente mostra i passaggi principali per pubblicare un'applicazione web esistente mediante l'aggiunta di un progetto Azure da utilizzare per la distribuzione. Questo processo viene aggiunto un progetto Azure con il ruolo web necessari alla soluzione. In base al tipo di progetto web che si dispone, le proprietà del progetto per assembly vengono aggiornate anche se il pacchetto di servizio richiede assembly aggiuntivi per la distribuzione.

![Pubblicare un'applicazione Web in Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] La **conversione**, comando **Converti in un progetto di servizi Cloud Azure** viene visualizzata solo per il progetto web della soluzione. Ad esempio, il comando non è disponibile per un progetto Silverlight della soluzione.
Quando si crea un pacchetto di servizio o pubblicare l'applicazione in Azure, possono verificarsi errori o avvisi. Questi errori e avvisi consentono di risolvere i problemi prima di distribuire in Azure. Ad esempio, si potrebbe ricevere un avviso relativo a un assembly mancante. Per ulteriori informazioni su come gestire gli avvisi come errori, vedere [configurare un progetto di servizio Cloud di Azure con Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se si genera l'applicazione, viene eseguito in locale tramite l'emulatore di calcolo o pubblicare il progetto in Azure, si può vedere l'errore seguente nella finestra **Elenco errori** : **il percorso, nome del file o entrambi sono troppo lunghe**. Questo errore si verifica perché la lunghezza del nome del progetto di Azure completo è troppo lunga. La lunghezza del nome del progetto, incluso il percorso completo non può essere più 146 caratteri. Ad esempio, si tratta del nome di progetto completo incluso il percorso di file per un progetto Azure creato per un'applicazione Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Potrebbe essere necessario spostare la soluzione in un'altra directory che contiene un percorso più breve per ridurre la lunghezza del nome del progetto completo.

Per eseguire la migrazione e pubblicare un'applicazione web in Azure da Visual Studio, seguire questa procedura.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Attivare un'applicazione Web per la distribuzione di Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Per attivare un'applicazione web per la distribuzione di Azure

1. Per attivare l'applicazione web per la distribuzione di Azure, aprire il menu di scelta rapida per un progetto web della soluzione e scegliere Aggiungi progetto di distribuzione di Azure.

    Si verificano le seguenti operazioni:

    - Un progetto di Azure denominato `<name of the web project>.Azure` aggiunto alla soluzione per l'applicazione.

    - Un ruolo web per il progetto web viene aggiunta al progetto Azure.

    - La proprietà **Copia locale** è impostata su true per qualsiasi assembly necessari per MVC MVC 2 MVC 3, 4 e applicazioni aziendali Silverlight. Consente di aggiungere questi assembly per il pacchetto del servizio utilizzato per la distribuzione.

  >[AZURE.IMPORTANT] Se si dispone di altri assembly o file necessari per l'applicazione web, è necessario impostare manualmente le proprietà dei file. Per informazioni su come impostare queste proprietà, vedere la sezione **Includere file del pacchetto di servizio** più avanti in questo articolo.

  >[AZURE.NOTE] Se esiste già un ruolo web per un progetto web specifici in un progetto Azure della soluzione, **conversione**, **convertire in un progetto di servizi Cloud Azure** non disponibile nel menu di scelta rapida per il progetto web.

  Se si dispone di più progetti web nell'applicazione web e si desidera creare ruoli web per ogni progetto web, è necessario eseguire la procedura descritta in questa procedura per ogni progetto web. Consente di creare progetti Azure distinti per ogni ruolo web. Ogni progetto web può essere pubblicato separatamente. In alternativa, è possibile aggiungere manualmente un altro ruolo web a un progetto di Azure esistente nell'applicazione web. A tale scopo, aprire il menu di scelta rapida per la cartella di **ruoli** di un progetto di Azure, scegliere **Aggiungi**, quindi **Progetto ruolo Web soluzione**, scegliere il progetto da aggiungere come un ruolo web e quindi fare clic su **OK** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Utilizzare un Database SQL Azure per un'applicazione

Se si dispone di una stringa di connessione per l'applicazione web che utilizza un database SQL Server in locale, è necessario modificare questa stringa di connessione da utilizzare un'istanza di Database SQL che ospita Azure invece.

>[AZURE.IMPORTANT] L'abbonamento deve consentono di utilizzare Database SQL. Se si accede a abbonamento dal [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), è possibile stabilire a quali servizi sono disponibili l'abbonamento. Le istruzioni seguenti riguardano rilasciato [Azure portale classica](http://go.microsoft.com/fwlink/?LinkID=213885). Se si utilizza il [portale di Azure](http://portal.microsoft.com), andare alla procedura successiva.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Usare un'istanza del Database di SQL nel proprio ruolo web per la stringa di connessione

1. Per creare un'istanza di Database SQL [Azure portale classico](http://go.microsoft.com/fwlink/?LinkID=213885), seguire la procedura descritta nell'articolo seguente: [creare un Database di SQL Server](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Quando Configura le regole del firewall per l'istanza di Database SQL, è necessario selezionare la casella di controllo **Consenti altri servizi di Azure accedere a questo server** .

1. Per creare un'istanza di SQL Database da utilizzare per la stringa di connessione, seguire i passaggi indicati nella sezione successiva, vedere l'articolo seguente: [creare un Database SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Per copiare la stringa di connessione ADO.NET da utilizzare per la stringa di connessione, eseguire la procedura seguente nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Fare clic sul pulsante **Database** e quindi aprire il file per l'abbonamento a cui è utilizzato per creare l'istanza di Database SQL.

  1. Per visualizzare le istanze di Database SQL di disponibili, scegliere il nodo di **Database SQL** .

  1. Per visualizzare le proprietà per il database, scegliere il database. Verrà visualizzata la visualizzazione **delle proprietà** .

      >[AZURE.NOTE] Se la visualizzazione **delle proprietà** non è disponibile, potrebbe essere necessario aprire utilizzando il divisore.

  1. Per visualizzare le stringhe di connessione, scegliere i puntini di sospensione (...) accanto a Visualizza.

    Verrà visualizzata la finestra di dialogo **Stringhe di connessione** .

  1. Per copiare la stringa di connessione ADO.NET, evidenziare il testo e scegliere i tasti Ctrl + C.

  1. Per chiudere la finestra di dialogo, scegliere il pulsante **Chiudi** .

1. Per sostituire la stringa di connessione nel file config per utilizzare questa istanza di Database SQL, aprire il file config, evidenziare la stringa di connessione esistente e quindi scegliere i tasti Ctrl + V. La stringa di connessione ADO.NET per l'istanza di Database SQL di sostituisce la stringa di connessione esistente.

1. È necessario aggiungere anche il parametro `MultipleActiveResultSets=True` alla stringa di connessione. La stringa di connessione deve avere il formato seguente:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Facoltativo) Un metodo alternativo per modificare la stringa di connessione direttamente nel file config consiste nell'aggiungere una sezione in un file di trasformazione config, a seconda della configurazione di compilazione utilizzati per creare un pacchetto di servizio. Aprire il file Web.Debug.Config o il file Web.Release.Config. Aggiungere la sezione seguente in questo file:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Salvare il file che è stata modificata e ripubblicare l'applicazione.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Usare un'istanza di Database SQL tramite il portale classico Azure

1. Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), scegliere il nodo di database SQL.

  - Se viene visualizzata l'istanza di Database SQL in cui si desidera utilizzare, scegliere di aprirlo.

  - Se è stata creata tutte le istanze, selezionare il collegamento appropriato e quindi si crea un'istanza.

1. Dopo che si apre o si crea un'istanza del database, selezionare il collegamento di **Stringhe di connessione** .

1. Nella parte inferiore della pagina, selezionare il collegamento per configurare le impostazioni di firewall e accettare i valori predefiniti o configurare i valori desiderati.

1. Copiare la stringa di connessione ADO.NET, incollarlo in un file config sopra la stringa di connessione precedente per il database locale e assicurarsi di aggiungere `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Pubblicare un'applicazione Web in Azure

### <a name="to-publish-a-web-application-to-azure"></a>Per pubblicare un'applicazione Web in Azure

1. Per testare l'applicazione di sviluppo locale ambiente usando il Azure calcola emulatore, apre il menu di scelta rapida per il progetto Azure per il ruolo web e sceglie **Imposta come progetto di avvio**. Scegliere **Debug** **Avvia debug** (tastiera: **F5**).

    Verrà aperta la finestra di dialogo **avviare l'ambiente di debug Azure** e avvio dell'applicazione nel browser. Per informazioni dettagliate su come avviare ogni tipo di applicazione web nell'emulatore di calcolo, vedere la tabella in questa sezione.

1. Per configurare i servizi dell'applicazione per la pubblicazione in Azure, è necessario disporre di un account Microsoft e un abbonamento Azure. Usare i passaggi descritti nell'argomento seguente per configurare il provider di servizi: [preparare l'ambiente per pubblicare o distribuire un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Per pubblicare l'applicazione web in Azure, aprire il menu di scelta rapida per il progetto web e scegliere **pubblica in Azure**.

    Verrà aperta la finestra di dialogo **Pubblica applicazione Azure** e Visual Studio avvia il processo di distribuzione. Per ulteriori informazioni su come pubblicare l'applicazione, vedere la sezione **pubblicare un'applicazione di Azure da Visual Studio** nella [pubblicazione di un servizio Cloud usando gli strumenti di Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] È anche possibile pubblicare l'applicazione web dal progetto Azure. A tale scopo, aprire il menu di scelta rapida per il progetto Azure e scegliere **pubblica**.

1. Per visualizzare l'avanzamento della distribuzione, è possibile visualizzare la finestra **Registro attività Azure** . Questo registro viene visualizzato automaticamente all'avvio del processo di distribuzione. È possibile espandere la voce nel log delle attività per visualizzare informazioni dettagliate, come illustrato nella figura seguente:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Facoltativo) Per annullare il processo di distribuzione, aprire il menu di scelta rapida per la voce nel log delle attività e scegliere **Annulla e rimuovere**. Questa interrompe il processo di distribuzione ed elimina l'ambiente di distribuzione comuni.

    >[AZURE.NOTE] Per rimuovere l'ambiente di distribuzione dopo che è stato distribuito, è necessario utilizzare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facoltativo) Dopo avere iniziato istanze del ruolo, Visual Studio viene automaticamente l'ambiente di distribuzione nel nodo **Azure calcolare** nel **Cloud Explorer** o **Esplora Server**. Da qui è possibile visualizzare lo stato delle istanze dei singoli ruoli.

    L'illustrazione seguente mostra le istanze di ruolo in **Esplora Server** mentre sono ancora in stato inizializzazione:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Per accedere all'applicazione dopo la distribuzione, scegliere la freccia accanto alla distribuzione di quando lo stato **completato** viene visualizzata nel **registro attività Azure**. Consente di visualizzare l'URL per l'applicazione web in Azure. Vedere la tabella seguente per informazioni dettagliate su come avviare un tipo specifico di applicazione web da Azure.

    Nella tabella seguente elenca i dettagli sull'avviare le applicazioni web specifico da Azure o esecuzione o il debug di un'applicazione web localmente utilizzando emulatore di calcolo Azure:

  	|Tipo di applicazione Web|Esecuzione/Debug localmente utilizzando l'emulatore di calcolo|In esecuzione in Azure|
  	|---|---|---|
  	|Applicazione Web ASP.NET|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **distribuzione** per il **registro attività Azure** caricare la pagina iniziale nel browser.|
  	|Applicazione di Web di Asp.net 2|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **distribuzione** per il **registro attività Azure** caricare la pagina iniziale nel browser.|
  	|Applicazione di Web di Asp.net 3|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **distribuzione** per il **registro attività Azure** caricare la pagina iniziale nel browser.|
  	|Applicazione di Web di Asp.net 4|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|Scegliere il collegamento ipertestuale URL visualizzato nella scheda **distribuzione** per il **registro attività Azure** caricare la pagina iniziale nel browser.|
  	|Applicazione Web ASP.NET vuota|È necessario aggiungere una pagina aspx dell'applicazione che si imposta come pagina iniziale per il progetto web. Scegliere barra dei menu **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|Se si dispone di una pagina aspx predefinita dell'applicazione, scegliere il collegamento ipertestuale URL visualizzato nella scheda **distribuzione** per il **registro attività Azure** e tale pagina viene caricata nel browser. Se si dispone di una pagina aspx diverso, è necessario passare a questa pagina specifica per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|
  	|Applicazione Silverlight|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario passare alla pagina specifica per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|
  	|Applicazione aziendale Silverlight|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario passare alla pagina specifica per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|
  	|Applicazione di esplorazione Silverlight|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario passare alla pagina specifica per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|
  	|Applicazione del servizio WCF|È necessario impostare il file con estensione svc come pagina iniziale per il progetto di servizio WCF. Scegliere barra dei menu **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario individuare il file svc per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of service file>.svc`|
  	|Applicazione del servizio WCF del flusso di lavoro|È necessario impostare il file con estensione svc come pagina iniziale per il progetto di servizio WCF. Scegliere barra dei menu **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario individuare il file svc per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of service file>.svc`|
  	|Entità dinamiche ASP.NET|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario aggiornare la stringa di connessione (vedere la sezione successiva). Inoltre, è necessario passare alla pagina specifica per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|
  	|ASP.NET Dynamic dati Linq to SQL|Sulla barra dei menu scegliere **Debug** **Avvia debug** (tastiera: scegliere il tasto **F5** .).|È necessario seguire la procedura descritta in questa procedura: utilizzare un database SQL Azure per l'applicazione (vedere la sezione precedente in questo argomento). Inoltre, è necessario passare alla pagina specifica per l'applicazione per l'url utilizzando il formato seguente:`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Aggiornare una stringa di connessione per entità dinamiche ASP.NET

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Per aggiornare una stringa di connessione per entità dinamiche ASP.NET

1. Per creare un database di SQL Azure che può essere usato per un'applicazione web ASP.NET entità dinamiche, seguire i passaggi della procedura **usare un database di SQL Azure per l'applicazione** in precedenza in questo argomento.

1. Aggiungere le tabelle e campi che è necessario per il database dal [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Stringa di connessione per questo tipo di applicazione ha il seguente formato in config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Aggiornare il valore di *connectionString* con la stringa di connessione ADO.NET per il database di SQL Azure come segue:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. Per salvare il file config con le modifiche apportate alla stringa di connessione, sulla barra dei menu scegliere **File**, **Salva config**.

## <a name="supported-project-templates"></a>Modelli di progetto supportati

Per pubblicare un'applicazione web in Azure, l'applicazione deve utilizzare uno dei modelli di progetto per c# o Visual Basic elencate nella tabella seguente.

|Gruppo di modelli di progetto|Modello di progetto|
|---|---|
|Web|Applicazione Web ASP.NET|
|Web|Applicazione di Web di Asp.net 2|
|Web|Applicazione di Web di Asp.net 3|
|Web|Applicazione Web ASP.NET MVC4|
|Web|Applicazione Web ASP.NET vuota|
|Web|Applicazione di Web vuoto di Asp.net 2|
|Web|Applicazione Web ASP.NET Dynamic entità di dati|
|Web|ASP.NET Dynamic dati Linq all'applicazione Web SQL|
|Silverlight|Applicazione Silverlight|
|Silverlight|Applicazione aziendale Silverlight|
|Silverlight|Applicazione di esplorazione Silverlight|
|WCF|Applicazione del servizio WCF|
|WCF|Applicazione del servizio WCF del flusso di lavoro|
|Flusso di lavoro|Applicazione del servizio WCF del flusso di lavoro|

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla pubblicazione, vedere [preparare l'ambiente per pubblica o distribuire un'applicazione di Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Vedere anche [Impostazione le credenziali di autenticazione denominato](vs-azure-tools-setting-up-named-authentication-credentials.md).
