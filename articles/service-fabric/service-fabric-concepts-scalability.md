<properties
   pageTitle="Scalabilità dei servizi di infrastruttura di servizio | Microsoft Azure"
   description="Descrive come ridurre i servizi di infrastruttura di servizio"
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

# <a name="scaling-service-fabric-applications"></a>Ridimensionamento delle applicazioni di servizio tessuti
Azure tessuti servizio semplifica delle applicazioni scalable bilanciamento del carico servizi, le partizioni e repliche in tutti i nodi in un cluster. In questo modo utilizzo delle risorse massimo.

Scala elevata per le applicazioni di servizio tessuti può essere ottenuta in due modi:

1. Scala a livello partizione

2. Scala a livello di nome servizio

## <a name="scaling-at-the-partition-level"></a>Scala a livello partizione
Servizio tessuti supporta partizioni un singolo servizio in più partizioni più piccole. [Partizione Panoramica](service-fabric-concepts-partitioning.md) fornisce informazioni sui tipi di schemi di partizioni supportate. Repliche di ogni partizione sono suddivisi in nodi in un cluster. Valutare la possibilità di un servizio che utilizza una combinazione di partizione nell'intervallo con una chiave bassa pari a 0, una chiave elevata di 99 e quattro partizioni. In un cluster di tre nodi, il servizio potrebbe essere disposti con quattro che condividono le risorse in ogni nodo, come illustrato di seguito:

![Layout delle partizioni con tre nodi](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentare il numero dei nodi consente tessuti servizio utilizzare le risorse sui nuovi nodi spostando alcuni replica nodi vuoti. Aumentando il numero dei nodi a quattro, il servizio è ora disponibile tre repliche in esecuzione su ogni nodo (delle partizioni diverse), consentendo per prestazioni e migliore utilizzo delle risorse.

![Layout delle partizioni con quattro nodi](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Scala a livello di nome servizio
Un'istanza del servizio è una specifica istanza del nome di un'applicazione e il nome di un tipo di servizio (vedere [ciclo di vita applicazione di servizio tessuti](service-fabric-application-lifecycle.md)). Durante la creazione di un servizio, è necessario specificare la partizione di schema (vedere [partizione tessuti servizio servizi](service-fabric-concepts-partitioning.md)) da utilizzare.

Il primo livello della scala è il nome del servizio. È possibile creare nuove istanze di un servizio, con diversi livelli delle partizioni, come il precedente istanze di servizio diventare occupate. In questo modo nuovi utenti che effettuano servizio utilizzare istanze del servizio non disponibile meno piuttosto che quelle aumenta.

Un'opzione per aumentare la capacità, come conteggi partizione crescente o decrescente, consiste nel creare una nuova istanza di servizio con una nuova combinazione di partizione. Viene aggiunto complessità, tuttavia, come richiedere client necessario sapere quando e come usare il servizio in questione in modo leggermente diverso.

### <a name="example-scenario-embedded-dates"></a>Scenario di esempio: incorporato date
Uno scenario, è possibile utilizzare le informazioni sulla data come parte del nome del servizio. Ad esempio, è possibile utilizzare un'istanza del servizio con un nome specifico per tutti i clienti che aggiungono 2013 e un nome diverso per i clienti che hanno eseguito il join nel 2014. Questo schema di denominazione consente di aumentare a livello di programmazione i nomi in base alla data (come approcci 2014, l'istanza del servizio di 2014 è possibile creare su richiesta).

Questo approccio è tuttavia, basato sul client utilizzando le informazioni di denominazione specifiche di un'applicazione che non rispetta l'ambito della knowledge servizio tessuti.

- *Utilizzando una convenzione di denominazione*: In 2013, quando l'applicazione passa live, si crea un servizio denominato tessuti: app/service2013. Accanto al secondo trimestre del 2013, si crea un altro servizio, denominato tessuti: app/service2014. Entrambi i servizi sono dello stesso tipo di servizio. In questo caso, il client dovrà utilizzare logica per costruire il nome del servizio appropriato in base all'anno.

- *Utilizzo di un servizio di ricerca*: un altro modello è fornire un servizio di ricerca secondaria, che può fornire il nome del servizio per una chiave desiderata. Nuove istanze del servizio possono essere create quindi il servizio di ricerca. Il servizio di ricerca non conserva i dati dell'applicazione, solo i dati relativi i nomi dei servizi che ha creato. In questo modo, per l'esempio in base anno precedente, il client prima di tutto contattare il servizio di ricerca per trovare il nome del servizio di gestione dei dati per un dato anno e quindi usare il nome del servizio per eseguire questa operazione effettiva. Il risultato di ricerca prima può essere memorizzati nella cache.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui concetti di tessuti servizio, vedere le operazioni seguenti:

- [Disponibilità dei servizi di infrastruttura di servizio](service-fabric-availability-services.md)

- [Partizione i servizi di infrastruttura di servizio](service-fabric-concepts-partitioning.md)

- [Definizione e la gestione dello stato](service-fabric-concepts-state.md)
