<properties
    pageTitle="Che cos'è inoltro Azure? | Microsoft Azure"
    description="Panoramica dell'inoltro Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>Che cos'è l'inoltro di Azure?

Servizio di inoltro Azure semplifica l'esecuzione di applicazioni ibride, consentendo di sicuro esporre i servizi che si trovano all'interno di una rete dell'organizzazione aziendale nel cloud pubblico, senza dover aprire una connessione firewall o richiedono intrusive modifiche a un'infrastruttura di rete aziendale. Inoltro supporta diversi diversi protocolli di trasporto e standard dei servizi web.

Il servizio di inoltro supporta tradizionale unidirezionale richiesta risposta e il traffico peer-to-peer. Supporta anche la distribuzione di evento nell'ambito di internet per attivare pubblicazione/sottoscrizione e scenari di comunicazione bidirezionali socket per migliorare l'efficienza punto maggiore. 

Nel modello di trasferimento dati inoltrati, un servizio locale si connette al servizio di inoltro tramite una porta in uscita e crea un socket bidirezionale per la comunicazione collegato a un indirizzo rendezvous specifico. Il cliente può quindi comunicare con il servizio locale inviando il traffico al servizio di inoltro destinate a indirizzo rendezvous. Il servizio di inoltro verrà quindi "inoltrare" dati al servizio locale tramite un socket bidirezionale impegna costantemente per ogni cliente. Il client non è necessaria una connessione diretta al servizio locale, non è necessario sapere dove si trova il servizio e le porte in ingresso non è necessario il servizio locale aperta sul firewall.

Gli elementi di funzionalità chiave forniti dall'inoltro sono comunicazione bidirezionali e privo di buffer oltre i confini di rete con limitazione mi piace TCP e individuazione endpoint, lo stato di connettività e sovrapposta sicurezza endpoint. Funzionalità di inoltro diversi da Technology integrazione a livello di rete VPN in possono esistere a un endpoint singola applicazione su un singolo computer, mentre la tecnologia VPN è molto più approfondita mentre si basa su Modifica l'ambiente di rete.

Inoltro Azure offre due caratteristiche:

1. [Connessioni ibrido](#hybrid-connections) - utilizza il socket Web standard aperti, abilitazione di scenari multipiattaforma

2. [Inoltro WCF](#wcf-relays) - utilizza Windows Communication Foundation per consentire chiamate Remote Procedure

Connessioni ibrida e inoltro WCF consentono di una connessione protetta a assests che si trovano all'interno di una rete dell'organizzazione aziendale. Utilizzo di uno su altro dipende dalle esigenze specifiche, descritto di seguito:

|                                    | Inoltro WCF | Connessioni ibrido |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **.NET core**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Protocollo aperto basate su standard**  |           |         x          |
| **Più modelli di programmazione RPC** |           |         x          |
*<sub>In base alla disponibilità generale</sub>

## <a name="hybrid-connections"></a>Connessioni ibrido

Connessioni ibrido dell'inoltro Azure funzionalità è un'evoluzione sicura, Apri protocollo delle caratteristiche di inoltro esistente che possono essere implementata su qualsiasi piattaforma e in qualsiasi lingua grado WebSocket base, che include in modo esplicito l'API WebSocket in comune di web browser. Connessioni ibrida è basato su HTTP e WebSockets.

## <a name="wcf-relays"></a>Inoltro WCF

Inoltro WCF funziona per completo .NET Framework (NETFX) e per WCF. Avviare la connessione tra il servizio in locale e il servizio di inoltro utilizzando una famiglia di prodotti di associazioni di inoltro"WCF". Dietro le quinte, le associazioni di inoltro eseguire il mapping a nuovi elementi di associazione di trasporto progettati per creare i componenti di canale WCF che si integrano con Bus di servizio nel cloud.

## <a name="service-history"></a>Cronologia del servizio

Connessioni ibrido trapianti della prima, identico denominata funzionalità "Servizi BizTalk" che è stato creato il l'inoltro di Azure servizio Bus WCF. La nuova funzionalità di connessioni ibrido integra esistente inoltro di WCF e queste funzionalità due service saranno presente side-by-side nel servizio di inoltro in futuro; condividere un gateway comune ma sono in caso contrario implementazioni diverse.

L'inoltro WCF è inoltro legacy che offre la possibilità che molti clienti sia già disponibile con i modelli di programmazione WCF.

## <a name="next-steps"></a>Passaggi successivi:

- [Inoltro domande frequenti](relay-faq.md)
- [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
- [Guida introduttiva a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Guida introduttiva a nodo](relay-hybrid-connections-node-get-started.md)