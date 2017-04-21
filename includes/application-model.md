# <a name="compute"></a>Calcolare

Azure consente di distribuire e monitorare il codice dell'applicazione in esecuzione all'interno di un data center Microsoft. Quando si crea un'applicazione e viene eseguito in Azure, il codice e configurazione insieme si chiama un Azure ospitato servizio. Servizi di hosting sono facili da gestire, scalare alto e verso il basso, riconfigurare e aggiornare con le nuove versioni del codice dell'applicazione. In questo articolo è incentrata su Azure ospitato modello di applicazione del servizio.<a id="compare" name="compare"></a>

## Sommario<a id="_GoBack" name="_GoBack"></a>

-   [Vantaggi di modello applicazione Azure][]
-   [Servizio di hosting concetti][]
-   [Considerazioni sulla progettazione di servizio di hosting][]
-   [La progettazione dell'applicazione di scala][]
-   [Definizione di servizio di hosting e configurazione][]
-   [File di definizione del servizio][]
-   [Il File di configurazione del servizio][]
-   [Creare e distribuire un servizio di hosting][]
-   [Riferimenti][]

## <a id="benefits"> </a>Vantaggi modello applicazione azure

Quando si distribuisce l'applicazione come un servizio di hosting, Azure consente di creare uno o più macchine () che contengono codice dell'applicazione e viene avviato macchine virtuali in computer che risiedono in una delle aree di dati di Azure. Come le richieste di client per l'applicazione ospitata immettono il centro dati, un servizio di bilanciamento del carico distribuisce queste richieste identico in macchine virtuali. Durante l'applicazione è ospitata in Azure, ottiene tre vantaggi principali:

-   **Disponibilità.** Disponibilità elevata significa che Azure garantisce che l'applicazione è in esecuzione il più possibile e in grado di rispondere a richieste di client. Se l'applicazione termina (a causa di un'eccezione non gestita, ad esempio), quindi Azure rileva questa e verrà automaticamente riavviare l'applicazione. Se il computer l'applicazione è in esecuzione esperienze alcuni tipi di errore hardware, quindi Azure verrà anche rilevare questo e automaticamente creare una nuova macchina virtuale in un altro computer fisico di lavoro ed eseguire il codice da tale posizione. Nota: Affinché l'applicazione ottenere livello contratto per i Microsoft servizi di 99,95% disponibile, è necessario disporre almeno due macchine virtuali in esecuzione di codice dell'applicazione. In questo modo una macchina virtuale per elaborare le richieste di client mentre Azure consente di spostare il codice da una macchina virtuale non riuscita a una macchina virtuale nuova, è possibile.

-   **Scalabilità.** Azure consente di facilmente e modificare in modo dinamico il numero di macchine virtuali in esecuzione di codice dell'applicazione per gestire il carico effettivo viene posizionato sull'applicazione. In questo modo è possibile regolare l'applicazione per il carico di lavoro che i clienti siano posizionando su di esso durante la riproduzione solo per le macchine virtuali è necessario quando è necessario. Quando si desidera modificare il numero di macchine virtuali, Azure risponde all'interno di minuti che consente di modificare in modo dinamico il numero di macchine virtuali in esecuzione come spesso in base alle esigenze.

-   **Gestibilità.** Poiché Azure è una piattaforma come un'offerta di servizi (PaaS), consente di gestire l'infrastruttura (hardware, elettricità e networking) necessaria per mantenere i computer che eseguono. Azure gestisce anche la piattaforma, garantire un sistema operativo aggiornato con tutte le patch corretta e aggiornamenti della sicurezza, nonché gli aggiornamenti dei componenti, ad esempio .NET Framework e Internet Information Server. Poiché tutte le macchine virtuali sono in esecuzione Windows Server 2008, Azure offre funzionalità aggiuntive, ad esempio monitoraggio diagnostica, il supporto per desktop remoto, firewall e configurazione di archivio certificati. Tutte queste funzionalità vengono fornite senza costi aggiuntivi. Infatti, quando si esegue l'applicazione in Azure, la licenza di sistema operativo (SO) di Windows Server 2008 è inclusa. Poiché tutte le macchine sono in esecuzione Windows Server 2008, qualsiasi codice eseguito su Windows Server 2008 funziona bene durante l'esecuzione di Azure.

## <a id="concepts"> </a>Ospitati i concetti di base del servizio

Quando l'applicazione viene distribuito come servizio ospitato in Azure, viene eseguito come uno o più *ruoli.* Un *ruolo* si riferisce semplicemente alla configurazione e file dell'applicazione. È possibile definire uno o più ruoli per l'applicazione, ognuna con una serie di file dell'applicazione e la configurazione. Per ogni ruolo nell'applicazione, è possibile specificare il numero di macchine virtuali o *istanze del ruolo*, per eseguire. Nella figura seguente mostra due semplici esempi di un'applicazione modellare come servizio ospitato tramite ruoli e delle istanze del ruolo.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Figura 1: Un singolo ruolo con tre istanze (macchine virtuali) in esecuzione in un centro di dati di Azure

![immagine][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Figura 2: Due ruoli, ognuna con due istanze (macchine virtuali), in esecuzione in un centro di dati di Azure

![immagine][1]

Istanze del ruolo di elaborazione richieste dei client di Internet nel centro di dati tramite quello che viene definito un *endpoint di input*. Un singolo ruolo può avere 0 o più input endpoint. Ogni endpoint indica un protocollo (HTTP, HTTPS o TCP) e una porta. Viene in genere per configurare un ruolo per due endpoint input: HTTP ascolto porte 80 e HTTPS in ascolto sulla porta 443. La figura seguente mostra un esempio di due diversi ruoli con diversi endpoint input indirizzare le richieste dei client ad essi.

![immagine][2]

Quando si crea un servizio ospitato in Azure, essa viene assegnato un indirizzo IP pubblico utilizzabili utilizzabile dai client ad accedervi. Quando si crea il servizio di hosting è necessario selezionare anche un prefisso URL mappato all'indirizzo IP. Il prefisso deve essere univoco come che si desidera riservare essenzialmente il *prefisso*. URL cloudapp.net in modo che nessun utente può avere. Client di comunicano con le istanze del ruolo utilizzando l'URL. In genere, si verrà non distribuire o pubblicare il *prefisso*Azure. URL cloudapp.net. Se, tuttavia, si acquistare un nome DNS dal registrar DNS desiderato e configurare il nome del DNS per reindirizzare le richieste di client per l'URL di Azure. Per ulteriori informazioni, vedere [configurazione di un nome di dominio personalizzato in Azure][].

## <a id="considerations"> </a>Ospitato considerazioni sulla progettazione di servizio

Quando si progetta un'applicazione da eseguire in un ambiente basato su cloud, esistono alcune considerazioni da considerare, ad esempio latenza elevata disponibilità e scalabilità.

Decidere dove si trova il codice dell'applicazione è importante durante l'esecuzione di un servizio ospitato in Azure. Vengono in genere per distribuire l'applicazione al data center più vicino ai clienti di ridurre la latenza e ottenere le massime prestazioni possibili.
Tuttavia, è possibile scegliere un data center più vicino alla società o vicino ai dati se si dispone di alcuni problemi relativi alla giurisdizione previsti o legali sui dati e in cui si trova. Esistono sei centri di dati in tutto il mondo in grado di ospitare il codice dell'applicazione. La tabella seguente illustra le posizioni disponibili:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Paese/area geografica**

</td>
<td style="width: 200px;">
**Aree secondari**

</td>
</tr>
<tr>
<td>
Stati Uniti

</td>
<td>
Nord centrale e Sud centrale

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
America del Nord e Ovest

</td>
</tr>
<tr>
<td>
Area Asia

</td>
<td>
Sudest & est

</td>
</tr>
</tbody>
</table>
Quando si crea un servizio di hosting, si seleziona una regione secondari che indica la posizione in cui si desidera eseguire il codice.

Per ottenere disponibilità e scalabilità elevate, è fondamentale conservare i dati dell'applicazione in un repository centrale accessibile a più istanze del ruolo. A tale scopo, Azure offre diverse opzioni relative all'archiviazione, ad esempio BLOB, tabelle e Database SQL. Vedere l'articolo di [Offerte di spazio di archiviazione di dati di Azure][] per ulteriori informazioni su queste tecnologie di archiviazione. La figura seguente mostra come il bilanciamento del carico all'interno del centro dati Azure distribuisce le richieste di client per le istanze di ruoli diversi che hanno accesso a stesso archivio dati.

![immagine][3]

In genere, si desidera individuare il codice dell'applicazione e i dati nell'interfaccia di dati stesso come in questo modo per una latenza bassa (prestazioni migliori) quando il codice dell'applicazione accede ai dati. Inoltre, non addebitate per la larghezza di banda quando i dati vengono spostati all'interno dello stesso data center.

## <a id="scale"> </a>La progettazione dell'applicazione di scala

In alcuni casi può essere necessario eseguire una singola applicazione (ad esempio un semplice sito web) ed è ospitato in Azure. Ma in genere, l'applicazione può essere costituito diversi ruoli che interagiscono. Ad esempio, nella figura seguente sono disponibili due istanze del ruolo del sito Web, tre istanze del ruolo ordine di elaborazione e una sola istanza del ruolo di Generatore Report. Questi ruoli tutti lavora insieme e il codice per ciascuno di essi può essere riunito e distribuito in un'unica fino a Azure.

![immagine][4]

Il motivo principale per dividere un'applicazione in diversi ruoli ognuna in esecuzione su una serie di istanze del ruolo (vale a dire macchine virtuali) consiste nel scalare i ruoli in modo indipendente. Ad esempio, durante le festività natalizie molti clienti possono acquistare prodotti all'azienda, in modo che è possibile aumentare il numero di istanze del ruolo che esegue il ruolo di sito Web, nonché il numero di istanze del ruolo che esegue il proprio ruolo ordine di elaborazione. Dopo Natale, è possibile ottenere numerose prodotti restituiti, in modo che potrebbe essere necessario molte istanze di sito Web, ma le istanze di ordine di elaborazione meno. Durante il resto dell'anno, potrebbe essere necessario solo le istanze di alcuni siti Web e ordine di elaborazione. Nell'intera tutte queste operazioni, potrebbe essere necessario solo un'istanza di Generatore Report. Flessibilità delle distribuzioni basato sui ruoli in Azure consente di adattare facilmente l'applicazione alle proprie esigenze aziendali.

Vengono in genere avere il ruolo istanze all'interno del servizio ospitato comunicano tra loro. Ad esempio, il ruolo di sito Web accetta ordine del cliente ma quindi si l'ordine di elaborazione per le istanze di ruolo ordine di elaborazione. Il modo migliore per passare modulo lavoro un insieme di istanze del ruolo a un'altra coppia di istanze è utilizzando la tecnologia Accodamento fornita da Azure, il servizio di coda o servizio Bus code. L'uso di una coda è un elemento essenziale del brano qui. Coda consente il servizio di hosting ridimensionare i ruoli in modo indipendente in modo da bilanciare il carico di lavoro con costi. Se il numero di messaggi nella coda aumenta nel tempo, è possibile ridimensionare il numero di ordini istanze del ruolo. Se il numero di messaggi nella coda diminuisce nel tempo, è possibile ridimensionarlo verso il basso il numero di ordini istanze del ruolo. In questo modo si sono acquistati solo per le istanze necessarie per gestire il carico di lavoro effettivo.

Coda vengono forniti anche l'affidabilità. In caso di ridimensionamento verso il basso il numero di ordini istanze del ruolo, Azure decide istanze da terminare. Può decidere di terminare un'istanza che si trova all'interno di un messaggio di coda di elaborazione. Tuttavia, poiché l'elaborazione del messaggio non viene completata correttamente, diventa visibile nuovamente a un'altra ordini ruolo istanza che raccoglie e viene elaborato il messaggio. A causa di visibilità messaggio coda messaggi sicuramente infine l'elaborazione. Coda funge anche da un servizio di bilanciamento del carico distribuendo in modo efficace i messaggi per le istanze di tutti i ruoli che richiedono i messaggi.

Per le istanze di ruolo sito Web, è possibile monitorare il traffico in arrivo nei controlli e decidere di ridurre il numero di esse verso l'alto o verso il basso anche. Coda consente di ridurre il numero di istanze del ruolo di sito Web indipendente le istanze di ruolo ordine di elaborazione. Questo è molto estesa e offre molte flessibilità. Naturalmente, se l'applicazione è costituita da altri ruoli, è possibile aggiungere code aggiuntive come canale per comunicare tra loro per sfruttare le stesse proporzioni e vantaggi di costo.

## <a id="defandcfg"> </a>Ospitato configurazione e la definizione di servizio

La distribuzione di un servizio ospitato in Azure è necessario avere un file di definizione del servizio e un file di configurazione del servizio. Entrambi i file sono file XML e consentono di specificare in modo dichiarativo opzioni di distribuzione per il servizio di hosting. File di definizione del servizio vengono illustrati tutti i ruoli che costituiscono il servizio host e la modalità di comunicazione. Il file di configurazione del servizio descrive il numero di istanze per ogni ruolo e le impostazioni per configurare ogni istanza del ruolo.

## <a id="def"> </a>File di definizione del servizio

Come detto in precedenza, la definizione di servizio (CSDEF) file è un file XML che descrive i vari ruoli che costituiscono l'applicazione completa. Lo schema completo per il file XML sono disponibili qui: [] [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx].
Il file CSDEF contiene un elemento WebRole o WorkerRole per ogni ruolo che si desidera che l'applicazione. Per distribuire un ruolo come ruolo web (con l'elemento WebRole), è necessario che il codice verrà eseguito su un'istanza di ruolo contenente Windows Server 2008 e Internet Information Server (IIS).
Distribuire un ruolo, come un ruolo di lavoro (con l'elemento WorkerRole) significa che l'istanza di ruoli Windows Server 2008 su di esso (IIS non verrà installato).

È possibile creare e distribuire un ruolo di lavoro che utilizza un altro meccanismo ascoltare le richieste web sicuramente (ad esempio, il codice potrebbe creare e utilizzare un HttpListener .NET). Poiché le istanze di ruolo in esecuzione Windows Server 2008, il codice può eseguire tutte le operazioni normalmente disponibili per un'applicazione che eseguono Windows Server
2008.

Per ogni ruolo, si indicano le dimensioni desiderate di macchine Virtuali che devono essere utilizzate le istanze di tale ruolo. La tabella seguente illustra le varie dimensioni macchine Virtuali attualmente disponibili e gli attributi di ogni:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Dimensione memoria virtuale**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Disco rigido**

</td>
<td>
**I/o di rete di punta**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Molto piccola**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Piccolo**

</td>
<td>
1 x 1.6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Media**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Grande**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Molto grandi**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
Addebitate ogni ora per ogni macchina virtuale si utilizza come un'istanza del ruolo e anche addebitate per tutti i dati che le istanze del ruolo inviare di fuori del centro dati. Non addebitate per i dati nel centro dati. Per ulteriori informazioni, vedere [prezzi di Azure] []. In generale, è consigliabile utilizzare più istanze di ruolo piccole e non in alcuni casi di grandi dimensioni in modo che l'applicazione è più guasti a. Al termine di tutte le istanze di ruolo meno, è più gravi un errore in uno di essi vengono per l'applicazione complessiva. Inoltre, come indicato in precedenza, è necessario distribuire almeno due istanze per ogni ruolo per ottenere il contratto di servizio 99,95% Microsoft offre.

File di definizione (CSDEF) del servizio è anche nel punto in cui specificare molti attributi relativi a ogni ruolo all'interno dell'applicazione. Ecco alcuni degli elementi più utili disponibili:

-   **I certificati**. Utilizzare i certificati per la crittografia dei dati o se il servizio web supporta SSL. I certificati devono essere caricate in Azure. Per ulteriori informazioni, vedere [Gestione dei certificati in Azure][]. Questa impostazione XML installa precedentemente caricati certificati nell'archivio certificati dell'istanza ruolo in modo che siano utilizzabili dal codice dell'applicazione.

-   **Impostazione di configurazione dei nomi**. Per i valori che si desidera che le applicazioni da leggere durante l'esecuzione di un'istanza del ruolo. Il valore effettivo delle impostazioni di configurazione è impostato nel file di configurazione (CSCFG) servizio che può essere aggiornato in qualsiasi momento senza che sia necessario ridistribuire il codice. Infatti, è possibile codificare le applicazioni in modo per rilevare i valori di configurazione modificata senza incorrere qualsiasi tempo di inattività.

-   **I punti finali di input**. Qui è possibile specificare HTTP, HTTPS o TCP endpoint (con porte) che si desidera esporre al mondo esterno tramite il *prefisso*. URL cloadapp.net. Quando Azure distribuisce il proprio ruolo, verrà configurato il firewall nell'istanza del ruolo automaticamente.

-   **Endpoint interno**. Specificare eventuali HTTP o TCP endpoint che si desidera esposti ad altre istanze di ruolo ricoperto dai partecipanti che vengono distribuite come parte dell'applicazione. Endpoint interno Consenti tutte le istanze di ruolo all'interno dell'applicazione di comunicare con loro ma non sono accessibili a tutte le istanze di ruolo che si trovano all'esterno dell'applicazione.

-   **Moduli di importazione**. Questi se lo si desidera installare componenti utili in istanze del ruolo. Componenti disponibili per diagnostica il monitoraggio, desktop remoto e Azure connettersi (quindi l'istanza del ruolo per l'accesso alle risorse locale tramite un canale sicuro).

-   **Archivio locale**. Si assegna una sottodirectory nell'istanza del ruolo per l'applicazione da usare. Viene descritta in modo più dettagliato in questo articolo [Offerte di spazio di archiviazione di dati di Azure][] .

-   **Attività di avvio**. Attività di avvio consentono di installare componenti necessari in un'istanza del ruolo all'avvio di. Le attività possono eseguire con privilegi elevate come amministratore, se necessario.

## <a id="cfg"> </a>Il File di configurazione del servizio

Il file di configurazione (CSCFG) del servizio è un file XML che descrive le impostazioni che possono essere modificate senza ridistribuire l'applicazione. Lo schema completo per il file XML sono disponibili qui: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
Il file CSCFG contiene un elemento di ruolo per ogni ruolo all'interno dell'applicazione. Ecco alcuni degli elementi che è possibile specificare nel file CSCFG:

-   **Versione del sistema operativo**. Questo attributo consente di selezionare la versione del sistema operativo (SO) desiderato utilizzata per tutte le istanze di ruolo in esecuzione di codice dell'applicazione. Questo sistema operativo è noto come il *sistema operativo guest*e ogni nuova versione include gli aggiornamenti disponibili in fase di che rilascio il sistema operativo guest e patch di protezione. Se si imposta il valore dell'attributo osVersion su "\*", quindi Azure Aggiorna automaticamente il sistema operativo guest in tutte le istanze del ruolo appena disponibili nuove versioni del sistema operativo guest. Tuttavia, è possibile rifiutare esplicitamente gli aggiornamenti automatici selezionando guest specifico versione del sistema operativo. Ad esempio, impostando l'attributo osVersion a un valore di "WA-GUEST OS 2,8\_201109-01" fa sì che tutte le istanze del ruolo ottenere che cos'è descritta in questa pagina web: [http://msdn.microsoft.com/library/hh560567.aspx][]. Per ulteriori informazioni sulle versioni del sistema operativo guest, vedere [Gestione di aggiornamenti per il sistema operativo guest Azure].

-   **Le istanze**. Valore dell'elemento indica il numero di istanze del ruolo desiderato provisioning in esecuzione il codice per un particolare ruolo. Dal momento che è possibile caricare un nuovo file CSCFG in Azure (senza ridistribuire l'applicazione), è semplice in modo semplice modificare il valore di questo elemento e il caricamento di un nuovo file CSCFG per aumentare o ridurre il numero di istanze di ruolo in esecuzione di codice dell'applicazione in modo dinamico. In questo modo è possibile ridimensionare l'applicazione su o giù per soddisfare carichi effettivo controllando anche quantità addebitate per l'esecuzione di istanze del ruolo.

-   **Valori delle impostazioni di configurazione**. Questo elemento indica i valori per le impostazioni (come definito nel file CSDEF). Il proprio ruolo possibile leggere questi valori mentre è in esecuzione. Questi valori di impostazioni di configurazione in genere utilizzati per le stringhe di connessione al Database SQL o allo spazio di archiviazione di Azure, ma possono essere utilizzati per scopi desiderato.

## <a id="hostedservices"> </a>Creare e distribuire un servizio di hosting

Creazione di un servizio di hosting è necessario innanzitutto accedere al [Portale di gestione Azure] ed effettuare il provisioning di un servizio di hosting specificando un prefisso DNS e il centro di dati desiderato il codice in esecuzione in. Quindi nel proprio ambiente di sviluppo, creare il file di definizione (CSDEF) del servizio, generare il codice dell'applicazione e pacchetto (zip) tutti questi file in un file di pacchetto (CSPKG) del servizio. È anche necessario preparare il file di configurazione (CSCFG) del servizio. Per distribuire il proprio ruolo, è caricare i file CSPKG e CSCFG con l'API di gestione del servizio di Azure. Una volta distribuito, Azure, verrà istanze del ruolo di provisioning nell'interfaccia di dati (in base ai dati di configurazione), estrarre il codice dell'applicazione dal pacchetto, copiarlo istanze del ruolo e le istanze di avvio. A questo punto, il codice sia in esecuzione.

La figura seguente mostra i file CSPKG e CSCFG che creare nel computer di sviluppo. Il file CSPKG contiene il file CSDEF e il codice per due ruoli. Dopo il caricamento di file CSPKG e CSCFG con l'API di gestione del servizio di Azure, Azure crea istanze del ruolo nell'interfaccia di dati. In questo esempio, il file CSCFG indicato che Azure deve creare tre istanze del ruolo \#1 e due istanze di ruolo \#2.

![immagine][5]

Per ulteriori informazioni sulla distribuzione, l'aggiornamento e riconfigurare i ruoli, vedere l'articolo di [distribuzione e aggiornamento di applicazioni Azure][] .<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Riferimenti

-   [Creazione di un servizio di hosting per Azure][]

-   [Gestione dei servizi ospitati in Azure][]

-   [La migrazione delle applicazioni Azure][]

-   [Configurazione di un'applicazione Azure][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Scrittura di Jeffrey Richter (Wintellect)</p>

</div>

  [Vantaggi di modello applicazione Azure]: #benefits
  [Servizio di hosting concetti]: #concepts
  [Considerazioni sulla progettazione di servizio di hosting]: #considerations
  [La progettazione dell'applicazione di scala]: #scale
  [Definizione di servizio di hosting e configurazione]: #defandcfg
  [File di definizione del servizio]: #def
  [Il File di configurazione del servizio]: #cfg
  [Creare e distribuire un servizio di hosting]: #hostedservices
  [Riferimenti]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configurazione di un nome di dominio personalizzato in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Offerte di spazio di archiviazione di dati di Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Gestione dei certificati di Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Gestire gli aggiornamenti a ospiti Azure OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Portale di gestione di Azure]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Distribuzione e all'aggiornamento di applicazioni Azure]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Creazione di un servizio di hosting per Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Gestione dei servizi ospitati in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [La migrazione delle applicazioni Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configurazione di un'applicazione Azure]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
