<properties 
    pageTitle="Panoramica di servizi multimediali Azure e scenari comuni | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica dei servizi multimediali di Windows Azure" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Panoramica di servizi multimediali Azure e scenari comuni

Servizi multimediali di Microsoft Azure è una piattaforma extensible basata su cloud che consente agli sviluppatori di applicazioni multimediali scalable gestione e la distribuzione. Servizi multimediali di si basa su API REST che consentono di caricare, archiviare, codificare e creare un pacchetto contenuto audio o video per il recapito della trasmissione su richiesta e live a vari client (ad esempio TV, PC e dispositivi mobili) in modo sicuro.

È possibile creare flussi di lavoro-to-end completamente tramite servizi multimediali. È anche possibile scegliere di utilizzare componenti di terze parti per alcune parti del flusso di lavoro. Ad esempio codificare utilizzando un codificatore di terze parti. Quindi caricare, proteggere, creare un pacchetto, offrire servizi di supporto.

È possibile scegliere di flusso del contenuto live o distribuire contenuto su richiesta. In questo argomento viene scenari comuni per l'esecuzione del contenuto [live](media-services-overview.md#live_scenarios) o [su richiesta](media-services-overview.md#vod_scenarios). L'argomento si collega anche agli altri argomenti pertinenti.

## <a name="sdks-and-tools"></a>Strumenti e SDK

Per creare soluzioni servizi di supporto, è possibile usare:

- [Servizi multimediali di API REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno dei client disponibili SDK:
- [Servizi multimediali azure SDK per .NET](https://github.com/Azure/azure-sdk-for-media-services)
- [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java)
- [Azure SDK PHP](https://github.com/Azure/azure-sdk-for-php),
- [Servizi multimediali Azure per Node](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Si tratta di una versione di un SDK Node non Microsoft. Gestita da una community e non dispone di una copertura 100% di APIs AMS).
- Strumenti esistenti:
- [Portale di Azure](https://portal.azure.com/)
- [Media di Azure-esplorazione di servizi](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) è un Windows Form / c# applicazione per Windows)

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

È possibile visualizzare AMS percorsi di apprendimento:

- [AMS Live flusso del flusso di lavoro](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS flusso del flusso di lavoro su richiesta](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare servizi multimediali di Windows Azure, è necessario disporre le operazioni seguenti:

3. Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com).
2. Un account di servizi multimediali di Windows Azure. Usare il portale di Azure, .NET o API REST per creare account servizi multimediali di Windows Azure. Per ulteriori informazioni, vedere [Creazione di Account](media-services-portal-create-account.md).
3. (Facoltativo) Configurare l'ambiente di sviluppo. Scegliere .NET o API REST per l'ambiente di sviluppo. Per ulteriori informazioni, vedere [configurare ambiente](media-services-dotnet-how-to-use.md).

Inoltre, informazioni su come connettersi a livello di programmazione [Connetti](media-services-dotnet-connect-programmatically.md).
4. (Scelta consigliata) Allocare una o più unità di scala. Si consiglia di assegnare una o più unità di scala per le applicazioni nell'ambiente di produzione.   Per ulteriori informazioni, vedere [Gestione streaming i punti finali](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Concetti e informazioni generali

Per concetti servizi multimediali di Windows Azure, vedere [concetti](media-services-concepts.md).

Per una serie di procedure che consente di accedere a tutti i componenti principali di servizi multimediali di Windows Azure, vedere [Esercitazioni passo-passo servizi multimediali di Azure](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Questa serie è una panoramica dei concetti e utilizza lo strumento AMSE per illustrare AMS attività. Si noti che AMSE è uno strumento di Windows. Questo strumento supporta la maggior parte delle attività che è possibile ottenere a livello di programmazione con [AMS SDK per .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java)o [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>L'esecuzione di elementi multimediali richiesta con servizi multimediali di Windows Azure: scenari comuni e attività

In questa sezione vengono descritti scenari comuni e vengono forniti collegamenti ad argomenti pertinenti. Il diagramma seguente illustra le parti principali della piattaforma servizi multimediali coinvolti in l'esecuzione di contenuto su richiesta. 

![Flusso di lavoro VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteggere il contenuto di spazio di archiviazione e in più assicurano multimediali in chiaro (non crittografata)

1. Caricare un file di alta qualità mezzanine in una risorsa.
    
    Si consiglia di applicare l'opzione crittografia lo spazio di archiviazione per le risorse per proteggere il contenuto durante il caricamento e al resto in archiviazione.
 
1. La codifica in una serie di file di velocità adattata MP4. 

    Si consiglia di applicare l'opzione crittografia lo spazio di archiviazione per la risorsa di output per proteggere il contenuto inattivi.
    
1. Configurare i criteri di recapito bene (utilizzato da imballaggio dinamico). 
    
    Se la risorsa è lo spazio di archiviazione crittografato, è **necessario** configurare i criteri di recapito della risorsa. 

1. Pubblicare la risorsa mediante la creazione di un indicatore di posizione su richiesta.

    Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.

1. Flusso contenuto pubblicato.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteggere il contenuto di spazio di archiviazione, deliver multimediali in modo dinamico crittografato  

Per utilizzare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata flusso sull'endpoint flusso da cui si desidera eseguire il flusso contenuto crittografato.

1. Caricare un file di alta qualità mezzanine in una risorsa. Applicare l'opzione crittografia lo spazio di archiviazione per la risorsa.
1. La codifica in una serie di file di velocità adattata MP4. Applicare l'opzione crittografia lo spazio di archiviazione per la risorsa di output.
1. Creare la crittografia chiave contenuto per risorse che si desidera crittografare in modo dinamico durante la riproduzione.
2. Configurare i criteri di autorizzazione chiave contenuto.
1. Configurare i criteri di recapito bene (utilizzato da imballaggio dinamico e dinamico crittografia).
1. Pubblicare la risorsa mediante la creazione di un indicatore di posizione su richiesta.
1. Flusso contenuto pubblicato. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Consente di elementi multimediali Analitica derivano approfondimenti i video 

Media Analitica è un insieme di componenti sintesi e riconoscimento vocale e visione che rendono più semplice per le aziende e alle aziende di derivano approfondimenti dai propri file video. Per ulteriori informazioni, vedere [Panoramica di Analitica servizi multimediali di Azure](media-services-analytics-overview.md).

1. Caricare un file di alta qualità mezzanine in una risorsa.
2. Utilizzare uno dei seguenti servizi multimediali Analitica per elaborare i video seguenti:
    
    - **Indicizzatore** - [video processo con Azure Media indicizzatore 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** : [i file multimediali Hyperlapse con Hyperlapse Media Azure](media-services-hyperlapse-content.md)
    - **Rilevamento** : [Rilevamento di Azure Media Analitica](media-services-motion-detection.md).
    - **Rilevamento nominale ed emozioni nominale** – [nominale e rilevamento emozioni per Azure Media Analitica](media-services-face-and-emotion-detection.md).
    - **Riepilogo video** : [Utilizzare Azure Media Video anteprime per creare un riepilogo di Video](media-services-video-summarization.md)
3. Processori multimediali Media Analitica generano file JSON o MP4. Se un processore media prodotto estensione MP4, è possibile scaricare gradualmente il file. Se un processore media prodotto un file JSON, è possibile scaricare il file dallo spazio di archiviazione blob Azure. 


###<a name="deliver-progressive-download"></a>Eseguire il download graduale 

1. Caricare un file di alta qualità mezzanine in una risorsa.
1. La codifica in un unico file MP4.
1. Pubblicare la risorsa mediante la creazione di un indicatore di posizione su richiesta o SA.

    Se Usa locator su richiesta, assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si intende gradualmente scaricare il contenuto.

    Se Usa locator SA, il contenuto viene scaricato dallo spazio di archiviazione blob Azure. In questo caso, non è necessario avere streaming unità riservata.
  
1. Scaricare gradualmente il contenuto.

##<a id="live_scenarios"></a>L'esecuzione di eventi di flussi in diretta con i servizi multimediali di Azure

Quando si lavora con Live Streaming comunemente coinvolge i componenti seguenti:

- Fotocamera utilizzato per trasmettere un evento.
- Codificatore video live che converte i segnali dalla fotocamera a flussi che vengono inviati a un servizio di trasmissione live.

Facoltativamente, è possibile ora live più sincronizzata Encoder. Per alcuni critiche live eventi che disponibilità molto elevato e qualità dell'esperienza, è consigliabile utilizzare Encoder ridondanti attivo attivo con l'ora synchronizationto ottenere di failover senza perdita di dati.
- Servizio di trasmissione live che consente di eseguire le operazioni seguenti:

- caricamento dei contenuti live utilizzando diversi protocolli flussi live (ad esempio RTP o Smooth Streaming)
- codificare, facoltativamente, il flusso in velocità adattata flusso
- visualizzare in anteprima il flusso in diretta
- registrare e archiviare contenuti acquisiti per trasmettere successive (punti)
- fornire il contenuto tramite protocolli flussi comuni (ad esempio MPEG trattino, uniforme, HLS, unità 10K) direttamente ai clienti o a un contenuto rete (CDN) per la distribuzione.


**Servizi multimediali di Microsoft Azure** (AMS) consente di acquisire, codificare, visualizzare in anteprima, archiviare e fornire il contenuto di flusso in diretta.

Durante l'esecuzione del contenuto ai clienti l'obiettivo è fornire un video di alta qualità a vari dispositivi in condizioni di rete diversa. Per occuparsi di condizioni di qualità e di rete, utilizzare live Encoder per codificare il flusso al flusso video (velocità adattata) con più velocità.  Per occuparsi di streaming su diversi dispositivi, utilizzare servizi multimediali [imballaggio dinamico](media-services-dynamic-packaging-overview.md) per dinamicamente assemblare nuovamente il flusso di altri protocolli. Servizi multimediali di supporta il recapito della velocità di adattamento seguenti streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Servizi multimediali di Windows Azure, **canali**, **applicazioni**e **StreamingEndpoints** gestire tutte le funzionalità trasmissione live inclusi acquisizione, formattazione, DVR, sicurezza, scalabilità e ridondanza.

Un **canale** rappresenta una pipeline di elaborazione del contenuto di flusso live. Un canale può ricevere un live flussi di input nei modi seguenti:

- Un locale live codificatore Invia con più velocità **RTP** o **Smooth Streaming** (frammentati MP4) per il canale è configurato per il recapito della **pass-through** . Il recapito **pass-through** è quando flussi acquisiti attraversano s **canale**senza ulteriori elaborazioni. È possibile utilizzare le seguenti Encoder live che generano multi-velocità Smooth Streaming: elementare, lingua inglese, Cisco.  Le seguenti Encoder live output RTP: Adobe Flash Live Telestream Wirecast e Tricaster trascodificatori.  Un codificatore live anche possibile inviare un flusso di velocità singola a un canale che non è abilitata per la codifica live, ma che non è consigliabile. Quando richiesto, al cliente servizi multimediali di un flusso.

>[AZURE.NOTE] Uso di un metodo pass-through è il modo più economico a live streaming quando si fa più eventi per un lungo periodo di tempo e hanno già investito in Encoder locale. Visualizzare i dettagli [prezzi](/pricing/details/media-services/) .

- Un codificatore live locale invia un flusso di velocità singola per il canale è abilitato per eseguire la codifica live con i servizi di supporto in uno dei formati seguenti: RTP (MPEG-TS), RTP o Smooth Streaming (frammentato MP4). Il canale avrà la codifica live di in entrata velocità singolo flusso in un flusso di video con più velocità (adattata). Quando richiesto, al cliente servizi multimediali di un flusso.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilizzo di canali che ricevono flusso in diretta con più velocità da Encoder locale (pass-through)

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolti nel flusso di lavoro **pass-through** .

![Flusso di lavoro Live][live-overview2]

Per ulteriori informazioni, vedere [utilizzo di canali che Multi ricezione-velocità flusso Live da Encoder locale](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilizzo dei canali abilitati per eseguire la codifica live con servizi multimediali di Windows Azure

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolti in Live Streaming del flusso di lavoro in cui un canale è abilitato per eseguire la codifica live con i servizi di supporto.

![Flusso di lavoro Live][live-overview1]

Per ulteriori informazioni, vedere [uso di canali che sono abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Utilizzo di contenuto

Servizi multimediali di Azure sono disponibili gli strumenti necessari per creare le applicazioni di Windows Media player accattivanti e dall'aspetto dinamico client per la maggior parte delle piattaforme, tra cui: iOS dispositivi, dispositivi Android, Windows, Windows Phone, Xbox e Set-top caselle. L'argomento seguente vengono forniti collegamenti a SDK e Framework lettore che è possibile utilizzare per sviluppare le proprie applicazioni client in grado di utilizzare multimediali da servizi di supporto.

[Sviluppo di applicazioni lettore di Video](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>Attivazione di Azure CDN

Servizi multimediali di supporta l'integrazione con Azure CDN. Per informazioni su come abilitare CDN Azure, vedere [come gestire gli endpoint Streaming in un Account di servizi di supporto](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Ridimensionamento di un account di servizi multimediali

È possibile ridimensionare **Servizi multimediali di** specificando il numero di **Unità riservate Streaming** e **Codifica unità riservate** che si desidera effettuare il provisioning con l'account.

È anche possibile ridimensionare l'account di servizi multimediali aggiungendo gli account di archiviazione. Ogni account di archiviazione è limitato a 500 TB. Per espandere lo spazio di archiviazione superare i limiti di impostazione predefinita, è possibile scegliere di allegare più account di archiviazione per un singolo account servizi multimediali.

[Questo](media-services-portal-scale-streaming-endpoints.md) argomento fornisce collegamenti a argomenti pertinenti.

##<a name="support"></a>Supporto tecnico

[Supporto di Azure](https://azure.microsoft.com/support/options/) offre opzioni di supporto per Azure, inclusi i servizi di supporto.

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Contratto di servizio (contratto di servizio)

- Per la codifica servizi multimediali è garantire 99,9% di disponibilità delle transazioni API REST.
- Per Streaming, è correttamente risponde alle richieste con una garanzia di disponibilità 99,9% per i contenuti multimediali esistenti quando viene acquistata almeno un'unità di trasmissione.
- Per i canali Live, si garantisce che esegue canali connettività esterna almeno 99,9% del tempo.
- Per la protezione del contenuto è garantire che si verranno correttamente soddisfare le richieste di chiave almeno 99,9% del tempo.
- Per indicizzatore, è correttamente risponde alle richieste di attività indicizzatore elaborate con un riservato codifica unità 99,9% del tempo.

Per ulteriori informazioni, vedere [Microsoft Azure contratto di servizio](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
