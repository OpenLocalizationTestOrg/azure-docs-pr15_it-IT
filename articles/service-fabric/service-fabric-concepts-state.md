<properties
   pageTitle="Definizione e la gestione dello stato | Microsoft Azure"
   description="Come definire e gestire lo stato del servizio in tessuti servizio"
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

# <a name="service-state"></a>Stato del servizio
**Stato del servizio** fa riferimento ai dati che è necessario il servizio per poter funzionare. Include le strutture di dati e le variabili che il servizio di legge e scrive lo svolgimento delle attività.

Si consideri ad esempio un servizio semplice calcolatrice. Questo servizio accetta due numeri e restituisce la somma. Si tratta di un servizio puramente senza informazioni sullo stato che non contiene dati associati.

Si consideri ora la stessa calcolatrice ma oltre elaborazione somma, ha anche un metodo per restituire la somma ultima che è calcolato. Questo servizio è ora con informazioni sullo stato - contiene uno stato che la scrittura (quando calcola una somma nuova) e lettura dalle (quando restituisce la somma calcolata ultima).

In Azure servizio tessuti, il primo servizio si chiama un servizio senza informazioni sullo stato. Il secondo servizio si chiama un servizio informazioni sullo stato.

## <a name="storing-service-state"></a>Stato del servizio di archiviazione
Stato può essere esternalizzato o situato con codice che sta modificando lo stato. Un'esternalizzazione dello stato in genere viene eseguita mediante un database esterno o store. In questo esempio calcolatrice trattarsi di un database SQL in cui è archiviato il risultato corrente in una tabella. Ogni richiesta per calcolare la somma esegue un aggiornamento la riga corrente.

Stato possa anche essere usata con il codice per la gestione di questo codice. Informazioni sullo stato servizi di infrastruttura di servizio sono progettati con questo modello. Servizio tessuti fornisce l'infrastruttura per garantire che questo stato è disponibilità e tolleranza in caso di errore.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui concetti di tessuti servizio, vedere le operazioni seguenti:

- [Disponibilità dei servizi di infrastruttura di servizio](service-fabric-availability-services.md)

- [Scalabilità dei servizi di infrastruttura di servizio](service-fabric-concepts-scalability.md)

- [Servizi di infrastruttura di servizio partizioni](service-fabric-concepts-partitioning.md)
