<properties 
    pageTitle="Configurare il codificatore Live elementare per inviare un flusso di live velocità singola | Microsoft Azure" 
    description="In questo argomento viene illustrato come configurare il codificatore Live elementare per inviare un flusso di velocità singola ai canali AMS abilitati per la codifica live." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="cenkdin;anilmur;juliako"/>

#<a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizzare il codificatore Live elementare per inviare un flusso di live velocità singola

> [AZURE.SELECTOR]
- [Elementare Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In questo argomento viene illustrato come configurare il codificatore [Live elementare](http://www.elementaltechnologies.com/products/elemental-live) per inviare un flusso di velocità singola ai canali AMS abilitati per la codifica live.  Per ulteriori informazioni, vedere [uso di canali che sono abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire servizi multimediali di Azure (AMS) con strumento Azure Media Services Explorer (AMSE). Questo strumento viene eseguita solo nel PC con Windows. Se utilizza Mac o Linux, usare il portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

##<a name="prerequisites"></a>Prerequisiti

- Deve avere una conoscenza dell'utilizzo di Live elementare interfaccia web per creare eventi live.
- [Creare un account di servizi multimediali di Windows Azure](media-services-portal-create-account.md)
- Assicurarsi che non esiste un Endpoint Streaming esecuzione con almeno un'unità flusso ricevuta. Per ulteriori informazioni, vedere [Gestire gli endpoint Streaming in un Account di servizi di supporto](media-services-portal-manage-streaming-endpoints.md).
- Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Avviare lo strumento e connettersi all'account AMS.

##<a name="tips"></a>Suggerimenti

- Se possibile, utilizzare una connessione cablata a internet.
- Una buona norma quando requisiti della larghezza di banda consiste nel fare doppio bitrate flusso. Anche se non è un requisito obbligatorio, essa aiuta a ridurre l'impatto di eccessivo traffico sulla rete.
- Quando si utilizza software in base a Encoder, chiudere tutti i programmi non necessari.

## <a name="elemental-live-with-rtp-ingest"></a>Acquisizione elementare Live con RTP

In questa sezione illustra come configurare il codificatore Live elementare che invia un flusso di live velocità singola in RTP.  Per ulteriori informazioni, vedere [flusso MPEG-TS su RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Creare un canale

1.  Nello strumento AMSE, passare alla scheda **Live** e fare clic con il pulsante destro all'interno dell'area di canale. Selezionare **Crea canale …** dal menu.

![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Specificare un nome di canale, il campo Descrizione è facoltativo. In impostazioni di canale, selezionare **Standard** per l'opzione codifica Live con il protocollo Input impostato su **RTP (MPEG-TS)**. È possibile mantenere tutte le altre impostazioni come.


Verificare che sia selezionata **avviare ora il nuovo canale** .

3. Fare clic su **Crea canale**.
![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] Il canale può richiedere fino a 20 minuti per iniziare.

Durante l'avvio il canale è possibile [configurare il codificatore](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT] Si noti che la fatturazione viene avviata non appena canale entra nello stato pronto. Per ulteriori informazioni, vedere [stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurare il codificatore Live elementare 

In questa esercitazione verranno usate le seguenti impostazioni di output. Il resto di questa sezione vengono descritti i passaggi di configurazione in modo più dettagliato. 

**Video**:
 
- Codec: h. 264 
- Profilo: Alto (livello 4.0) 
- Velocità: kbps 5000 
- Fotogramma chiave: 2 secondi (60 secondi) 
- Aggiungere una cornice tasso: 30
 
**Audio**:

- Codec: AAC (LC) 
- Velocità: 192 kbps 
- Frequenza campione: 44,1 kHz


####<a name="configuration-steps"></a>Passaggi di configurazione

1. Passare all'interfaccia di web **Live elementare** e configurare il codificatore per lo streaming **UDP/TS** . 

2. Dopo aver creato un nuovo evento, scorrere fino a gruppi di output e aggiungere il gruppo di output **UDP/TS** . 

3. Creare un nuovo output selezionando **nuovo flusso** e quindi fare clic su **Aggiungi Output**.  
    
    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] È consigliabile che l'evento elementare ha timecode impostata su "Orologio di sistema" per aiutare il codificatore riconnettersi in caso di errore flusso.

4. Una volta creato l'Output, fare clic su **Aggiungi flusso**. Le impostazioni di output ora possono essere configurate. 
5. Scorrere fino a "Flusso 1" appena creato, fare clic sulla scheda **Video** sul lato sinistro ed espandere la sezione Impostazioni **Avanzate** . 

    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Mentre Live elementare offre una vasta gamma di personalizzazione disponibili, le impostazioni seguenti sono consigliate per acquisire familiarità con streaming AMS. 
    
    - Risoluzione: 1280x720 
    - Frequenza fotogrammi: 30 
    - Dimensioni GOP: 60 frame 
    - Interlacciata: graduale 
    - Velocità: 5000000 bit/s (questo può essere modificato in base alle limitazioni della rete) 
    

    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. È possibile ottenere l'URL di input del canale.
    
    Passare allo strumento AMSE e controllare lo stato di completamento di canale. Una volta lo stato è cambiato **partire** sia in **esecuzione**, è possibile ottenere l'URL di input.
      
    Quando viene eseguito il canale, fare clic con il pulsante destro il nome del canale, spostarsi fino al passaggio del mouse **sull'URL di Input copia negli Appunti** e quindi selezionare **URL Input principale**.  
    
    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. Incollare le informazioni nel campo di **Destinazione principale** dell'elementare. Tutte le altre impostazioni possono rimanere il valore predefinito.
    
    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Per la ridondanza aggiuntiva, ripetere questi passaggi con l'URL di Input secondario mediante la creazione di una scheda distinta "Output" per lo Streaming UDP/TS.
    
7. Fare clic su **Crea** (se è stato creato un nuovo evento) o **Aggiorna** (se la modifica di un evento esistente) e quindi procedere per avviare il codificatore. 

>[AZURE.IMPORTANT]Prima di scegliere **Avvia** nell'interfaccia web Live elementare, è **necessario** assicurarsi che il canale sia pronto. 
>Inoltre, assicurarsi di non lasciare il canale in uno stato pronto senza un evento per più di 15 minuti di >.

Dopo il flusso è stata eseguita per 30 secondi, passare alla riproduzione di test e strumenti AMSE.  

###<a name="test-playback"></a>Riproduzione di test
  
1. Passare allo strumento AMSE e fare clic con il pulsante destro del canale da testare. Dal menu di scelta al passaggio del mouse sulla **riproduzione dell'anteprima** e selezionare **con Azure Media Player**.  

    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Se il flusso viene visualizzata in Windows Media player, quindi il codificatore correttamente configurato per connettersi a AMS. 

Se si riceve un messaggio di errore, sarà necessario reimpostare il canale e impostazioni codificatore corretti. Vedere l'argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per indicazioni.   

###<a name="create-a-program"></a>Creare un programma

1. Dopo la riproduzione di canale è confermata, creare un programma. Nella scheda **Live** nello strumento AMSE, fare clic con il pulsante destro all'interno dell'area di programma e selezionare **Crea nuovo programma**.  

    ![Elementare](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Assegnare un nome al programma e, se necessario, modificare la **Durata della finestra archivio** (che per impostazione predefinita a 4 ore). È anche possibile specificare una posizione di archiviazione o lasciare come predefinito.  
3. Selezionare la casella di **avviare il programma di ora** .
4. Fare clic su **Crea programma**.  
  
    Nota: La creazione di programma ha meno tempo rispetto alla creazione del canale.    
 
5. Dopo il programma è in esecuzione, verificare la riproduzione facendo clic con il pulsante destro del programma e passando alla **riproduzione di programmi** e quindi selezionando **con Azure Media Player**.  
6. Una volta confermata, destro del mouse fare nuovamente clic sull'applicazione e selezionare **copiare l'URL di Output negli Appunti** oppure recuperare questa informazione dall'opzione **impostazioni e informazioni sul programma** dal menu di scelta. 

Il flusso è ora pronto per essere incorporato in un lettore o distribuite a un gruppo di destinatari per una visualizzazione dinamica.  

## <a name="troubleshooting"></a>Risoluzione dei problemi

Vedere l'argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per indicazioni. 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
