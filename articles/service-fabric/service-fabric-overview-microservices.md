<properties
   pageTitle="Informazioni sulle microservices | Microsoft Azure"
   description="Cenni preliminari perché è importante per lo sviluppo di applicazione moderna la creazione di applicazioni cloud con un approccio microservices e come Azure servizio tessuti fornisce una piattaforma a tale scopo"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="mfussell"/>

# <a name="why-a-microservices-approach-to-building-applications"></a>Perché un microservices approccio alla creazione di applicazioni?
Gli sviluppatori di software, non è nuovo in modalità pensa a separazione di un'applicazione in componenti. È il paradigma centrale dell'orientamento dell'oggetto, astrazioni software e la creazione dei componenti. Oggi, questo GPO viene in forma di classi e interfacce tra i livelli di tecnologia e librerie condivise. In genere, un approccio a più livelli viene accettato con un punto vendita back-end, logica aziendale di secondo livello e un'interfaccia utente front-end. Quali *è* stato modificato negli ultimi anni è che, come gli sviluppatori, stiamo creando applicazioni distribuite per il cloud, basata sulle risorse dall'azienda.

Esigenze di business sono:

- Creare e utilizzare un servizio in scala per raggiungere i clienti di nuove aree geografiche (ad esempio).
- Processore recapito delle caratteristiche e funzionalità di essere in grado di rispondere alle esigenze dei clienti in modo agile.
- Utilizzo delle risorse migliorato per ridurre i costi.

Le esigenze aziendali influiscono sulla *modalità* compilazione di applicazioni.

Per ulteriori informazioni sull'approccio di Azure microservices, leggere [Microservices: rivoluzione un'applicazione dal cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Qualsiasi e approccio di progettazione microservice
Tutte le applicazioni evolvono nel tempo. Applicazioni evolvono per essere utile agli utenti. Le applicazioni non riuscite non evolversi e infine sono obsoleti. La domanda da porsi: quanto spazio si capisce sui requisiti di oggi e cosa si sarà in futuro? Ad esempio, supponiamo che si sta creando un'applicazione di creazione di report per un reparto. Si è certi che l'applicazione rimarrà nell'ambito dell'azienda e i report sarà breve. Scelta dell'approccio è diversa, ad esempio, la creazione di un servizio per la distribuzione di contenuti video a decine di milioni di clienti. In alcuni casi ottenere qualcosa fuori dalla porta come modello di prova è il fattore di Guida, con la conoscenza che l'applicazione può essere riprogettato in un secondo momento. Esiste un inutile eccessiva ingegneria qualcosa che non viene mai utilizzata. È il compromesso ingegneristica comune. Mano, quando società parla predefiniti per il cloud, la previsione è crescita e l'uso. Il problema è che proporzioni e crescita sono imprevisti. Si desidera essere in grado di prototipo rapidamente durante anche sapere che sono in un percorso di occuparsi di successo. Questo è l'approccio avvio snella: creare, misurare, informazioni, scorrere.

Durante l'era client / server, è fosse per concentrarsi su applicazioni a più livelli utilizzando tecnologie specifiche in ogni livello. Il termine "su" applicazione ha pratica per questi approcci. Le interfacce fosse sia compreso tra i livelli e più strettamente tra componenti all'interno di ogni livello di struttura. Gli sviluppatori progettato e inseriti classi compilate in raccolte, collegate tra loro in pochi eseguibili e DLL. Esistono vantaggi di questo approccio su struttura. È spesso più semplice progettare e si dispone di più veloce chiamate tra i componenti, dal momento che le chiamate sono spesso IPC. Inoltre, tutti gli utenti di test di un unico prodotto, in genere efficiente altre persone alla risorsa. Lo svantaggio è che strette tra i risultati a più livelli livelli ed è possibile ridimensionare i singoli componenti. Se è necessario eseguire correzioni o aggiornamenti automatici, è necessario attendere che altri utenti alla fine della verifica. È più difficile da agile.

Microservices indirizzo questi svantaggi e altre allineare strettamente ai requisiti business precedente, ma hanno anche upsides e svantaggi. Vantaggi della microservices sono che ognuna di esse include in genere più semplice funzionalità di business, che scalare verso l'alto o verso il basso, test, distribuire e gestire in modo indipendente. Uno dei vantaggi più importante di un approccio microservice è che team sono basate su altri elementi scenari aziendali più dalla tecnologia, incoraggino l'approccio a più livelli. In pratica, team più piccoli sviluppare un microservice in base a uno scenario di cliente, utilizzando le tecnologie che scelgono. In altre parole, l'organizzazione non è necessario Normalizza tecnica per gestire le applicazioni su. Gruppi di singoli che servizi personalizzati è possono eseguire operazioni significativo in base competenze del team o che cos'è più appropriato per il problema da risolvere. In pratica, un set di tecnologie consigliate, ad esempio un particolare archivio NoSQL o web framework di applicazione, è preferibile.

Di sotto di microservices viene fornito in Gestione aumenta il numero di entità e sulla gestione del controllo delle versioni e installazioni più complesse. Il traffico di rete tra il microservices aumenta dell'utente e la latenza di rete corrispondente. Grazie a numerosi semplici, granulare services è disponibile una soluzione per un incubo prestazioni. Senza strumenti per la visualizzazione delle dipendenze, è difficile "vedere" l'intero sistema. Standard sono i approccio microservice aziendale contrattazione della comunicazione e vengano tolleranza solo le operazioni che è necessario da un servizio, anziché rigidi contratti. È importante definire questi contatti anticipo nella finestra di progettazione, poiché servizi aggiornare indipendente tra loro. Un'altra descrizione definita per la progettazione con un approccio microservices è "SOA diffusamente".


***Nella forma più semplice l'approccio di progettazione microservices riguarda una federazione disaccoppiata dei servizi con le modifiche indipendenti a ogni e serie standard per la comunicazione.***


Prodotta altre applicazioni basate su cloud, gli altri utenti trovare che questo scomposizione dell'app complessiva nei servizi indipendenti, incentrati sui scenario è un approccio più efficace più lungo termine.

## <a name="comparison-between-application-development-approaches"></a>Confronto tra approcci di sviluppo di applicazioni

![Sviluppo di applicazioni di servizio tessuti piattaforma][Image1]

1. Un'app qualsiasi contiene funzionalità specifiche del dominio e normalmente diviso per livelli funzionali, ad esempio web, business e dati.

2. Ridimensionate un'app qualsiasi tramite la duplicazione su più server/macchine virtuali/contenitori.

3. Un'applicazione microservice separa funzionalità nei servizi minori separati.

4. Questo approccio scale fuori distribuendo ogni servizio in modo indipendente, la creazione di istanze di questi servizi in macchine virtuali/server/contenitori.


Progettazione con un microservice approccio non costituiscono un rimedio per tutti i progetti, ma Allinea più da vicino con gli obiettivi di business descritti in precedenza. Iniziare con un approccio su può essere accettabile se si è sicuri che in un secondo momento non si avrà l'opportunità di modificare il codice in una struttura microservice, se necessario. Più comunemente, si iniziano con un'app qualsiasi e lentamente suddividere la in fasi, a partire da aree funzionali che devono essere più ridimensionabile o agile.

Per riepilogare, l'approccio microservice consiste nel creare l'applicazione di molti servizi più piccoli.  I servizi eseguiti in contenitori distribuiti in un cluster di computer. Team più piccoli sviluppare un servizio che è incentrata su uno scenario e in modo indipendente provare, versione, distribuire e ridimensionare ogni servizio in modo che può evolversi dell'intera applicazione.

## <a name="what-is-a-microservice"></a>Che cos'è un microservice?

Esistono diverse definizioni di microservices e ricerca su Internet offre molte risorse buone che forniscono i propri punti di vista e le definizioni. Tuttavia, la maggior parte delle caratteristiche di microservices ampiamente sono stata accettata su:

- Racchiudere uno scenario commerciale o cliente. Che cos'è il problemi?
- Sviluppati da un piccolo team engineering.
- Scritti in qualsiasi linguaggio di programmazione e utilizzare qualsiasi framework.
- Sono costituiti da codice e, facoltativamente, stato, che si verificano entrambe in modo indipendente versione distribuito e architetture.
- Interagire con le altre microservices su protocolli e interfacce ben definite.
- Avere nomi univoci (URL) utilizzati per la risoluzione relativa posizione.
- Rimangono coerenti e sono disponibili in presenza di errori.

È possibile riepilogare queste caratteristiche in:

***Applicazioni Microservice sono costituite da piccole in modo indipendente versioni servizi e scalable orientati ai clienti di comunicano con loro tramite protocolli standard con interfacce ben definite.***


Sono state esaminate i primi due punti nella sezione precedente e ora è ampliare e rendere più chiara sulle altre colonne.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Scritti in qualsiasi linguaggio di programmazione e utilizzare qualsiasi framework
Gli sviluppatori dovrebbe essere possibile scegliere qualsiasi lingua o framework vogliamo, in base alle esigenze del servizio o servizi offerti liberamente. In alcuni servizi, si potrebbero valore le prestazioni di C++ sopra tutte le altre. In altri servizi, la facilità di sviluppo gestito in c# o linguaggio potrebbe essere più importante. In alcuni casi, potrebbe essere necessario usare una specifica della libreria di terze parti, grazie alla tecnologia di archiviazione dati o mezzo di esporre il servizio clienti.

Dopo aver selezionato una tecnologia si raggiunge la gestione del ciclo di vita o operativa e proporzioni dei caratteri del servizio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Consente di codice e in modo indipendente versione distribuito e architetture  

Tuttavia si sceglie di scrivere i microservices, il codice e facoltativamente lo stato deve in modo indipendente distribuire, aggiornare e scalare. Si tratta in realtà uno dei problemi più difficili per risolvere, poiché si tratta verso il basso per la scelta delle tecnologie. Per informazioni sulle proporzioni, come partizione (o condiviso) sia il codice di stato è difficile. Quando il codice e lo stato utilizza tecnologie distinte (che al momento sono eseguire), è necessario essere in grado di gestire il ridimensionamento in entrambi gli script di distribuzione per il microservice. È inoltre sulle flessibilità e flessibilità, in modo che è possibile aggiornare alcuni il microservices senza dover aggiornare tutti gli elementi contemporaneamente.
Tornare su confronto microservice approccio per qualche minuto, il diagramma seguente illustra le differenze nell'approccio per l'archiviazione di stato.

#### <a name="state-storage-between-application-styles"></a>Archiviazione tra gli stili dell'applicazione
![Archiviazione dello stato di piattaforma tessuti servizio][Image2]

***Sul lato sinistro è l'approccio su, con un solo database e livelli di tecnologie specifiche.***

***Sulla destra è l'approccio microservices, un grafico di interconnessi microservices nel punto in cui lo stato in genere l'ambito è il microservice e vengono usate diverse tecnologie.***

In un approccio su, in genere esiste un solo database utilizzato dall'applicazione. Il vantaggio è che si tratti di un'unica posizione, semplificando la distribuzione. Ognuno dei quali può avere un'unica tabella per memorizzare il relativo stato. Team devono essere strict separare lo stato, che è un'operazione impegnativa. Esistono inevitabile temptations per aggiungere una nuova colonna a una tabella clienti esistente, eseguire un join tra le tabelle e creare dipendenze a livello di archiviazione. Una volta che si verifica questo evento, non è possibile ridimensionare singoli componenti. Approccio microservices ogni servizio gestisce e archivia il proprio stato. Ogni servizio è responsabile per il ridimensionamento codice e stato per soddisfare le esigenze del servizio. Un svantaggio è che quando è necessario creare visualizzazioni o query, dei dati dell'applicazione sarà necessario eseguire una query tra archivi stato diversi. In genere, questo viene risolto mediante la ricezione di un microservice separato che consente di creare una visualizzazione in una raccolta di microservices. Se è necessario eseguire più query ad hoc sui dati, ogni microservice consigliabile scrivere i dati in una data warehouse servizio per analitica offline.

Controllo delle versioni è specifico per la versione di un microservice distribuita in modo che più versioni diverse distribuire ed eseguire side-by-side. Controllo delle versioni consente di risolvere gli scenari in cui una versione più recente di un microservice ha esito negativo durante l'aggiornamento ed è necessario ripristinare una versione precedente. Altri scenari per il controllo delle versioni utilizzato per eseguire il test A, B-stile, in cui gli utenti diversi riscontrare diverse versioni del servizio. Ad esempio, è comune per l'aggiornamento microservice per un determinato set di clienti di testare nuove funzionalità prima di distribuirlo più ampiamente. Dopo la gestione del ciclo di vita dei microservices, adesso ci porta per le comunicazioni tra di esse.


### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagisce con altri microservices su protocolli e interfacce ben

In questo argomento richiede attenzione piccola qui, poiché non esiste una vasta documentazione sull'architettura orientata ai servizi pubblicata in ultimi 10 anni che descrive i modelli di comunicazione. In genere, la comunicazione dei servizi utilizza un approccio REST con protocolli TCP e HTTP e XML o JSON come formato di serializzazione. Dal punto di vista dell'interfaccia è sulle accogliere l'approccio di progettazione web. Ma non è impedire l'utilizzando i protocolli binari o formati personalizzati dati. Preparare gli utenti hanno difficoltà a usando il microservices se questi sono disponibili aperto.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ha un nome univoco (URL) utilizzato per risolvere il relativo percorso

Ricordare come abbiamo continuiamo in cui viene indicato che l'approccio microservice è ad esempio web? Ad esempio web, il microservice deve essere utilizzabili nel punto in cui è in esecuzione. Se pensa macchine e quello che viene eseguito un particolare microservice, aspetti andrà errate rapidamente. Nello stesso modo che DNS risolve un URL specifico a un determinato computer, il microservice deve avere un nome univoco in modo che sia individuabile posizione corrente. Microservices necessario utilizzabili nomi che li si rende indipendente dal infrastruttura che sono in esecuzione su. Questo significa che si è un'interazione tra la modalità di distribuzione del servizio e come si scopre, dal momento che è necessario essere un registro di sistema di servizio. Allo stesso modo, quando un computer non riesce il Registro di sistema servizio necessario indicare nel punto in cui il servizio è in esecuzione. In questo modo ci all'argomento successivo: resilienza e la coerenza.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Rimane coerente e disponibile in presenza di errori

La gestione di errori imprevisti corrisponde a uno dei problemi più difficili per risolvere, in particolare in un sistema distribuito. La maggior parte del codice scritto come gli sviluppatori gestisce le eccezioni si tratta anche in più tempo è sostenute nei mesi di test. Ma il problema è più complesso rispetto alla scrittura di codice per gestire gli errori. Cosa accade quando il computer in cui viene eseguita la microservice non riesce? Non solo è necessario rilevare questo errore microservice (un problema difficile autonomamente), ma è anche necessario è necessario riavviare il microservice. Un microservice deve essere flessibile a errori e riavviare spesso su un altro computer per motivi di disponibilità. Questo è anche disponibile fino a che lo stato è stato salvato per conto di microservice, in cui gli elementi ripristinabili questo stato dal e se è possibile riavviare correttamente. In altre parole, è necessario essere adattabilità nel calcolo (riavvia il processo), nonché adattabilità nello stato o dati (senza perdita di dati e i dati rimangono coerenti).

Risolvere il problema di flessibilità, composti durante altri scenari, ad esempio quando viene eseguita errori durante l'aggiornamento dell'applicazione. Microservice, utilizzo di sistema di distribuzione, non è necessario ripristinare. È anche necessario quindi decidere se può continuare a spostarsi in avanti alla versione più recente o invece ripristinare una versione precedente di mantenere uno stato coerente. Domande, ad esempio, se sono sufficienti macchine disponibili per proseguire in avanti e su come ripristinare versioni precedenti dei microservice necessario prendere in considerazione. È necessario microservice per generare informazioni di stato per poter prendere le decisioni.

### <a name="reports-health-and-diagnostics"></a>Report integrità e diagnostica

Può sembrare più evidente e viene spesso trascurato, ma è essenziale che un microservice segnala l'integrità e diagnostica. In caso contrario, esiste solo poche indicazioni dal punto di vista operazioni. Correlare eventi di diagnostica un set di servizi indipendenti e sulla gestione del computer orologio inclinazioni di ordine degli eventi è difficile. Nello stesso modo interagiscono con un microservice serie protocolli e i formati di dati, sono uscita la necessità di norme in modalità di accesso salute e gli eventi di diagnostica in definitiva che vengono utilizzati per un archivio di eventi per la ricerca e la visualizzazione. In un approccio microservices chiave diversi team concordino su un formato di registrazione singola.  È necessario essere un approccio coerente alla visualizzazione di eventi di diagnostica nell'applicazione nel suo insieme.

Integrità è la differenza tra diagnostica. Integrità è relativo microservice dei report di stato corrente di eseguire azioni appropriate. Un buon esempio sta collaborando con meccanismi di aggiornamento e distribuzione per mantenere la disponibilità. Un servizio attualmente non integro a causa di un arresto anomalo del processo o computer riavviare il computer, ma ancora operativi. L'ultima operazione che è necessario è per effettuare questa operazione peggiori effettuando un aggiornamento. L'approccio ottimale consiste nell'eseguire un'analisi prima di tutto o attendere microservice recuperare. Eventi di integrità di una microservice consentono di prendere decisioni e, in realtà consente creare servizi automatica.

## <a name="service-fabric-as-a-microservices-platform"></a>Servizio tessuti come piattaforma microservices

Azure servizio tessuti derivato dai transizione Microsoft da l'esecuzione di prodotti, casella che erano in genere su stile, per l'esecuzione di servizi. L'esperienza di creazione e il funzionamento di servizi di grandi dimensioni, ad esempio i database di SQL Azure e DocumentDB, una forma servizio tessuti. La piattaforma cambiato nel tempo adottato più servizi. È importante servizio tessuti doveva eseguire ovunque: non solo in Azure, ma anche nelle distribuzioni di Windows Server autonomo.

***Lo scopo del servizio tessuti consiste nel risolvere i problemi di creazione e l'esecuzione di un servizio disco rigido e utilizzare le risorse dell'infrastruttura in modo efficiente, in modo che team possono aiutare a risolvere i problemi aziendali usando un approccio microservices.***

Servizio tessuti sono disponibili due aree generali per la creazione di applicazioni con un approccio microservices:

- Piattaforma che offre servizi di sistema per distribuire, eseguire l'aggiornamento, rilevare e riavviare servizi non riusciti, individuare la posizione del servizio, gestire lo stato e monitorare l'integrità. Questi servizi abilitare valide molte delle caratteristiche di microservices descritto in precedenza.

-  API di programmazione o Framework, per facilitare la compilazione di applicazioni come microservices: [gli operatori affidabili e servizi affidabili](service-fabric-choose-framework.md). Naturalmente, è possibile usare un codice di propria scelta per creare il microservice. Ma queste API rendere più semplice il processo e si integrano con la piattaforma a un livello più approfondita. In questo modo, ad esempio, è possibile ottenere informazioni di diagnostica e integrità o è possibile sfruttare disponibilità incorporata.

***È possibile utilizzare qualsiasi tecnologia tessuti servizio è indipendente in come si creano i servizi. Tuttavia, offre le API di programmazione predefinite che rendono più semplice creare microservices.***

### <a name="are-microservices-right-for-my-application"></a>Sono microservices verso destra per l'applicazione?

È possibile. Abbiamo registrato è stato come più team Microsoft ha iniziato a compilare per il cloud per motivi aziendali, molte delle loro realizzati vantaggi di un approccio microservice. Bing, ad esempio, ha sviluppato microservices in Cerca anni. Per altri team, l'approccio microservices era nuovo. Sono presenti che sono stati problemi difficili per risolvere esterni alle aree principali di forza della funzione. Si tratta perché servizio tessuti acquisita trazione come la tecnologia di scelta per la creazione di servizi.

L'obiettivo di servizio tessuti è ridurre la complessità delle applicazioni con un approccio microservice, in modo che non si dispone elaborata come molti costosa presenta la nuova. Avviare small, se necessario, impostare come deprecato servizi e aggiungerne di nuove evolversi con l'uso dei clienti, che rappresenta l'approccio. Si noti anche che in realtà sono disponibili molti altri problemi ancora da risolvere per rendere più accessibile per gli sviluppatori microservices. Contenitori e il modello di programmazione attore sono riportati alcuni esempi di piccole operazioni nella direzione desiderata e siamo assicurarsi che altre innovazioni verranno emerge per semplificare questa operazione.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni:
    * [Panoramica della terminologia tessuti servizio](service-fabric-technical-overview.md)
    * [Microservices: Un rivoluzione applicazione dal cloud](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)


[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
