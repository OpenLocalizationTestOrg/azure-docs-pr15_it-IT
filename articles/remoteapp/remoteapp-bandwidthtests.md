<properties 
    pageTitle="Azure RemoteApp - verifica l'utilizzo della larghezza di banda di rete con alcuni scenari comuni | Microsoft Azure"
    description="Informazioni su come informazioni comuni scenari di utilizzo che consentono di scoprire le proprie esigenze della larghezza di banda di rete per RemoteApp di Azure."
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
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - verifica l'utilizzo della larghezza di banda di rete con alcuni scenari comuni

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Come illustrato [nell'utilizzo della larghezza di banda di rete stima Azure RemoteApp](remoteapp-bandwidth.md), si verifica il modo migliore per stabilire quali l'impatto di Azure RemoteApp alla rete consiste nell'eseguire alcune l'uso. Eseguire i test per un periodo imposta data/ora e misurare la larghezza di banda necessaria per ogni scenario. Se si dispone di funzionalità, è inoltre possibile misurare il pacchetto perdita e rete instabilità di rete per comprendere i modelli di rete che verranno creati nel proprio ambiente specifico.

    
Quando si valuta l'utilizzo della larghezza di banda, tenere presente che l'utilizzo varia tra diversi utenti all'interno della società. Ad esempio, gli autori e lettori di testo in genere occupare larghezza di banda minore rispetto agli utenti che funzionano con video. Per ottenere risultati ottimali, lo Studio su campioni esigenze utente e creare una combinazione di scenari seguenti che meglio rappresenta gli utenti della società. È necessario ricordare di [controllare i fattori che possono verificarsi utente e l'utilizzo della larghezza di banda impatto](remoteapp-bandwidthexperience.md) - che consenta di identificare i test ideale.

Leggere innanzitutto sui test, selezionare la combinazione di e quindi eseguirli. È possibile utilizzare la tabella seguente per tenere traccia delle prestazioni.

>[AZURE.NOTE] Se non è possibile eseguire il proprio test di rete o non è il tempo necessario per eseguire questa operazione, consultare la [stima della larghezza di banda di rete di base/consigli](remoteapp-bandwidthguidelines.md). Il chilometraggio può variare, tuttavia, in modo che è *possibile* eseguire il proprio test, è necessario.


## <a name="the-usage-tests"></a>Il test con un utilizzo
Ciascuno di questi test eseguire per diverse quantità di tempo e testare diverse funzioni/funzionalità che utilizzano la larghezza di banda di rete. È necessario ricordare di scegliere la combinazione di test che più corrisponde agli utenti della società.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint Executive/complesso - eseguire per 900 1000 secondi

Un utente presenta tra le diapositive ad alta fedeltà di 45-50 utilizzando Microsoft Office PowerPoint in modalità a schermo intero. Le diapositive devono contenere immagini, transizioni (con le animazioni) e sfondi con sfumature di colore sono le più comuni per la propria azienda. L'utente deve dedicare almeno 20 secondi a ogni diapositiva.
    
Questo scenario consente di creare il traffico bursty, quando una diapositiva passa alla diapositiva successiva nella presentazione.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint semplice - eseguita per ~ 620 secondi

Un utente presenta un semplice file di PowerPoint con circa 30 diapositive usando Microsoft Office PowerPoint in modalità a schermo intero. Le diapositive sono testo con maggiore frequenza rispetto in uno scenario di PowerPoint Executive/complesso e più semplice sfondi e immagini (diagrammi neri). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - eseguita per ~ 250 secondi

Un utente Esplora il web utilizzando Internet Explorer. L'utente accede e scorre una combinazione di testo, immagini naturali e alcuni diagrammi. Le pagine web archiviati sull'unità disco locale del server Host sessione Desktop remoto (Host sessione Desktop remoto) come un. File MHT. L'utente scorre tramite PGSU, PGGIÙ, su e giù tasti, con intervalli diversi per ogni tipo o un tasto di scorrimento:
    
    - Verso il basso - 250 tasti molto 500 ms
    - PGSU - 36 tasti ogni ms 1000
    - Giù - 75 tasti ogni 100 ms
    - PGGIÙ - 20 tasti ogni 500 ms
    - Massimo - 120 sequenze di tasti ogni ms 300
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Documento PDF - semplice - eseguita per ~ 610 secondi
Un utente legge ed esegue la ricerca di un documento PDF in diversi modi usando Adobe Acrobat Reader. Il documento deve essere costituito da tabelle, grafici semplici e più tipi di carattere di testo. Il documento è 35 40 pagine. L'utente scorre a tassi diversi due, le versioni precedenti e inoltra quattro dimensioni di zoom (adatta alla pagina, adattarla alla larghezza, 100% e un'altra scelta). L'ingrandimento garantisce che il testo (tipo di carattere) esegue il rendering in diversi formati. È verso il basso con intervalli diversi per ogni scorrimento PGSU, PGGIÙ, su e giù tasti, lo scorrimento.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>PDF del documento - misto - Esegui per ~ 320 secondi
Un utente legge ed esegue la ricerca di un documento PDF in diversi modi usando Adobe Acrobat Reader. Il documento è costituito da immagini di alta qualità (comprese le fotografie), tabelle, grafici semplici e più tipi di carattere di testo. L'utente scorre a tassi diversi due, le versioni precedenti e inoltra quattro dimensioni di zoom (adatta alla pagina, adattarla alla larghezza, 100% e un'altra scelta). L'ingrandimento garantisce che il testo (tipo di carattere) esegue il rendering in diversi formati. È verso il basso con intervalli diversi per ogni scorrimento PGSU, PGGIÙ, su e giù tasti, lo scorrimento.

### <a name="flash-video-playback---run-for-180-seconds"></a>Riproduzione di video Flash - eseguita per ~ 180 secondi
Un utente visualizza un video con codifica Adobe Flash incorporato in una pagina web. La pagina web vengono archiviati in unità disco rigido locale del server che ospita la sessione Desktop remoto. Il video verrà riprodotto in Internet Explorer tramite un lettore incorporato plug-in.

Questo scenario emula gli utenti che visualizzano RTF pagine web contenuto contenente multimediali. La maggior parte dei dati deve casella tramite VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word digitando remote - eseguita per ~ 1800 secondi
Durante una sessione RDP l'utente digita un documento. Le combinazioni di tasti vengono inviati dal lato client attraverso la sessione RDP a un documento in Microsoft Word in esecuzione nella sessione remota. Il tasso di digitazione è un carattere ogni ms 250 (totali 7050 caratteri). 

Questa è una delle situazioni più comuni per esperti. Questo scenario di test di velocità di risposta dell'utente digitando in un processore moderno. Questo scenario è sensibile anche piccole modifiche in utilizzo della larghezza di banda.

## <a name="tracking-the-test-results"></a>I risultati dei test di verifica

È possibile utilizzare la tabella seguente per valutare gli scenari nel proprio ambiente. Dati forniti di seguito sono solo per completezza, può essere molto diverso da osservano. 

Per semplicità, si presuppone che tutti gli scenari sono verificati utilizzando la stessa risoluzione 1920x1080 pixel e trasporti TCP in una rete con latenza (ritardo) sotto 200 ms e di rete variazione nella casella Segna 120 ms + di circa 1%.

Relative alla tabella:
- **Esperienza utente Media** contiene la larghezza di banda di rete in cui la produttività degli utenti non influisce notevolmente sulle ma non esclude occasionali anomalie audio o video. Il sistema è possibile recuperare rapidamente sfruttando la logica dinamica. Il tentativo di stima della larghezza di banda rete per garantire la qualità dell'esperienza utente.
 - **Noticeable problemi (punto di interruzione)** contiene la larghezza di banda di rete in cui gli utenti potrebbero verificarsi problemi significativi nel loro esperienza e la propria produttività impatto per periodi di tempo misurabile. A questo punto algoritmi RDP lavorano e non garantiscono qualità dell'utente dell'esperienza a causa di larghezza di banda insufficiente.
 - **Consigliato** contiene la larghezza di banda di rete consigliato per un'esperienza utente ottimale o eccellente. È in genere un passaggio maggiore del valore nella colonna **esperienza Media** corrispondente.
 - **Note** includono osservazioni e commenti.
 
| Test                  | Esperienza Media | Problemi più evidenti (punto di interruzione) | Larghezza di banda di rete consigliati | Note                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| In PowerPoint Executive/complesso | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferito    | 1 MB/s vengono perse molti animazioni                                   |
| Semplice in PowerPoint            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | 256 KB/s caricano con ritardo più visibili le diapositive                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferito    | 1 MB/s video web sono sfuocato o e veloce e irregolare scorrimento problemi |
| PDF semplice            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | 256 KB/s richiede un po' di tempo il caricamento della pagina                       |
| PDF misti             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | 256 KB/s la pagina richiede molto più tempo per caricare    |
| Riproduzione di video in anteprima  | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferito    | 1 MB/s è granuloso il video e alcuni frame vengono eliminati           |
| Word digitando remoto    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | 256 KB/s utente potreste riscontrare ora tra le combinazioni di tasti             |

Per valutare la larghezza di banda di rete per utente, creare una combinazione di scenari precedenti e la percentuale della larghezza di banda necessari corrispondente. Selezionare il numero più alto necessario per gli scenari. Poiché non è quasi mai, gli utenti utilizzano il sistema solo, è consigliabile eseguire alcune riserva per gli utenti di lavorare contemporaneamente nella stessa rete.
     
## <a name="learn-more"></a>Ulteriori informazioni
- [Valutare l'utilizzo della larghezza di banda di rete RemoteApp di Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - come la larghezza di banda di rete e la qualità dell'esperienza utente lavoro insieme?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp larghezza di banda - linee guida generali (se non è possibile verificare il proprio)](remoteapp-bandwidthguidelines.md)