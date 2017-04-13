<properties 
    pageTitle="Architettura di Bus di servizio | Microsoft Azure"
    description="Descrive il messaggio e l'architettura di Azure Bus di servizio per l'elaborazione di inoltro."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Architettura di Bus di servizio

Questo articolo descrive il messaggio e l'architettura di Azure Bus di servizio per l'elaborazione di inoltro.

## <a name="service-bus-scale-units"></a>Unità scala Bus di servizio

Servizio Bus è organizzato per *le unità della scala*. Un'unità in scala è un'unità di distribuzione e contiene tutti i componenti necessari eseguire il servizio. Ogni area distribuisce una o più unità di scala Bus di servizio.

Uno spazio dei nomi Bus di servizio sia associato a un'unità in scala. L'unità in scala gestisce tutti i tipi di entità Bus di servizio: inoltro e gestita entità messaggistica (code, argomenti, gli abbonamenti). Un'unità in scala Bus di servizio include i componenti seguenti:

- **Un set di nodi di un gateway.** I nodi gateway autenticare le richieste in arrivo e gestiscono le richieste di inoltro. Ogni nodo gateway ha un indirizzo IP pubblico.

- **Un set di messaggistica gestore nodi.** Messaggistica nodi gestore elaborano le richieste relative entità messaggistica.

- **Archiviazione di un gateway.** Archivio di gateway contiene i dati per ogni entità definiti in questa unità in scala. L'archivio di gateway viene implementato nella parte superiore di un database SQL Azure.

- **Più archivi di messaggistica.** Archivi messaggistica tenere i messaggi di tutte le code, argomenti e abbonamenti definiti in questa unità in scala. Contiene anche tutti i dati di sottoscrizione. A meno che non [partizionata messaggistica entità](service-bus-partitioning.md) è abilitata, una coda o argomento sia associato a un archivio di messaggistica. Gli abbonamenti vengono memorizzati nello stesso archivio di messaggistica l'argomento padre. Ad eccezione del servizio Bus [Premium messaggistica](service-bus-premium-messaging.md), archivi messaggistica sono implementati nella parte superiore di database SQL Azure.

## <a name="containers"></a>Contenitori

Ogni entità messaggistica viene assegnato un contenitore specifico. Un contenitore è un costrutto logico che utilizza un negozio a altro di messaggistica tutti i dati rilevanti per il contenitore. Ogni contenitore è assegnata a un nodo gestore messaggistica. In genere, sono disponibili ulteriori contenitori di messaggistica gestore nodi. Pertanto, ogni nodo gestore messaggistica Carica più contenitori. La distribuzione dei contenitori a un nodo gestore messaggistica è organizzata in modo che tutti i nodi di gestore messaggistica identico caricati. Se il carico di una serie di modifiche (ad esempio, uno dei Ottiene contenitori molto occupata) o se un nodo gestore messaggistica diventa temporaneamente non disponibile, i contenitori sono ridistribuiti tra i nodi gestore messaggistica.

## <a name="processing-of-incoming-messaging-requests"></a>Elaborazione delle richieste di messaggi in arrivo

Quando viene inviata una richiesta al servizio Bus, il servizio di bilanciamento del carico Azure lo indirizza a nessuno dei nodi gateway. Il nodo gateway consente la richiesta. Se la richiesta riguarda un'entità messaggistica (coda, argomento, sottoscrizione), il nodo gateway cerca entità nell'archivio gateway e determina in quale archivio messaggistica si trova l'entità. Cerca quindi il nodo gestore messaggistica è utilizzato il contenitore e invia la richiesta a tale nodo gestore messaggistica. Il nodo gestore messaggistica elabora la richiesta e aggiorna lo stato dell'entità nell'archivio contenitore. Il nodo gestore messaggistica quindi invia la risposta al nodo gateway, che invia una risposta appropriata al client che inviata la richiesta originale.

![Elaborazione delle richieste di messaggi in arrivo](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Elaborazione delle richieste di inoltro

Quando viene inviata una richiesta al servizio Bus, il servizio di bilanciamento del carico Azure lo indirizza a nessuno dei nodi gateway. Se la convocazione è una richiesta di attesa, il nodo gateway crea un nuovo inoltro. Se la convocazione è stata inviata una richiesta di connessione a un inoltro specifica, il nodo gateway inoltra la richiesta di connessione per il nodo gateway cui appartiene l'inoltro. Il nodo gateway cui appartiene l'inoltro invia una richiesta di rendezvous al client in attesa, comunicare ascoltatore per creare un canale temporaneo del nodo di gateway che ha ricevuto la richiesta di connessione.

Quando la connessione di inoltro, i client possono scambiare messaggi tramite il nodo gateway utilizzato per il rendezvous.

![Elaborazione delle richieste di inoltro](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata leggere una panoramica dell'architettura Bus di servizio, iniziare a visitare i collegamenti seguenti:

- [Cenni preliminari sulla messaggistica Bus di servizio](service-bus-messaging-overview.md)
- [Nozioni fondamentali su Bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Una soluzione di messaggistica in coda tramite code Bus di servizio](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
