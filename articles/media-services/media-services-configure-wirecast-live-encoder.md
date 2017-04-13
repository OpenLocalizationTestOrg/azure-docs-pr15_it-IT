<properties 
    pageTitle="Configurare il codificatore di Telestream Wirecast per inviare un flusso di live velocità singola | Microsoft Azure" 
    description="In questo argomento viene illustrato come configurare il codificatore di live Wirecast per inviare un flusso di velocità singola ai canali AMS abilitati per la codifica live. " 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizzare il codificatore Wirecast per inviare un flusso di live velocità singola

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elementare Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

In questo argomento viene illustrato come configurare il codificatore di live [Questo Wirecast](http://www.telestream.net/wirecast/overview.htm) per inviare un flusso di velocità singola ai canali AMS abilitati per la codifica live.  Per ulteriori informazioni, vedere [uso di canali che sono abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md).

In questa esercitazione viene illustrato come gestire servizi multimediali di Azure (AMS) con strumento Azure Media Services Explorer (AMSE). Questo strumento viene eseguita solo nel PC con Windows. Se utilizza Mac o Linux, usare il portale di Azure per creare [canali](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programmi](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


##<a name="prerequisites"></a>Prerequisiti

- [Creare un account di servizi multimediali di Windows Azure](media-services-portal-create-account.md)
- Assicurarsi che non esiste un Endpoint Streaming esecuzione con almeno un'unità flusso ricevuta. Per ulteriori informazioni, vedere [Gestire gli endpoint Streaming in un Account di servizi di supporto](media-services-portal-manage-streaming-endpoints.md)
- Installare la versione più recente dello strumento [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Avviare lo strumento e connettersi all'account AMS.

##<a name="tips"></a>Suggerimenti

- Se possibile, utilizzare una connessione cablata a internet.
- Una buona norma quando requisiti della larghezza di banda consiste nel fare doppio bitrate flusso. Anche se non è un requisito obbligatorio, essa aiuta a ridurre l'impatto di eccessivo traffico sulla rete.
- Quando si utilizza software in base a Encoder, chiudere tutti i programmi non necessari.


## <a name="create-a-channel"></a>Creare un canale

1.  Nello strumento AMSE, passare alla scheda **Live** e fare clic con il pulsante destro all'interno dell'area di canale. Selezionare **Crea canale...** dal menu.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Specificare un nome di canale, il campo Descrizione è facoltativo. In impostazioni di canale, selezionare **Standard** per l'opzione codifica Live con il protocollo Input impostato su **RTP**. È possibile mantenere tutte le altre impostazioni come.


Verificare che sia selezionata **avviare ora il nuovo canale** .

3. Fare clic su **Crea canale**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] Il canale può richiedere fino a 20 minuti per iniziare.

Durante l'avvio il canale è possibile [configurare il codificatore](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Si noti che la fatturazione viene avviata non appena canale entra nello stato pronto. Per ulteriori informazioni, vedere [stati del canale](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurare il codificatore Telestream Wirecast

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


###<a name="configuration-steps"></a>Passaggi di configurazione

1. Aprire l'applicazione Telestream Wirecast sul computer utilizzato e configurare per lo streaming RTP.
2. Configurare l'output, passare alla scheda **Output** e selezionare **Le impostazioni di Output...**.
    
    Assicurarsi che la **Destinazione di uscita** è impostato su **Server RTP**.
3. Fare clic su **OK**.
4. Nella pagina Impostazioni, impostare il campo di **destinazione** sia **Servizi multimediali di Windows Azure**.
 
    Il profilo di codifica è già selezionato a **H. 264 Azure 720 p 16:9 (1280x720)**. Per personalizzare queste impostazioni, selezionare l'icona a forma di ingranaggio a destra della casella di riepilogo a discesa e quindi scegliere **Nuovo predefinito**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configurare le impostazioni predefinite codificatore.

    Assegnare un nome al predefinito e verificare quanto segue impostazioni consigliate:

    **Video**
    
    - Codificatore: Codec altamente ottimizzati h. 264
    - Frame al secondo: 30
    - Velocità media in bit: 5000 kbit/sec (può essere modificato in base alle limitazioni della rete)
    - Profilo: principali
    - Fotogramma chiave ogni: 60 frame

    **Audio**

    - Destinazione velocità: 192 kbit/sec
    - Frequenza campione: 44,100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Premere **Salva**.

    Il campo codifica è ora disponibile il nuovo profilo disponibile per la selezione. 

    Assicurarsi che sia selezionato il nuovo profilo.

7. È possibile ottenere l'URL di input del canale per assegnare al Wirecast **RTP Endpoint**.
    
    Passare allo strumento AMSE e controllare lo stato di completamento di canale. Una volta lo stato è cambiato **partire** sia in **esecuzione**, è possibile ottenere l'URL di input.
      
    Quando viene eseguito il canale, fare clic con il pulsante destro il nome del canale, spostarsi fino al passaggio del mouse **sull'URL di Input copia negli Appunti** e quindi selezionare **URL Input principale**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Nella finestra di Wirecast **Le impostazioni di Output** incollare le informazioni nel campo **indirizzo** della sezione di output e assegnare un nome di flusso. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Fare clic su **OK**.

10. Nella schermata principale di **Wirecast** , confermare origini di input per video e audio siano pronte e quindi premere **flusso** nell'angolo in alto a sinistra.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Prima di scegliere **flusso**, è **necessario** assicurarsi che il canale sia pronto. 
>Inoltre, assicurarsi di non lasciare il canale in uno stato pronto senza un contributo input feed per più di 15 minuti di >.

##<a name="test-playback"></a>Riproduzione di test
  
1. Passare allo strumento AMSE e fare clic con il pulsante destro del canale da testare. Dal menu di scelta al passaggio del mouse sulla **riproduzione dell'anteprima** e selezionare **con Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se il flusso viene visualizzata in Windows Media player, quindi il codificatore correttamente configurato per connettersi a AMS. 

Se si riceve un messaggio di errore, sarà necessario reimpostare il canale e impostazioni codificatore corretti. Vedere l'argomento [risoluzione dei problemi](media-services-troubleshooting-live-streaming.md) per indicazioni.  

##<a name="create-a-program"></a>Creare un programma

1. Dopo la riproduzione di canale è confermata, creare un programma. Nella scheda **Live** nello strumento AMSE, fare clic con il pulsante destro all'interno dell'area di programma e selezionare **Crea nuovo programma**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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
