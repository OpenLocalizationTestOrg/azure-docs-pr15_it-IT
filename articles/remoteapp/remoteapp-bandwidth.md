
<properties 
    pageTitle="Valutare l'utilizzo della larghezza di banda di rete RemoteApp di Azure | Microsoft Azure"
    description="Informazioni sui requisiti di larghezza di banda di rete per le raccolte di Azure RemoteApp e aggiungere app."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Valutare l'utilizzo della larghezza di banda di rete RemoteApp di Azure 

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp utilizza Remote Desktop Protocol (RDP) per le comunicazioni tra le applicazioni in esecuzione nel cloud Azure e gli utenti. Questo articolo offre alcune linee guida fondamentali che è possibile utilizzare per valutare l'utilizzo della rete e potenzialmente valutare l'utilizzo della larghezza di banda di rete per ogni utente RemoteApp di Azure.

Stima dell'utilizzo della larghezza di banda per ogni utente è molto complessa e, è necessario eseguire più applicazioni contemporaneamente in multitasking scenari in cui potrebbero influire su applicazioni di altri prestazioni in base alla loro domanda per la larghezza di banda di rete. Anche il tipo di client Desktop remoto (ad esempio client Mac e HTML5 client) può generare risultati diversi della larghezza di banda. Per informazioni su come lavorare anche questi problemi, si verranno scomporre gli scenari di utilizzo in varie categorie comuni per la replica di scenari reali. (Dove lo scenario reale è, naturalmente, una combinazione di categorie ed è diverso dall'utente.)

Prima di continuare, si noti che si presuppongono RDP offre accessoria esperienza eccellente per la maggior parte dei scenari di utilizzo su reti con latenza sotto ms 120 e della larghezza di banda su 5 MB - ciò si basa sulla possibilità di RDP per regolare automaticamente utilizzando la larghezza di banda di rete disponibili e la larghezza di banda prevista applicazione deve. In questo articolo si estende oltre quelli "maggior parte dei casi di utilizzo" per esaminare il bordo in scenari avviata la rimozione ed esperienza utente inizia a diminuire.

Controllare gli articoli seguenti per informazioni dettagliate, inclusi i fattori da considerare, consigli previsione e cosa non si include le stime.

- [Come la larghezza di banda di rete e la qualità dell'esperienza utente lavoro insieme?](remoteapp-bandwidthexperience.md)
- [Test l'utilizzo della larghezza di banda di rete con alcuni scenari comuni](remoteapp-bandwidthtests.md)
- [Linee guida rapide se non si ha l'ora o possibilità di test](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>Che cos'è non incluso?

Dopo avere esaminato i test proposti e i suggerimenti complessivi (e ammettere generici), tenere presente che esistono diversi fattori che non ha è considerato. Ad esempio, i problemi di esperienza utente forniti dalla natura asimmetrica di caricamento e download della larghezza di banda. La maggior parte delle reti Wi-Fi, asimmetrica natura un impatto inoltre le prestazioni e la percezione esperienza utente. Per scenari interattivi il traffico downstream potrebbe priorità inferiori il padre, che possono aumentare il numero di fotogrammi video o audio persi e pertanto gli effetti in termini di visualizzazione di utente dell'esperienza di trasmissione. È possibile eseguire esperimenti per vedere che cos'è utile per il caso di utilizzo specifici e di rete.

Anche se verranno trattati gli argomenti il reindirizzamento del dispositivo, è non prendere in considerazione l'impatto della larghezza di banda il traffico di rete causato da dispositivi collegati, ad esempio lo spazio di archiviazione, stampanti, scanner, webcam e altri dispositivi USB. L'effetto di tali dispositivi in genere picchi temporaneamente le esigenze di larghezza di banda e non si ripete una volta completata l'attività. Ma se svolte di frequente, che richiedono la larghezza di banda può essere molto più evidente.

Anche non verrà invece come un utente può influire sulla altri utenti all'interno della stessa rete. Ad esempio, un utente che utilizzano 4K video in una rete MB/s 100 in modo significativo potrebbe influire su altri utenti nella stessa rete si tenta di eseguire la stessa attività. Purtroppo ottiene sempre più difficile determinare l'impatto dell'utilizzo simultaneo per assegnare una soluzione per comune o che include tutte le prestazioni del sistema di aggregazione. Suggerisce è la tecnologia protocollo sottostante verrà utilizzare al meglio della larghezza di banda di rete disponibili, ma presenta alcune limitazioni.