<properties
    pageTitle="Cenni preliminari su relay Service Bus | Microsoft Azure"
    description="Panoramica dell'inoltro Bus di servizio."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Panoramica dell'inoltro Bus di servizio

Un componente principale del servizio Bus è un servizio centralizzato (ma altamente bilanciamento del carico) *inoltro* che consente di compilare applicazioni ibride che eseguono in un Data Center Azure e ambiente aziendale locale.  Relay Service Bus supporta diversi diversi protocolli di trasporto e standard dei servizi web. Sono inclusi SOAP WS-, * e anche resto. Il servizio di inoltro semplifica l'esecuzione di applicazioni ibride, consentendo di sicuro espongono i servizi di Windows Communication Foundation (WCF) che si trovano all'interno di una rete dell'organizzazione aziendale nel cloud pubblico, senza dover aprire una connessione firewall o richiedono intrusive modifiche a un'infrastruttura di rete aziendale. 

![Concetti di inoltro](./media/service-bus-relay-overview/sb-relay-01.png)

Il servizio di inoltro supporta messaggistica unidirezionale tradizionale, richiesta di messaggistica e la messaggistica peer-to-peer. Supporta anche la distribuzione di evento nell'ambito di internet per attivare pubblicazione/sottoscrizione e scenari di comunicazione bidirezionali socket per migliorare l'efficienza punto maggiore. 

Nel modello di messaggi inoltrato, un servizio locale si connette al servizio di inoltro tramite una porta in uscita e crea un socket bidirezionale per la comunicazione collegato a un indirizzo rendezvous specifico. Il cliente può quindi comunicare con il servizio locale mediante l'invio di messaggi per il servizio di inoltro destinate a indirizzo rendezvous. Il servizio di inoltro verrà quindi "inoltrare" messaggi al servizio locale tramite socket bidirezionale già presenti. Il client non è necessaria una connessione diretta al servizio locale, non è necessario sapere dove si trova il servizio e le porte in ingresso non è necessario il servizio locale aperta sul firewall.

Avviare la connessione tra il servizio in locale e il servizio di inoltro utilizzando una famiglia di prodotti di associazioni di inoltro"WCF". Dietro le quinte, le associazioni di inoltro eseguire il mapping a nuovi elementi di associazione di trasporto progettati per creare i componenti di canale WCF che si integrano con Bus di servizio nel cloud. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'inoltro Bus di servizio, vedere gli argomenti seguenti.

- [Cenni preliminari sull'architettura di Bus servizio Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Come utilizzare il servizio di assistenza Bus inoltro](service-bus-dotnet-how-to-use-relay.md)

 