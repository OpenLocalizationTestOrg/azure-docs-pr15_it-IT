<properties 
    pageTitle="Panoramica di servizio Bus AMQP con Java | Microsoft Azure" 
    description="Informazioni sull'uso di linguaggio con le avanzate messaggio Accodamento Protocol (AMQP) 1.0 in Azure." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="amqp-10-support-in-service-bus"></a>Supporto di AMQP 1.0 in Bus di servizio

Locale sia il servizio cloud di Azure servizio Bus [Bus servizio per Windows Server (servizio Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) supporta l'avanzate messaggio Accodamento Protocol (AMQP) 1.0. AMQP consente di compilare multipiattaforma e applicazioni ibride utilizzando un protocollo standard aperto. È possibile creare applicazioni che utilizzano componenti che sono stati creati utilizzando diverse lingue e Framework e in esecuzione su sistemi operativi diversi. Tutti questi componenti è possono connettersi al servizio Bus e diretta scambiare messaggi aziendali strutturati in modo efficiente e fedeltà completa.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduzione: Che cos'è 1.0 AMQP e perché è importante?

In genere, prodotti middleware orientati ai messaggi hanno implementato protocolli proprietari per le comunicazioni tra le applicazioni client e intermediari. Questo significa che dopo avere selezionato gestore messaggistica del fornitore specifico, è necessario utilizzare le raccolte del fornitore per connettersi alle applicazioni client di tale gestore. Il risultato è in grado di dipendenza su tale fornitore, poiché portabilità un'applicazione di un altro prodotto richiede modifiche al codice in tutte le applicazioni connessione. 

Inoltre, è difficile connessione Broker messaggistica fornitori diversi. È necessario stabilire a livello di applicazione bridging per spostare i messaggi da un sistema e per tradurre testi tra i formati dei messaggi proprietari. Si tratta di un requisito comune; ad esempio, quando è necessario specificare una nuova interfaccia unificata sistemi a versioni precedenti o integrare sistemi IT seguendo una fusione.

Il settore del software è un'azienda rapido movimento. a una velocità talvolta contribuita vengono introdotti nuovi linguaggi di programmazione e Framework di applicazione. Analogamente, i requisiti di sistemi evolvono nel tempo e gli sviluppatori desiderano sfruttare le funzionalità di piattaforma più recente. Tuttavia, a volte fornitore messaggistica selezionato non supporta queste piattaforme. Poiché i protocolli di messaggistica sono proprietari, non è possibile ad altri utenti fornire le raccolte per le nuove piattaforme. Di conseguenza, è necessario utilizzare approcci, ad esempio la creazione di gateway o ponti che consentono di continuare a usare il prodotto SMS.

Sviluppo dell'avanzate messaggio Accodamento Protocol (AMQP) 1.0 è stata base da questi problemi. Ha avuto origine nel JP Morgan Chase, che, ad esempio società di servizi più finanziarie, gli utenti piena di middleware orientati ai messaggi. L'obiettivo è semplice: per creare un protocollo messaggistica Apri standard che è stato possibile creare applicazioni basate su messaggi utilizzando componenti creati con lingue diverse, Framework e sistemi operativi, tutto utilizzando componenti di migliori da un intervallo di fornitori.

## <a name="amqp-10-technical-features"></a>Caratteristiche tecniche AMQP 1.0

AMQP 1.0 è un protocollo messaggistica efficiente, affidabile e a livello di rete che è possibile utilizzare per creare efficaci e multipiattaforma e applicazioni di messaggistica. Il protocollo dispone di un semplice obiettivo: definire i meccanismi di trasferimento sicuro, affidabile ed efficiente di messaggi tra due parti. I messaggi vengono codificati utilizzando una rappresentazione di dati portabili che consente a mittenti eterogenei e ricevitori scambiare messaggi aziendali strutturati fedeltà completo. Di seguito è riportato un riepilogo delle caratteristiche più importanti:

*    **Efficiente**: AMQP 1.0 è orientato alla connessione protocollo che utilizza una codifica binaria per le istruzioni di protocollo e i messaggi di business trasferita. Incorpora combinazioni di controllo di flusso sofisticate per ottimizzare l'utilizzo della rete e i componenti connessi. Nonostante ciò, il protocollo progettato per raggiungere un equilibrio tra l'efficienza, flessibilità e interoperabilità.
*    **Affidabile**: 1.0 AMQP il protocollo consente messaggi scambiati con un intervallo di garanzie di affidabilità, da fire-e-dimenticare affidabile, esattamente-una volta riconosciuto recapito.
*    **Flessibile**: AMQP 1.0 è un protocollo flessibile che può essere utilizzato per supportare topologie diverse. Protocollo stesso può essere utilizzato per le comunicazioni da client, client al gestore e gestore a gestore.
*    **Modello di gestore indipendente**: Specifica il AMQP 1.0 non verificare i requisiti sul modello di messaggistica utilizzato da un gestore. Ciò significa che è possibile aggiungere facilmente supporto AMQP 1.0 Broker messaggistica esistente.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 è uno Standard (con la maiuscola ')

AMQP 1.0 è stato in fase di sviluppo da 2008 da un gruppo di base di più di 20 società specializzate, fornitori di tecnologia e imprese degli utenti finali. Nel frattempo, imprese utente hanno contribuito esigenze aziendali reali e i fornitori di tecnologia hanno derivato il protocollo per soddisfare tali requisiti. Durante il processo, fornitori hanno partecipato workshop in cui si collabora per convalidare l'interoperabilità tra le implementazioni.

In ottobre 2011, il lavoro sviluppo utilizzino un comitato tecnico all'interno dell'organizzazione per l'avanzamento di strutturati informazioni standard OASIS () e Standard OASIS AMQP 1.0 pubblicazione: ottobre 2012. Le seguenti imprese partecipato comitato tecnico durante lo sviluppo dello standard:

*    **Fornitori di tecnologia**: Axway Software, Huawei Technologies, la relativa Software, INETCO sistemi, Kaazing, Microsoft, Mitre Corporation, Primeton Technology, lo stato di avanzamento Software, Red Hat, SITA, Software AG, sistemi Solace, VMware, WSO2, Zenika.
*    **Imprese utente**: Bank of America, Suisse carta di credito, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Alcuni dei vantaggi le citazioni sono in genere di standard aperti includono:

*    Ridurre il rischio di blocco aggiuntivo fornitore
*    Interoperabilità
*    Ampia disponibilità degli utensili e raccolte
*    Protezione contro obsolescenza
*    Disponibilità di competenza del personale
*    Rischio inferiore e gestibile

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e Service Bus

Supporto di AMQP 1.0 in Azure servizio Bus significa che è possibile sfruttare l'accodamento Bus di servizio e pubblicazione/sottoscrizione funzionalità di messaggistica gestita da un intervallo di piattaforme con un utilizzo efficiente protocollo binario. Inoltre, è possibile creare applicazioni comprende i componenti creati utilizzando una combinazione di sistemi operativi, Framework e lingue.

La figura seguente illustra un esempio di distribuzione in cui i clienti di linguaggio su Linux, scritto utilizzando il standard Java Message Service (JMS) API e i client .NET in esecuzione su Windows, scambiano di messaggi tramite Bus di servizio utilizzando AMQP 1.0.

![][0]

**Figura 1: Scenario di distribuzione di esempio con multipiattaforma messaggistica utilizzando Bus di servizio e AMQP 1.0**

Al momento le librerie client seguenti sono noti per l'uso con Bus di servizio:

| Lingua | Raccolta                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Client Apache Qpid Java Message Service (JMS)<br/>Client la relativa Software SwiftMQ Java |
| C        | Apache Qpid Proton-C                                                          |
| PHP      | Apache Qpid Proton-PHP                                                        |
| Python   | Apache Qpid Proton-Python                                                     |


**Figura 2: Indice delle raccolte di client AMQP 1.0**

Per ulteriori informazioni su come ottenere e usare queste raccolte con Bus di servizio, vedere la [Guida per gli sviluppatori di servizio Bus AMQP][]. Per i collegamenti a ulteriori informazioni, vedere [passaggi successivi](service-bus-java-amqp-overview.md#next-steps) .

## <a name="summary"></a>Riepilogo

*    AMQP 1.0 è aperto, affidabile messaggistica protocollo che è possibile utilizzare per creare multipiattaforma e applicazioni ibride. AMQP 1.0 è uno standard OASIS.
*    Supporto AMQP 1.0 è ora disponibile in Bus di servizio Azure, nonché Service Bus per Windows Server (servizio Bus 1.1). Prezzi è uguale a quella per i protocolli esistenti.

## <a name="next-steps"></a>Passaggi successivi

Visitare i collegamenti seguenti per informazioni sul supporto AMQP in Bus di servizio.

*    [Come utilizzare 1.0 AMQP con l'API di .NET Bus del servizio](service-bus-dotnet-advanced-message-queuing.md)
*    [Come usare Java Message Service (JMS) API con Bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Servizio Guida per gli sviluppatori di Bus AMQP][]
*    [Specifica della avanzate messaggio Accodamento Protocol (AMQP) versione 1.0 OASIS](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-java-amqp-overview/Example1.png
[Servizio Guida per gli sviluppatori di Bus AMQP]: service-bus-amqp-dotnet.md

 
