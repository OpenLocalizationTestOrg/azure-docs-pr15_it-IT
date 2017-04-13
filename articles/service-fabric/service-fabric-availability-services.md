<properties
   pageTitle="Disponibilità dei servizi di infrastruttura di servizio | Microsoft Azure"
   description="Vengono descritti rilevamento errori, failover e ripristino per i servizi"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="availability-of-service-fabric-services"></a>Disponibilità dei servizi di infrastruttura di servizio
Servizi di infrastruttura servizio Azure possono essere informazioni sullo stato o senza informazioni sullo stato. In questo articolo viene fornita una panoramica di gestione di disponibilità di un servizio in caso di errori servizio tessuti.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilità dei servizi senza informazioni sullo stato del servizio tessuti
Un servizio senza informazioni sullo stato è un servizio applicazioni che non dispone di uno [stato persistente locale](service-fabric-concepts-state.md).

Creazione di un servizio senza informazioni sullo stato, è necessario definire un conteggio istanza, il numero di istanze del servizio senza informazioni sullo stato che deve essere in esecuzione nel cluster. Questo è il numero di copie della logica dell'applicazione verrà avviata l'istanza del cluster. Aumento del numero di istanze è il modo consigliato di ridimensionamento di un servizio senza informazioni sullo stato.

Quando si verifica un errore in qualsiasi istanza di un servizio senza informazioni sullo stato, viene creata una nuova istanza su un altro nodo idoneo del cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilità dei servizi di infrastruttura servizio informazioni sullo stato
Uno stato è associato un servizio informazioni sullo stato. Nel servizio tessuti, un servizio informazioni sullo stato è basato come un set di repliche. Ogni replica è un'istanza del codice del servizio che dispone di una copia dello stato. Operazioni di lettura e scrittura vengono eseguite in una replica (denominata primario). Le modifiche apportate allo stato di scrittura operazioni sono *replicate* in più sé (denominato dei secondari attivi). La combinazione di principale e attivare riproduzioni secondari è l'insieme di replica del servizio.

Può essere solo una replica primaria manutenzione richieste lettura e scrittura, ma possono essere presenti più repliche secondarie attive. Il numero di repliche secondarie attive è configurabile e un numero elevato di repliche tollerabile un maggior numero di errori hardware e software simultaneo.

In caso di un errore (quando replica primaria spostarsi verso il basso), servizio tessuti rende più replica secondario attiva la nuova replica principale. La replica secondaria attiva è già presente la versione aggiornata dello stato (tramite *replica*) e può continuare l'elaborazione ulteriormente lettura e scrittura.

Questo concetto - di una replica da un principale o attivo secondario, ossia viene definito il ruolo di replica.

### <a name="replica-roles"></a>Ruoli di replica
Il ruolo di una replica viene utilizzato per gestire il ciclo di vita dello stato gestito da tale replica. Una replica il cui ruolo è principali servizi richieste di lettura. Inoltre, servizi di richieste di scrittura, aggiornamento dello stato e la replica delle modifiche al database secondario attivo nel proprio gruppo di replica. Il ruolo di un secondario attivo è per ricevere le modifiche di stato che ha replicato replica primaria e aggiornare la visualizzazione dello stato.

>[AZURE.NOTE] Modelli di programmazione di livello superiore, ad esempio il [Framework degli operatori affidabile](service-fabric-reliable-actors-introduction.md) estraggono il concetto di ruolo duplicata dello sviluppatore del.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui concetti di tessuti servizio, vedere le operazioni seguenti:

- [Scalabilità dei servizi di infrastruttura di servizio](service-fabric-concepts-scalability.md)

- [Servizi di infrastruttura di servizio partizioni](service-fabric-concepts-partitioning.md)

- [Definizione e la gestione dello stato](service-fabric-concepts-state.md)
