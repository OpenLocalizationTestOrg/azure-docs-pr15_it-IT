<properties 
    pageTitle="Relay Service Bus esempi Panoramica | Microsoft Azure"
    description="Classifica e vengono illustrati alcuni esempi di relay Service Bus con collegamenti a tutti."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-samples"></a>Esempi di inoltro Bus di servizio

Gli esempi di relay Service Bus illustrano principali funzionalità di [inoltro Bus di servizio](https://azure.microsoft.com/services/service-bus/). In questo articolo classifica e vengono illustrati alcuni esempi disponibili, con collegamenti a tutti.

>[AZURE.NOTE] Esempi di Bus di servizio non è installati con SDK. Per ottenere gli esempi, visitare la [pagina degli esempi di Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>È disponibile anche un insieme aggiornato campioni relay Service Bus [qui](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (in questo modo, non sono descritti in questo articolo).  

Per esempi di messaggistica, vedere [Bus di servizio di messaggistica esempi](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Inoltro Bus di servizio

Negli esempi seguenti viene illustrano come scrivere applicazioni che utilizzano il servizio di inoltro Bus di servizio.

Tenere presente che gli esempi di inoltro richiedono una stringa di connessione per accedere a spazio dei nomi Bus di servizio.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Per ottenere una stringa di connessione per Bus di servizio Azure

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Nella colonna sinistra, fare clic su **Bus di servizio**.

1. Fare clic sul nome dello spazio dei nomi nell'elenco.

3. Selezionare **i criteri di accesso condivisi**e dello spazio dei nomi.

4. In e il **criteri di accesso condiviso** , fare clic su **RootManageSharedAccessKey**.

6. Copiare la stringa di connessione negli Appunti.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Per ottenere una stringa di connessione per servizio Bus per Windows Server

1. Eseguire il seguente cmdlet di PowerShell:

    ```
    get-sbClientConfiguration
    ```

2. Incollare la stringa di connessione nel file App per il campione.

## <a name="service-bus-relay"></a>Inoltro Bus di servizio

Esempi che illustrano inoltro Bus di servizio.

### <a name="getting-started"></a>Guida introduttiva

|Nome di esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Inoltro dei messaggi: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Viene illustrato come eseguire un client di Bus di servizio e il servizio in Azure. In questo esempio Configura Bus di servizio a livello di programmazione. Solo le informazioni di protezione e ambiente vengono archiviate nei file di configurazione.|1.8|Bus di servizio di Microsoft Azure|
|[Autenticazione messaggi inoltrato: Segreto condiviso](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Di seguito viene illustrato come utilizzare un nome autorità che emette e segreto emittente per eseguire l'autenticazione con Bus di servizio.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltro dei messaggi di autenticazione: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Viene illustrato come esporre un servizio HTTP che non richiede l'autenticazione utente del client.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltro dei messaggi associazioni: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Di seguito viene illustrato come utilizzare l'associazione **WebHttpRelayBinding** per restituire dati binari utilizzando il modello di programmazione Web.|1.8|Bus di servizio di Microsoft Azure|
|[Associazioni di messaggi inoltrate: NetTcp inoltrati](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Viene illustrato come utilizzare l'associazione **NetTcpRelayBinding** .|1.8|Bus di servizio di Microsoft Azure|

### <a name="exploring-features"></a>Esplorazione delle funzionalità

Esempi che illustrano varie caratteristiche di inoltro Bus di servizio.

|Nome di esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Autenticazione messaggistica inoltrato: WebToken semplice](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Viene illustrato come utilizzare credenziali token web semplice per eseguire l'autenticazione con Bus di servizio. L'esempio è simile all'esempio eco, con alcune modifiche. In particolare, in questo esempio aggiunge un comportamento nelle applicazioni ChannelFactory (client) e ServiceHost (servizio).|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrati messaggistica: Bilanciamento del carico](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Viene illustrato come utilizzare Microsoft Azure servizio Bus per inviare messaggi a più destinatari. Che mostra più istanze di un servizio semplice la comunicazione con un client tramite l'associazione **NetTcpRelayBinding**|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: Evento netto](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Viene illustrato come utilizzare l'associazione **NetEventRelayBinding** su Bus di servizio di Microsoft Azure.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: Sessione WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Viene illustrato come utilizzare l'associazione **WS2007HttpRelayBinding** con sessioni affidabili abilitate. Viene illustrato come specificare le credenziali del servizio Bus nel file di configurazione anziché a livello di programmazione.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: MsgSecCertificate WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Viene illustrato come utilizzare l'associazione **WS2007HttpRelayBinding** con la sicurezza per proteggere i messaggi-to-end durante la richiesta ancora di client eseguire l'autenticazione con Bus di servizio.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrati messaggistica: Scambio di metadati](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Viene illustrato come esporre un endpoint metadati che utilizza l'associazione di inoltro. **MetadataExchange** è supportata nelle associazioni inoltro seguenti: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**e **WS2007HttpRelayBinding**.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: Pubblicitario NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Viene illustrato come configurare l'associazione **NetTcpRelayBinding** per la modalità di connessione **ibrido** che prima di tutto stabilisce una connessione inoltrata e, se possibile, passa automaticamente a una connessione diretta tra un client a un servizio di supporto.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: Il nome utente MsgSec NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Viene illustrato come utilizzare l'associazione **NetTcpRelayBinding** con la protezione dei messaggi.|1.8|Bus di servizio di Microsoft Azure|
|[Inoltrate associazioni messaggistica: Unidirezionale netto](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Viene illustrato come esporre e utilizzare un endpoint del servizio mediante l'associazione **NetOnewayRelayBinding** .|1.8|Bus di servizio di Microsoft Azure|
|[Associazioni di messaggi inoltrate: WS2007Http semplici](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Viene illustrato come utilizzare l'associazione **WS2007HttpRelayBinding** . Viene illustrato come un semplice servizio che non utilizza nessuna opzione di sicurezza e non richiede client eseguire l'autenticazione.|1.8|Bus di servizio di Microsoft Azure|

## <a name="next-steps"></a>Passaggi successivi

Vedere gli argomenti seguenti per una panoramica concettuale del servizio Bus.

- [Panoramica di inoltro Bus di servizio](service-bus-relay-overview.md)
- [Architettura di Bus di servizio](../service-bus-messaging/service-bus-architecture.md)
- [Nozioni fondamentali su Bus di servizio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)