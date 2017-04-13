<properties 
    pageTitle="Azure RemoteApp larghezza di banda - linee guida generali | Microsoft Azure"
    description="Comprendere alcune linee guida della larghezza di banda di rete di base per le raccolte di Azure RemoteApp e aggiungere app."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp larghezza di banda - linee guida generali (se non è possibile verificare il proprio)

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Se non si dispone di tempo o possibilità di eseguire i [test della larghezza di banda di rete](remoteapp-bandwidthtests.md) per RemoteApp di Azure, ecco alcune linee guida generica che consentono di stimare la larghezza di banda di rete per ogni utente.

Se si dispone di una combinazione di questi scenari, non è consigliabile nulla minore (o uguale a) 10 MB/s come la larghezza di banda minimo per le app moderna connesso a Internet in un ambiente remoto. (Anche se, come illustrato, questo non garantisce un migliore rispetto esperienza utente medio.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint complesse, PowerPoint semplice

RemoteApp Azure funziona meglio nella LAN a 100 MB. Al profilo di 10 MB/s rete, quando variazione sopra ms 120 è superiore al 5%, l'utente visualizzerà un'esperienza Media. 1 MB/s che i diversi è ritornare - ad esempio, in una presentazione, l'utente potrebbe non essere visibile transizioni animate tutto perché frame vengono ignorati.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, misto PDF, file PDF, testo

Profilo di rete 10 MB/s è vicino LAN aspetti. 1 MB/s fornisce un'esperienza OK, anche se può essere alcune instabilità quando un utente scorre nonostante siano disponibili immagini sullo schermo.

## <a name="flash-video-youtube"></a>Flash video (YouTube)

100 MB/s LAN fornisce risultati migliori, mentre 10 MB/s è accettabile (significato è per mantenersi la frequenza ma variazione aumenta). 1 MB/s, variazione è molto alta e più evidenti.

## <a name="word-typing-word-remote-input"></a>Word digitazione (input di remote Word)
Si tratta di uno scenario di utilizzo di larghezza di banda ridotta. 256 KB/s offre qualità di un'esperienza come LAN.

## <a name="learn-more"></a>Ulteriori informazioni
- [Valutare l'utilizzo della larghezza di banda di rete RemoteApp di Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - come la larghezza di banda di rete e la qualità dell'esperienza utente lavoro insieme?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting l'utilizzo della larghezza di banda di rete con alcuni scenari comuni](remoteapp-bandwidthtests.md)