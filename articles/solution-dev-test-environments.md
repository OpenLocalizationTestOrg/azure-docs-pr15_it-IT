<properties
   pageTitle="Ambienti di sviluppo e test | Microsoft Azure"
   description="Informazioni su come usare i modelli di gestione di risorse Azure per rapidamente in modo coerente ed eliminare sviluppo e creare ambienti di prova."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Ambienti di sviluppo e test in Microsoft Azure

Applicazioni personalizzate vengono in genere distribuite a più ambienti di sviluppo e test prima della distribuzione di produzione. Quando si creano ambienti in locale, le risorse di elaborazione sono acquistate o assegnate a ogni ambiente per ogni applicazione. Gli ambienti spesso includono diversi computer fisici o virtuali con configurazioni specifiche distribuiti manualmente o mediante gli script di automazione complesse. Distribuzioni spesso ore e produrre configurazioni non coerente in ambienti.

## <a name="scenario"></a>Scenario: ##

Durante il provisioning ambienti di sviluppo e test in Microsoft Azure, pagando per le risorse utilizzate.  Questo articolo viene spiegato come rapidamente e in modo coerente è possibile creare, gestire ed eliminare sviluppo e testare ambienti tramite Gestione risorse Azure modelli e i file di parametro, come illustrato di seguito.

![Scenario:](./media/solution-dev-test-environments/scenario.png)

Tre ambienti di sviluppo e testing sono mostrati sopra.  Ogni applicazione web e risorse di database SQL, vengono specificate in un file modello.  I nomi dell'applicazione e database in ogni ambiente sono diversi e vengono specificati nei file di parametro univoco per ogni ambiente.  

Se non si ha familiarità con i concetti di Manager delle risorse di Azure, è consigliabile leggere l'articolo [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md) prima di leggere questo articolo.

Può essere necessario accedere tramite i passaggi descritti in questo articolo prima di tutto come indicato senza leggere uno degli articoli di riferimento per ottenere rapidamente l'esperienza di utilizzo dei modelli di gestione di risorse Azure. Dopo che si è stati una sola volta i passaggi, sarà possibile ottenere risposte alla maggior parte delle domande che si verifica la prima volta tramite facendo diversi tentativi ulteriormente con la procedura e, leggere gli articoli di riferimenti.

## <a name="plan-azure-resource-use"></a>Pianificare l'utilizzo delle risorse Azure
Dopo avere inserito una struttura di alto livello per l'applicazione, è possibile definire:

- Le risorse Azure conterrà l'applicazione. Si può creare l'applicazione e distribuire come un'App Web di Azure con un Database di SQL Azure.  Si può creare l'applicazione in macchine virtuali di PHP e MySQL o IIS e SQL Server o altri componenti. L'articolo [servizio App Azure, servizi Cloud e il confronto macchine virtuali]( app-service-web/choose-web-site-cloud-service-vm.md) consente di decidere quali Azure risorse è possibile utilizzare per l'applicazione.
- Quali requisiti del livello servizio, ad esempio disponibilità, sicurezza e scala in grado di soddisfare l'applicazione.

## <a name="download-an-existing-template"></a>Scaricare un modello esistente
Un modello di gestione di risorse Azure definisce tutte le risorse Azure che utilizza l'applicazione. Diversi modelli già esistono che è possibile distribuire direttamente nel portale di Azure o scaricare, modificare e salvare in un controllo del codice sorgente con il codice dell'applicazione.  Completare la procedura seguente per scaricare un modello esistente.

1. Sfoglia modelli esistenti nel repository GitHub [Modelli Guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates/) . Nell'elenco verrà visualizzata una cartella "[201-web-app-database sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Poiché molte applicazioni personalizzate includono un'applicazione web e database SQL, questo modello viene utilizzato come esempio nella parte restante di questo articolo consentono di imparare a usare i modelli. È lo scopo di questo articolo per spiegare completamente tutti gli elementi di questo modello consente di creare e configura, ma se si prevede di usarlo per creare ambienti effettivi all'interno dell'organizzazione, è consigliabile comprendere, leggere l'articolo di [provisioning un'app web con un Database SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Nota: questo articolo è stato scritto per la versione di dicembre 2015 del modello [201-web-app-database sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . I collegamenti sotto di esso dal modello e file dei parametri disponibili per la versione del modello.
2. Fare clic sul file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) nella cartella 201-web-app-database sql per visualizzarne il contenuto. Si tratta del file di modello Manager delle risorse di Azure. 
3. Nella modalità di visualizzazione, fare clic sul pulsante "[materie](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)". 
4. Con il mouse, selezionare il contenuto del file e salvarli nel computer come un file denominato "TestApp1 Template.json." 
5. Esaminare il contenuto del modello e tenere presente quanto segue:
 - Sezione **risorse** : in questa sezione consente di definire i tipi di risorse Azure creati da questo modello. Tra altri tipi di risorse, questo modello consente di creare risorse [Azure Web App](app-service-web/app-service-web-overview.md) e [Database di SQL Azure](sql-database/sql-database-technical-overview.md) . Se si preferisce eseguire e gestire web e SQL Server in macchine virtuali, è possibile utilizzare i modelli di "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" o "[luce app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", ma le istruzioni fornite in questo articolo sono basate sul modello [201-web-app-database sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - Sezione **parametri** : in questa sezione consente di definire i parametri che è possibile configurare con ogni risorsa. Alcuni dei parametri specificati nel modello hanno proprietà di "defaultValue", mentre altri non. Quando la distribuzione delle risorse Azure con un modello, è necessario fornire i valori per tutti i parametri che non dispongono di proprietà defaultValue specificata nel modello.  Se non vengono forniti valori dei parametri con il comando proprietà defaultValue, viene utilizzato il valore specificato per il parametro defaultValue nel modello.

Un modello consente di definire le risorse Azure vengono create e i parametri può essere configurato con ogni risorsa. È possibile altre informazioni sui modelli e procedure per la progettazione personalizzata, leggere l'articolo [procedure consigliate per la progettazione di modelli di gestione risorse di Azure](best-practices-resource-manager-design-templates.md) .

## <a name="download-and-customize-an-existing-parameter-file"></a>Scaricare e personalizzare un file di parametro esistente

Se si consiglia la *stessa* Azure risorse create in ogni ambiente, probabilmente si la configurazione delle risorse può essere *diverso* in ogni ambiente.  Verrà visualizzata nel punto in cui possono essere file dei parametri. Creare file di parametro che contengono valori univoci presenti in ogni ambiente completando la procedura seguente.   

1. Visualizzare il contenuto del file [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) nella cartella 201-web-app-database sql. Si tratta del file di parametro per il file del modello salvato nella sezione precedente. 
2. Nella modalità di visualizzazione, fare clic sul pulsante "[materie](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Con il mouse, selezionare il contenuto del file e salvarli in tre file distinti nel computer in uso con i nomi seguenti:
 - Development.json di parametri TestApp1
 - Test.json di parametri TestApp1
 - TestApp1-parametri-Pre-Production.json

3. Utilizzando qualsiasi editor di testo o JSON, modificare il file di parametro ambiente di sviluppo che è stato creato nel passaggio 3, la sostituzione di valori nell'elenco a destra dei valori di parametro nel file con i *valori* nell'elenco a destra dei **parametri di** seguito: 
 - **NomeSito**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Centrale USA*
 - **nomeserver**: *testapp1devsrv*
 - **serverLocation**: *Centrale USA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *sostituire con la password*
 - **databaseName**: *testapp1devdb*

4. Utilizzando qualsiasi editor di testo o JSON, modificare il file di parametro ambiente di Test è stato creato nel passaggio 3, sostituire i valori nell'elenco a destra dei valori di parametro nel file con i *valori* nell'elenco a destra dei **parametri** seguenti:
 - **NomeSito**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Centrale USA*
 - **nomeserver**: *testapp1testsrv*
 - **serverLocation**: *Centrale USA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *sostituire con la password*
 - **databaseName**: *testapp1testdb*

5. Utilizzando qualsiasi editor di testo o JSON, modificare il file di parametro pre-produzione creata nel passaggio 3.  Sostituire l'intero contenuto del file con quello di seguito:

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

Nel file parametri pre-produzione, i parametri **sku** e **requestedServiceObjectiveName** sono stati *aggiunti*, mentre non sono state aggiunte nei file di parametri di sviluppo e Test. Perché sono presenti valori predefiniti specificati per questi parametri nel modello e in ambienti di sviluppo e Test, vengono utilizzati i valori predefiniti, ma in pre-produzione vengono utilizzati i valori di ambiente non predefiniti per i parametri.

Il motivo per i valori predefiniti non vengono utilizzati per questi parametri nell'ambiente di pre-produzione è per verificare i valori per i parametri che può essere preferibile per l'ambiente di produzione in modo che possono anche essere verificate.  Questi parametri tutte le relative alla Azure [piani di hosting Web App](https://azure.microsoft.com/pricing/details/app-service/), o **sku** e [Database SQL](https://azure.microsoft.com/pricing/details/sql-database/)di Azure o **requestedServiceObjectiveName** utilizzati dall'applicazione.  SKU diverso e i nomi degli obiettivi di servizio hanno costi diversi e funzionalità e metrica di livello diversi servizi di supporto.

La tabella seguente elenca i valori predefiniti per i parametri specificati nel modello e i valori che vengono usati i valori predefiniti nel file di parametri pre-produzione.

| Parametro | Valore predefinito | Valore del parametro file |
|---|---|---|
| **SKU** | Libera | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Creare ambienti
Tutte le risorse Azure devono essere create all'interno di un [Gruppo di risorse Azure](azure-resource-manager/resource-group-overview.md). Gruppi di risorse consentono di raggruppare le risorse Azure in modo che possano essere gestiti collettivamente.  In modo che alcune persone specifiche all'interno dell'organizzazione possono creare, modificare, eliminare o visualizzarli, le risorse al loro interno, è possibile assegnare [autorizzazioni](./active-directory/role-based-access-built-in-roles.md) a gruppi di risorse.  Avvisi e informazioni di fatturazione per le risorse nel gruppo di risorse possono essere visualizzati nel [Portale di Azure](https://portal.azure.com). Gruppi di risorse vengono creati in [un'area](https://azure.microsoft.com/regions/)di Azure.  In questo articolo, tutte le risorse vengono create nell'area centrale USA. Quando si avvia la creazione di ambienti effettivi, si verrà scegliere l'area più affine alle proprie esigenze. 

Creare gruppi di risorse per ogni ambiente utilizzando uno dei metodi descritti di seguito.  Tutti i metodi di ottenere lo stesso risultato.

###<a name="azure-command-line-interface-cli"></a>Azure interfaccia riga di comando (CLI)

Verificare di avere il CLI [installato](xplat-cli-install.md) in uno Windows, OS X o Linux computer e che si è [connessi](xplat-cli-connect.md) all' [account Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) (denominato anche un account aziendale o dell'istituto di istruzione) all'abbonamento Azure. Dalla riga di comando CLI digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo.

    azure group create "TestApp1-Development" "Central US"

Il comando restituirà le operazioni seguenti caso di esito positivo:

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Per creare il gruppo di risorse per l'ambiente di testing, digitare il comando seguente:

    azure group create "TestApp1-Test" "Central US"

Per creare il gruppo di risorse per l'ambiente di pre-produzione, digitare il comando seguente:

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Assicurarsi di avere Azure PowerShell 1.01 o versioni successive installato in un computer Windows e connessi all' [account Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) (denominato anche un account aziendale o dell'istituto di istruzione) al proprio abbonamento come indicato nell'articolo [come installare e configurare Azure PowerShell](powershell-install-configure.md) . Da un prompt dei comandi di PowerShell, digitare il comando seguente per creare il gruppo di risorse per l'ambiente di sviluppo.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Il comando restituirà le operazioni seguenti caso di esito positivo:

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Per creare il gruppo di risorse per l'ambiente di testing, digitare il comando seguente:

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Per creare il gruppo di risorse per l'ambiente di pre-produzione, digitare il comando seguente:

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con un account [Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) (denominato anche un aziendale o dell'istituto di istruzione). Fare clic su New--> Gestione--> gruppo di risorse e immettere "TestApp1 sviluppo" nella casella Nome gruppo risorse, selezionare l'abbonamento e selezionare "Central US" nella casella Percorso gruppo risorse come illustrato nella figura seguente.
   ![Portale](./media/solution-dev-test-environments/rgcreate.png)
2. Fare clic sul pulsante Crea per creare il gruppo di risorse.
3. Fare clic su Sfoglia, scorrere verso il basso l'elenco di gruppi di risorse e fare clic su gruppi di risorse, come illustrato di seguito.
   ![Portale](./media/solution-dev-test-environments/rgbrowse.png) 
4. Dopo aver fatto clic su gruppi di risorse e il gruppi risorse verrà visualizzato con il nuovo gruppo di risorse.
   ![Portale](./media/solution-dev-test-environments/rgview.png)
5. Creare il Test TestApp1 e risorsa TestApp1-Pre-produzione gruppi esattamente come che è stato creato il gruppo di risorse TestApp1 sviluppo sopra.

##<a name="deploy-resources-to-environments"></a>Distribuire le risorse in ambienti

Distribuire risorse Azure al gruppo di risorse per ogni ambiente utilizzando il file del modello per la soluzione e i file di parametro per ogni ambiente utilizzando uno dei metodi descritti di seguito.  Entrambi i metodi modo da ottenere lo stesso risultato.

###<a name="azure-command-line-interface-cli"></a>Azure interfaccia riga di comando (CLI)

Dalla riga di comando CLI digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di sviluppo, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Dopo aver visualizzato un messaggio "In attesa per la distribuzione completare" per alcuni minuti, il comando restituirà le operazioni seguenti caso di esito positivo:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Se il comando non è riuscita, risolvere i messaggi di errore e riprovare.  Problemi comuni usano valori dei parametri che non sono compatibili con le limitazioni dei nomi delle risorse Azure. Sono disponibili per altri suggerimenti sulla risoluzione dei problemi nell'articolo [distribuzioni di gruppo di risorse di risoluzione dei problemi in Azure](./resource-manager-troubleshoot-deployments-cli.md) .

Dalla riga di comando CLI digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di testing, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Dalla riga di comando CLI digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di produzione precedenti, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

Da un prompt dei comandi PowerShell di Azure (1.01 o versione successiva), digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di sviluppo, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Dopo aver esaminato un cursore lampeggiante per alcuni minuti, il comando restituirà le operazioni seguenti caso di esito positivo:

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Se il comando non è riuscita, risolvere i messaggi di errore e riprovare.  Problemi comuni usano valori dei parametri che non sono compatibili con le limitazioni dei nomi delle risorse Azure. Sono disponibili per altri suggerimenti sulla risoluzione dei problemi nell'articolo [distribuzioni di gruppo di risorse di risoluzione dei problemi in Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  Da un prompt dei comandi di PowerShell, digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di testing, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Da un prompt dei comandi di PowerShell, digitare il comando seguente per distribuire le risorse al gruppo di risorse creato per l'ambiente di produzione precedenti, sostituendo [percorso] con il percorso di file che è stato salvato in precedenza.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

I file di modello e un parametro possono essere gestita con il codice dell'applicazione e versione in un controllo del codice sorgente.  È anche possibile salvare i comandi sopra per file di script e salvarli con il codice.

> [AZURE.NOTE] È possibile distribuire il modello in Azure direttamente facendo clic sul pulsante "Distribuzione di Azure" nell'articolo di [provisioning un'App Web con un Database SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Questa opzione potrebbe risultare utili per informazioni sui modelli, ma ciò in modo non consentono di modificare, versione e salvare i valori del modello e del parametro con il codice dell'applicazione in modo che ulteriori dettagli su questo metodo non viene descritto in questo articolo.

## <a name="maintain-environments"></a>Creare un ambiente
Nel corso dello sviluppo, configurazione delle risorse di Azure in ambienti diversi può essere modificata in modo incoerente intenzionalmente o accidentalmente.  In questo modo la risoluzione dei problemi non necessari e risoluzione dei problemi durante le fasi di sviluppo di applicazioni.

1. Modificare gli ambienti, aprire il [portale di Azure](https://portal.azure.com). 
2. Accedere al suo interno con lo stesso account utilizzato per completare la procedura indicata sopra. 
3. Come illustrato nell'immagine seguente, fare clic su Sfoglia--> gruppi di risorse (potrebbe essere necessario scorrere verso il basso per visualizzare gruppi di risorse).
   ![Portale](./media/solution-dev-test-environments/rgbrowse.png)
4. Dopo aver fatto clic su gruppi di risorse nella figura riportata sopra, vengono visualizzati e il gruppi delle risorse e i gruppi di tre risorse creato nel passaggio precedente, come illustrato nella figura seguente. Fare clic sul gruppo di risorse TestApp1 sviluppo e verrà visualizzato e il che elenca le risorse create dal modello della distribuzione del gruppo di risorse TestApp1 sviluppo effettuate in un passaggio precedente.  Eliminare la risorsa TestApp1DevApp Web App facendo clic su TestApp1DevApp in e il gruppo delle risorse TestApp1 sviluppo e quindi fare clic su Elimina in e il app Web TestApp1DevApp.
   ![Portale](./media/solution-dev-test-environments/portal2.png)
5. Fare clic su "Sì" quando il portale chiesto se si è sicuri che si desidera eliminare la risorsa. Parentesi di chiusura e il gruppo delle risorse per lo sviluppo di TestApp1 e riaprire verrà ora illustrato senza l'applicazione Web che appena eliminato.  Il contenuto del gruppo di risorse è ora diverso da quella devono essere. È possibile sperimentare ulteriormente eliminando più risorse da più gruppi di risorse o anche la modifica di impostazioni di configurazione per alcune delle risorse. Invece di usare il portale di Azure per eliminare una risorsa da un gruppo di risorse, è possibile utilizzare il comando PowerShell [AzureResource Rimuovi](https://msdn.microsoft.com/library/azure/dn757676.aspx) o o il comando "Elimina risorsa azure" da CLI per eseguire la stessa attività.
6. Per ottenere tutte le risorse e configurazione che deve per essere in gruppi di risorse allo stato che devono essere nel, ridistribuire gli ambienti al gruppo di risorse utilizzando gli stessi comandi usato nell'area [risorse di distribuzione per gli ambienti](#deploy-resources-to-environments) , ma sostituire "Deployment1" con "Deployment2".
7.  Come illustrato nella sezione Riepilogo della stessa e TestApp1 sviluppo dell'immagine visualizzata nel passaggio 4, si noterà che il Web app che è stato eliminato nel portale di nel passaggio precedente esista nuovamente, mentre altre risorse non si è scelto di eliminare. Se si modifica la configurazione di tutte le risorse, è inoltre possibile verificare che è stato impostato nuovamente i valori nei file di parametro troppo. Uno dei vantaggi della distribuzione gli ambienti con i modelli di gestione di risorse Azure è che può essere nuovamente facilmente gli ambienti uno stato noti in qualsiasi momento.
8. Se si fa clic sul testo in "Ultima distribuzione" nella figura seguente, si noterà blade che mostra la cronologia di distribuzione per il gruppo di risorse.  Dal momento che il nome "Deployment1" per la distribuzione e "Deployment2" prima per la distribuzione seconda, saranno disponibili due voci.  Fare clic su una distribuzione visualizzerà una pala che mostra i risultati per ogni distribuzione.
  ![Portale](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Eliminare ambienti
Dopo aver terminato utilizza un ambiente, verrà desidera eliminarlo in modo che non implicano costi di utilizzo per le risorse Azure che non si usa.  Eliminazione di ambienti è persino più semplice che vengono creati.  Nei passaggi precedenti, sono stati creati gruppi di risorse Azure singoli per ogni ambiente e quindi risorse distribuite nei gruppi di risorse. 

Eliminare gli ambienti utilizzando uno dei metodi descritti di seguito.  Tutti i metodi di ottenere lo stesso risultato.

### <a name="azure-cli"></a>CLI Azure

Da un prompt CLI, digitare quanto segue:

    azure group delete "TestApp1-Development"

Quando richiesto, immettere y e quindi premere INVIO per rimuovere l'ambiente di sviluppo e tutte le relative risorse. Dopo alcuni minuti, il comando restituirà le operazioni seguenti:

    info:    group delete command OK

Da un prompt CLI, digitare quanto segue per eliminare gli ambienti rimanenti:

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

Da un prompt dei comandi PowerShell di Azure (1.01 o versione successiva), digitare il comando seguente per eliminare il gruppo di risorse e il relativo contenuto.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Quando viene richiesto se si è sicuri che si desidera rimuovere il gruppo di risorse, immettere y, seguito dal tasto INVIO.

Digitare le operazioni seguenti per eliminare gli ambienti rimanenti:

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Portale di Azure

1. Nel portale di Azure, passare a gruppi di risorse come descritto nei passaggi precedenti. 
2. Selezionare il gruppo di risorse TestApp1 sviluppo e quindi fare clic su Elimina e il gruppo delle risorse per lo sviluppo di TestApp1. Verrà visualizzata una nuova pala. Immettere il nome del gruppo di risorse e fare clic sul pulsante Elimina.
![Portale](./media/solution-dev-test-environments/rgdelete.png)
3. Eliminare il Test TestApp1 e risorsa TestApp1-Pre-produzione gruppi nello stesso modo che è stato eliminato il gruppo di risorse TestApp1 sviluppo.

Indipendentemente dal metodo utilizzato, i gruppi di risorse e tutte le risorse che sono contenute non saranno più presenti e non verrà non è più sostenere le spese di fatturazione per le risorse.  

Per ridurre al minimo l'Azure risorsa utilizzo le spese sostenute durante lo sviluppo di applicazioni è possibile utilizzare [l'automazione Azure](automation/automation-intro.md) per pianificare processi:

- Arrestare macchine virtuali alla fine di ogni giorno e riavviare loro all'inizio di ogni giornata.
- Eliminare interi ambienti alla fine di ogni giorno e ricrearle all'inizio di ogni giornata.
 
Ora che si sono verificati quanto sia semplice creare, gestire ed eliminare sviluppo e testare ambienti, è possibile ottenere ulteriori informazioni su ciò che è appena si è verificato ulteriormente diversi tentativi con la procedura indicata sopra e lettura indicazioni contenute in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Delega del controllo amministrativo](./active-directory/role-based-access-control-configure.md) per risorse diverse in ogni ambiente mediante l'assegnazione di gruppi di Microsoft Azure Active Directory o utenti a ruoli specifici che hanno la possibilità di eseguire un sottoinsieme delle operazioni sulle risorse Azure.
- [Assegnare tag](resource-group-using-tags.md) ai gruppi di risorse per ogni ambiente e/o le singole risorse. È possibile aggiungere un tag "Ambiente" ai gruppi di risorse e impostarne il valore in modo che corrispondano ai nomi ambiente. Tag può essere particolarmente utili quando si desidera organizzare le risorse per la fatturazione o la gestione.
- Monitorare gli avvisi e fatturazione per gruppo di risorse nel [portale di Azure](https://portal.azure.com).
