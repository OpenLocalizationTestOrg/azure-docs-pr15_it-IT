<properties 
    pageTitle="Servizio Bus messaggistica esempi Panoramica | Microsoft Azure"
    description="Classifica e descrive Bus di servizio di messaggistica campioni con collegamenti a tutti."
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

# <a name="service-bus-messaging-samples"></a>Esempi di messaggistica Bus di servizio

Gli esempi di messaggistica Bus di servizio illustrano principali funzionalità di [messaggistica Bus di servizio](https://azure.microsoft.com/services/service-bus/) (servizio cloud) e [Servizio Bus per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). In questo articolo classifica e vengono illustrati alcuni esempi disponibili, con collegamenti a tutti.

>[AZURE.NOTE] Esempi di Bus di servizio non è installati con SDK. Per ottenere gli esempi, visitare la [pagina degli esempi di Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>È disponibile anche un insieme aggiornato di Bus di servizio di messaggistica esempi [qui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (in questo modo, non sono descritti in questo articolo).  

Per esempi di inoltro, vedere [esempi di inoltro Bus di servizio](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Messaggistica Bus di servizio

Negli esempi seguenti viene illustrano come scrivere applicazioni che utilizzano Bus di servizio di messaggistica.

Tenere presente che gli esempi di messaggistica richiedono una stringa di connessione per accedere a spazio dei nomi Bus di servizio.

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

### <a name="getting-started-samples"></a>Guida introduttiva esempi

In questi esempi descrivono funzionalità di messaggistica di base.

|Nome di esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Guida introduttiva: Messaggistica con code](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Viene illustrato come utilizzare Microsoft Azure servizio Bus per inviare e ricevere messaggi da una coda.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Guida introduttiva: Messaggistica con gli argomenti](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Viene illustrato come utilizzare Microsoft Azure servizio Bus per inviare e ricevere messaggi da un argomento con più abbonamenti.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|

### <a name="exploring-features"></a>Esplorazione delle funzionalità

Negli esempi seguenti viene varie caratteristiche di Bus di servizio.

|Nome di esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Provider di Token HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Illustra i diversi metodi di autenticazione un client HTTP/resto con Bus di servizio.|2.1|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Servizio Bus HTTP Client](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Viene illustrato come inviare e ricevere messaggi da Bus di servizio tramite HTTP/resto.|2.3|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Inoltro automatico Bus di servizio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Viene illustrato come inoltrare automaticamente i messaggi da una coda di un abbonamento o una coda di messaggi non recapitabili in un'altra coda o argomento. Inoltre, viene illustrato inviare un messaggio in una coda o un argomento tramite una coda di trasferimento.|2.3|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Esempio di sessione canale WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Viene illustrato come utilizzare Microsoft Azure servizio Bus mediante i canali di Windows Communication Foundation (WCF). Nell'esempio viene illustrato come utilizzare i canali WCF da inviare e ricevere messaggi tramite una coda Bus di servizio. Nell'esempio viene sessione e la comunicazione non sessione tramite il Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Negoziate messaggistica: le transazioni](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Viene illustrato come utilizzare il Bus di servizi di Microsoft Azure messaggistica caratteristiche all'interno di un ambito di transazione per garantire batch della messaggistica operazioni viene eseguito il commit in modo separato.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Operazioni di gestione uso resto](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Viene illustrato come eseguire le operazioni di gestione sul servizio Bus utilizzando resto.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Provider di risorse API REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Viene illustrato come utilizzare le nuove API REST servizio Bus RDFE per gestire gli spazi dei nomi e messaggistica entità.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Esempio di sessione servizio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Viene illustrato come utilizzare il modello di servizio WCF Bus di servizio di Microsoft Azure. Nell'esempio viene illustrato come utilizzare il modello dei servizi WCF da eseguire per completare la comunicazione basata su sessione tramite una coda Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Risposta richiesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Viene illustrato come utilizzare Microsoft Azure Service Bus e la funzionalità di richiesta/risposta. Nell'esempio viene semplice client e server comunicare tramite una coda Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: coda lettera inattiva](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Viene illustrato come utilizzare Bus di servizio di Microsoft Azure e le funzionalità di messaggistica "lettera inattiva coda". L'esempio mostra una semplice mittente e il destinatario comunicare tramite una coda Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Posticipato messaggi](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Viene illustrato come utilizzare la funzione di esclusione di messaggi di Microsoft Azure servizio Bus. L'esempio mostra una semplice mittente e il destinatario comunicare tramite una coda Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Messaggi di sessione](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Viene illustrato come utilizzare la funzionalità di sessione di messaggistica e Bus di servizio di Microsoft Azure. Nell'esempio viene mittenti semplici e ricevitori di comunicare tramite una coda Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Argomento di risposta richiesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Viene illustrato come implementare la serie di richiesta/risposta utilizzando le sottoscrizioni e gli argomenti della Bus di servizio di Microsoft Azure. Nell'esempio viene semplice client e server comunicare tramite un argomento Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Coda di risposta richiesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Viene illustrato come utilizzare Microsoft Azure servizio Bus e la funzionalità di richiesta/risposta. Nell'esempio viene semplice client e server comunicare tramite due code Bus di servizio.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Il rilevamento duplicati](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Viene illustrato come utilizzare il rilevamento dei messaggi duplicati Bus di servizio di Microsoft Azure con code. Crea due code, uno con il rilevamento duplicati abilitato e un'altra senza duplicati.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Messaggistica asincrono](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Viene illustrato come utilizzare Microsoft Azure servizio Bus per inviare e ricevere messaggi in modo asincrono da una coda. Coda consente la comunicazione disaccoppiata, asincrona tra un mittente e qualsiasi numero di ricevitori (qui, un singolo destinatario).|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Filtri avanzati](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Viene illustrato come utilizzare Microsoft Azure servizio Bus pubblicazione/sottoscrizione filtri avanzati. Crea un argomento e 3 abbonamenti con le definizioni di filtro diverso, invia messaggi all'argomento e riceve tutti i messaggi da abbonamenti.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Messaggistica gestito: Prelettura messaggi](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Viene illustrato come utilizzare la funzionalità di prelettura messaggi Bus di servizio di Microsoft Azure. Viene illustrato come utilizzare la funzionalità di prelettura messaggi alla ricezione.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|

## <a name="service-bus-reference-tools"></a>Strumenti di riferimento Bus di servizio

Negli esempi seguenti viene diverse altre funzionalità del servizio.

|Nome di esempio|Descrizione|Versione minima SDK|Disponibilità|
|---|---|---|---|
|[Servizio Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|Esplora aree Bus di servizio consente agli utenti di connettersi a uno spazio dei nomi del servizio Bus di servizio e gestire messaggistica entità in modo semplice. Lo strumento offre funzionalità avanzate, ad esempio le funzionalità di importazione/esportazione e la possibilità di testare entità messaggistica e servizi di inoltro.|1.8|Bus di servizio di Microsoft Azure; Bus di servizio per Windows Server|
|[Autorizzazione: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|In questo esempio viene illustrato come creare e gestire le identità di servizio in Microsoft Azure Active Directory controllo di accesso (noto anche come servizio controllo di accesso o ACS) per l'utilizzo con Bus di servizio.|N/D|Bus di servizio di Microsoft Azure|

## <a name="next-steps"></a>Passaggi successivi

Vedere gli argomenti seguenti per una panoramica concettuale del servizio Bus.

- [Cenni preliminari sulla messaggistica Bus di servizio](service-bus-messaging-overview.md)
- [Architettura di Bus di servizio](service-bus-architecture.md)
- [Nozioni fondamentali su Bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
