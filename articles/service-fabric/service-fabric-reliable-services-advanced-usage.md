<properties
   pageTitle="Utilizzo dei servizi affidabile avanzato | Microsoft Azure"
   description="Le informazioni sull'utilizzo avanzato di servizi di affidabile dell'infrastruttura di servizio per ottenere una maggiore flessibilità nel provider di servizi."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Utilizzo dei servizi affidabile modello di programmazione avanzato
Azure tessuti servizio semplifica la scrittura e la gestione dei servizi e senza stati affidabili. Questa Guida verrà illustrate avanzati utilizzi dei servizi affidabile per ottenere maggiore flessibilità e controllo tramite il provider di servizi. Prima di leggere questa Guida, acquisire familiarità con [I servizi affidabile modello di programmazione](service-fabric-reliable-services-introduction.md).

Informazioni sullo stato e senza stati servizi presentano due punti di ingresso principale per il codice utente:

 - `RunAsync`è un punto di ingresso generico per il codice di servizio.
 - `CreateServiceReplicaListeners`e `CreateServiceInstanceListeners` è per l'apertura di listener di comunicazione per le richieste dei client.
 
Nella maggior parte dei servizi, questi punti di due ingresso sono sufficienti. In casi quando un maggiore controllo del ciclo di vita del servizio è necessario, gli eventi del ciclo di vita aggiuntive sono disponibili.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo di vita istanza senza informazioni sullo stato del servizio

Ciclo di vita del servizio senza informazioni sullo stato è molto semplice. Un servizio senza informazioni sullo stato solo può essere aperto, chiuso o interrotta. `RunAsync`in un servizio senza informazioni sullo stato viene eseguito quando un'istanza del servizio viene aperto e annullata quando un'istanza del servizio è stato chiuso o interrotta. 

Sebbene `RunAsync` sarà sufficiente in quasi tutti i casi, Apri, chiudere e gli eventi di interruzione in un servizio senza informazioni sullo stato sono inoltre disponibili:

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync viene chiamato quando l'istanza senza informazioni sullo stato del servizio deve essere utilizzato. È possibile avviare attività di inizializzazione "Extended" servizio al momento.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync è chiamato quando l'istanza senza informazioni sullo stato del servizio sta per essere la corretta chiusura. Questo può verificarsi quando si sta aggiornando codice del servizio, l'istanza del servizio è stato spostato a causa di bilanciamento del carico o si verifica un errore temporaneo. OnCloseAsync può essere utilizzato per in modo sicuro chiudere tutte le risorse, interrompere qualsiasi elaborazione in background, completare il salvataggio stato esterno o chiudere le connessioni esistenti.

- `void OnAbort()`
  OnAbort è chiamato quando l'istanza senza informazioni sullo stato del servizio forzata chiusura. Si tratta in genere quando si verifica un errore permanente sul nodo o tessuti servizio non è possibile gestire in modo affidabile del ciclo di vita dell'istanza del servizio a causa di errori interni.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo di vita del servizio informazioni sullo stato replica

Ciclo di vita della replica di informazioni sullo stato del servizio è molto più complessa di un'istanza senza informazioni sullo stato del servizio. Per aprire, chiudere e interrompere eventi, inoltre una replica di servizio informazioni sullo stato subisce modifiche ai ruoli per tutta la durata. Quando una replica di servizio informazioni sullo stato Cambia ruolo, il `OnChangeRoleAsync` evento:

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync viene chiamato quando la replica di informazioni sullo stato del servizio in fase di modifica ruolo, ad esempio per primario o secondario. Repliche principale vengono assegnate lo stato di scrittura (sono consentiti per creare e scrivere alle raccolte affidabile). Repliche secondarie figurano stato di lettura (solo leggere da raccolte affidabile esistente). La maggior parte delle operazioni in un servizio informazioni sullo stato vengono eseguite in replica principale. Repliche secondarie possono eseguire la convalida di sola lettura, generazione di report, il data mining o altri processi di sola lettura.

In un servizio informazioni sullo stato replica primaria ha accesso in scrittura allo stato e pertanto in genere quando il servizio effettua il lavoro effettivo. Il `RunAsync` metodo in un servizio informazioni sullo stato viene eseguito solo quando la replica di informazioni sullo stato del servizio è principale. Il `RunAsync` metodo è stata annullata quando ruolo della replica principale cambia lontano da principale, oltre che durante gli eventi di chiusura e interruzione. 

Utilizzo di `OnChangeRoleAsync` evento consente di svolgere un'attività in base al ruolo di replica anche in risposta alla modifica del ruolo.

Un servizio informazioni sullo stato vengono forniti anche gli stessi eventi del ciclo di vita quattro un servizio senza informazioni sullo stato, con la stessa semantica e casi di utilizzo:

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Passaggi successivi
Gli argomenti più avanzati relativi alla struttura del servizio, vedere gli articoli seguenti:

- [Configurazione dei servizi affidabili informazioni sullo stato](service-fabric-reliable-services-configuration.md)

- [Introduzione di integrità servizio tessuti](service-fabric-health-introduction.md)

- [Uso di report di stato di sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configurazione dei servizi con il servizio tessuti Cluster gestore delle risorse](service-fabric-cluster-resource-manager-configure-services.md)
