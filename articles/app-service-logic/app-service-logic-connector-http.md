<properties
   pageTitle="Uso delle comunicare ascoltatore HTTP e connettore nelle App logica | Servizio di Microsoft Azure App "
   description="Come creare e configurare il comunicare ascoltatore HTTP e HTTP azione API o connettore app e usarlo in un'app di logica nel servizio App Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Guida introduttiva a comunicare ascoltatore HTTP e all'azione HTTP e aggiungere logica all'applicazione in uso

> [AZURE.NOTE]È in corso fine supporto per il connettore poiché la relativa funzionalità è ora incluso per impostazione predefinita come **trigger manuale** quando si creano nuove app logica.  È consigliabile eseguire l'aggiornamento di tutte le app di logica che utilizzano il connettore.  
> Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Connettere direttamente alle risorse HTTP per ascoltare le richieste HTTP e configurare le richieste web HTTP. Esistono alcuni scenari in cui potrebbe essere necessario lavorare con le connessioni HTTP dirette, tra cui:

1.  Sviluppare una logica di applicazione che supporta web o front-end interattivi utenti mobili.
2.  Per ottenere ed elaborare dati da un servizio web che non dispone di un timeout del connettore casella.
3.  Per eseguire le azioni che fanno già esposti come un servizio web, ma non disponibile come un'app API.

Per gli scenari seguenti sono disponibili due opzioni:

1. **Comunicare ascoltatore HTTP**: il connettore funziona come trigger e riceve le richieste HTTP su un endpoint configurato. Quando si riceve una chiamata per l'endpoint configurato, avvia una nuova istanza del flusso di e passa i dati ricevuti nella richiesta per il flusso per l'elaborazione. Può inoltre essere configurato per rispondere automaticamente alla richiesta di posta in arrivo quando il flusso ha avviato o consentono di creare una risposta in base all'esecuzione del flusso e inviare una risposta al chiamante.
2. **Azione HTTP**: questo consente di configurare una richiesta web per qualsiasi endpoint disponibile su internet, consente di ottenere una risposta e rende disponibile per altre azioni del flusso di utilizzare.

Consente di attivare App logica in base a una varietà di origini dati e connettori offerta per ottenere ed elaborare dati come parte del flusso. È possibile aggiungere il connettore HTTP ai dati business flussi di lavoro e processo come parte del flusso di lavoro all'interno di un'App di logica. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Creazione di un comunicare ascoltatore HTTP per l'App logica
Un connettore, questo può essere creato all'interno di un'app logica o creato direttamente da Azure Marketplace. Per creare un connettore da Marketplace:  

1. Nella finestra startboard Azure, selezionare **Marketplace**.
2. Cercare "HTTP", selezionare comunicare ascoltatore HTTP e selezionare **Crea**.
3.  Configurare comunicare ascoltatore HTTP come segue:  
![][1]

4.  Quando si configura le impostazioni del pacchetto, verrà visualizzato l'opzione seguente in cui deve rispondere automaticamente o è necessario comunicare ascoltatore per inviare una risposta esplicita. Impostare questa opzione su **False** per inviare la propria risposta:  
![][2]

5.  Fare clic su **OK** per creare.
6.  Dopo aver creato l'istanza di app API, aprire le impostazioni per configurare la protezione. Comunicare ascoltatore HTTP supporta attualmente l'autenticazione di base. È possibile configurare questa utilizzando l'opzione di sicurezza quando si apre comunicare ascoltatore HTTP:  
![][3]
  
    **Problemi noti** Impostazioni di *di sicurezza indicano "Nessuno" come valore predefinito, ma non è definita. È necessario modificare l'impostazione Basic e tornare al nessuno prima di salvare, per assicurarsi che comunicare ascoltatore HTTP sia configurato correttamente.*  

7. Infine, configurare le impostazioni di sicurezza dell'API App al pubblico (anonimo) per consentire ai client esterni di accedere al punto finale. Questa impostazione è disponibile in "tutte le impostazioni > Impostazioni applicazione" dell'API App comunicare ascoltatore HTTP:![][10]

Al termine dell'operazione, è ora possibile creare un'app di logica per l'utilizzo di comunicare ascoltatore HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>Utilizzo di comunicare ascoltatore HTTP nell'App logica
Dopo aver creato l'app API, è possibile utilizzare comunicare ascoltatore HTTP come trigger per l'App logica. A questo scopo è necessario:

4.  Creare una nuova App logica.
5.  Aprire "Trigger e azioni" per aprire la finestra di progettazione di applicazioni di logica e configurare il flusso. Comunicare ascoltatore HTTP è elencato nella raccolta. Selezionarla.
6.  È ora possibile impostare il metodo HTTP e l'URL relativo su cui si desidera comunicare ascoltatore avviare il flusso:  
![][4]  
![][5]

7.  Per ottenere URI completo, fare doppio clic comunicare ascoltatore HTTP per visualizzare le impostazioni di configurazione e copiare l'URL per "Host" dell'app API:  
![][6]
8.  È ora possibile usare i dati ricevuti nella richiesta HTTP in altre azioni del flusso di come indicato di seguito:  
![][7]  
![][8]
9.  Infine, per inviare una risposta, aggiungere un altro comunicare ascoltatore HTTP e selezionare l'azione Invia risposta HTTP. Impostare l'ID di richiedere l'ID richiesta ottenuto da comunicare ascoltatore HTTP e popolare la risposta e si desidera tornare indietro di stato HTTP:  
![][9]

## <a name="using-the-http-action"></a>Tramite l'azione HTTP
L'azione HTTP a livello nativo supportato da logica di applicazioni e non richiede un'app di API per creare innanzitutto essere in grado di usarlo. È possibile inserire un'azione HTTP in qualsiasi momento nell'App logica e scegliere l'URI, intestazioni e il corpo della chiamata.
L'azione HTTP supporta più opzioni per la protezione sul lato client. Vedere le [Opzioni di protezione sul lato client](../scheduler/scheduler-outbound-authentication.md).

L'output dell'azione HTTP è corpo, che può essere utilizzato ulteriormente valle nel flusso simile alla modalità di utilizzo di output di altre azioni e connettori e le intestazioni.

## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere un flusso di lavoro di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

Visualizzare il riferimento all'API REST Swagger [connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare sicurezza di statistiche e controllo delle prestazioni per il connettore. Vedere [gestire e monitorare le app API e i connettori incorporati](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Se si desidera iniziare a utilizzare App logica Azure prima di iscriversi a un account Azure, passare a [Provare App logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare un'app di logica starter breve immediatamente nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
