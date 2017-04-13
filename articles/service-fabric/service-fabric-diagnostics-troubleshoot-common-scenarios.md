<properties
   pageTitle="Risoluzione dei problemi con la traccia di eventi | Microsoft Azure"
   description="Problemi comuni riscontrati durante la distribuzione dei servizi in Microsoft Azure servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Risolvere i problemi comuni quando si distribuisce servizi su Azure servizio infrastruttura

Quando si esegue servizi nel computer di sviluppo, è facile da usare [gli strumenti di debug Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Per i cluster remoto, [report di stato](service-fabric-view-entities-aggregated-health.md) sono sempre un buon punto di partenza. Metodi più semplici per accedere a questi report sono tramite PowerShell o [SFX](service-fabric-visualizing-your-cluster.md). In questo articolo si presuppone che si sta eseguendo il debug di un cluster remoto e di base su come usare uno di questi strumenti.

##<a name="application-crash"></a>Arresto anomalo dell'applicazione
La "partizione è inferiore al conteggio di replica o l'istanza di destinazione" report è molto probabile che subisca un arresto anomalo del servizio. Per scoprire dove un arresto anomalo del servizio consente di accedere leggermente ulteriori indagini. Quando il servizio è in esecuzione in scala, la migliore amico sarà un insieme di tracce ben strutturato.  È consigliabile provare [Azure diagnostica](service-fabric-diagnostics-how-to-setup-wad.md) per la raccolta di tali tracce e utilizzo di una soluzione ad esempio la [Ricerca flessibile](service-fabric-diagnostic-how-to-use-elasticsearch.md) per la visualizzazione e la ricerca le tracce.

![SFX partizione integrità](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Durante l'inizializzazione del servizio o attore
Le eventuali eccezioni prima che il tipo di servizio sia inizializzazione causa l'arresto anomalo del processo. Per questi tipi di anomalo, il registro eventi applicazione illustra l'errore dal servizio.
Queste sono le eccezioni più comuni per visualizzare prima che il servizio venga inizializzato.

***System.IO.FileNotFoundException***

Questo errore è spesso a causa di mancanti dipendenze assembly. Verificare la proprietà CopyLocal in Visual Studio o global assembly cache per il nodo.

***COMException***
 *in System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Indica che il nome del tipo di servizio registrato non corrisponde a manifesto servizio.

[Azure diagnostica](service-fabric-diagnostics-how-to-setup-wad.md) può essere configurato per caricare automaticamente il registro eventi di applicazioni per tutti i nodi.

###<a name="runasync-or-onactivateasync"></a>RunAsync() o OnActivateAsync()
Se si verifica l'arresto anomalo durante l'inizializzazione o l'esecuzione di un tipo di servizio registrati o attore, l'eccezione viene rilevata da Azure servizio tessuti. È possibile visualizzare tali dal provider EventSource descritti nella sezione "Passaggi successivi".

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su diagnostica esistente fornita dal servizio tessuti:

* [Diagnostica operatori affidabile](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostica servizi affidabile](service-fabric-reliable-services-diagnostics.md)
