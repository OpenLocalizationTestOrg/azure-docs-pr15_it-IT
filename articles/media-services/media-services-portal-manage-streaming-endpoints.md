<properties 
    pageTitle="Gestire i punti finali flussi con il portale di Azure | Microsoft Azure" 
    description="In questo argomento viene illustrato come gestire i punti finali flussi con il portale di Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gestire i punti finali flussi con il portale di Azure

## <a name="overview"></a>Panoramica

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

In servizi multimediali di Microsoft Azure, un **Endpoint Streaming** rappresenta un servizio di trasmissione in grado di fornire contenuto direttamente a un'applicazione di Windows Media player client o a un contenuto rete (CDN) per la distribuzione. Servizi multimediali vengono forniti anche integrazione di Azure CDN. Il flusso in uscita da un servizio StreamingEndpoint può essere un flusso live o un video su richiesta bene nell'account servizi multimediali.

Inoltre, è possibile controllare la capacità del servizio di trasmissione Endpoint per gestire l'aumento della larghezza di banda esigenze modificando l'unità di trasmissione. Si consiglia di assegnare una o più unità di scala per le applicazioni nell'ambiente di produzione. Unità di trasmissione offrire capacità di uscita dedicato che può essere acquistata con incrementi di 200 MB/s e funzionalità aggiuntiva, che include: [imballaggio dinamico](media-services-dynamic-packaging-overview.md), l'integrazione di rete CDN e configurazione avanzata.

>[AZURE.NOTE]Vengono addebitate solo quando l'Endpoint Streaming è in stato di esecuzione.

In questo argomento fornisce una panoramica delle principali funzionalità che sono disponibili i punti finali flusso. Inoltre, l'argomento viene illustrato come utilizzare il portale di Azure per gestire i punti finali flussi. Per informazioni su come ridimensionare l'endpoint di flusso, consultare [questo](media-services-portal-scale-streaming-endpoints.md) argomento.

## <a name="start-managing-streaming-endpoints"></a>Iniziare a gestire i punti finali flussi

Per iniziare a gestire i punti finali flussi per il proprio account, eseguire le operazioni seguenti.

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2. Nella finestra **Impostazioni** selezionare **i punti finali flusso**.

    ![Endpoint trasmissione](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Aggiungi/Elimina un endpoint di flusso

Per aggiungere/eliminare endpoint trasmissione tramite il portale di Azure, eseguire le operazioni seguenti:

1. Per aggiungere un endpoint di flusso, fare clic sul **punto finale +** nella parte superiore della pagina. 
2. Per eliminare un endpoint di flusso, premere **Elimina** . 

    Il valore predefinito streaming endpoint non può essere eliminato.
2. Fare clic sul pulsante **Avvia** per avviare l'endpoint di trasmissione.

    ![Endpoint trasmissione](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Per impostazione predefinita è possibile impostare fino a due punti finali flussi. Se è necessario richiedere informazioni, vedere [le quote e limitazioni](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>La configurazione dell'Endpoint trasmissione

Streaming Endpoint consente di configurare le proprietà seguenti quando si dispone di almeno 1 unità in scala: 

- Controllo dell'accesso
- Controllo della cache
- Tra i criteri di accesso del sito

Per informazioni dettagliate su queste proprietà, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

È possibile configurare endpoint flusso eseguendo le operazioni seguenti:

1. Selezionare l'endpoint di flusso che si desidera configurare.
1. Fare clic su **Impostazioni**.
  
Segue una breve descrizione dei campi.

![Endpoint trasmissione](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Criteri di cache massimo: utilizzato per configurare la durata della cache per risorse servita tramite questo endpoint flusso. Se è impostato alcun valore, viene utilizzato il valore predefinito. I valori predefiniti possono essere definiti anche direttamente nell'archiviazione Azure. Se Azure CDN è abilitato per l'endpoint di trasmissione, non impostare il valore di criterio della cache di inferiore a 600 secondi.  

2. Indirizzi IP consentiti: consente di specificare gli indirizzi IP consentiti per connettersi all'endpoint flusso pubblicato. Se nessun indirizzo IP specificato, qualsiasi indirizzo IP è possibile connettersi. Indirizzi IP possono essere specificati come un solo indirizzo IP (ad esempio, ' 10.0.0.1"), un intervallo di indirizzi IP mediante un indirizzo IP e una maschera di subnet CIDR (ad esempio, ' 10.0.0.1/22') o un intervallo di indirizzi IP mediante indirizzo IP e una maschera con punti subnet decimale (ad esempio, ' 10.0.0.1(255.255.255.0)').

3. Configurazione per l'autenticazione di intestazione firma Akamai: consente di specificare configurazione richiesta di autenticazione di intestazione di firma del server Akamai. Scadenza è in formato UTC.



##<a id="enable_cdn"></a>Attivare l'integrazione di rete CDN di Azure

È possibile specificare per attivare l'integrazione di Azure CDN per un Endpoint Streaming (disattivata per impostazione predefinita.)

Per impostare l'integrazione di Azure CDN su true:

- L'endpoint di trasmissione deve avere almeno un'unità di trasmissione. Se in seguito si desidera impostare le unità della scala a 0, è necessario prima disattivare l'integrazione di rete CDN. Per impostazione predefinita quando si crea un nuovo flusso unità flusso un endpoint viene impostata automaticamente.

- L'endpoint di trasmissione deve essere interrotto. Dopo la rete CDN viene abilitato, è possibile iniziare l'endpoint di trasmissione. 

Potrebbe richiedere fino a 90 min per l'integrazione di Azure CDN ottenere abilitate.  Bastano fino a due ore essere attivo in tutti i CDN estrae le modifiche.

È abilitata l'integrazione di rete CDN in tutte le aree di dati di Azure: Contattaci ovest Contattaci est, Nord Europa, Europa occidentale, Giappone ovest, Giappone Oriente, Asia sudorientale e Asia orientale.

Dopo aver abilitato, la configurazione di **Controllo di accesso** viene disabilitata.

![Endpoint trasmissione](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Integrazione di servizi multimediali Azure con Azure CDN è implementata nella **Rete CDN di Azure da Verizon**.  Se si desidera utilizzare **CDN Azure da Akamai** per servizi multimediali di Windows Azure, è necessario [configurare manualmente il punto finale](../cdn/cdn-create-new-endpoint.md).  Per ulteriori informazioni sulle funzionalità di rete CDN di Azure, vedere la [Panoramica CDN](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Altre considerazioni

- Quando CDN è abilitato per un endpoint di flusso, client non è possibile richiedere il contenuto direttamente dall'origine. Se è necessaria la possibilità di provare il contenuto con o senza CDN, è possibile creare un altro endpoint di flusso che non è abilitato CDN.
- Il flusso di endpoint hostname rimane inalterato dopo l'abilitazione CDN. Non è necessario apportare le modifiche per il flusso di lavoro di servizi di supporto dopo l'attivazione CDN. Ad esempio, se il flusso di endpoint hostname strasbourg.streaming.mediaservices.windows.net, dopo aver attivato CDN, viene utilizzato il nome host stesso esatto.
- Per gli endpoint flussi nuovi, è possibile abilitare la rete CDN semplicemente creando un nuovo endpoint; per gli endpoint flussi esistenti, è necessario interrompere l'endpoint e quindi attivare la rete CDN.
 

Per ulteriori informazioni, vedere [integrazione annuncio servizi multimediali di Windows Azure con Azure CDN (rete CDN)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
