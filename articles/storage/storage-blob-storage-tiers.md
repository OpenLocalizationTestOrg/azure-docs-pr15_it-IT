<properties
    pageTitle="Azure interessante lo spazio di archiviazione per i BLOB | Microsoft Azure"
    description="Livelli di spazio di archiviazione per l'archiviazione Blob Azure offrono conveniente spazio di archiviazione di dati dell'oggetto in base a modelli di accesso. Il livello di spazio di archiviazione interessanti ottimizzato per i dati che si accede raramente."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Archiviazione Blob Azure: Hot e raffreddare livelli di spazio di archiviazione

## <a name="overview"></a>Panoramica

Spazio di archiviazione Azure offre due livelli di spazio di archiviazione per l'archiviazione Blob (oggetto lo spazio di archiviazione), in modo che è possibile archiviare i dati più economico a seconda di come si usa. Il **livello di spazio di archiviazione attivo** Azure è ottimizzato per l'archiviazione dei dati che si accede frequentemente. Il **livello di spazio di archiviazione interessanti** Azure è ottimizzato per l'archiviazione dei dati che sono raramente accessibile e lunga durata. Dati nel livello di spazio di archiviazione interessanti tollerabile una disponibilità leggermente inferiore, ma richiedono comunque elevata resistenza e l'ora simile per accedere e le caratteristiche di velocità come dati attivo. Per i dati interessanti, contratto di servizio disponibilità leggermente inferiore e costi più elevati di accesso sono accettabili compromessi per ridurre i costi di molto spazio di archiviazione.

Oggi, i dati archiviati nel cloud cresce a una velocità esponenziale. Per gestire i costi per le esigenze di spazio di archiviazione espansione, può essere utile organizzare i dati in base agli attributi come la frequenza di accesso e periodo di conservazione pianificata. Dati archiviati nel cloud possono essere diversi in termini di come viene generato, elaborato e accessibili tramite la relativa durata. Alcuni dati attivamente è accessibile e modificate per tutta la durata. Alcuni dati di accesso molto spesso in anticipo la relativa durata, con accesso rilascio notevolmente la validità dei dati. Alcuni dati di inattività nel cloud e raramente, se mai, aperto una volta archiviate.

Tutti gli scenari dati access descritte in precedenza vantaggi da un livello di applicazione di archiviazione ottimizzate per un criterio di accesso specifici. Con l'introduzione dei livelli di spazio di archiviazione attivo e interessanti, Blob Azure archiviazione indirizzi ora questa esigenza di livelli di spazio di archiviazione applicazione con separare prezzi modelli.

## <a name="blob-storage-accounts"></a>Account di archiviazione BLOB

**Gli account di archiviazione BLOB** sono archiviazione specializzati account per la memorizzazione dei dati non strutturati come BLOB (oggetti) in archiviazione Azure. Negli account di archiviazione Blob, è possibile scegliere tra livelli di spazio di archiviazione attivo e interessanti per archiviare i dati interessanti meno frequentemente all'archiviazione inferiore costi e archiviano dati attivo più di frequente in un accesso basso costo. Gli account di archiviazione BLOB sono simili agli account di archiviazione generico esistente e condividono tutti la durata ideale disponibilità, scalabilità e funzionalità per le prestazioni utilizzate oggi, tra cui la coerenza API 100% per BLOB blocco e accoda BLOB.

> [AZURE.NOTE] Gli account di archiviazione BLOB supportano solo blocco e accoda BLOB e non BLOB di pagine.

Gli account di archiviazione BLOB espongono attributo **Livello di accesso** che consentono di specificare il livello di spazio di archiviazione come **Hot** o **interessanti** in base ai dati archiviati nella finestra account. Se viene apportata una modifica nel modello di utilizzo dei dati, è anche possibile passare tra i livelli di spazio di archiviazione in qualsiasi momento.

> [AZURE.NOTE] Modificare il livello di spazio di archiviazione potrebbe costi aggiuntivi. Vedere la sezione [prezzo e la fatturazione](storage-blob-storage-tiers.md#pricing-and-billing) per altri dettagli.

Scenari di utilizzo di esempio per il livello di spazio di archiviazione attivo includono:

- I dati in uso o previsto per accedervi (lettura da e scritti) frequentemente.
- Dati che viene gestiti per la migrazione di elaborazione e finale al livello di spazio di archiviazione avanzate.

Scenari di utilizzo di esempio per il livello di spazio di archiviazione interessanti includono:

- Eseguire il backup, archiviazione e set di dati di emergenza ripristino.
- Contenuti multimediali precedenti non visualizzare spesso più ma dovrebbe essere disponibile immediatamente quando accede.
- Grandi quantità di dati che devono essere costo archiviata in modo efficace mentre altri dati raccolta delle informazioni per l'elaborazione futuri. (*ad esempio*, a lungo termine dello spazio di archiviazione dei dati scientifici, i dati di telemetria elaborati da una struttura di produzione)
- Originali (dati non elaborati) devono essere mantenuti, anche se è stata elaborata in formato utilizzabile finale. (*ad esempio*, i file multimediali elaborati dopo ricorrere in altri formati)
- Conformità e archiviazione dati che devono essere memorizzati una lunga esperienza e quasi accedere. (*ad esempio*, ripresa videocamera di sicurezza, vecchio X-Rays/MRIs per organizzazioni, le registrazioni audio e trascrizioni delle chiamate di assistenza clienti per i servizi finanziari)

Per ulteriori informazioni sull'account di archiviazione, vedere [gli account di archiviazione su Azure](storage-create-storage-account.md) .

Per le applicazioni che richiedono solo bloccano o aggiungere spazio di archiviazione blob, si consiglia di utilizzare gli account di archiviazione Blob, in modo da sfruttare il modello prezzo applicazione di archiviazione a più livelli. Tuttavia, siamo consapevoli questo potrebbe non essere possibile in alcuni casi in cui usare gli account di archiviazione generico è il modo per passare, ad esempio:

- È necessario utilizzare tabelle, code o file e il BLOB memorizzati nello stesso account di archiviazione. Si noti che non esiste alcun vantaggio tecnica per l'archiviazione di questi elementi nello stesso account diversa da quella che hanno la stessa chiavi condivise.
- Occorre usare il modello di distribuzione classica. Sono disponibili tramite il modello di distribuzione di gestione di risorse Azure solo gli account di archiviazione BLOB.
- È necessario utilizzare BLOB di pagine. Gli account di archiviazione BLOB non supportano BLOB di pagine. In genere, è consigliabile usare BLOB blocco a meno che non si hanno esigenze specifiche per BLOB di pagine.
- Si usa una versione di [API REST di servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) precedente 2014-02-14 o una raccolta di client con una versione inferiore a 4. x e non è possibile aggiornare l'applicazione.

> [AZURE.NOTE] Gli account di archiviazione BLOB sono attualmente supportati gran parte delle aree geografiche Azure con altre da seguire. È possibile trovare l'elenco aggiornato delle aree disponibili nella pagina [Servizi di Windows Azure per area geografica](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Confronto tra i livelli di spazio di archiviazione

Nella tabella seguente evidenzia il confronto tra i due livelli di spazio di archiviazione:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Livello di spazio di archiviazione attivo</center></strong></td>
    <td><strong><center>Livello di spazio di archiviazione avanzate</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilità</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilità<br>(RA GRS legge)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Costi di utilizzo</center></strong></td>
    <td><center>Costi più elevati lo spazio di archiviazione<br>Ridurre i costi di accesso e transazione</center></td>
    <td><center>Ridurre i costi di spazio di archiviazione<br>Costi più elevati di accesso e transazione</center></td>
</tr>
<tr>
    <td><strong><center>Dimensione minima dell'oggetto<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Durata minima di spazio di archiviazione<center></strong></td>
    <td colspan="2"><center>N/D</center></td>
</tr>
<tr>
    <td><strong><center>Latenza<br>(Time to primo byte)<center></strong></td>
    <td colspan="2"><center>millisecondi</center></td>
</tr>
<tr>
    <td><strong><center>Prestazioni e scalabilità destinazioni<center></strong></td>
    <td colspan="2"><center>Diverso da quello di account di archiviazione generico</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Gli account di archiviazione BLOB supportano le stesse prestazioni e destinazioni di scalabilità come account di archiviazione generico. Per ulteriori informazioni, vedere [destinazioni prestazioni e scalabilità di spazio di archiviazione Azure](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Gli account di archiviazione BLOB usare un nuovo modello prezzo per l'archiviazione blob in base a livello di spazio di archiviazione. Quando si utilizza un account di archiviazione Blob, di fatturazione considerazioni seguenti:

- **Costi di archiviazione**: oltre la quantità di dati memorizzati, il costo di archiviazione dei dati varia a seconda del livello di spazio di archiviazione. Il costo per gigabyte è inferiore per il livello di spazio di archiviazione interessanti rispetto per il livello di spazio di archiviazione attivo.
- **I costi di accesso ai dati**: per i dati nel livello di spazio di archiviazione interessanti, si verrà addebitato una spesa di accesso di dati per gigabyte per lettura e scrittura.
- **Costi delle transazioni**: esiste un costo per ogni transazione per entrambi i livelli. Tuttavia, il costo per ogni transazione per il livello di spazio di archiviazione interessanti è superiore a quello per il livello di spazio di archiviazione attivo.
- **I costi di trasferimento di replica geografico dati**: valida solo per gli account con geografico replica configurata, tra cui GRS e RA GRS. Trasferimento di dati geografico replica comporta un costo per gigabyte.
- **Trasferire i costi di dati in uscita**: il trasferimento di dati in uscita (dati trasferiti fuori area Azure) sostenere fatturazione per l'utilizzo della larghezza di banda per gigabyte alla scala cronologica, coerenti con gli account di archiviazione generico.
- **Modificare il livello di spazio di archiviazione**: modificare il livello di spazio di archiviazione da interessante a caldo causeranno un'importo pari a tutti i dati esistenti in account di archiviazione per ogni transizione di lettura. Mano, modificando il livello di spazio di archiviazione da attivo a interessante sono gratuito di costo.

> [AZURE.NOTE] Per consentire agli utenti di provare la nuova livelli di spazio di archiviazione e convalidare funzionalità dopo il lancio, le spese per modificare lo spazio di archiviazione livello da interessante a caldo sarà applicata disattivata finché 2016 il 30 giugno. A partire da 2016 1 ° luglio, le spese verranno applicate a tutte le transizioni da interessante a caldo. Per ulteriori informazioni sul modello di prezzi per lo spazio di archiviazione Blob account, vedere pagina [Prezzi lo spazio di archiviazione Azure](https://azure.microsoft.com/pricing/details/storage/) . Per ulteriori informazioni sui dati in uscita trasferimento in base alle tariffe, vedere la pagina [Dettagli prezzi trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Guida introduttiva

In questa sezione è mostrerà gli scenari seguenti tramite il portale di Azure:

- Come creare un account di archiviazione Blob.
- Modalità di gestione di un account di archiviazione Blob.

### <a name="using-the-azure-portal"></a>Tramite il portale di Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Creare un account di archiviazione Blob tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Scegliere **Nuovo**dal menu Hub > **dati + spazio di archiviazione** > **account di archiviazione**.

3. Immettere un nome per il proprio account di archiviazione.

    Questo nome deve essere univoco globale; viene utilizzato come parte dell'URL utilizzato per accedere agli oggetti di account di archiviazione.  

4. Selezionare **Gestione risorse** come modello di distribuzione.

    Spazio di archiviazione a più livelli può essere utilizzato solo con gli account di archiviazione di Manager delle risorse; si tratta del modello di distribuzione consigliata per le nuove risorse. Per ulteriori informazioni, vedere la [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).  

5. Nell'elenco a discesa tipo Account selezionare **Archiviazione Blob**.

    Verrà visualizzata nel punto in cui si seleziona il tipo di account di archiviazione. Spazio di archiviazione a più livelli non è disponibile in generale archiviazione; è disponibile solo in account di tipo di archiviazione Blob.    

    Si noti che, quando si seleziona questa opzione, il livello di prestazioni sia impostato su Standard. Spazio di archiviazione a più livelli non è disponibile con il livello di prestazioni Premium.

6. Selezionare l'opzione di replica per l'account di archiviazione: **LRS**, **GRS**o **RA GRS**. Il valore predefinito è **RA GRS**.

    LRS = localmente ridondanti archiviazione; GRS = archiviazione geografico ridondanti (2 aree geografiche); RA GRS è accesso in lettura geografico ridondanti dello spazio di archiviazione (2 aree geografiche con accesso in lettura alla seconda).

    Per ulteriori informazioni sulle opzioni di replica di spazio di archiviazione di Azure, consultare anche [replica di archiviazione Azure](storage-redundancy.md).

7. Selezionare il livello di destra dello spazio di archiviazione per le proprie esigenze: impostare il **livello di accesso** a **interessanti** o **Hot**. Il valore predefinito è **Hot**.

8. Selezionare l'abbonamento in cui si desidera creare il nuovo account di archiviazione.

9. Specificare un nuovo gruppo di risorse o selezionare un gruppo di risorse esistenti. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

10. Selezionare l'area per l'account di archiviazione.

11. Fare clic su **Crea** per creare l'account di archiviazione.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Modificare il livello di spazio di archiviazione di un account di archiviazione Blob tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per passare al proprio account di archiviazione, selezionare tutte le risorse, quindi selezionare il proprio account di archiviazione.

3. In e l'impostazioni, fare clic su **configurazione** in modo da visualizzare e/o modificare la configurazione dell'account.

4. Selezionare il livello di destra dello spazio di archiviazione per le proprie esigenze: impostare il **livello di accesso** a **interessanti** o **Hot**.

5. Fare clic su Salva nella parte superiore e il.

> [AZURE.NOTE] Modificare il livello di spazio di archiviazione potrebbe costi aggiuntivi. Vedere la sezione [prezzo e la fatturazione](storage-blob-storage-tiers.md#pricing-and-billing) per altri dettagli.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>La valutazione e l'esecuzione della migrazione per gli account di archiviazione Blob

Lo scopo di questa sezione è per consentire agli utenti di inserire una transizione graduale all'utilizzo di account di archiviazione Blob. Esistono due scenari di utente:

- Si dispone di un account di archiviazione generico esistente e da valutare una modifica apportata a un account di archiviazione Blob con il livello di destra dello spazio di archiviazione.
- Si è deciso di usare un account di archiviazione Blob o già CE l'hai e da valutare se è necessario utilizzare il livello di spazio di archiviazione attivo o interessanti.

In entrambi i casi, la prima di importanza consiste nel stimare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione Blob e confrontarla con rispetto ai costi attuali.

### <a name="evaluating-blob-storage-account-tiers"></a>Valutazione di livelli di account di archiviazione Blob

Per calcolare il costo di archiviazione e accesso ai dati archiviati in un account di archiviazione Blob, sarà necessario valutare il modello di utilizzo esistente o approssimativo i criteri di utilizzo previsti. In generale, sarà necessario conoscere:

- Il consumo di spazio di archiviazione - come quantità di dati vengono conservati e come implicazioni su base mensile?
- Il modello di access di spazio di archiviazione - è in corso la quantità di dati leggere e scrivere account (inclusi i nuovi dati)? Quante transazioni vengono utilizzate per accedere ai dati e tipi di transazioni sono?

#### <a name="monitoring-existing-storage-accounts"></a>Monitoraggio gli account di archiviazione esistente

Per controllare gli account di archiviazione esistenti e raccogliere questi dati, è possibile effettuare l'uso delle Azure archiviazione Analitica che esegue la registrazione e fornisce i dati di metrica per un account di archiviazione.
Spazio di archiviazione Analitica consentono di memorizzare metriche che includono transazione aggregato statistiche e capacità dati sulle richieste per il servizio di archiviazione Blob per entrambi gli account di archiviazione generico come account di archiviazione Blob.
Questi dati vengono archiviati in tabelle note nello stesso account di archiviazione.

Per ulteriori informazioni, vedere [Sull'archiviazione Analitica metriche](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Schema tabella metriche di archiviazione Analitica](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Gli account di archiviazione BLOB espongono endpoint del servizio tabella solo per l'archiviazione e accesso ai dati metriche per tale account.

Per controllare il consumo di spazio di archiviazione per il servizio di archiviazione Blob, sarà necessario abilitare le metriche capacità.
Con questo valore abilitato, dati capacità registrati giornaliera per servizio Blob dell'account di archiviazione e registrato come una voce della tabella in cui è scritta la tabella *$MetricsCapacityBlob* nello stesso account di archiviazione.

Per controllare il modello di access dati per il servizio di archiviazione Blob, sarà necessario abilitare le metriche transazione oraria un livello di API.
Con questo valore abilitato, per API transazioni sono aggregate ogni ora e registrate come una voce della tabella in cui è scritta la tabella *$MetricsHourPrimaryTransactionsBlob* nello stesso account di archiviazione. La tabella *$MetricsHourSecondaryTransactionsBlob* vengono registrate le transazioni per l'endpoint secondario in caso di account di archiviazione RA GRS.

> [AZURE.NOTE] Nel caso in cui si dispone di un account di archiviazione generico in cui è già stato archiviato BLOB di pagine e dischi macchina virtuale insieme a blocco e aggiungere dati blob, questo processo di stima non è applicabile. In questo modo non si avrà alcun modo distintiva capacità di e metriche transazione solo in base al tipo di blob per bloccano e accoda blob che viene eseguita la migrazione a un account di archiviazione Blob.

Per ottenere una buona approssimazione del è consumo di dati e allo schema di accesso, è consigliabile scegliere un periodo di conservazione per le metriche illustra l'utilizzo normale ed estrapolare.
È possibile mantenere i dati metriche per 7 giorni e raccogliere i dati di ogni settimana, per l'analisi alla fine del mese.
È anche possibile mantenere i dati metriche per gli ultimi 30 giorni e raccogliere e analizzare i dati alla fine del periodo di 30 giorni.

Per informazioni dettagliate sull'attivazione, la raccolta e la visualizzazione di dati di metrica, vedere, [metriche di archiviazione di Azure attivazione e la visualizzazione di dati di metrica](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] L'archiviazione, accesso e il download dei dati analitica viene addebitata anche come dati utente normale.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizzando il formato di utilizzo per valutare i costi

##### <a name="storage-costs"></a>Costi di archiviazione

L'ultima voce capacità metriche tabella *$MetricsCapacityBlob* con la chiave della riga *'dati'* Mostra la capacità di archiviazione consumata da dati utente.
L'ultima voce capacità metriche tabella *$MetricsCapacityBlob* con la chiave riga *'analitica'* Mostra la capacità di archiviazione consumata da registri analitica.

Questa capacità complessiva consumata da sia i dati utente e registri analitica (se abilitata) possono quindi essere usati per stimare il costo di archiviazione dei dati nell'account di archiviazione.
Lo stesso metodo può anche essere usata per la stima dei costi di spazio di archiviazione per blocco e accoda BLOB di account di archiviazione generico.

##### <a name="transaction-costs"></a>Costi delle transazioni

La somma di *'TotalBillableRequests'*in tutte le voci per un'API della tabella metriche indica il numero totale di transazioni per quel particolare API. *ad esempio*, il numero totale di transazioni *'GetBlob'* in un determinato periodo può essere calcolato per la somma delle richieste fatturabili totali per tutte le voci con la riga *' utente. GetBlob'*.

Per valutare i costi di transazione per gli account di archiviazione Blob, sarà necessario suddividere le transazioni in tre gruppi poiché prezzo in modo leggermente diverso.

- Scrivere le transazioni, ad esempio *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*e *'CopyBlob'*.
- Eliminare le transazioni, ad esempio *"DeleteBlob"* e *'DeleteContainer'*.
- Tutte le altre operazioni.

Per valutare i costi di transazione per gli account di archiviazione generico, è necessario aggregare tutte le transazioni indipendentemente dalla operazione/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dati di access e geografico replica trasferire i costi

Mentre analitica lo spazio di archiviazione non fornisce la quantità di dati leggere e scrivere un account di archiviazione, può essere stimato circa esaminando della tabella metriche.
La somma di *'TotalIngress'* in tutte le voci per un'API della tabella metriche indica la quantità totale di dati di ingresso in byte per quel particolare API.
Analogamente la somma dei *'TotalEgress'* indica la quantità totale di dati esterni, in byte.

Per valutare i costi di accesso di dati per gli account di archiviazione Blob, sarà necessario suddividere le transazioni in due gruppi.

- La quantità di dati recuperati da account di archiviazione può essere stimata esaminando la somma di *'TotalEgress'* per principalmente le operazioni *"GetBlob"* e *"CopyBlob"* .
- La quantità di dati scritti nell'account di archiviazione può essere stimata esaminando la somma di *'TotalIngress'* per principalmente *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* le operazioni di *'AppendBlock'* .

Il costo di trasferimento di dati della replica geografico per gli account di archiviazione Blob può essere calcolato anche mediante il valore stimato per la quantità di dati scritti in caso di un account di archiviazione GRS o RA GRS.

> [AZURE.NOTE] Ad esempio ulteriori informazioni su come calcolare i costi per l'uso del livello di spazio di archiviazione attivo o interessanti, dare un'occhiata le domande frequenti intitolate *'informazioni sui livelli di accesso calda e interessante e come è consigliabile determinare quella da use?'* nello spazio di [archiviazione Azure prezzi pagina](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>La migrazione dei dati esistente

Un account di archiviazione Blob specifico per l'archiviazione solo blocco e accoda BLOB. Gli account generiche dello spazio di archiviazione esistente, che consentono di memorizzare tabelle, code, file e dischi, nonché BLOB, non possono essere convertiti per gli account di archiviazione Blob. Per utilizzare i livelli di spazio di archiviazione, sarà necessario creare nuovi account di archiviazione Blob e la migrazione dei dati esistenti all'account appena creato.
Eseguire la migrazione di dati esistenti all'account di archiviazione Blob da dispositivi di archiviazione in locale, dal provider di archiviazione cloud di terze parti o dagli account esistente generiche dello spazio di archiviazione in Azure, è possibile utilizzare i metodi seguenti:

#### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità della riga di comando di Windows progettata per prestazioni elevate la copia di dati da e verso lo spazio di archiviazione di Azure. È possibile utilizzare AzCopy per copiare i dati nel proprio account di archiviazione Blob dagli account generiche dello spazio di archiviazione esistenti o per caricare dati dai dispositivi di archiviazione locale nel proprio account di archiviazione Blob.

Per ulteriori informazioni, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Raccolta di spostamento dei dati

Raccolta di spostamento dei dati dello spazio di archiviazione Azure per .NET dipende il framework di spostamento dei dati di base che si AzCopy. La raccolta è progettata per prestazioni elevate, dati e affidabile semplici operazioni di trasferimento simile a AzCopy. In questo modo è possibile sfruttare le funzionalità offerte da AzCopy applicazione a livello nativo senza la necessità di occuparsi di esecuzione e il monitoraggio esterne istanze di AzCopy meglio.

Per ulteriori informazioni, vedere [Azure lo spazio di archiviazione dati di spostamento per .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API REST o una raccolta di Client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati in un account di archiviazione Blob utilizzando uno dei raccolte client Azure o servizi di archiviazione Azure API REST. Archiviazione Azure fornisce raccolte rich client per più lingue e piattaforme come .NET, Java, C++, Node, PHP, trascrizione e Python. Le raccolte di client offrono funzionalità avanzate, ad esempio ritentare, registrazione e i caricamenti in parallelo. È anche possibile sviluppare direttamente rispetto all'API REST, che possono essere chiamati da qualsiasi linguaggio che effettua richieste di HTTP/HTTPS.

Per ulteriori informazioni, vedere [Guida introduttiva a archiviazione Blob Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOB crittografato utilizzando la crittografia lato client archiviare metadati relativi a crittografia archiviati con il blob. È fondamentale che qualsiasi meccanismo di copia è necessario assicurarsi che i metadati blob e soprattutto i metadati relativi a crittografia, viene mantenuto. Se si copia BLOB senza metadati, il contenuto di blob non saranno nuovamente recuperabile. Per ulteriori informazioni sui metadati relativi ai crittografia, vedere [crittografia lato client archiviazione Azure](storage-client-side-encryption.md).

## <a name="faqs"></a>Domande frequenti

1. **Sono già gli account di archiviazione disponibili?**

    Sì, gli account esistenti dello spazio di archiviazione sono ancora disponibili e invariati in prezzi o funzionalità.  Hanno non ha la possibilità di scegliere un livello di spazio di archiviazione e non avranno funzionalità il in futuro.

2. **Perché e quando è opportuno iniziare a utilizzare gli account di archiviazione Blob?**

    Gli account di archiviazione BLOB specifico per l'archiviazione BLOB e consentono di introdurre nuove funzionalità basate su blob. In futuro, gli account di archiviazione Blob rappresentano il modo consigliato per l'archiviazione BLOB, come funzionalità future, ad esempio lo spazio di archiviazione gerarchica e collegamento verrà inserito in base a questo tipo di account. Tuttavia, è fino a quando si desidera eseguire la migrazione in base alle esigenze aziendali.

3. **È possibile convertire il mio account di archiviazione esistente a un account di archiviazione Blob?**

    No. Account di archiviazione BLOB è un altro tipo di account di archiviazione e sarà necessario creare di nuovo e la migrazione dei dati, come illustrato sopra.

4. **È possibile archiviare oggetti nei livelli entrambi lo spazio di archiviazione nello stesso account?**

    L'attributo *' Livello di accesso* che indica il livello di spazio di archiviazione è impostato un livello di account e si applica a tutti gli oggetti in quell'account. Non è possibile impostare l'attributo di livello di accesso a un livello di oggetto.

5. **È possibile modificare il livello di spazio di archiviazione dell'account di archiviazione Blob?**

    Sì. Sarà possibile modificare il livello di spazio di archiviazione impostando l'attributo *' livelli di accesso '* su account di archiviazione. Modificare il livello di spazio di archiviazione vengono applicate a tutti gli oggetti archiviati nella finestra account. Modificare il livello di spazio di archiviazione da attivo a interessante non comporta le spese durante la modifica da interessante a caldo causeranno un costo GB per la lettura di tutti i dati dell'account per.

6. **Con quale frequenza è possibile modificare il livello di spazio di archiviazione dell'account di archiviazione Blob?**

    Mentre si non si applica un limite con quale frequenza è possibile modificare il livello di spazio di archiviazione, tenere presente che la modifica del livello di spazio di archiviazione da interessante a caldo causeranno in base alle tariffe significative. Non è consigliabile modificare il livello di spazio di archiviazione spesso.

7. **Verrà BLOB nel livello di spazio di archiviazione interessanti hanno un funzionamento diverso rispetto a quelle del livello di spazio di archiviazione attivo?**

    BLOB del livello di spazio di archiviazione attivo hanno la latenza stesso come BLOB nell'account di archiviazione generico. Nel livello interessanti dello spazio di archiviazione BLOB avere una latenza simile (in millisecondi) come BLOB nell'account di archiviazione generico.

    Nel livello interessanti dello spazio di archiviazione BLOB avrà un leggermente disponibilità servizio (contratto di servizio) più basso BLOB archiviato nel livello di spazio di archiviazione attivo. Per ulteriori informazioni, vedere [contratto di servizio per l'archiviazione](https://azure.microsoft.com/support/legal/sla/storage).

8. **Posso archiviare BLOB di pagine e dischi macchina virtuale nell'account di archiviazione Blob?**

    Gli account di archiviazione BLOB supportano solo blocco e accoda BLOB e non BLOB di pagine. Azure macchina virtuale dischi sono supportati da BLOB di pagine e di conseguenza gli account di archiviazione Blob non possono essere usati per archiviare dischi macchina virtuale. Tuttavia, è possibile archiviare i backup dei dischi macchina virtuale come BLOB di blocco in un account di archiviazione Blob.

9. **Sarà necessario modificare le applicazioni di utilizzare gli account di archiviazione Blob esistenti?**

    Gli account di archiviazione BLOB sono API di 100% coerente con gli account di archiviazione generico per blocco e accodare BLOB. Come l'applicazione è utilizzando bloccare BLOB o accodare BLOB e si utilizza il 2014-02-14 dell' [API REST di servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) o versione successiva quindi deve utilizzare solo l'applicazione. Se si utilizza una versione precedente del protocollo, sarà necessario aggiornare l'applicazione per utilizzare la nuova versione in modo da assicurare la compatibilità con entrambi i tipi di account di archiviazione. In generale, è sempre consigliabile usando la versione più recente indipendentemente da quale account di archiviazione al tipo di utilizzare.

10. **Sarà disponibile una modifica dell'esperienza utente?**

    Gli account di archiviazione BLOB sono molto simili a un account di archiviazione generico per l'archiviazione dei blocchi accodare BLOB e supportano tutte le funzionalità di archiviazione di Azure, inclusi l'affidabilità e disponibilità, scalabilità, prestazioni e sicurezza. Diverso dalle caratteristiche e limitazioni specifiche per gli account di archiviazione Blob e relativi livelli di spazio di archiviazione che evidenziati in precedenza, tutti gli altri elementi rimarrà invariato.

## <a name="next-steps"></a>Passaggi successivi

### <a name="evaluate-blob-storage-accounts"></a>Valutare gli account di archiviazione Blob

[Verificare la disponibilità degli account di archiviazione Blob per area geografica](https://azure.microsoft.com/regions/#services)

[Valutare l'utilizzo corrente dello spazio di archiviazione gli account di posta, consentendo metriche di archiviazione di Azure](storage-enable-and-view-metrics.md)

[Verificare lo spazio di archiviazione di Blob prezzi per area geografica](https://azure.microsoft.com/pricing/details/storage/)

[Trasferimenti di dati di controllo prezzi](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Iniziare a usare gli account di archiviazione Blob

[Guida introduttiva a archiviazione Blob Azure](storage-dotnet-how-to-use-blobs.md)

[Spostare dati da e verso lo spazio di archiviazione di Azure](storage-moving-data.md)

[Trasferire i dati con l'utilità AzCopy della riga di comando](storage-use-azcopy.md)

[Individuare ed esplorare gli account di archiviazione](http://storageexplorer.com/)
