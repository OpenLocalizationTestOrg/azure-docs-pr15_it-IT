<properties
   pageTitle="Disponibilità di applicazioni Azure | Microsoft Azure"
   description="Panoramica tecnica e informazioni dettagliate sulla progettazione e compilazione di applicazioni per disponibilità in Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Disponibilità di applicazioni basate su Microsoft Azure

Un'applicazione di disponibilità assorbe fluttuazioni disponibilità, carico ed errori temporanei nelle servizi dipendenti e hardware. L'applicazione continua a funzionare un utente accettabile e a livello di risposta sistematico, come definito da esigenze aziendali o dell'applicazione contratti di servizio (contratti di servizio).

##<a name="azure-high-availability-features"></a>Caratteristiche di disponibilità Azure

Azure è disponibili numerose funzionalità di piattaforma incorporata che supportano applicazioni altamente disponibili. In questa sezione vengono descritte alcune di queste funzionalità principali. Per un'analisi più completa della piattaforma, vedere [indicazioni tecniche sulla resilienza Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Controller dell'infrastruttura

Il controller dell'infrastruttura di Azure esegue il provisioning e monitora la condizione delle istanze di Azure calcolo. Il controller dell'infrastruttura controlla lo stato dell'hardware e software delle istanze di computer host e guest. Quando viene rilevato un errore, applica contratti di servizio spostando automaticamente le istanze di macchine Virtuali. Il concetto di guasto e aggiornamento dei domini ulteriormente supporta il contratto di servizio di calcolo.

Quando vengono distribuite più istanze del ruolo di servizio Cloud, Azure distribuisce le istanze di domini guasto diverso. Il limite di dominio guasto è sostanzialmente un rack hardware diverso nella stessa regione. Domini guasto riducono la probabilità che un errore hardware localizzata verrà interrotto il servizio di un'applicazione. Non è possibile gestire il numero di domini guasto che sono assegnate al lavoro o ruoli web. Il controller dell'infrastruttura utilizza risorse dedicate distinte dalle applicazioni ospitate da Azure. Ha dei tempi di attività al 100% in quanto funge da nucleo del sistema di Azure. Esegue il monitoraggio e si gestiscono istanze del ruolo domini guasto.

Il diagramma seguente illustra le risorse condivise Azure che il controller dell'infrastruttura distribuisce e gestione domini guasto diverso.

![Vista semplificata di isolamento dominio degli errori](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Aggiornamento domini sono simili ai domini guasto nella funzione ma supportano aggiornamenti invece di errori. Un aggiornamento del dominio è un'unità logica di separazione istanza che determina quali istanze di un particolare servizio verranno aggiornati in un punto nel tempo. Per impostazione predefinita, per la distribuzione di servizio di hosting, sono definiti cinque domini aggiornamento. Tuttavia, è possibile modificare tale valore nel file di definizione del servizio. Si supponga ad esempio si dispone di otto istanze del ruolo web. Ci sono due istanze di aggiornamento e tre i domini e due istanze di un aggiornamento del dominio. Azure definisce la sequenza di aggiornamento, ma si basa sul numero di domini aggiornamento. Per ulteriori informazioni sui domini aggiornamento, vedere [aggiornare un servizio cloud](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Caratteristiche di altri servizi

Oltre alle funzionalità di piattaforma che supportano la disponibilità di elaborazione elevato, Azure incorpora elevata disponibilità in altri servizi. Ad esempio, lo spazio di archiviazione di Azure gestisce tre repliche di tutti i blob, tabella e i dati di coda. Consente inoltre l'opzione di replica geografico archiviare i backup di BLOB e tabelle in un'area secondaria. La rete CDN Azure consente BLOB da memorizzare nella cache tutto il mondo per la ridondanza e scalabilità. Database SQL Azure gestisce più repliche anche.

Oltre alle serie [indicazioni tecniche sulla resilienza](https://aka.ms/bctechguide) di articoli, vedere il documento di [Procedure consigliate per la progettazione di grandi dimensioni servizi nel server di servizi Cloud Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) . Questi white paper forniscono informazioni più approfondite delle caratteristiche di disponibilità piattaforma Azure.

Benché Azure offre più funzioni che supportano la disponibilità, è importante tenere presente le limitazioni:

- Per elaborazione, Azure garantisce che i ruoli sono disponibili e in esecuzione, ma non sanno se l'applicazione è in esecuzione o sovraccarico.
- Per i Database di SQL Azure, dati vengono replicati in modo sincrono all'interno dell'area. È possibile scegliere Attiva geografico replica, che consente a tutte le quattro copie di database aggiuntivi nella stessa regione (o aree geografiche diverse). Queste repliche di database non sono backup in un momento. Database SQL offrono funzionalità di backup in un momento. Per ulteriori informazioni sulle funzionalità di SQL Database in un momento, leggere [Punto di Database SQL Azure in fase di ripristinare](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Per l'archiviazione di Azure, dati di tabella e blob viene replicati per impostazione predefinita per un'area alternativa. Tuttavia, è possibile accedere repliche fino a Microsoft sceglie di non riuscire sul sito alternativo. L'area geografica caso di errore si verifica solo in caso di interruzione prolungata del servizio a livello di area geografica e non c'è alcun contratto di servizio per il tempo di failover geografico. È anche importante tenere presente che gli eventuali dati danneggiati pagine affiancate rapidamente alle repliche.

Per questi motivi, è necessario completare le funzionalità di disponibilità della piattaforma con funzioni specifiche di un'applicazione disponibilità. Le caratteristiche specifiche di un'applicazione disponibilità includono la caratteristica di snapshot blob per creare i backup in un momento dei dati blob.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilità imposta per macchine virtuali di Azure

La maggior parte di questo articolo è incentrato sui servizi cloud, che utilizzano una piattaforma come modello di servizio (PaaS). Tuttavia, sono disponibili anche le caratteristiche di disponibilità specifiche per macchine virtuali di Azure, che utilizza un'infrastruttura come modello di servizio (IaaS). Per ottenere la disponibilità elevata con macchine virtuali, è necessario utilizzare set di disponibilità. Un set di disponibilità viene utilizzata una funzione analoga a guasto e aggiornamento dei domini. All'interno di un set di disponibilità, Azure posiziona macchine virtuali in modo che impedisce a errori hardware localizzate e attività di manutenzione di arrestare tutti i computer di tale gruppo. Set di disponibilità sono necessarie per realizzare il contratto di servizio Azure la disponibilità di macchine virtuali.

Nel diagramma seguente offre una rappresentazione di due set di disponibilità di tale gruppo web e macchine virtuali di SQL Server, rispettivamente.

![Disponibilità imposta per macchine virtuali di Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] Nella figura precedente, SQL Server è installato e in esecuzione in macchine virtuali. Questa è la differenza tra la discussione precedente del Database di SQL Azure, che fornisce un database come servizio gestito.

##<a name="application-strategies-for-high-availability"></a>Strategie di applicazioni per disponibilità

La maggior parte delle strategie di applicazioni per disponibilità implicano ridondanza o la rimozione del disco rigido dipendenze tra componenti dell'applicazione. Progettazione di applicazioni deve supportare tolleranza di errore durante il tempo di inattività sporadiche di Azure o servizi di terze parti. Le sezioni seguenti descrivono i modelli di applicazione per aumentare la disponibilità dei servizi cloud.

###<a name="asynchronous-communication-and-durable-queues"></a>Comunicazioni asincrone e code permanente

Valutare la possibilità di comunicazioni asincrone tra servizi associati per aumentare la disponibilità di applicazioni Azure. In questo modello, scrivere messaggi di code di spazio di archiviazione o code di Azure Bus di servizio per l'elaborazione in un secondo momento. Quando si scrive il messaggio alla coda, controllo restituisce immediatamente al mittente del messaggio. Un altro livello dell'applicazione gestisce il messaggio di elaborazione, in genere implementato come un ruolo di lavoro. Se il ruolo di lavoro viene interrotto, i messaggi inseriti nella coda finché non viene ripristinato il servizio di elaborazione. Come la coda è disponibile, non esiste alcun dipendenza diretta tra il mittente front-end e processore messaggio. Questo elimina la necessità di interventi di assistenza icona del che può essere una bottiglia velocità nelle applicazioni distribuite.

Una variazione di queste utilizza archiviazione Azure (BLOB, tabelle, code) o code Bus di servizio come posizione failover per le chiamate di database danneggiato. Ad esempio, una chiamata icona all'interno di un'applicazione a un altro servizio (ad esempio il Database di SQL Azure) non riesce più volte. Potrebbe essere possibile serializzare i dati in archivio permanente. In un momento successivo quando il servizio o database alla modalità online, l'applicazione inviare nuovamente richiesta dallo spazio di archiviazione. La differenza in questo modello è che il percorso intermedio non è una costante parte del flusso di lavoro dell'applicazione. Viene utilizzato solo in scenari di errore.

In entrambi gli scenari, la comunicazione asincrona e archiviazione intermedia impediscono un servizio di back-end inattivo arrestare l'intera applicazione. Code servono come un intermedio logico. Per altre indicazioni su come scegliere il servizio di Accodamento corretto, vedere [code Azure e Azure servizio Bus - compared e rispetto](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Logica di rilevamento e riprovare guasto

Un punto chiave nella finestra di progettazione di applicazioni disponibilità consiste nell'usare ritentare all'interno del codice per gestire correttamente un servizio è temporaneamente verso il basso. Il [Blocco dell'applicazione di gestione temporaneo Fault](https://msdn.microsoft.com/library/hh680934.aspx), sviluppato dal team Microsoft Patterns and Practices aiuta gli sviluppatori di applicazioni in questa procedura. La parola "temporaneo" indica che una condizione temporanea che dura solo una volta relativamente breve. Nel contesto di questo articolo, gestione degli errori di temporanei fa parte di sviluppo di un'applicazione di disponibilità. Esempi di condizioni temporanee errori di rete intermittente e persi connessioni al database.

Blocco di guasto Gestione applicazioni temporaneo è un modo semplificato per gestire gli errori all'interno del codice in modo normale. È possibile utilizzare per migliorare la disponibilità delle applicazioni mediante l'aggiunta di logica di gestione degli errori temporanea efficace. Nella maggior parte dei casi, ritentare gestisce breve interruzione e si riconnette il mittente e il destinatario dopo una o più tentativi. Un tentativo ha esito positivo passa in genere rilevato agli utenti dell'applicazione.

Gli sviluppatori sono disponibili tre opzioni per la gestione dei loro ritentare: intervallo fisso di incrementale ed esponenziale. Attesa incrementale più prima ogni Riprova in un modo lineare crescente (ad esempio 1, 2, 3 e 4 secondi). Intervalli di tempo di attesa la stessa quantità di tempo tra ogni tentativi (ad esempio 2 secondi). Per un'opzione più casuale back-off esponenziale attesa più tra tentativi. Tuttavia, Usa il comportamento esponenziale (ad esempio, 2, 4, 8 e 16 secondi).

La strategia di alto livello all'interno del codice è:

1. Definire la strategia di Riprova e criteri.
1. Ripetere l'operazione potrebbe causare un errore temporaneo.
1. Se si verifica l'errore temporanee, richiamare il criterio Riprova.
1. Se tutti i tentativi hanno esito negativo, rilevare un'eccezione finale.

Testare la logica riprova simulata errori per garantire che tentativi in operazioni successive non vengano un ritardo lungo imprevisto. Ripetere l'operazione prima di decidere di non riuscire l'intera attività.

###<a name="reference-data-pattern-for-high-availability"></a>Serie di dati di riferimento per la disponibilità elevata

Dati di riferimento sono i dati di sola lettura di un'applicazione. Questi dati forniscono il contesto di business in cui l'applicazione genera dati delle transazioni nel corso di un'operazione di business. Dati delle transazioni sono una funzione in un momento dei dati di riferimento. Di conseguenza, la sua integrità dipende snapshot dei dati di riferimento al momento della transazione. Si tratta di una definizione di un po' separata, ma dovrebbe essere sufficiente per il nostro obiettivo qui.

Dati di riferimento nel contesto di un'applicazione sono necessari per il funzionamento dell'applicazione. Le applicazioni rispettive creare e gestire dati di riferimento; sistemi di gestione (MDM) dati master spesso eseguono questa funzione. Questi sistemi sono responsabili per il ciclo di vita dei dati di riferimento. Catalogo di prodotti, dipendenti master, schema parti e apparecchiature master esempi di dati di riferimento. Dati di riferimento possono derivano all'esterno dell'organizzazione, ad esempio codici postali o sulle imposte tariffe. Strategie per migliorare la disponibilità dei dati di riferimento sono in genere meno difficile quelli per i dati delle transazioni. Dati di riferimento ha il vantaggio di essere prevalentemente non modificabile.

È possibile rendere Azure ruoli web e di lavoro che utilizzano i dati di riferimento autonomo in fase di esecuzione distribuendo i dati di riferimento insieme dell'applicazione. Se le dimensioni dello spazio di archiviazione locale consentono ad esempio una distribuzione, si tratta di uno stato ideale. Database integrati (SQL, NoSQL) o file XML distribuiti in un file system locale consentiranno agli autonomia calcolo Azure unità della scala. Tuttavia, è necessario avere un meccanismo per aggiornare i dati associati a ogni ruolo senza richiedere la ridistribuzione. A tale scopo, posizionare tutti gli aggiornamenti per i dati di riferimento a un endpoint di spazio di archiviazione cloud (ad esempio, archiviazione Blob Azure o Database SQL). Aggiungere codice a ogni ruolo scaricati gli aggiornamenti dei dati in nodi di elaborazione all'avvio di ruolo. In alternativa, aggiungere il codice che consente agli amministratori di eseguire il download forzato nella istanze del ruolo.

Per aumentare la disponibilità, i ruoli devono contenere un set di dati di riferimento nel caso in cui lo spazio di archiviazione è verso il basso. In questo modo i ruoli iniziare con un set di dati di riferimento base finché la risorsa lo spazio di archiviazione diventa disponibile per gli aggiornamenti.

![Disponibilità dell'applicazione tramite nodi di calcolo autonomo](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Una soluzione per questo motivo è la distribuzione e la velocità di avvio per i ruoli. Se si sta distribuzione o il download di grandi quantità di dati di riferimento all'avvio, questa operazione possibile aumentare la quantità di tempo che necessario per la riattivazione nuove distribuzioni o istanze del ruolo. È possibile accettabili anche per autonomia di disporre dei dati di riferimento immediatamente disponibili in ogni ruolo anziché a seconda del tipo servizi esterni dello spazio di archiviazione.

###<a name="transactional-data-pattern-for-high-availability"></a>Modello di dati delle transazioni per disponibilità

Dati delle transazioni sono i dati che genera l'applicazione in un contesto aziendale. Dati delle transazioni sono una combinazione di set di processi aziendali che l'applicazione implementato e i dati di riferimento che supporta questi processi. Esempi di dati delle transazioni possono includere gli ordini, avvisi di spedizione avanzate, fatture e relationship management (CRM) opportunità di vendita. I dati transazioni generati in questo modo verranno notte per sistemi esterni per la registrazione o per ulteriori elaborazioni.

Tenere presente che fanno riferimento a dati possono cambiare in sistemi responsabili di questi dati. Per questo motivo, dati delle transazioni necessario salvare il contesto di dati di riferimento nel momento in modo che abbia dipendenze esterne minime per la coerenza semantica. Si consideri ad esempio la rimozione di un prodotto dal catalogo alcuni mesi dopo un ordine viene eseguito. La procedura consigliata consiste nell'incorporare il maggior numero contesto di dati di riferimento possibile nella transazione. Per mantenere la semantica associata alla transazione, anche se i dati di riferimento vengono modificati dopo la transazione verrà acquisita.

Come detto in precedenza, architetture che utilizzano aggancio separato e comunicazioni asincrone possono livelli superiori di disponibilità. Ciò avviene per transazione i dati, ma l'implementazione è più complessa. Nozioni di transazione tradizionali si basano sul database per garantire la transazione. Quando si introducono livelli intermedi, il codice dell'applicazione necessario gestire correttamente i dati su vari livelli per garantire la coerenza e durata sufficienti.

La sequenza seguente descrive un flusso di lavoro che separa l'elaborazione di acquisizione di dati delle transazioni:

1. Nodo di calcolo Web: presenta fanno riferimento a dati.
1. Archiviazione esterna: salvare i dati delle transazioni intermedi.
1. Nodo di calcolo Web: completare la transazione per l'utente finale.
1. Nodo di calcolo Web: inviare i dati transazioni completati, insieme al contesto di dati di riferimento a temporaneo archivio permanente che sicuramente prevedibili di risposta.
1. Nodo di calcolo Web: segnalare il completamento per l'utente finale della transazione.
1. Nodo di calcolo in background: estrarre i dati delle transazioni, post-elaborazione, se necessario e inviarla alla posizione di archiviazione finale nel sistema corrente.

Il diagramma seguente illustra una possibile implementazione di questo tipo di progetto in un servizio cloud ospitato Azure.

![Disponibilità tramite aggancio separato](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Le frecce tratteggiate nel diagramma precedente indicano asincronia. Il ruolo front-end web non è presente questo asincronia. Questo comporta l'archiviazione della transazione la destinazione finale per il sistema corrente. A causa della latenza che questo modello asincrono introduce, transazioni dati non sono immediatamente disponibili per la query. Pertanto, ogni unità di dati transazioni deve essere salvato in una cache o una sessione utente per la riunione immediata UI specifiche esigenze.

Il ruolo web è autonomo rispetto al resto dell'infrastruttura. Il suo profilo disponibilità è una combinazione di ruolo web e coda Azure e non l'intera infrastruttura. Oltre a disponibilità, in questo modo il ruolo web scalare orizzontalmente, indipendente archiviazione back-end. Questo modello di disponibilità può avere un impatto sulla economico delle operazioni. Componenti aggiuntivi come code Azure ruoli e di lavoro possono influire sulla mensile costi di utilizzo.

Si noti che il diagramma precedente mostra una implementazione di questo approccio disaccoppiato ai dati delle transazioni. Sono disponibili molte altre implementazioni possibili. L'elenco seguente include alcune alternative:

 * Un ruolo di lavoro potrebbe essere inserito tra il ruolo web e coda di spazio di archiviazione.
 * Invece di una coda di archiviazione Azure, è possibile utilizzare una coda Bus di servizio.
 * La destinazione finale potrebbe essere Azure lo spazio di archiviazione o il provider di un database diverso.
 * Cache Azure utilizzabili nel livello web per fornire i requisiti di memorizzazione nella cache immediatamente dopo la transazione.

###<a name="scalability-patterns"></a>Modelli di scalabilità

È importante tenere presente che la scalabilità del servizio cloud influisce direttamente sulla disponibilità. Se aumento del carico, il servizio di rispondere, la stampa di utente è l'applicazione verso il basso. Seguire le procedure consigliate per la scalabilità in base al carico previsto dell'applicazione e le aspettative future. La scala massima comporta molte considerazioni, ad esempio l'utilizzo del valore singolo o più account di archiviazione, condivisione tra più database e la memorizzazione nella cache strategie. Per in modo approfondito questi modelli, vedere [Procedure consigliate per la progettazione di grandi dimensioni servizi nel server di servizi Cloud Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie di articoli basato sul [ripristino di emergenza e disponibilità per applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). Articolo successivo in questa serie è [di emergenza per applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
