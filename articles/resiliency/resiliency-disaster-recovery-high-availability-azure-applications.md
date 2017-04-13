<properties
   pageTitle="Ripristino di emergenza e disponibilità per applicazioni Azure | Microsoft Azure"
   description="Cenni preliminari tecnici e informazioni più approfondite sulla progettazione di applicazioni per alta disponibilità e ripristino di emergenza delle applicazioni basate su Microsoft Azure."
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

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Ripristino di emergenza e disponibilità per applicazioni basate su Microsoft Azure

##<a name="introduction"></a>Introduzione

In questo articolo è incentrata su disponibilità per le applicazioni in esecuzione in Azure. Una strategia globale per disponibilità include anche l'aspetto di emergenza. Pianificazione di errori e guasti nel cloud è necessario riconoscere rapidamente gli errori. Quindi possibile implementare una strategia che soddisfa la tolleranza di inattività dell'applicazione. Inoltre, è necessario prendere in considerazione quanto perdita di dati che dell'applicazione tollerabile senza conseguenze business negativi al momento del ripristino.

La maggior parte delle società pronunciare che sono pronti per gli errori temporanei e su larga scala. Tuttavia, prima che la domanda per se stessi, la propria azienda prova questi errori? Il ripristino del database avere a disposizione i processi corretti nella posizione si testano? Probabilmente no. Ciò avviene perché il ripristino di emergenza ha esito positivo inizia con numerosi passaggi di pianificazione e architettura per implementare questi processi. Come molti altri requisiti non funzionali, ad esempio la sicurezza di emergenza Ottiene raramente occupata dall'analisi e allocazione tempo che richiede l'esecuzione. Inoltre, la maggior parte delle società, non è il preventivo delle aree geografiche con capacità ridondanti. Di conseguenza, applicazioni critiche pari spesso vengono esclusi dalla pianificazione del ripristino di emergenza corretto.

Piattaforme di cloud, ad esempio Azure, forniscono distribuiti in diverse località geografiche tutto il mondo. Queste piattaforme offrono funzionalità che supportano la disponibilità e una varietà di emergenza. A questo punto, possono essere specificata tutte le applicazioni cloud fondamentale missione terrà debito per strumenti di correzione emergenza del sistema. Azure adattabilità e integrato in molti dei relativi servizi di emergenza. È necessario esaminare attentamente queste funzionalità di piattaforma e integrare con strategie di applicazioni.

In questo articolo sono descritte le procedure necessarie architettura è necessario eseguire il controllo ortografico di emergenza distribuzione di Windows Azure. È possibile implementare il processo di continuità aziendale. Un piano di continuità aziendale è una Guida di orientamento per continuare operazioni condizioni negativi. Può trattarsi di un errore con la tecnologia, ad esempio un servizio inattivo o emergenza naturale, ad esempio un'interruzione dell'eccesso o power. Adattabilità applicazione di emergenza è solo un sottoinsieme del processo di ripristino di emergenza più grande, come descritto in questo documento NIST: [Guida alla pianificazione di emergenza per sistemi informatici](https://www.fismacenter.com/sp800-34.pdf).

Nelle sezioni seguenti vengono definiscono diversi livelli di errori, tecniche per gestire e architetture che supportano queste tecniche. Queste informazioni sono disponibili input per i processi di ripristino di emergenza e le procedure, per assicurarsi che la strategia di ripristino di emergenza funziona correttamente ed efficace.

##<a name="characteristics-of-resilient-cloud-applications"></a>Caratteristiche delle applicazioni cloud flessibili

Un'applicazione correttamente possibile contrastare errori di funzionalità a un livello tattico ed anche possibile tollerate strategici errori di sistema a livello di area. Nelle sezioni seguenti vengono definiscono la terminologia a cui fa riferimento in tutto il documento per descrivere vari aspetti dei servizi cloud flessibile.

###<a name="high-availability"></a>Disponibilità

Un'applicazione cloud disponibilità implementato strategie facciano l'interruzione di dipendenze, ad esempio i servizi gestiti offerti da piattaforma cloud. Nonostante possibili problemi di funzionalità della piattaforma cloud, questo approccio consente all'applicazione di continuare a presentare le caratteristiche di sistematico funzionale e non funzionali previste. Questo argomento viene trattato in modo completo della serie di video Channel 9 [sicura: indicazioni per architetture Cloud flessibili](https://channel9.msdn.com/Series/FailSafe).

Quando l'implementazione dell'applicazione, è necessario considerare la probabilità di un'interruzione di funzionalità. Inoltre, considerare l'impatto che avrà un'interruzione sull'applicazione dal punto di vista business prima di leggere profondità i strategie di implementazione. Senza scadenza considerazione l'impatto aziendale e la probabilità di raggiungere la condizione di rischio, l'implementazione può essere costosa e potenzialmente non necessarie.

Valutare la possibilità di un'analogia componenti auto disponibilità elevata. Anche parti di qualità e progettazione superiore non impedisce l'occasionali errori. Ad esempio, quando l'auto Ottiene un pneumatico flat, Auto viene ancora eseguito, ma funziona con la funzionalità peggiore. Se è pianificata per questa occorrenza potenziale, è possibile usare uno di questi pneumatici riserva rimmed sottile fino a raggiungere un negozio di ripristino. Sebbene il pneumatico riserva non consente di velocità elevate, è possibile utilizzare ancora veicolo fino a quando non si sostituisce il pneumatico. Analogamente, un servizio cloud che plan di messaggistica unificata per la possibile perdita di funzionalità impedire un problema di lieve arrestare l'intera applicazione. Ciò avviene anche se il servizio cloud deve essere eseguito con funzionalità peggiore.

Esistono alcune caratteristiche chiave di servizi cloud disponibilità: disponibilità, scalabilità e la tolleranza. Sebbene queste caratteristiche sono correlate, è importante conoscere ogni e come contribuire alla disponibilità complessiva della soluzione.

###<a name="availability"></a>Disponibilità

Un'applicazione disponibile vengono tenuti in considerazione la disponibilità dei servizi dipendenti e infrastruttura sottostante. Applicazioni disponibili rimuovere singoli punti di errore ridondanza e nella progettazione flessibile. Quando si ampliare da prendere in considerazione la disponibilità di Azure, è importante conoscere il concetto di disponibilità efficace della piattaforma. Disponibilità efficace considera il livello di contratti di servizio di ogni servizio dipendente e il loro effetto cumulativo sulla disponibilità totale del sistema.

Disponibilità del sistema è la misura della percentuale di un intervallo di tempo il sistema sarà in grado di funzionare. Ad esempio, la disponibilità contratto di servizio di almeno due istanze di un ruolo web o lavoro Azure è 99,95% (fuori al 100%). Misurare le prestazioni dei servizi in esecuzione ai ruoli. Tuttavia, la disponibilità effettiva del servizio cloud influisce anche i contratti di servizio diversi degli altri servizi dipendenti. Le parti più spostamento all'interno del sistema, maggiore attenzione che è necessario eseguire per garantire l'applicazione possibile resiliently soddisfare i requisiti di disponibilità degli utenti finali.

Considerare le seguenti contratti di servizio per un servizio di Azure che utilizza servizi Azure: calcolo, Database SQL Azure e lo spazio di archiviazione di Azure.

|Servizio di Azure|CONTRATTO DI SERVIZIO   |Potenziali minuti il tempo di inattività/mese (30 giorni)|
|:------------|:-----|:----------------------------------------:|
|Calcolare      |99,95%|minuti 21,6                              |
|Database SQL |99,99%|minuti 4.3                              |
|Spazio di archiviazione      |99,90%|minuti 43,2                              |

È necessario pianificare per tutti i servizi a potenzialmente scendere di orari. In questo esempio semplificato, il numero totale di minuti al mese per l'applicazione potrebbe essere verso il basso è 108 minuti. Un mese di 30 giorni di un numero totale di 43,200 minuti. minuti 108 è.25 percentuale del totale dei minuti in un mese di 30 giorni (43,200 minuti). Si ottiene così un'effettiva disponibilità del 99.75% per il servizio cloud.

Tuttavia, utilizzando tecniche di disponibilità descritte in questo documento può migliorare seguente. Ad esempio, se la progettazione dell'applicazione venga eseguito quando il Database di SQL non è disponibile, è possibile rimuovere che dall'equazione. Questa operazione, è probabile che l'applicazione viene eseguita con funzionalità ridotte, pertanto non esistono anche esigenze da prendere in considerazione. Per un elenco completo dei contratti di servizio di Azure, vedere [I contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Scalabilità

Scalabilità influisce direttamente sulla disponibilità. Un'applicazione che si verifica un errore in condizioni di carico maggiore non è più disponibile. Scalable applicazioni in grado di soddisfare un aumento della domanda con risultati coerenti, in intervalli di tempo accettabile. Quando un sistema è scalable, viene modificato in scala orizzontalmente o verticalmente per gestire gli aumenti di carico mantenendo prestazioni coerenti. In poche parole, scalabilità orizzontale aggiunge più macchine della stessa dimensione (processore, memoria e della larghezza di banda), mentre si aumenta di ridimensionamento verticale le dimensioni del computer esistenti. Per Azure, sono disponibili le opzioni di ridimensionamento verticale per la selezione di varie dimensioni machine per calcolo. Tuttavia, modificare le dimensioni del computer richiede una nuova distribuzione. Di conseguenza, le soluzioni più flessibile sono progettate per il ridimensionamento orizzontale. In questo modo soprattutto per calcolo, è possibile aumentare facilmente il numero di istanze di qualsiasi ruolo web o lavoro in esecuzione. Le istanze aggiuntive gestiscono un aumento del traffico tramite il portale Web Azure, gli script di PowerShell o il codice. Questa decisione sul aumento della metriche monitorate specifiche di base. In questo scenario, delle prestazioni o metrica non può essere notevole in condizioni di carico. In genere, i ruoli web e lavoro archiviano uno stato esternamente. In questo modo per bilanciamento del carico flessibile e la gestione delle modifiche apportate a conteggi istanza. Scalabilità orizzontale funziona anche con i servizi, ad esempio lo spazio di archiviazione di Azure, che non consentono a più livelli per il ridimensionamento verticale.

Distribuzioni cloud devono essere considerate come un insieme di unità di scala. In questo modo un'applicazione di essere flessibile in manutenzione le esigenze di velocità degli utenti finali. Le unità di scala sono più facili da visualizzare a livello di server web e applicazione. In questo modo Azure offre già nodi di calcolo senza informazioni sullo stato tramite i ruoli web e di lavoro. Aggiunta di che più calcolare le unità della scala alla distribuzione non impedirà l'applicazione stato gestione effetti secondari, perché le unità della scala calcolo sono prive di stato. Un'unità di scala dello spazio di archiviazione è responsabile della gestione di una partizione di dati (strutturata o non strutturata). Esempi di unità di spazio di archiviazione scala includono partizione tabella Azure, contenitore Blob Azure e Database di SQL Azure. Anche l'utilizzo di più account di archiviazione Azure ha un impatto diretto sulla scalabilità dell'applicazione. È necessario progettare un servizio cloud scalabilità per incorporare più spazio di archiviazione scala a unità. Ad esempio, se un'applicazione utilizza dati relazionali, dividere i dati tra diversi database SQL. In questo modo consente l'archiviazione di gestire il modello di unità scala elaborazione flessibile. Allo stesso modo, lo spazio di archiviazione di Azure consente schemi che richiedono strutture intenzionale per soddisfare le esigenze di velocità del livello di calcolo di partizione i dati. Per un elenco di procedure consigliate per la progettazione dei servizi cloud scalable, vedere [Procedure consigliate per la progettazione di grandi dimensioni servizi nel server di servizi Cloud Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolleranza

Applicazioni devono presuppongono che ogni funzionalità cloud dipendenti possibile e andrà verso il basso in un determinato momento nel tempo. Un'applicazione di tolleranza di errore vengono rilevate e mosse attorno agli elementi non riusciti, per continuare e restituire i risultati corretti all'interno di un periodo di tempo specifico. Per le condizioni di errore temporaneo di un sistema di tolleranza di errore verrà utilizzano un criterio di tentativi. Errori più gravi, per l'applicazione possibile rilevare i problemi e non su hardware alternativo o piani finché non viene risolto l'errore. Un'applicazione affidabile correttamente possa gestire l'errore di una o più parti e continuare a funzionare correttamente. Le applicazioni di tolleranza di errore possono utilizzare uno o più strategie di progettazione, ad esempio la ridondanza, replica o funzionalità peggiore.

##<a name="disaster-recovery"></a>Ripristino di emergenza

Una distribuzione cloud può smettere di funzionare a causa di un'interruzione del sistema di servizi dipendenti o infrastruttura. In queste condizioni, un piano di continuità aziendale avvia il processo di ripristino di emergenza. Questo processo prevede in genere personale e procedure automatiche per riattivare l'applicazione in un'area disponibile. È necessario il trasferimento di utenti dell'applicazione, dati e servizi per la nuova area. Prevede anche l'utilizzo di supporti di backup o replica in corso.

Valutare la possibilità di analogia precedente che compared disponibilità per la possibilità di ripristinare da un pneumatico flat mediante l'utilizzo di riserva. Invece di emergenza prevede i passaggi effettuati dopo un arresto anomalo Auto, in cui l'auto non è più operativa. In questo caso, la soluzione migliore è trovare un modo efficace per modificare Auto, chiamando un amico o un servizio di viaggio. In questo scenario, è probabile che agirà da un più ritardi nell'apertura di nuovo in viaggio. Inoltre, è più complessa il ripristino e tornare alla veicolo originale. Nello stesso modo di emergenza a un'altra area è un'attività complessa che prevede in genere alcuni i tempi di inattività e perdita dei dati. Per comprendere e valutare strategie di ripristino di emergenza meglio, è importante definire due termini: obiettivo di tempo di ripristino (RTO) e il ripristino scegliere obiettivo (rilasciato).

###<a name="recovery-time-objective"></a>Obiettivo di tempo di ripristino

Il RTO è la maggior quantità di tempo a disposizione per il ripristino delle funzionalità dell'applicazione. Questo è in base alle esigenze aziendali e correlata alla priorità dell'applicazione. Applicazioni aziendali più importanti richiedono un RTO bassa.

###<a name="recovery-point-objective"></a>Obiettivo punto ripristino

Ordine di produzione rilasciato è l'intervallo di tempo accettabile di perdita di dati a causa del processo di ripristino. Se, ad esempio ordine di produzione rilasciato è un'ora, è necessario eseguire il backup a completamente o replicare i dati almeno ogni ora. Dopo l'applicazione è visualizzata in un'area alternativa, i dati di backup potrebbero essere presenti fino a un'ora di dati. Ad esempio RTO, applicazioni critiche destinazione minore rilasciato.

##<a name="checklist"></a>Elenco di controllo

Di seguito riepilogare i punti chiavi che sono stato trattati in questo articolo (e il relativo articoli correlati sulla [disponibilità](./resiliency-high-availability-azure-applications.md) e [ripristino](./resiliency-disaster-recovery-azure-applications.md) per le applicazioni Azure). Questo riepilogo agirà come un elenco di elementi che è necessario prendere in considerazione per la propria disponibilità e pianificazione del ripristino di emergenza. Si tratta di procedure consigliate che sono stati utili per i clienti che cercano di ottenere seriamente l'implementazione di una soluzione di successo.

1. Condurre una valutazione dei rischi per ogni applicazione, in quanto ogni può avere diverse esigenze. Alcune applicazioni più critiche rispetto ad altri e da giustificare i costi per poterli architettura di emergenza aggiuntivi.
1. Utilizzare queste informazioni per definire la RTO e rilasciato per ogni applicazione.
1. Progettazione per l'errore, iniziando l'architettura dell'applicazione.
1. Implementare le procedure consigliate per la disponibilità elevata durante la riconciliazione costi, complessità e i rischi.
1. Implementare piani di ripristino e processi.
  * Valutare la possibilità di errori che si estendono su livello di modulo a un servizio cloud completo.
  * Stabilire strategie di backup per tutti i riferimenti e i dati delle transazioni.
  * Scegliere un'architettura di ripristino di emergenza più siti.
1. Definire un proprietario per processi di ripristino di emergenza, automazione e verifica. Il proprietario deve gestire e proprietari l'intero processo.
1. Documentare i processi in modo che siano agevolmente riprodotta. Anche se esiste un proprietario, più persone dovrebbero essere possibile comprendere e seguire i processi in caso di emergenza.
1. Formare il personale per implementare il processo.
1. Utilizzare simulazioni di emergenza normale per la formazione e convalida del processo.

##<a name="summary"></a>Riepilogo

Quando hardware o applicazioni non all'interno di Azure, le tecniche e strategie per gestirle sono diverse rispetto a quando si verifica errore nei sistemi locali. Il motivo principale è che soluzioni basate su cloud che in genere altre dipendenze sulle infrastruttura distribuita in un'area di Azure e gestita come servizi distinti. È necessario gestire con errori parziali utilizzando le tecniche di disponibilità. Per gestire errori più gravi, probabilmente a causa di un evento di emergenza, utilizzare strategie di ripristino di emergenza.

Azure rileva e gestisce molti errori, ma sono disponibili molti tipi di errori che richiedono strategie specifiche dell'applicazione. Attivamente pianificare e gestire gli errori delle applicazioni e servizi di dati.

Durante la creazione dell'applicazione disponibilità ed emergenza, prendere in considerazione le conseguenze business di errore dell'applicazione. Definizione dei processi, criteri e le procedure per ripristinare il sistema critico dopo un errore irreversibile richiede tempo, pianificazione e impegno. E dopo aver stabilito i piani, non è possibile impedire non esiste. È necessario analizzare periodicamente, provare e continuamente e migliorare i piani in base a un portfolio di applicazioni, le esigenze aziendali e le tecnologie disponibili per l'utente. Azure offre nuove funzionalità e viene quindi generato nuove sfide per la creazione di applicazioni affidabili contrastare errori.

##<a name="additional-resources"></a>Risorse aggiuntive

[Disponibilità di applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Ripristino delle applicazioni sviluppate in Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Indicazioni tecniche sulla resilienza Azure](resiliency-technical-guidance.md)

[Panoramica: Cloud business continuità e database di emergenza con Database SQL](../sql-database/sql-database-business-continuity.md)

[Alto disponibilità e ripristino di emergenza per SQL Server in macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Sicura: Indicazioni per architetture cloud flessibile](https://channel9.msdn.com/Series/FailSafe)

[Procedure consigliate per la progettazione di grandi dimensioni pagina servizi nel servizi Cloud Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Passaggi successivi

In questo articolo fa parte di una serie di articoli basato sul ripristino di emergenza e disponibilità per applicazioni Azure. Articolo successivo in questa serie è [disponibilità per applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).
