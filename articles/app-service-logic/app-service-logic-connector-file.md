<properties
    pageTitle="Usando il connettore File nelle App logica | Servizio di Microsoft Azure App"
    description="Come creare e configurare il connettore file o l'API app e usarlo in un'app di logica nel servizio App Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Iniziare a utilizzare il connettore file e aggiungere logica all'applicazione in uso
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Connettersi a un file system per caricare, scaricare e altri elementi per i file su un computer host. Consente di attivare App logica in base a un'ampia varietà di origini dati e connettori offerta per ottenere ed elaborare dati. È possibile aggiungere il connettore file ai dati business flussi di lavoro e processo come parte del flusso di lavoro all'interno di un'app di logica. 

Il connettore file utilizza la gestione di connessione ibrido per la connettività ibrido nel file System host.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Creazione di un connettore file per l'app logica ##
Per utilizzare il connettore file, è necessario prima di tutto creare un'istanza dell'API app connettore file. Questa operazione può essere eseguita come indicato di seguito:

1.  Aprire Azure Marketplace mediante il + nuova opzione sul lato sinistro del portale di Azure.
2.  Cercare "connettore file".
3.  Selezionare **File connettore (preview)** nei risultati della ricerca.
4.  Selezionare il pulsante **Crea**
5.  Configurare il connettore file come indicato di seguito:  
![][1]

    - **Nome** - assegnare un nome per il connettore file
    - **Impostazioni del pacchetto**
        - **Cartella radice** - specificare il percorso della cartella radice nel computer host. Ad esempio. D:\FileConnectorTest
        - **Stringa di connessione di servizio Bus** - fornire una stringa di connessione Bus di servizio. Assicurarsi che lo spazio dei nomi di servizio bus è di tipo Standard e non base per consentire l'uso del servizio Bus inoltro.  Servizio Bus inoltro viene utilizzato per connettersi al Gestore connessione ibrida.
    - **Piano di servizio app** - selezionare o creare un piano di servizio di App
    - **Prezzi livello** - scegliere un livello prezzo per il connettore
    - **Gruppo di risorse** , selezionare o creare un gruppo di risorse in cui devono essere inclusi il connettore
    - **Abbonamento** , scegliere un abbonamento a cui si desidera che il connettore deve essere creato nel
    - **Posizione** , scegliere l'area geografica in cui si desidera il connettore per la distribuzione

4. Fare clic su Crea. Verrà creato un nuovo connettore file

## <a name="configure-hybrid-connection-manager"></a>Configurare la gestione connessione ibrido ##
Dopo aver creato l'istanza di API App, passare alla relativa dashboard.  A tale scopo, fare clic su Sfoglia > API App > selezionare il connettore file API App.  Da qui è necessario configurare la gestione di connessione ibrida.
Per ulteriori informazioni sulla configurazione e la gestione di connessione ibrido risoluzione dei problemi vedere [utilizzando la gestione di connessione ibrida].

## <a name="using-the-file-connector-in-your-logic-app"></a>Usando il connettore file nell'app logica ##
Dopo aver creato l'app API, è possibile utilizzare il connettore file come un'azione per l'app logica. A questo scopo è necessario:

1.  Creare una nuova app logica e scegliere stesso gruppo di risorse con il connettore file. Seguire le istruzioni per [creare una nuova app logica].

2.  Aprire l'app logica creato per aprire l'App logica Designer e configurare il flusso di "Trigger e azioni".

3.  Il connettore file viene visualizzato nella sezione "API App in questo gruppo di risorse" nella raccolta sul lato destro.

4.  È possibile eliminare l'app di API connettore file nell'editor facendo clic su "connettore file". connettore file esposti un trigger e 4 azioni:  
![][5]

6.  Ognuno di essi espone alcune proprietà. L'immagine riportata di seguito sono elencate le proprietà per il trigger e ottenere file azione:  
![][6]

7. Dopo che sono configurati, i Trigger e azione è possibile utilizzare il flusso. Analogamente, è possibile configurare anche altre azioni.

> [AZURE.NOTE] Il file verrà eliminato trigger file dopo che è in grado di leggere la cartella.

## <a name="file-connector-rest-apis"></a>Connettore API REST file ##
Per utilizzare il connettore all'esterno di un'app di logica, è possibile sfruttare le API REST esposte dal connettore. È possibile Visualizza definizioni questa API utilizzando il pulsante Sfoglia -> Api App -> connettore file. Fare clic sull'obiettivo definizione dell'API della sezione di riepilogo per visualizzare tutte le API esposte dal connettore:  
![][7]

Dettagli delle API sono disponibili nella [definizione dell'API del connettore file].

## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere un flusso di lavoro di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se si desidera iniziare a utilizzare App Azure logica prima di iscriversi a un account Azure, passare all' [app di logica di prova](https://tryappservice.azure.com/?appservice=logic), in cui è possibile creare immediatamente un'app di logica starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

Visualizzare il riferimento all'API REST Swagger [connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare sicurezza di statistiche e controllo delle prestazioni per il connettore. Vedere [gestire e monitorare l'API App e il connettore incorporato](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Creare una nuova app logica]: app-service-logic-create-a-logic-app.md
[Connettore definizione dell'API file]: https://msdn.microsoft.com/library/dn936296.aspx
[Utilizzando la gestione di connessione ibrido]: app-service-logic-hybrid-connection-manager.md
