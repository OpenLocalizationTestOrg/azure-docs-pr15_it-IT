<properties 
    pageTitle="Guida alla risoluzione dei problemi per lo streaming live | Microsoft Azure" 
    description="Questo argomento fornisce suggerimenti su come risolvere i problemi di trasmissione live." 
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
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guida alla risoluzione dei problemi per lo streaming live

Questo argomento fornisce suggerimenti sulla risoluzione dei problemi di trasmissione live.

## <a name="issues-related-to-on-premises-encoders"></a>Problemi relativi all'Encoder locale 

In questa sezione fornisce suggerimenti su come risolvere i problemi relativi a Encoder locale configurati per l'invio di un flusso di velocità singola ai canali AMS abilitati per la codifica live.

###<a name="problem-would-like-to-see-logs"></a>Problema: Si desidera visualizzare registri 

- **Potenziale problema**: non è possibile trovare codificatore registri che potrebbe essere utile in il debug dei problemi.
    
    - **Questo Wirecast**: si trova di solito registri in C:\Users\{username} \AppData\Roaming\Wirecast\ 
    - **Live elementare**: È possibile trovare vengono forniti i collegamenti ai registri nel portale di gestione. Fare clic su **statistiche**, quindi **registri**. Nella pagina **File di Log** , verrà visualizzato un elenco dei registri per tutti gli elementi di LiveEvent; Selezionare l'opzione corrispondente la sessione corrente. 
    - **Flash Media Encoder Live**: È possibile trovare nella **Directory di Log...** , passare alla scheda **Codifica Log** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Non sarà disponibile per l'invio di un flusso graduale

- **Potenziale problema**: il codificatore in uso non Deinterlaccia automaticamente. 

    **Risoluzione dei problemi**: cercare un'opzione di deinterlacciamento all'interno dell'interfaccia codificatore. Una volta deinterlacciamento è attivato, selezionare di nuovo per le impostazioni di output graduale. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Tentate diverse impostazioni di output codificatore e ancora non riesce a connettersi. 

- **Potenziale problema**: canale codifica Azure non è stato reimpostato correttamente. 

    **Risoluzione dei problemi**: verificare che il codificatore non è più inserendo a AMS, arrestare e reimpostare il canale. Una volta eseguire di nuovo, provare a connettersi il codificatore con le nuove impostazioni. Se ancora non viene corretto il problema, provare a creare un nuovo canale completamente, talvolta canali vengano danneggiati dopo diversi tentativi.  

- **Potenziale problema**: GOP la dimensione o impostazioni fotogramma chiave non sono ottimali. 

    **Risoluzione dei problemi**: l'intervallo di dimensioni o il fotogramma consigliati GOP è 2 secondi. Alcuni Encoder calcolare questa impostazione in numero di frame, mentre altri utilizzano secondi. Ad esempio: in caso di output 30 f/s, le dimensioni GOP sarebbe 60 frame, che corrisponde al contenuto 2 secondi.  
     
- **Potenziale problema**: porte sottotitoli bloccano il flusso. 

    **Risoluzione dei problemi**: quando streaming tramite RTP, controllare le impostazioni proxy e/o il firewall per confermare che le porte in uscita 1935 e 1936 siano aperte. Quando si usa streaming RTP, verificare che la porta in uscita 2010 sia aperta. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: Quando si configura il codificatore di flusso con il protocollo RTP, non è possibile immettere un nome host. 

- **Potenziale problema**: non consentono di Encoder RTP molti per i nomi host e sarà necessario acquisire un indirizzo IP.  

    **Risoluzione dei problemi**: per trovare l'indirizzo IP, aprire un prompt dei comandi in qualsiasi computer. Per eseguire questa operazione in Windows, aprire l'icona di avvio di esecuzione (logo Windows + R) e digitare "cmd" per aprire.  

    Dopo avere aperto il prompt dei comandi, digitare "Ping [AMS Host Name]". 

    Nome host può essere derivato omettendo il numero di porta dell'URL con l'acquisizione Azure, come mostrato nell'esempio seguente: 

    RTP://test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problema: Impossibile eseguire la riproduzione del flusso pubblicato.
 
- **Potenziale problema**: nessun endpoint Streaming in esecuzione o non c'è alcun unità streaming (unità scala) ricevuto. 

    **Risoluzione dei problemi**: passare alla scheda "Streaming Endpoint" nello strumento AMSE e verificare sia presente un Endpoint Streaming esecuzione con un'unità di trasmissione. 
    


>[AZURE.NOTE] Se dopo aver seguito le procedure di risoluzione dei problemi che non è ancora correttamente era possibile aprire, inviare un ticket di supporto tramite il portale di Azure.

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
