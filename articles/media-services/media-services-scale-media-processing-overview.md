<properties
    pageTitle="Ridimensionamento panoramica dell'elaborazione Media | Microsoft Azure"
    description="In questo argomento viene fornita una panoramica dell'elaborazione di ridimensionamento multimediali con servizi multimediali di Windows Azure."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Ridimensionamento panoramica dell'elaborazione di elementi multimediali

Questa pagina offre una panoramica su come e perché scalare elaborazione di elementi multimediali. 

## <a name="overview"></a>Panoramica

Un account di servizi multimediali è associato a un tipo di unità riservate, che determina la velocità con cui vengono elaborato supporto di attività di elaborazione. È possibile scegliere tra i tipi seguenti di unità riservate: **S1**, **S2**o **S3**. Ad esempio lo stesso processo di codifica viene eseguito più velocemente quando si utilizza il confronto di tipo di unità **S2** riservato al tipo di **S1** . Per ulteriori informazioni, vedere [Tipi di unità riservati](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oltre a specificare il tipo di unità riservate, è possibile specificare per eseguire il provisioning di account con unità riservata. Il numero di unità riservate provisioning determina il numero di elementi multimediali attività che possono essere elaborati contemporaneamente in un determinato account. Ad esempio, se l'account ha cinque unità riservata, quindi attività cinque multimediali verrà eseguita contemporaneamente purché come sono disponibili le attività di elaborazione. Le attività rimanenti di attesa nella coda e verranno visualizzato selezionate per l'elaborazione in sequenza termine di un'attività in esecuzione. Se un account non dispone di qualsiasi unità di misura riservato viene completato il provisioning, quindi le attività verranno selezionate in sequenza. In questo caso, il tempo di attesa tra il primo successivo le attività terminano dipenderà la disponibilità delle risorse del sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>La scelta tra tipi diversi unità riservato

Nella tabella seguente consente di prendere decisioni per la scelta tra diverse velocità di codifica. Inoltre, sono disponibili alcuni casi benchmark e offre URL sa che è possibile utilizzare per scaricare i video in cui è possibile eseguire test delle:

Scenari|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Uso di maiuscole/minuscole| Velocità singola codifica. <br/>File in SD o sotto risoluzioni, tempo non riservate a basso costo.|Singolo velocità e la codifica di più velocità.<br/>Uso normale per la codifica SD e HD. |Singolo velocità e la codifica di più velocità.<br/>Video risoluzione HD e 4 KB completi. Tempo codifica risoluzione riservato, più veloce. 
Benchmark|[File di input: durata di 5 minuti 640x360p in 29.97 fotogrammi/secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codifica a un singolo velocità file MP4, alla stessa risoluzione richiede circa 11 minuti.|[File di input: durata di 5 minuti 1280x720p in 29.97 fotogrammi/secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codifica con "H264 singola velocità 720p" preimpostate abbia circa 5 minuti.<br/><br/>Codifica con "H264 velocità più 720p" preimpostato richiede circa 11,5 minuti.|[File di input: durata di 5 minuti 1920x1080p in 29.97 fotogrammi/secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codifica con "H264 singola velocità 1080p" preimpostate accetta 2.7 minuti.<br/><br/>Codifica con "H264 velocità più 1080p" preimpostato richiede circa 5.7 minuti.

##<a name="considerations"></a>Considerazioni

>[AZURE.IMPORTANT] Esaminare le considerazioni descritti in questa sezione.  

- Unità riservata lavorano in parallelo tutti elaborazione dei file multimediali, inclusa l'indicizzazione processi utilizzando Azure Media indicizzatore.  Tuttavia, a differenza di codifica, i processi di indicizzazione non vengano elaborati più velocemente con più velocemente riservata unità.

- Se Usa pool condiviso, vale a dire senza qualsiasi unità di misura riservato, quindi le attività di codifica sono le stesse prestazioni come con S1 destinatari. C'è però nessun limite massimo per il tempo le attività possono nella coda e in qualsiasi momento, non più di una attività è sia in esecuzione.

- Interfacce di dati seguenti non offrono il tipo di unità **S2** riservato: Brasile sud ovest India, India centrale e Sud India.

- Interfacce di dati seguenti non offrono il tipo di unità **S3** riservato: Brasile a sud, India ovest, India centrale.

- Il numero massimo di unità specificate per il periodo di 24 ore viene utilizzato il calcolo del costo.


##<a name="quotas-and-limitations"></a>Le quote e limitazioni

Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [le quote e limitazioni](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Passaggio successivo

Ottenere l'attività di elaborazione media scala con una di queste tecnologie: 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portale](media-services-portal-scale-media-processing.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
