#<a name="data-management-and-business-analytics"></a>Gestione dei dati e Business Analitica

Gestire e analizzare i dati nel cloud è importante come in altre posizioni. Per consentire di eseguire questa operazione, Azure offre una gamma di tecnologie per l'utilizzo di dati relazionali e non relazionali. In questo articolo vengono presentati ciascuna delle opzioni. 

##<a name="table-of-contents"></a>Sommario      

- [Archiviazione BLOB](#blob)
- [Esecuzione di un DBMS in una macchina virtuale](#dbinvm)
- [Database SQL](#sqldb)
    - [Sincronizzazione di dati SQL](#datasync)
    - [Dati di SQL Reporting utilizzando macchine virtuali](#datarpt)
- [Archiviazione tabella](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Archiviazione BLOB

La parola "blob" è abbreviazione di "oggetto binario di grandi dimensioni" e le descrizioni esattamente quali BLOB: una raccolta di informazioni binarie. Ancora anche se semplice, BLOB sono molto utili. Introduzione all'archiviazione Blob Azure illustrato [nella figura 1](#Fig1) .

<a name="Fig1"></a>![Diagramma di BLOB][blobs]
 
**Figura 1: Archiviazione Blob Azure archivia dati binari - BLOB - nei contenitori.**

Per utilizzare BLOB, creare innanzitutto un *account di archiviazione*di Azure. Durante il processo, specificare la data center Azure in cui verranno archiviati gli oggetti creati tramite questo account. Nel punto in cui si trova, ogni blob create appartiene a un determinato contenitore nell'account di archiviazione. Per accedere a un blob, un'applicazione fornisce un URL con il modulo:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*contenitore*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; è un identificatore univoco assegnato quando viene creato un nuovo account di archiviazione, mentre &lt; *contenitore* &gt; e &lt; *BlobName* &gt; sono i nomi di un contenitore specifico e un blob all'interno del contenitore. 

Azure offre due tipi diversi di BLOB. Sono disponibili i seguenti:

- *Blocco* BLOB, ognuno dei quali può contenere un massimo di 200 GB di dati. Come suggerisce il nome, un blob di blocco è suddiviso in un certo numero di blocchi predefiniti. Se si verifica un errore durante il trasferimento di un blob di blocco, trasmissione più fluida riprendere con il blocco più recente invece di inviare di nuovo l'intero blob. Blocco BLOB sono un approccio piuttosto generale allo spazio di archiviazione e che sia il tipo di uso più frequente blob oggi.

- BLOB di *pagine* , può essere più grande in un terabyte. Pagina BLOB sono progettate per l'accesso casuale e pertanto ciascuno di essi è diviso in un certo numero di pagine. Un'applicazione è gratuita leggere e scrivere in modo casuale singole pagine nel blob. Macchine virtuali di Microsoft Azure, ad esempio macchine virtuali create utilizzano BLOB di pagine come archivio permanente per sia dischi del sistema operativo e di dati.

Se si sceglie blocco BLOB o BLOB di pagine, le applicazioni possono accedere dati blob in molti modi diversi. Le opzioni includono le seguenti:

- Direttamente tramite un RESTful (ad esempio, basate su HTTP) protocollo di accesso. Applicazioni Azure e le applicazioni esterne, incluse le applicazioni in esecuzione in locale, è possano usare questa opzione.
- Utilizzo della libreria di Client di spazio di archiviazione di Azure, che offre un'interfaccia più sviluppo orientata al sopra il protocollo di accesso non elaborato blob REST. Nuovo applicazioni Azure e le applicazioni esterne accedere BLOB mediante questa libreria.
- Utilizzo di unità di Azure, un'opzione che consente di un'applicazione Azure deve essere considerato un blob di pagine un'unità locale con un file system NTFS. All'applicazione, ai blob pagina si presenta come un normale file system di Windows accedervi usando i/o file standard. Infatti, legge e scrive vengono inviate a quello pagina sottostante che implementa l'unità di Azure. 

Per evitare errori hardware e migliorare la disponibilità, ogni blob viene replicato tra tre computer in un Data Center di Azure. Scrivere un blob Aggiorna tutte le copie di tre, in modo che legge successive non è possibile visualizzare risultati incoerenti. È inoltre possibile specificare che i dati del blob devono essere copiati in un'altra data center Azure nella stessa geografico ma almeno 500 miglia fuori sede. La copia, denominata *geografico replica*avviene nell'arco di qualche minuto di un aggiornamento a quello ed è utile per il ripristino di emergenza.

Dati nei blob possono anche essere resi disponibili tramite Azure *Di contenuti rete (CDN)*. Per la memorizzazione nella cache copie dei dati blob in decine di server in tutto il mondo, la rete CDN possibile velocizzare l'accesso alle informazioni che si accede a più volte. 

Semplice come sono, BLOB sono la soluzione ideale nella maggior parte dei casi. L'archiviazione e la trasmissione video e audio sono esempi più evidenti, come backup e altri tipi di archiviazione dei dati. Gli sviluppatori possono anche utilizzare BLOB per contenere qualsiasi tipo di dati non strutturati che vengono soddisfatti. Con un semplice per archiviare e accedere a dati binari può essere estremamente utile.


## <a name="dbinvm"></a>Esecuzione di un DBMS in una macchina virtuale

Molte applicazioni oggi si basano su un tipo di sistema di gestione database (DBMS). Sistemi relazionali, ad esempio SQL Server sono più usate scelta, ma non relazionali approcci, in genere noti come tecnologie *NoSQL* , ottenere più popolari ogni giorno. Per consentire applicazioni cloud utilizzare queste opzioni di gestione dei dati, macchine virtuali di Azure consente di eseguire un DBMS (relazionale o NoSQL) in una macchina virtuale. [Figura 2](#Fig2) Mostra l'aspetto con SQL Server.

<a name="Fig2"></a>![Diagramma di SQL Server in una macchina virtuale][SQLSvr-vm]
 
**Figura 2: Macchine virtuali di Azure consente di eseguire un DBMS in una macchina virtuale con una connessione permanente fornito da BLOB.**

Per gli sviluppatori e amministratori di database, questo scenario l'aspetto molto esegue lo stesso software nel proprio Data Center. Nell'esempio illustrato di seguito, ad esempio, quasi tutte le funzionalità di SQL Server possono essere utilizzate e si dispone dell'accesso amministrativo completo per il sistema. È inoltre la responsabilità di gestione del server di database, naturalmente, come se fosse eseguito in locale.

Come illustrato [nella figura 2](#Fig2) , i database sembrano essere archiviati sul disco locale di macchine Virtuali in cui viene eseguito il server. Dietro le quinte, tuttavia, tali dischi è scritto blob Azure. (È simile all'uso di una rete SAN in Data Center, con un blob molto usarle come un LUN.) Come con qualsiasi blob Azure, i dati contenuti viene replicati tre volte all'interno di un Data Center e, se richiesto, geografico replicate in un'altra Data Center nella stessa regione. È inoltre possibile utilizzare le opzioni, ad esempio database di SQL Server che rispecchiano per migliorare l'affidabilità. 

Un altro modo per usare SQL Server in una macchina virtuale consiste nel creare un'applicazione ibrida, in cui i dati su cui si basa Azure mentre la logica dell'applicazione è in esecuzione in locale. Ad esempio, questa può essere utile quando applicazioni in esecuzione in più posizioni o su diversi dispositivi mobili necessario condividere gli stessi dati. Per rendere le comunicazioni tra il cloud database locale la logica e più semplice, un'organizzazione possa usare virtuali Azure per creare una connessione di rete privata virtuale (VPN) tra un Data Center Azure e il proprio data center locale.


## <a name="sqldb"></a>Database SQL

Per molti utenti, l'esecuzione di un DBMS in una macchina virtuale è la prima opzione fornito in considerazione per la gestione dei dati strutturati nel cloud. Non è l'unica scelta, tuttavia, né viene sempre la scelta migliore. In alcuni casi, la gestione dei dati utilizzando una piattaforma come un approccio modello PaaS ha più senso. Azure offre una tecnologia PaaS denominata Database SQL che consente di ripetere l'operazione per dati relazionali. Questa opzione è illustrato [nella figura 3](#Fig3) . 

<a name="Fig3"></a>![Diagramma di Database SQL][SQL-db]
 
**Figura 3: Database SQL offre un servizio di archiviazione relazionale PaaS condiviso.**

Database SQL non forniscono ogni cliente la propria fisica istanza di SQL Server. Se, tuttavia, fornisce un servizio multi-tenant, con un server di Database SQL logico per ogni cliente. Tutti i clienti condividono la capacità di elaborazione e di archiviazione che fornisce il servizio. E come con archiviazione Blob, tutti i dati nel Database di SQL archiviata nel computer distinto tre all'interno di un Data Center di Azure, assegnando i database predefinite disponibilità (HA).

Per un'applicazione, Database SQL di aspetto molto SQL Server. Applicazioni possono eseguire query SQL su tabelle relazionali, usare T-stored procedure SQL ed eseguire le transazioni in più tabelle. E perché le applicazioni di accedono a Database SQL mediante il protocollo di flusso di dati tabulari (TD), lo stesso protocollo utilizzato per accedere a SQL Server, funzionano con i dati utilizzando entità Framework, ADO.NET, JDBC e altre interfacce di accesso ai dati note. 

Ma poiché Database SQL di un servizio cloud in esecuzione in Azure data center, non è necessario per la gestione di aspetti fisici del sistema, ad esempio di utilizzo del disco. Anche non occorre preoccuparsi di aggiornamento software o la gestione di altre attività amministrative raggio. Ogni organizzazione del cliente controlla ancora i propri database, naturalmente, inclusi i relativi schemi e accessi utente, ma molte attività amministrative più comuni vengono eseguite automaticamente. 

Mentre Database SQL di molto simile a SQL Server alle applicazioni, non funzioni esattamente come un DBMS in esecuzione in una macchina virtuale o fisica. Perché è in esecuzione su hardware condiviso, le prestazioni varia a seconda del carico per tale hardware da tutti i suoi clienti. Questo errore indica che le prestazioni di, ad esempio, una stored procedure nel Database di SQL potrebbe variare da un giorno alla successiva. 

Oggi, Database SQL consente di creare un database contenente i fino a 150 GB. Se è necessario lavorare con database di grandi dimensioni, il servizio è disponibile la *federazione*. A tale scopo, l'amministratore del database crea due o più *membri federazione*, ognuno dei quali è un database separato con il proprio schema. Dati tutta questi membri è quello che viene spesso come *sharding*, con ogni membro assegnata una *chiave federazione*univoco. Una query SQL di problemi di applicazione su questi dati specificando il tasto federazione che identifica il membro federazione la query deve essere indirizzata. In questo modo con un approccio relazionale tradizionale con grandi quantità di dati. Come sempre, esistono compromessi; query né transazioni può essere esteso membri federazione, ad esempio. Ma quando un servizio PaaS relazionale è la scelta migliore e tali compromessi accettabili, utilizzo SQL federazione può essere un'ottima soluzione.

Database SQL possono essere usate dal applicazioni in esecuzione su Azure o in un'altra, ad esempio nel centro dati locale. Questo risulta utile per applicazioni cloud che richiedono dati relazionali, nonché le applicazioni che possono trarre vantaggio dalla memorizzazione dei dati nel cloud in locale. Applicazione per dispositivi mobili potrebbe si basano sul Database di SQL per gestire condivisi dati relazionali, ad esempio, come potrebbe un'applicazione di inventario eseguito più rivenditori in tutto il mondo.

Pensare Database SQL genera un problema più evidente (e importante): quando è perfettamente SQL Server in una macchina virtuale e quando è preferibile Database SQL? Come di consueto, esistono compromessi e pertanto l'approccio è migliore dipende da ai propri requisiti. 

Un modo semplice per interpretare affrontarlo consiste nel visualizzare Database SQL come per le nuove applicazioni, mentre SQL Server in una macchina virtuale è preferibile quando si spostano esistente locale applicazione nel cloud. Può inoltre essere utile esaminare questa decisione in modo più accurato, tuttavia. Ad esempio, è più semplice da utilizzare, poiché è minima Database SQL di impostazione e amministrazione. Ma che esegue SQL Server in una macchina virtuale può avere prestazioni più prevedibili - non è un servizio condiviso - e supporta anche i database non federato maggiori rispetto a Database di SQL. Database SQL fornisce comunque, replica incorporate di dati e trasformazione, in modo efficace dato un DBMS disponibilità in modo molto semplice e. Mentre SQL Server consente un maggiore controllo e un set di opzioni più ampio, è più semplice di impostare e notevolmente meno lavoro per gestire Database SQL.

Infine, è importante sottolineare che SQL Database non sia inserito solo PaaS servizio dati disponibile in Azure. Partner Microsoft forniscono anche altre opzioni. Ad esempio, ClearDB offre un MySQL PaaS che offre la possibilità, mentre Cloudant vende un'opzione NoSQL. Servizi dati PaaS sono la soluzione ideale nella maggior parte dei casi, e pertanto questo approccio alla gestione dei dati è una parte importante di Azure.


### <a name="datasync"></a>Sincronizzazione di dati SQL

Mentre Database SQL di mantenere tre copie di ogni database all'interno di un singolo Data Center Azure, essa non replica automaticamente i dati tra Azure Data Center. Se, tuttavia, fornisce sincronizzazione di dati SQL, un servizio che è possibile utilizzare per eseguire questa operazione. [Figura 4](#Fig4) Mostra l'aspetto.

<a name="Fig4"></a>![Diagramma di sincronizzazione di dati SQL][SQL-datasync]
 
**Figura 4: Sincronizzazione di dati SQL Sincronizza i dati in un Database SQL di dati in altri Azure e Data Center locale.**

Come illustrato nel diagramma, sincronizzazione di dati SQL possibile sincronizzare i dati in posizioni diverse. Si supponga che si esegue un'applicazione in più Data Center Azure, ad esempio, con i dati archiviati in Database SQL. Sincronizzazione di dati SQL è possibile utilizzare per mantenere i dati sincronizzati. Sincronizzazione di dati SQL è inoltre possibile sincronizzare dati tra un Data Center Azure e un'istanza di SQL Server in esecuzione in un data center locale. Potrebbe essere utile per mantenere una copia locale di dati utilizzati dalle applicazioni in locale e una copia di cloud usato dalle applicazioni in esecuzione in Azure. E anche se non è visualizzata nella figura, sincronizzazione di dati SQL possono anche essere utilizzato per sincronizzare i dati tra Database SQL e SQL Server in esecuzione in una macchina virtuale Azure o in un' posizione.

Sincronizzazione può essere bidirezionale e si stabilisce esattamente quali dati vengono sincronizzati e con quale frequenza viene eseguita. (La sincronizzazione tra database non è atomica, tuttavia, è sempre almeno un certo tempo) E ma viene utilizzata, configurare la sincronizzazione con sincronizzazione di dati SQL è completamente basate su configurazione; non è presente codice per scrivere.


### <a name="datarpt"></a>Dati di SQL Reporting utilizzando macchine virtuali

Una volta un database contiene dati, qualcuno probabilmente necessario creare report con i dati. Azure supporti l'esecuzione di SQL Server Reporting Services (SSRS) macchine virtuali di Microsoft Azure, ovvero equivalente all'esecuzione di SQL Server Reporting Services in locale. È quindi possibile utilizzare SSRS per eseguire report sui dati memorizzati in un Database di SQL Azure.  [La figura 5](#Fig5) illustra il funzionamento del processo.

<a name="Fig5"></a>![Diagramma di un report di SQL][SQL-report]
 
**Figura 5: SQL Server Reporting Services in esecuzione in un macchine virtuali di Azure fornisce reporting services per i dati nel Database di SQL. .**

Prima di un utente può visualizzare un report, un utente definisce quel rapporto di aspetto (passaggio 1). Con SSRS in una macchina virtuale, questa operazione può essere eseguita utilizzando uno dei due strumenti: SQL Server Data Tools, parte di SQL Server 2012 o versione precedente, Business Intelligence (BI) Development Studio. Come con SSRS, queste definizioni di report sono espressi nei Report RDL (Definition Language). Dopo avere creati i file RDL per un report, questi vengono caricati in una macchina virtuale nel cloud (passaggio 2). È ora possibile utilizzare la definizione del report.

Successivamente, un utente dell'applicazione accede a report (passaggio 3). L'applicazione passa la richiesta per SSRS VM (passaggio 4), quali contatti Database SQL o altre origini dati per ottenere i dati necessari (passaggio 5). Questi dati vengono utilizzati SSRS e i relativi file RDL per il rendering del report (passaggio 6), quindi restituisce il report per l'applicazione (passaggio 7), che visualizza all'utente (passaggio 8).

Incorporamento di un report in un'applicazione, lo scenario illustrato di seguito, non è l'unica opzione disponibile. È anche possibile visualizzare i report in Gestione rapporti in macchine Virtuali, SharePoint nella macchina virtuale o in altri modi. Report possono anche essere combinati, con un report contenente un collegamento a un altro.

SSRS in una macchina virtuale Azure offre funzionalità complete come una soluzione di report nel cloud. Report è possono utilizzare qualsiasi origine dati supportati da SSRS. Applicazioni e report possono includere codice incorporato o assembly per supportare comportamenti personalizzati. Il rendering e l'esecuzione del report sono fast motore e il contenuto del server di report vengono eseguite nello stesso server virtuale.



## <a name="tblstor"></a>Archiviazione tabella

Dati relazionali sono utili nella maggior parte dei casi, ma non è sempre la scelta migliore. Se l'applicazione necessita di accesso rapido e semplice per grandi quantità di dati strutturati, ad esempio, un database relazionale non funzionino correttamente. Una tecnologia NoSQL è probabile che sia un'opzione migliore.

Archivio tabelle Azure è illustrato un esempio di questo tipo di approccio NoSQL. Nonostante il nome dello spazio di archiviazione tabella non supporta tabelle relazionali standard. Se, tuttavia, fornisce cosiddetta *chiave/valore archiviare*, associazione di un set di dati con una determinata chiave e quindi consentire l'accesso un'applicazione che i dati, fornendo la chiave. [La figura 6](#Fig6) illustra le nozioni di base.

<a name="Fig6"></a>![Diagramma di spazio di archiviazione tabella][SQL-tblstor]
 
**Figura 6: Archivio tabelle Azure è un archivio di chiave/valore che offre un accesso semplice a grandi quantità di dati rapido.**

Come BLOB, ogni tabella è associata a un account di archiviazione Azure. Tabelle denominate anche molto come BLOB, con l'URL del modulo

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Come illustrato nella figura seguente, ogni tabella è diviso in un certo numero di partizioni, ognuno dei quali può essere archiviata in un altro computer. (Questa è una forma di sharding, come con la federazione di SQL) Applicazioni Azure e le applicazioni in esecuzione in un'altra possano accedere a una tabella mediante il protocollo OData REST o alla raccolta di Azure lo spazio di archiviazione Client.

Ognuna in una tabella contiene un certo numero di *entità*, ogni contenente un massimo di 255 *proprietà*. Ogni proprietà ha un nome, un tipo (ad esempio binario, Bool, DateTime, Int o stringa) e un valore. A differenza di archiviazione relazionale, tali tabelle sono Nessuno schema fisso ed entità in modo diversa nella stessa tabella può contenere proprietà con diversi tipi. Un'entità potrebbe essere una proprietà stringa contenente un nome, ad esempio, mentre un altro entità nella stessa tabella ha due proprietà Int contenente un numero di ID cliente e una classificazione di carta di credito.

Per individuare una particolare entità all'interno di una tabella, un'applicazione fornisce chiave dell'entità. La chiave composta da due parti: una *chiave di partizione* che identifica una partizione specifica e una *chiave riga* che identifica un'entità in essa contenuti. Nella [Figura 6](#Fig6), ad esempio, il client richiede l'entità con partizione chiave A e riga 3 e archiviazione tabella restituisce tale entità, incluse tutte le proprietà che contiene.

La struttura consente tabelle essere grande - un'unica tabella può includere fino a 100 terabyte di dati, e consente di accedere rapidamente ai dati che contengono. Anche offre limitazioni, tuttavia. Ad esempio, non è disponibile alcun supporto per gli aggiornamenti transazioni che includono tabelle o le partizioni pari in un'unica tabella. Un insieme di aggiornamenti a una tabella può essere raggruppato in una transazione atomica solo se tutte le entità correlate sono nella stessa partizione. Vi è anche possibile eseguire una query una tabella in base al valore delle proprietà non è disponibile il supporto per i join tra più tabelle. E, a differenza di database relazionali, le tabelle non sono supportate per le stored procedure.

Archivio tabelle Azure è ideale per applicazioni che richiedono un accesso rapido e conveniente per grandi quantità di dati strutturati. Ad esempio, un'applicazione di Internet che archivia le informazioni del profilo per un numero elevato di utenti potrebbe utilizzare tabelle. Accesso rapido è importante in questa situazione e l'applicazione non è probabile che sia necessario tutta la potenza di SQL. Rinuncia a questa funzionalità di ottenere velocità e le dimensioni di talvolta senso e pertanto archiviazione tabella è solo la soluzione ideale per alcuni problemi.


## <a name="hadoop"></a>Hadoop

Organizzazioni sono compilando data warehouse per molti anni. Questi insiemi di informazioni, in genere archiviati in tabelle relazionali, consentire alle persone di lavorare con e imparare dai dati in modi diversi. Con SQL Server, ad esempio, è comune usare gli strumenti, ad esempio SQL Server Analysis Services per eseguire questa operazione.

Ma si supponga di che voler eseguire analisi dati non relazionali. I dati possono richiedere diversi formati: le informazioni provenienti da sensori o tag RFID, file di log in server farm, questi dati prodotti da applicazioni web, le immagini da dispositivi diagnostici medici e altro ancora. Questi dati può essere molto grande, troppo grande per essere utilizzato in modo efficiente con una data warehouse tradizionale. Problemi di dati come il seguente, rari pochi anni, è ora ormai.

Per analizzare questo tipo di dati, settore si è ampiamente orientato in un'unica soluzione: la tecnologia open source Hadoop. Hadoop viene eseguita in un cluster di fisici o macchine virtuali, distribuzione dei dati che funziona in tali computer ed elaborazione in parallelo. Più macchine deve utilizzare Hadoop, più veloce possono completare il lavoro eseguite.

Questo tipo di problema è una scelta naturale per il cloud pubblico. Invece di un macchine virtuali esercito del server che potrebbero rimanere inattive la maggior parte dei casi, in esecuzione Hadoop nel cloud consente di creare locale (e non pagare) solo quando è necessario. Ancora meglio, altre e più grandi dati che si desidera analizzare con Hadoop viene creata nel cloud, senza che sia necessario di spostarlo. Per informazioni su come sfruttare le sinergie, Microsoft offre un servizio di Hadoop in Azure. [Figura 7](#Fig7) Mostra i componenti più importanti del servizio.

<a name="Fig7"></a>![Diagramma di hadoop][hadoop]

**Figura 7: Hadoop in Azure esegue processi MapReduce per l'elaborazione dei dati in parallelo utilizzando più macchine virtuali.**

Per utilizzare Hadoop su Azure, è prima di tutto richiedere questa piattaforma cloud per creare un cluster Hadoop specificando il numero di macchine virtuali è necessario. Configurare un cluster di Hadoop è un'attività non semplice, e pertanto consentendo Azure inserisca automaticamente senso. Dopo aver completato con i cluster, si chiude. Non è necessario effettuare il pagamento per le risorse di elaborazione che non si utilizzano.

Un'applicazione di Hadoop, ossia un *processo*utilizza un modello di programmazione noto come *MapReduce*. Come illustrato nella figura seguente, la logica per un processo MapReduce venga eseguita contemporaneamente su più macchine virtuali. Per l'elaborazione dei dati in parallelo, Hadoop possibile analizzare i dati molto più rapidamente soluzioni solo computer.

Su Azure, i dati di un MapReduce lavora processo è in genere mantenuti nell'archiviazione blob. In Hadoop, tuttavia, MapReduce processi pensare dati da archiviare in *Hadoop Distributed File System (HDFS)*. HDFS è simile a archiviazione Blob in alcuni modi; replica dati più server fisici, ad esempio. Anziché duplicare questa funzionalità, Hadoop in Azure espone invece archiviazione Blob tramite l'API HDFS, come mostrato nella figura. Durante la logica di un processo MapReduce ritiene che accede a normali file HDFS, il processo è infatti uso dei dati in streaming da BLOB. E per supportare il caso in cui vengono eseguiti più processi sopra gli stessi dati, Hadoop in Azure anche Consenti la copia di dati da BLOB in HDFS completo in esecuzione in macchine virtuali. 

Processi MapReduce sono in genere scritto in linguaggio oggi, un approccio che supporta Hadoop in Azure. Microsoft ha aggiunto il supporto per la creazione di processi MapReduce in altre lingue, inclusi c#, F # e JavaScript. L'obiettivo è per rendere più facilmente accessibile a un gruppo più ampio di sviluppatori questa tecnologia di dati.

Oltre a HDFS e MapReduce Hadoop include altre tecnologie che consentire alle persone di analizzare i dati senza la scrittura di un processo MapReduce se stessi. Maialino è ad esempio, un linguaggio di alto livello progettato per l'analisi dei dati, mentre Hive offre un linguaggio SQL denominato HiveQL. Maialino e Hive effettivamente generare lavorative MapReduce elaborano dati HDFS, ma vengono nascondere questa complessità da utenti. Entrambi vengono forniti con Hadoop in Azure.

Microsoft offre anche un driver HiveQL per Excel. Usa un componente aggiuntivo di Excel, business analyst creare query HiveQL (e pertanto processi MapReduce) direttamente da Excel, quindi elaborare e visualizzare i risultati tramite PowerPivot e altri strumenti di Excel. Hadoop su Azure include altre tecnologie, ad esempio apprendimento raccolte Mahout, il sistema di data mining grafico Pegasus e altro ancora.

Analisi dei dati sono importante e pertanto Hadoop è anche importante. Fornendo Hadoop come servizio gestito in Azure, con collegamenti agli strumenti familiari come Excel, Microsoft intende effettuare questa tecnologia accessibile a un gruppo più ampio di utenti.

Un pubblico più ampio, tutti i tipi di dati sono importanti. Questo è il motivo per cui Azure offre una gamma di opzioni per analitica business e gestione dei dati. Qualsiasi altra applicazione che si desidera creare, è probabile che è presente un elemento in questa piattaforma cloud che funziona per è.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
