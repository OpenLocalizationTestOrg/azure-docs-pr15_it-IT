<properties
    pageTitle="Usare i database MySQL come PaaS nella pila Azure | Microsoft Azure"
    description="Comprendere le azioni rapide per distribuire il provider di risorse MySQL e fornire MySQL come servizio su Azure Stack."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Connetti a database MySQL come PaaS nella pila di Azure

> [AZURE.NOTE] Le informazioni seguenti si applicano solo alle distribuzioni TP1 Stack Azure.

È possibile distribuire un provider di risorse MySQL nella pila di Azure. Dopo aver distribuito il provider di risorse, è possibile creare server MySQL e database di modelli di distribuzione di gestione di risorse Azure e fornire database MySQL come servizio. Database MySQL, che sono comuni sui siti web, supportano molte piattaforme di sito Web. Dopo aver distribuito il provider di risorse, è possibile creare siti Web WordPress da piattaforma Azure Web Apps come un componente aggiuntivo modello PaaS per Stack di Azure.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Azioni rapide per distribuire il provider di risorse
Utilizzare questa procedura se si ha già familiarità con Stack di Azure. Se si desiderano ulteriori dettagli, seguire i collegamenti in ogni sezione o passare direttamente alle [Distribuisci la scheda di Provider di risorse Database MySQL nel Azure Stack prova](azure-stack-mysql-rp-deploy-long.md).

1.  Assicurarsi che non è [completato tutti i passaggi di configurazione](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) prima di distribuire il provider di risorse:

    - .NET framework 3.5 è già impostato in immagine di Windows Server base. (Se è stato scaricato bit dello Stack di Azure dopo febbraio 23, 2016, è possibile ignorare questo passaggio.)
    - [È installata una versione di Azure PowerShell compatibile con Stack di Azure](http://aka.ms/azStackPsh).
    - Impostazioni di sicurezza di Internet Explorer in ClientVM, [Internet Explorer sicurezza avanzata è disattivata e i cookie sono abilitati](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Scaricare il file di file binari provider risorsa MySQL](http://aka.ms/masmysqlrp) ed estrarre i file in ClientVM in pila Azure prova (prova).

3. [Eseguire bootstrap.cmd e gli script](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Una serie di script raggruppati in base a due schede principali viene aperta nell'ambiente di Scripting integrato PowerShell (ISE). Eseguire tutti gli script caricati in sequenza da sinistra a destra in ogni scheda.

    1. Esegui script nella scheda della **Preparazione** da sinistra a destra per:

        - Creare un certificato con caratteri jolly per proteggere le comunicazioni tra il provider di risorse e gestione di risorse di Azure.
        - Accettare le condizioni del contratto di licenza MySQL e scaricare i file binari MySQL.
        - Caricare i certificati e tutti gli altri elementi a un account di archiviazione di Azure Stack.
        - Pubblicare pacchetti raccolta in modo che è possibile distribuire risorse MySQL attraverso la raccolta.

        > [AZURE.IMPORTANT] Se uno degli script senza alcun motivo evidente dopo l'invio del tenant di Azure Active Directory, le impostazioni di protezione potrebbero essere bloccato una DLL necessaria per la distribuzione per l'esecuzione. Per risolvere il problema, cercare Microsoft.AzureStack.Deployment.Telemetry.Dll nella cartella del provider di risorse, destra fare clic su essa, fare clic su **proprietà**e quindi selezionare **Sblocca** nella scheda **Generale** .

    2. Esegui script nella scheda **Distribuisci** da sinistra a destra per:

        - [Distribuire una macchine ()](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) contenenti sia il provider di risorse, MySQL Server e database che creerà. Questo script fa riferimento a un file di parametri JSON, è necessario aggiornare con alcuni valori prima di eseguire lo script.
        - [Registrare un record DNS locale](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) che verranno mappate al provider di risorse macchine Virtuali.
        - [Registrare il provider di risorse](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) con locale Manager delle risorse Azure.

        > [AZURE.IMPORTANT] Tutti gli script presuppongono che l'immagine di base del sistema operativo soddisfi i prerequisiti (.NET 3.5 installato, JavaScript e cookie attivati il ClientVM sia la versione più recente di PowerShell Azure installato). Se si riceve errori durante l'esecuzione di script, verificare di soddisfare i prerequisiti.

5. Per [testare il nuovo provider risorsa MySQL](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), distribuire un database MySQL dal portale di Azure Stack. Fare clic su **Crea** &gt; **personalizzata** &gt; **Server MySQL e Database**.

È visualizzato il provider di risorse MySQL e l'esecuzione di circa 25 minuti.
