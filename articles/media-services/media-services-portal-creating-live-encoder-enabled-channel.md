<properties 
    pageTitle="Come eseguire il flusso live tramite servizi multimediali di Windows Azure per creare flussi di velocità con più con il portale di Azure | Microsoft Azure" 
    description="In questa esercitazione sono illustrati i passaggi della creazione di un canale che riceve un flusso di live singola velocità e codifica al flusso con più velocità tramite il portale di Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Come eseguire il flusso live tramite servizi multimediali di Windows Azure per creare flussi di velocità con più con il portale di Azure

> [AZURE.SELECTOR]
- [Portale](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

In questa esercitazione sono illustrati i passaggi della creazione di un **canale** che riceve un flusso di live singola velocità e codifica con più velocità flusso.

>[AZURE.NOTE]Per ulteriori informazioni relative ai canali abilitati per la codifica live, vedere [multimediali tramite servizi multimediali di Windows Azure per creare flussi di velocità con più attivi](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Scenario di trasmissione Live comune

Di seguito sono passaggi generali per la creazione di applicazioni comuni di trasmissione live.

>[AZURE.NOTE] Attualmente, la durata massima consentita consigliata di un evento live è 8 ore. Se è necessario eseguire un canale per periodi di tempo più, contattare amslived per il sito Microsoft.com.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che può trasmettere un flusso di velocità singola in uno dei seguenti protocolli: RTP, Smooth Streaming o RTP (MPEG-TS). Per ulteriori informazioni, vedere [supporto RTP servizi multimediali di Azure e Live Encoder](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Questo passaggio può anche essere eseguito dopo aver creato il canale.

1. Creare e avviare un canale. 

1. Recuperare il canale acquisizione URL. 

    L'URL di caricamento viene utilizzato dal codificatore di live per inviare il flusso per il canale.
1. Recuperare l'URL di anteprima di canale. 

    Utilizzare questo URL per verificare che il canale correttamente riceva flusso live.

3. Creare un evento/programma (che è possibile creare anche un bene). 
1. Pubblicare l'evento (che consente di creare un indicatore di posizione su richiesta per la risorsa associata).  

    Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.
1. Avviare l'evento quando si è pronti avviare il flusso e l'archiviazione.
2. Facoltativamente, il codificatore live può essere segnalato per avviare un annuncio pubblicitario. L'annuncio viene inserito nel flusso di output.
1. Interrompere l'evento ogni volta che si desidera interrompere la trasmissione e archiviazione l'evento.
1. Eliminare l'evento (e facoltativamente bene).   

##<a name="in-this-tutorial"></a>In questa esercitazione

In questa esercitazione, il portale di Azure viene utilizzato per eseguire le operazioni seguenti: 

2.  Configurare i punti finali flussi.
3.  Creare un canale che è attivato per eseguire la codifica live.
1.  È possibile ottenere l'URL di caricamento per fornire in modo da live codificatore. Il codificatore live utilizzerà questo URL per il caricamento del flusso nel canale. .
1.  Creare un evento/programma (e un bene)
1.  Pubblicare la risorsa e ottenere l'URL di flusso  
1.  Riprodurre il contenuto 
2.  Pulizia

##<a name="prerequisites"></a>Prerequisiti

Sono necessari per completare l'esercitazione.

- Per completare questa esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- Un account di servizi di supporto. Per creare un account di servizi multimediali, vedere [Creazione di Account](media-services-portal-create-account.md).
- Una webcam e un codificatore in grado di inviare un flusso di live velocità singola.

##<a name="configure-streaming-endpoints"></a>Configurare i punti finali flussi 

Servizi multimediali di fornisce imballaggio dinamico che consente di eseguire il MP4s con più velocità nei seguenti formati di trasmissione: trattino MPEG, HLS, Smooth Streaming o unità 10K, senza che sia necessario creare un nuovo pacchetto in questi formati di flusso. Con imballaggio dinamico è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali la compilazione e fornire la risposta appropriata in base a richieste da un client.

Per sfruttare imballaggio dinamico, è necessario ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto.  

Per creare e modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare il proprio account AMS.
1. Nella finestra **Impostazioni** fare clic sui **punti finali flusso**. 

2. Fare clic su predefinito streaming endpoint. 

    Viene visualizzata la finestra **Predefinito STREAMING informazioni relative all'ENDPOINT** .

3. Per specificare il numero di unità di trasmissione, spostare il cursore **Streaming unità** .

    ![Unità di trasmissione](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Fare clic sul pulsante **Salva** per salvare le modifiche.

    >[AZURE.NOTE]L'allocazione di qualsiasi unità nuova può richiedere fino a 20 minuti.

##<a name="create-a-channel"></a>Creare un canale

1. Nel [portale di Azure](https://portal.azure.com/), selezionare Servizi multimediali di e quindi scegliere il nome dell'account servizi multimediali.
2. Selezionare **Live flusso**.
3. Selezionare **Crea personalizzata**. Questa opzione consente di creare un canale è abilitato per la codifica live.

    ![Creare un canale](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Fare clic su **Impostazioni**.
    
    1.  Scegliere il tipo di canale **Codifica Live** . Questo tipo specifica che si desidera creare un canale è abilitato per la codifica live. Questo significa che la posta in arrivo velocità singolo flusso inviato al canale e codificato in un flusso di velocità multi con le impostazioni codificatore live specificato. Per ulteriori informazioni, vedere [multimediali tramite servizi multimediali di Windows Azure per creare flussi di velocità con più attivi](media-services-manage-live-encoder-enabled-channels.md). Fare clic su OK.
    2. Specificare il nome del canale.
    3. Fare clic su OK nella parte inferiore dello schermo.
    
5. Selezionare la scheda di **caricamento** .

    1. In questa pagina, è possibile selezionare un protocollo di trasmissione. Per il tipo di canale **Codifica Live** , le opzioni del protocollo validi sono:
        
        - È possibile applicare singola velocità Fragmented MP4 (Smooth Streaming)
        - È possibile applicare singola velocità RTP
        - RTP (MPEG-TS): Flusso di trasporto MPEG-2 su RTP.
        
        Per ogni protocollo spiegazione dettagliata, vedere [Live streaming tramite servizi multimediali di Windows Azure per creare flussi di velocità multi](media-services-manage-live-encoder-enabled-channels.md).
    
        Non è possibile modificare l'opzione protocollo durante il canale o relativi eventi/programmi in esecuzione. Se si richiedono protocolli diversi, è necessario creare canali distinti per ogni protocollo flusso.  

    2. È possibile applicare la restrizione IP sul caricamento. 
    
        È possibile definire gli indirizzi IP consentiti per il caricamento di un video per il canale. Consentito indirizzi IP possono essere specificati come un solo indirizzo IP (ad esempio "10.0.0.1"), un intervallo di indirizzi IP mediante un indirizzo IP e una maschera di subnet CIDR (ad esempio "10.0.0.1/22") o un intervallo di indirizzi IP mediante un indirizzo IP e una maschera con punti subnet decimale (ad esempio "10.0.0.1(255.255.252.0)').

        Se viene specificato alcun indirizzo IP e non vi è alcuna definizione regola non sarà autorizzato alcun indirizzo IP. Per consentire a qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

6. Nella scheda **Anteprima** di applicare restrizione IP in anteprima.
7. Nella scheda **codifica** , specificare il codifica predefinito. 

    Al momento l'unico sistema predefinito è possibile selezionare è **predefinito 720p**. Per specificare un predefinito personalizzato, aprire un ticket di supporto tecnico Microsoft. Immettere il nome del predefinito creato. 

>[AZURE.NOTE] Al momento l'inizio di canale potrebbe richiedere fino a 30 minuti. Ripristino canale può richiedere fino a 5 minuti.

Una volta creato il canale, è possibile fare clic sul canale e selezionare **Impostazioni** nel punto in cui è possibile visualizzare le configurazioni di canali. 

Per ulteriori informazioni, vedere [multimediali tramite servizi multimediali di Windows Azure per creare flussi di velocità con più attivi](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>Ottenere l'acquisizione URL

Dopo aver creato il canale, è possibile ottenere il caricamento di URL che si fornirà al codificatore live. Il codificatore utilizza questi URL per un flusso live di input.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Creare e gestire gli eventi

###<a name="overview"></a>Panoramica

Un canale è associato a eventi e i programmi che consentono di controllare la pubblicazione e l'archiviazione dei segmenti di un flusso in diretta. I canali di gestire gli eventi/programmi. Il canale e applicazione di una relazione è molto simile a supporti tradizionali in un canale dispone di un flusso costante di contenuto e un programma ambito è impostato su alcune evento programmato in tale canale.

È possibile specificare il numero di ore che si desidera conservare il contenuto registrato per l'evento impostando la lunghezza **Finestra archivio** . Per un massimo di 25 ore, è possibile impostare questo valore da un minimo di 5 minuti. La lunghezza della finestra archivio determina anche la maggior quantità di client ora è possibile cercare indietro nel tempo dalla posizione corrente live. Eventi possono eseguire per il periodo di tempo specificato, ma verrà continuamente eliminato contenuto che si trova dietro la lunghezza della finestra. Questo valore di questa proprietà determina anche la durata delle manifesti client espandibile.

Ogni evento è associata a una risorsa. Per pubblicare l'evento è necessario creare un indicatore di posizione su richiesta per le risorse associate. Con questo locator consentirà di generare un URL di trasmissione che è possibile fornire per i clienti.

Un canale supporta fino a tre eventi contemporaneamente in esecuzione in modo che è possibile creare più archivi di stesso flusso di posta in arrivo. In questo modo è possibile pubblicare e archiviare diverse parti di un evento in base alle esigenze. Ad esempio, l'esigenza consiste nell'archiviare 6 ore di un evento di trasmettere solo ultimi 10 minuti. A tale scopo, è necessario creare due in esecuzione simultanea evento. Un evento è impostato per archiviare 6 ore dell'evento, ma il programma non è stato pubblicato. L'altro evento è impostato per l'archiviazione di 10 minuti e questo programma viene pubblicato.

Evitare di riutilizzare programmi esistenti per i nuovi eventi. In alternativa, creare e avviare un nuovo programma per ogni evento.

Avviare un evento/programma quando si è pronti avviare il flusso e l'archiviazione. Interrompere l'evento ogni volta che si desidera interrompere la trasmissione e archiviazione l'evento. 

Per eliminare il contenuto archiviato, interrompere ed eliminare l'evento e quindi bene associate. Una risorsa non può essere eliminata se viene usata per l'evento. prima di tutto è necessario eliminare l'evento. 

Anche se si interrompere ed elimina l'evento, gli utenti sarebbero in grado di flusso il contenuto archiviato come video su richiesta per finché non si elimina la risorsa.

Se si desidera conservare il contenuto archiviato, ma non è disponibile per il flusso, eliminare locator flusso.

###<a name="createstartstop-events"></a>Creare/inizio/fine eventi

Dopo avere inserito il flusso che scorre nel canale è possibile iniziare l'evento flusso mediante la creazione di un bene, programma e Streaming Locator. Verrà archiviare il flusso e renderlo disponibile per i visualizzatori attraverso l'Endpoint di flusso. 

Esistono due modi per avviare evento: 

1. Nella pagina del **canale** , premere **Live evento** per aggiungere un nuovo evento.

    Specificare: nome dell'evento, il nome di risorsa, finestra archivio e di crittografia.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Se si è interrotto **Pubblica questo evento live ora** selezionata, verrà visualizzato creare l'evento URL di pubblicazione.
    
    È possibile premere **iniziare**, ogni volta che si è pronti per eseguire il flusso dell'evento.

    Dopo avere iniziato l'evento, è possibile premere **espressioni di controllo** per la riproduzione del contenuto.

2. In alternativa, è possibile utilizzare una scelta rapida e pulsante **Go Live** nella pagina del **canale** . Verrà creato predefinito bene, programma e Streaming Locator.

    L'evento è denominato **predefinito** e la finestra archivio è impostata su 8 ore.

È possibile guardare pubblicata evento dalla pagina **Live evento** . 

Se si sceglie di **Disattivare aria**, interromperà tutti gli eventi live. 


##<a name="watch-the-event"></a>Guardare l'evento

Per controllare l'evento, fare clic su **controllo** nel portale di Azure o copiare l'URL di trasmissione e utilizzare un lettore di propria scelta. 
 
![Creato](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Evento Live converte automaticamente gli eventi in contenuto su richiesta quando è in esecuzione.

##<a name="clean-up"></a>Pulire

Se si desidera liberare le risorse viene completato il provisioning in precedenza vengono eseguite eventi flussi, seguire la procedura seguente.

- Non inviare il flusso dal codificatore.
- Interrompere il canale. Una volta viene interrotto il canale, non comporta le spese. Quando è necessario riavviarla, avrà la stessa acquisizione URL in modo che non sarà necessario riconfigurare il codificatore.
- È possibile interrompere l'Endpoint di flusso, a meno che non si desidera continuare a offrire l'archivio di ogni evento live come flusso su richiesta. Se il canale è in stato di interruzione, non comporta le spese.
  
##<a name="view-archived-content"></a>Visualizzare il contenuto archiviato

Anche se si interrompere ed elimina l'evento, gli utenti sarebbero in grado di flusso il contenuto archiviato come video su richiesta per finché non si elimina la risorsa. Una risorsa non può essere eliminata se viene utilizzato da un evento. prima di tutto è necessario eliminare l'evento. 

Per gestire le risorse, selezionare **Impostazioni** e fare clic su **risorse**.

![Risorse](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Considerazioni

- Attualmente, la durata massima consentita consigliata di un evento live è 8 ore. Se è necessario eseguire un canale per periodi di tempo più, contattare amslived per il sito Microsoft.com.
- Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.


##<a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
