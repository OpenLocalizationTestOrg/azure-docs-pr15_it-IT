<properties
    pageTitle="Aggiungere un Provider di risorse App Web Stack Azure | Microsoft Azure"
    description="Istruzioni dettagliate per la distribuzione Web Apps in pila di Azure"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Aggiungere un provider di risorse Web Apps dello Stack di Azure

> [AZURE.NOTE] Le informazioni seguenti si applicano solo alle distribuzioni TP1 Stack Azure.

Aggiunta di un Provider di risorse Web App a Stack di Azure è sette passaggi:

1.  Scaricare i componenti necessari.
2.  Creare certificati che verrà utilizzato da Azure Stack Web Apps.
3.  Usare il programma di installazione per scaricare, secondario e installare Azure Stack Web Apps. 
4.  Convalidare l'installazione di applicazioni Web.
5.  Creare record DNS per il front-End e i servizi di bilanciamento del carico Management Server.
6.  Registrare il provider di risorse Web Apps appena distribuito con ARM.
7.  Unità di test il Provider di risorse di applicazioni Web.

## <a name="download-required-components"></a>Scaricare i componenti necessari

1.  Scaricare [il programma di installazione di Azure Stack App Service anteprima](http://aka.ms/azasinstaller). 
2.  Scaricare gli [script di supporto di distribuzione di Azure Stack App Service](http://aka.ms/azashelper). 
3.  Estrarre i file dal file zip script di supporto, devono essere presenti tre script:
    - Creare AppServiceCerts.ps1
    - Creare AppServiceDnsRecords.ps1
    - Registro AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Creare i certificati che verrà utilizzato da Azure Stack Web Apps

Questo script prima funziona con l'autorità di certificazione dello Stack di Azure per creare 3 dei certificati necessari dalle App Web. Eseguire lo script nella ClientVM garantire che PowerShell è in esecuzione come azurestack\administrator:
1.  In una sessione di PowerShell in esecuzione come **azurestack\administrator**, eseguire lo script **Crea AppServiceCerts.ps1** .  Consente di creare tre certificati, nella stessa cartella in cui lo script che sono necessari per Web Apps.
2.  Immettere una password per proteggere i file pfx e prendere nota del come sarà necessario immettere del programma di installazione delle app di Azure Stack Web.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Usare il programma di installazione per scaricare e installare Azure Stack Web Apps

Il programma di installazione appservice.exe sarà:
1.  Richiedere all'utente di accettare la Microsoft e terze parti EULA.
2.  Raccogliere informazioni sulla distribuzione dello Stack di Azure.
3.  Creare un contenitore di blob l'account di archiviazione Azure Stack specificato.
4.  Scaricare i file necessari per installare il provider di risorse Azure Stack Web App.
5.  Preparare l'installazione per distribuire il provider di risorse Web App in ambiente dello Stack di Azure.
6.  Caricare i file all'account di archiviazione del servizio di App.
7.  Presentare le informazioni necessarie per avviare il modello di gestione di risorse Azure.

I passaggi seguenti per le fasi di installazione:

>[AZURE.NOTE] Per eseguire il programma di installazione, è necessario utilizzare un account con privilegi elevato (amministratore locale o dominio). Se non si accedere come azurestack\azurestackuser, verranno richieste le credenziali con privilegi elevate. 

1.  Eseguire appservice.exe come **azurestack\administrator**. 
2.  Fare clic su **Distribuisci Azure Gestione risorse**.

![Programma di installazione di Azure Stack servizio App Technical Preview 1][1]

3.  Rivedere e accettare le condizioni di licenza non definitiva di Software Microsoft e quindi fare clic su **Avanti**.
4.  Rivedere e accettare le condizioni partylicense terza e quindi fare clic su **Avanti**.
5.  Esaminare le informazioni di configurazione del servizio Cloud di App e fare clic su **Avanti**.

![Azure Stack servizio App Technical Preview 1 App servizio Cloud configurazione][2]

6. Fare clic su **Connetti** (accanto alla casella Azure Stack abbonamenti).

![Azure Stack servizio App Technical Preview 1 App servizio Cloud configurazione schermo due][3]

7.  Nella finestra di autenticazione Stack Azure specificare **l'account di amministratore dei servizi Azure Active Directory** e la **password**e quindi fare clic su **Accedi**.
**Nota:** Questo è l'account Azure Active Directory disponibili per la distribuzione di Azure Stack.
    - Fare clic sulla **Freccia verso il basso** sul lato destro della casella accanto a **Azure Stack sottoscrizioni** e quindi selezionare l'abbonamento.

![Selezione di sottoscrizione di Azure Stack del servizio di App Technical Preview 1][5]

8.  Fare clic sulla **Freccia verso il basso** sul lato destro della casella accanto a **Percorsi dello Stack di Azure**.
    - Selezionare **locale**.
9. Immettere un **nome** per l'amministratore.
10. Immettere una **Password** per l'amministratore.
11. Rivedere i **dettagli di SQL Server** e apportare le modifiche necessarie.
12. Verificare l' **Account di accesso amministrativi** e apportare le modifiche necessarie.
13. Immettere la **Password SysAdmin**.
14. Fare clic su **Avanti**.  A questo punto il programma di installazione ora verificare i dettagli della connessione per SQL Server fornito.

![Selezione di sottoscrizione di Azure Stack del servizio di App Technical Preview 1][4]    

15. Fare clic su **Sfoglia** accanto al **Web App predefinita SSL File di certificato** e passare a WebApp **. AzureStack.Local** certificato [creato in precedenza](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Immettere la **password di protezione** impostato al momento della creazione i certificati.
17. Fare clic su **Sfoglia** accanto al **File di certificato SSL del Provider di risorse** e passare a gestione **. AzureStack.Local** certificato [creato in precedenza](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Immettere la **password di protezione** impostato al momento della creazione i certificati.
19. Fare clic su **Sfoglia** accanto al **File di certificato radice di Provider di risorse** e passare a gestione **. AzureStack.Local** certificato [creato in precedenza](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Fare clic su **Avanti** il programma di installazione verifica la password del certificato fornita.

![Dettagli del certificato Stack Azure servizio App Technical Preview 1][6]

La distribuzione richiederà circa 45-60 minuti per completare.

![Stato dell'installazione di Azure Stack servizio App Technical Preview 1][7]

21. Completamento del programma di installazione fare clic su **Esci**.

## <a name="validate-web-apps-installation"></a>Convalidare l'installazione di applicazioni Web

1.  Sul proprio **Computer Host Stack Azure** aprire **Hyper-V Manager**.
2.  Individuare le **Macchine Virtuali CN0** e **connettersi** a macchina virtuale.
![Gestione di Hyper-V di Azure Stack servizi App Technical Preview 1][8]
3.  Sul desktop di questa macchina virtuale fare doppio clic su **Web Cloud Management Console**.
![Console di gestione di Azure Stack servizio App Technical Preview 1][9]
4.  Passare al **server gestiti**.
5.  Quando tutti i computer siano **pronti** procedere al passaggio successivo. 
![Stato dei server gestiti 1 Stack Azure servizi di App Technical Preview][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Creare record DNS per il Server di gestione dei servizi di bilanciamento del carico Front-End
1.  Aprire un'istanza di PowerShell come **azurestack\administrator**.
2.  Passare al percorso del file di script scaricato ed estratto nel [passaggio](#Download-Required-Components).
3.  Eseguire lo script **Crea AppServiceDnsRecords.ps1** , verrà creato DNS entries per abilitare le richieste di app portale e web da indirizzare il server front-End.  Durante la distribuzione di ARM di applicazioni Web, bilanciamento Software due (SLBs), sono stati creati nel provider di risorse di rete. Sono i server di gestione e dal server front-End. Il portale e le richieste di ARM in base al provider di risorse Azure Stack App Service passare al Server di gestione.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Registrare il provider di Azure Stack Web Apps appena distribuito con ARM
1.  Aprire un'istanza di PowerShell come **azurestack\administrator**.
2.  Passare al percorso del file di script scaricato ed estratto nel [passaggio](#Download-Required-Components).
3.  Eseguire lo script di **Registro AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Digitare il nome utente e la password **esattamente (tra maiuscole e minuscole)** che è stata immessa per i campi di **Password** nell'installazione e **l'Amministratore di macchine virtuali** o la registrazione di provider di risorse avrà esito negativo.

## <a name="test-drive-azure-stack-web-apps"></a>Test unità Stack Azure Web App

Ora che si ha distribuito e registrato il provider di risorse Web Apps, è possibile verificare per assicurarsi che i tenant possono distribuire le applicazioni web.

1.  Nel portale dello Stack di Azure, fare clic su Nuovo, fare clic su Web + Mobile e fare clic su App Web.
2.  In e il Web App, digitare un nome nella casella app Web.
3.  In gruppo di risorse, fare clic su Nuovo e quindi digitare un nome nella casella di gruppo di risorse. 
4.  Fare clic su percorso/del piano di servizio di App e fare clic su Crea nuovo.
5.  In e il piano di servizio di App, digitare un nome nella casella di piano di servizio di App.
6.  Fare clic su livello prezzi, fare clic su Free Shared o condivisi condiviso, fare clic su Seleziona, fare clic su OK e quindi fare clic su Crea.
7.  In un minuto, un titolo per la nuova app web verrà visualizzato nel Dashboard. Fare clic sul riquadro.
8.  In e il app web, fare clic su Sfoglia per visualizzare il sito Web predefinito per questa applicazione.


**Distribuire un sito Web WordPress, DNN o Django (facoltativo)**

1. Nel portale dello Stack di Azure, fare clic su "+", passare a Azure Marketplace distribuire un sito Web Django e attendere il completamento. La piattaforma web Django utilizza un database di file basato su sistema e non richiede qualsiasi provider di risorse aggiuntive ad esempio SQL o MySQL.  

2. Se è stato distribuito anche un provider di risorse MySQL, è possibile distribuire un sito Web WordPress da Marketplace. Quando viene richiesto per i parametri del database, immettere il nome utente come *User1@Server1* (con il nome utente e il nome del server di propria scelta).

3. Se è stato distribuito anche un provider di risorse di SQL Server, è possibile distribuire un sito Web DNN da Marketplace. Quando viene richiesto per i parametri del database, selezionare un database nel computer che esegue SQL Server che sia connesso al proprio provider di risorse.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile provare altre [piattaforma come un servizio di assistenza (PaaS)](azure-stack-tools-paas-services.md), come il [provider di risorse di SQL Server](azure-stack-sql-rp-deploy-short.md) e un [provider di risorse MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
