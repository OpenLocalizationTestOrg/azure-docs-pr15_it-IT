<properties 
    pageTitle="Creare flussi di lavoro di codifica avanzate con Progettazione flussi di lavoro | Microsoft Azure" 
    description="Informazioni sulle procedure creare flussi di lavoro di codifica avanzate con Progettazione flussi di lavoro." 
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
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Creare flussi di lavoro di codifica avanzate con Progettazione flussi di lavoro

##<a name="overview"></a>Panoramica

**Progettazione flussi di lavoro** è uno strumento desktop di Windows che serve per progettare e creare flussi di lavoro personalizzati per la codifica con **Flusso di lavoro Media Encoder Premium**.
Grazie all'utilizzo dello strumento di progettazione del flusso di lavoro, è possibile progettare e creare flussi di lavoro complessi che verrà eseguita in **Media Encoder Premium**.  

Flussi di lavoro possono includere la logica di decisione del cliente e rami in base alle proprietà del file di origine di input. È possibile creare flussi di lavoro con proprietà override e valori dinamici per facilitare la ripetere e personalizzare nel cloud anche le attività di codifica più complesse.

I flussi di lavoro di esempio che è possibile creare includono:

- Decisione in base a flussi di lavoro che controlla il contenuto di origine per la risoluzione e codificare solo le tracce di output desiderato.  Si tratta helfpul eliminando le tracce inutilizzate generati da upscaling inavvertitamente contenuto di origine.
- File di input più possono essere utilizzati per supportare le didascalie, sovrapposizioni e panorama contenuto insieme. 

Questo strumento può anche essere utilizzato per modificare qualsiasi nostro [pubblicato flussi di lavoro](media-services-workflow-designer.md#existing_workflows). 

>[AZURE.NOTE]Per ottenere la copia dello strumento di progettazione flussi di lavoro, contattare mepd@microsoft.com.


Dopo aver creato un file di flusso di lavoro, può essere caricato come una risorsa e quindi essere usata per la codifica dei file multimediali. Per informazioni su come codificare con **Flusso di lavoro Media Encoder Premium** utilizzando **.NET**, vedere [Avanzate codifica Media Encoder Premium e flusso di lavoro](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificare i flussi di lavoro esistente

Il predefinito [pubblicato flussi di lavoro](media-services-workflow-designer.md#existing_workflows) possono essere modificati tramite lo strumento di progettazione. È possibile ottenere il valore predefinito del flusso di lavoro file [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La cartella contiene anche la descrizione di questi file.

I video seguenti viene illustrato come utilizzare la finestra di progettazione.

###<a name="day-1--getting-started"></a>Giorno 1-Guida introduttiva

1 giorno video illustra come:

- Panoramica della finestra di progettazione
- Base flussi di lavoro: "Hello World"
- Creazione di più file MP4 per l'utilizzo con servizi multimediali di Windows Azure streaming di output

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>Giorno 2

Giorno 2 video illustra come:

- Diversi scenari di file di origine: consente di gestire l'audio
- Flussi di lavoro con una logica avanzata
- Grafico fasi

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>Giorno 3

Giorno 3 video illustra come:

- Gli script all'interno di flussi di lavoro/piantine
- Restrizioni con il codificatore corrente
- Domande e risposte
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


Se è necessario supporto tecnico o domande sulla creazione di flussi di lavoro personalizzati nello strumento di progettazione del flusso di lavoro, inviare messaggi di posta elettronica mepd@microsoft.com.

##<a name="see-also"></a>Vedere anche

[Corsi di formazione della finestra di progettazione del flusso di lavoro di Azure Premium codificatore](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
