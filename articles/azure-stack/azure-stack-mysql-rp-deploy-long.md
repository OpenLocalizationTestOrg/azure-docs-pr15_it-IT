<properties
    pageTitle="Distribuire il provider di risorse MySQL nella pila Azure | Microsoft Azure"
    description="Procedura dettagliata per distribuire il Provider di risorse MySQL dello Stack di Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Distribuire il Provider di risorse MySQL nella pila di Azure da utilizzare con WebApp

> [AZURE.NOTE] Le informazioni seguenti si applicano solo alle distribuzioni TP1 Stack Azure.

Usare questo articolo per la procedura dettagliata per la configurazione di Provider di risorse MySQL sulla prova Azure Stack di prova, in modo che è possibile iniziare a [usare database MySQL](azure-stack-mysql-rp-deploy-short.md) nello Stack di Azure, incluso l'utilizzo di MySQL come back-end per i siti WordPress creato con [Azure Web Apps](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Configurare la procedura prima di distribuire

Prima di distribuire il provider di risorse, è necessario:

- Dispone di un'immagine di Windows Server predefinito con .NET 3.5
- Disattivare la protezione avanzata di Internet Explorer (IE)
- Installare la versione più recente di PowerShell di Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>Creare un'immagine di Windows Server inclusi .NET 3.5

Se è stato scaricato bit dello Stack di Azure dopo il 23/2/2016 perché l'immagine di Windows Server 2012 R2 base predefinito include .NET framework 3.5 in questo download e versioni successive, è possibile ignorare questo passaggio.

Se è stato scaricato prima 23/2/2016, è necessario creare un Windows Server 2012 R2 del Data Center del disco rigido virtuale con con immagine .NET 3.5 e set è possibile eseguire l'immagine predefinita nell'archivio di immagini di piattaforma.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Disattiva IE enhanced sicurezza e abilitare i cookie

Per distribuire un provider di risorse, eseguire dell'ambiente di Scripting integrato PowerShell (ISE) come amministratore, in modo che sia necessario consentire i cookie e JavaScript nel profilo di Internet Explorer che è possibile accedere a Azure Active Directory per gli accessi utente e amministratore.

**Per disattivare la IE protezione avanzata:**

1. Accedere al computer Azure Stack di prova (prova) come AzureStack/amministratore e quindi aprire Server Manager.

2. Disattivare **La protezione avanzata di Internet Explorer** per amministratori e utenti.

3. Accedere al computer virtuale **ClientVM.AzureStack.local** come amministratore e quindi aprire Server Manager.

4. Disattivare **La protezione avanzata di Internet Explorer** per amministratori e utenti.

**Per abilitare i cookie:**

1. Nella schermata Start di Windows, fare clic su **tutte le app**, fare clic su **Accessori di Windows**, rapida **Internet Explorer**, scegliere **altre**e quindi scegliere **Esegui come amministratore**.

2. Se richiesto, controllare **sicurezza è consigliabile utilizzare**e quindi fare clic su **OK**.

3. In Internet Explorer, fare clic su **Strumenti (icona dell'ingranaggio)** &gt; **Opzioni Internet** &gt; scheda **Privacy** .

4. Fare clic su **Avanzate**, verificare che siano selezionati entrambi i pulsanti **accetta** , fare clic su **OK**e quindi fare clic su **OK** .

5. Chiudere Internet Explorer e riavviare PowerShell ISE come amministratore.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Installare una versione compatibile Azure Stack di PowerShell di Azure

1. Disinstallare eventuali PowerShell Azure esistente da macchine Virtuali il Client.

2. Accedere al computer di prova Stack Azure come AzureStack/amministratore.

3. Usa Desktop remoto, accedere al computer virtuale **ClientVM.AzureStack.local** come amministratore.

4. Aprire il pannello di controllo, fare clic su **Disinstalla un programma** &gt; fare clic su **Azure PowerShell** &gt; fare clic su **Disinstalla**.

5. [Scaricare il più recente PowerShell di Azure che supporta Stack di Azure](http://aka.ms/azstackpsh) e installarlo.

    Dopo aver installato PowerShell, è possibile eseguire la verifica script di PowerShell per assicurarsi che sia possibile connettersi all'istanza di Azure Stack (dovrebbero essere visualizzate una pagina web di accesso).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Eseguire l'avvio della distribuzione di provider di risorse PowerShell

1. Connessione desktop remoto Azure Stack prova a clientVm.AzureStack.Local e accedere come azurestack\\azurestackuser.

2. [Scaricare i file binari MySQL RP](http://aka.ms/masmysqlrp) file ed estrarre i file in d:\\MySQLRP.

3. Eseguire d:\\MySQLRP\\Bootstrap.cmd file come amministratore (azurestack\administrator).

    Si apre il file Bootstrap.ps1 in PowerShell ISE.

4. Al termine del caricamento windows PowerShell ISE, fare clic sul pulsante "Riproduci" oppure premere F5.

    Verrà caricato due schede principali, ciascuno contenente tutti gli script e i file necessari per distribuire il Provider di risorse MySQL.

## <a name="prepare-prerequisites"></a>Preparare i prerequisiti

Fare clic sulla scheda **Preparare i prerequisiti** per:

- Creare certificati necessari
- Scaricare i file binari MySQL alla pila Azure
- Caricare gli elementi in un account di archiviazione in pila di Azure
- Pubblicare elementi della raccolta

### <a name="create-the-required-certificates"></a>Creare i certificati necessari
Questo script di **Nuovo SslCert.ps1** aggiunge il \_. Certificato SSL di AzureStack.local.pfx sull'unità d:\\MySQLRP\\prerequisiti\\BlobStorage\\cartella contenitore. Il certificato consente di proteggere le comunicazioni tra il provider di risorse e l'istanza locale di gestione risorse di Azure.

1. Nella scheda principale **Preparare prerequisiti** , fare clic sulla scheda **Nuovo SslCert.ps1** ed eseguirlo.

2. Nel messaggio che viene visualizzata, digitare una password PFX che consente di proteggere la chiave privata e **prendere nota della password**. È necessario in un secondo momento.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Scaricare i file binari MySQL alla pila Azure

1. Selezionare la scheda **MySqlServer.ps1 Download** ed eseguirlo.
2. Quando richiesto, fare clic su **Sì** nella finestra di dialogo Conferma per accettare il contratto di licenza.

    Questo comando aggiunge due file zip nella cartella D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Caricare tutti gli elementi in un account di archiviazione in pila di Azure

1. Fare clic sulla scheda **RP.ps1 di Microsoft.MySql carica** ed eseguirlo.

2. In Windows PowerShell credenziali richiesta la finestra di dialogo, digitare le credenziali di amministratore di servizio Stack di Azure.

3. Quando richiesto per l'ID di Tenant Azure Active Directory, digitare il nome di dominio completo tenant di Azure Active Directory: ad esempio microsoftazurestack.onmicrosoft.com.

    Una finestra popup richiede le credenziali.

    > [AZURE.TIP] Se non viene visualizzata la finestra popup, che uno non sono stati disattivati IE migliore sicurezza per abilitare JavaScript in questo computer e di un utente o non hanno accettato i cookie in Internet Explorer. Vedere [configurare la procedura prima di distribuire](#set-up-steps-before-you-deploy).

4. Digitare le proprie credenziali di amministratore del servizio Stack Azure e quindi fare clic su **Accedi**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Pubblicare elementi della raccolta per la successiva creazione di risorse

Selezionare la scheda **Pubblica GalleryPackages.ps1** ed eseguirlo. Questo script aggiunge due elementi marketplace marketplace del portale Azure Stack prova che è possibile utilizzare per distribuire le risorse di database come elementi marketplace.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Distribuire il Provider di risorse MySQL macchine Virtuali

Ora che è stato creato la prova pratica dello Stack di Azure con i certificati necessari e gli elementi di marketplace, è possibile distribuire un Provider di risorse di SQL Server. Fare clic sulla scheda **distribuzione MySQL provider** per:

   - Fornire i valori in un file JSON che fa riferimento il processo di distribuzione
   - Distribuire il provider di risorse
   - Aggiornare il sistema DNS locale
   - Registrare la scheda Provider delle risorse SQL Server

### <a name="provide-values-in-the-json-file"></a>Fornire i valori nel file JSON

Fare clic su **Microsoft.MySqlprovider.Parameters.JSON**. Questo file con i parametri il modello di gestione di risorse Azure deve distribuire correttamente in pila Azure.

1. Compilare i parametri nel file JSON **vuota** :

    - Assicurarsi di specificare la **adminusername** e **adminpassword** per la macchina virtuale Provider risorsa MySQL.

    - Assicurarsi di specificare la password per il parametro **SetupPfxPassword** apportate annotare nel passaggio [Prepara prequisites](#prepare-prerequisites) .

    - Assicurarsi di specificare parametri **basicAuthUserName** e **basicAuthPassword** . **Prendere nota dei seguenti valori.** È necessario più avanti per registrare il provider di risorse.

2. Fare clic su **Salva**.

### <a name="deploy-the-resource-provider"></a>Distribuire il provider di risorse

1. Fare clic sulla scheda **distribuzione-Microsoft.Mysql-provider.PS1** ed eseguire lo script.
2. Digitare il nome del tenant di Azure Active Directory quando richiesto.
3. Nella finestra popup, inviare le credenziali di amministratore di servizio Stack di Azure.

La distribuzione completa può richiedere tra 45 e 15 minuti su alcune altamente utilizzata POCs Stack Azure.

### <a name="update-the-local-dns"></a>Aggiornare il sistema DNS locale

1. Fare clic sulla scheda **fqdn.ps1 di Microsoft.MySQL registro** ed eseguire lo script.
2. Quando viene richiesto di Azure Active Directory Tenant ID, immettere il nome di dominio completo tenant di Azure Active Directory: ad esempio **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Registrare il Provider di risorse RP SQL

1. Fare clic sulla scheda **provider.ps1 di Microsoft.My registro** ed eseguire lo script.

2. Quando richiesto per le credenziali, utilizzare indicato come parametri **basicAuthUserName** e **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verificare la distribuzione tramite il portale di Stack di Azure

1. Disconnettersi dalla ClientVM e accedere nuovamente come **AzureStack\User**.

2. Sul desktop, fare clic su **Azure Stack prova portale** e accedere al portale come amministratore del servizio.

3. Verificare che la distribuzione completata. Fare clic su **Sfoglia** &gt; **Gruppi di risorse**, fare clic sul gruppo di risorse è stato utilizzato (impostazione predefinita è **MySQLRP**) e quindi assicurarsi che la parte nozioni di base della stessa e (metà superiore) legge **distribuzione completata**.


4. Verificare che la registrazione è stata eseguita correttamente. Fare clic su **Sfoglia** &gt; **provider di risorse**e quindi cercare **MySQL locale**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Creare il primo database MySQL per testare la distribuzione

1. Accedere al portale di Azure Stack prova come amministratore del servizio.

2. Fare clic sul **+** pulsante &gt; **personalizzata** &gt; **MySQL Server e database**.

3. Compilare il modulo con i dettagli del database.

    **Annotare il "nome del server".** La stringa di connessioni per il database include il nome del server"" come parte del nome utente: ad esempio ** "user@ <ServerName>"**. È necessario immettere un nome utente in questo formato quando ci si connette al database: ad esempio, quando si distribuisce un sito web MySQL mediante il provider di risorse del sito Web di Azure


## <a name="next-steps"></a>Passaggi successivi

Provare ad altri [servizi PaaS](azure-stack-tools-paas-services.md) come [provider di risorse di SQL Server](azure-stack-sql-rp-deploy-short.md) e il [provider di risorse Web Apps](azure-stack-webapps-deploy.md).
