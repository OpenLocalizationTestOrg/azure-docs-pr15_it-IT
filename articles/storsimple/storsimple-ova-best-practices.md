<properties 
   pageTitle="Procedure consigliate per matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive le procedure consigliate per la distribuzione e gestione della matrice virtuale StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Matrice virtuale StorSimple procedure consigliate

## <a name="overview"></a>Panoramica

Matrice virtuale di Microsoft Azure StorSimple è una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra un dispositivo virtuale locale in esecuzione in una riunione hypervisor e lo spazio di archiviazione cloud Microsoft Azure. Matrice virtuale StorSimple costituisce un'alternativa efficiente e conveniente per la matrice fisica 8000 serie. Matrice virtuale possono essere eseguiti sui infrastruttura hypervisor esistente, supporta iSCSI e i protocolli piccole e medie imprese ed è ideale per gli scenari di office remote office/diramazione. Per ulteriori informazioni sulle soluzioni StorSimple, passare a [Microsoft Azure StorSimple Panoramica](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

In questo articolo vengono descritte le procedure consigliate implementate durante l'installazione e configurazione iniziali, distribuzione e gestione della matrice virtuale StorSimple. Queste procedure consigliate forniscono convalidate linee guida per la configurazione e la gestione della matrice virtuale. In questo articolo è rivolto gli amministratori IT che distribuiscono e gestire le matrici virtuale nei Data Center.

È consigliabile una revisione periodica delle procedure consigliate per garantire che il dispositivo è ancora in conformità quando vengono apportate modifiche al flusso di operazione o il programma di installazione. Devono si riscontrano problemi durante l'implementazione di queste procedure consigliate sulla matrice virtuale, [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per assistenza.

## <a name="configuration-best-practices"></a>Procedure consigliate di configurazione 

Queste procedure consigliate illustrate le linee guida che devono essere seguito durante l'installazione e configurazione iniziali e la distribuzione delle matrici virtuale. Queste procedure consigliate includono quelle relative all'implementazione della macchina virtuale, criteri di gruppo, di ridimensionamento, configurare la rete, configurare gli account di archiviazione e la creazione di condivisioni e volumi per la matrice virtuale. 

### <a name="provisioning"></a>Il provisioning 

Matrice virtuale StorSimple è una macchine () viene completato il provisioning su hypervisor (Hyper-V o VMware) host del server di posta. Durante il provisioning la macchina virtuale, assicurarsi che l'host è in grado di allocare risorse sufficienti. Per ulteriori informazioni, vedere [requisiti minimi](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) per effettuare il provisioning di una matrice. 

Durante il provisioning di matrice virtuale per implementare le procedure consigliate seguenti:


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Tipo di macchina virtuale**   | **Generazione 2** Macchine Virtuali per l'utilizzo con Windows Server 2012 o versione successiva e un'immagine *.vhdx* . <br></br> **Generazione 1** Macchine Virtuali per l'utilizzo con Windows Server 2008 o versione successiva e un'immagine *VHD* .                                                                                                              | Utilizzare macchina virtuale versione 8-11 quando si utilizza *estensione VMDK* immagine.                                                                      |
| **Tipo di memoria**            | Configurare come **statica della memoria**. <br></br> Non utilizzare l'opzione di **memoria dinamica** .            |                                                    |
| **Tipo di dati su disco**         | Eseguire il provisioning come **espansione dinamica**.<br></br> **Dimensioni fisse** richiede molto tempo. <br></br> Non utilizzare l'opzione **di visualizzazione delle differenze** .                                                                                                                   | Utilizzare l'opzione di **disposizione sottile** .                                                                                      |
| **Modifica dei dati su disco** | Espansione o la compattazione non è consentita. Tentativo di eseguire questa operazione comporta la perdita di tutti i dati locali nel dispositivo.                       | Espansione o la compattazione non è consentita. Tentativo di eseguire questa operazione comporta la perdita di tutti i dati locali nel dispositivo. |

### <a name="sizing"></a>Ridimensionamento

Ridimensionamento della matrice virtuale StorSimple, considerare i fattori seguenti:

- Locale la caratteristica di prenotazione per volumi o condivisioni. Circa 12% dello spazio riservato a livello locale per ogni volume a più livelli di provisioning o la condivisione. Circa 10% dello spazio riservato anche per un volume locale bloccato per file system.
- Snapshot sovraccarico. Circa 15% a livello locale è riservato per istantanee.
- Necessità di ripristino. Se eseguendo ripristino come una nuova operazione, è necessario definire ridimensionamento lo spazio necessario per il ripristino. Ripristino è fine a una condivisione o volume della stessa dimensione o superiore.
- Alcuni buffer devono essere attribuiti per qualsiasi crescita imprevista.

In base a fattori precedenti, i requisiti di ridimensionamento possono essere rappresentati dall'equazione seguente:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Negli esempi seguenti viene illustrato come è possibile ridimensionare una matrice virtuale in base alle esigenze.

#### <a name="example-1"></a>Esempio 1:
Nella matrice virtuale si desidera essere in grado di 

- effettuare il provisioning di 2 TB volume a più livelli o la condivisione.
- effettuare il provisioning di 1 TB a più livelli volume o la condivisione.
- effettuare il provisioning di 300 GB del volume localmente bloccata o condividere.


Per volumi o condivisioni precedente, possiamo calcolare i requisiti di spazio a livello locale. 

Prima di tutto, per ogni volume a più livelli/condivisione, la caratteristica di prenotazione locale è uguale a 12% della dimensione del volume/Condividi. Per il volume localmente bloccata/condivisione, prenotazione locale è 10% della dimensione del volume/Condividi localmente bloccata (oltre alle dimensioni provisioning). In questo esempio, è necessario

- Prenotazione locale 240 GB (per 2 TB a volume/Condividi livelli)
- Prenotazione locale 120 GB (per 1 TB a volume/Condividi livelli)
- 330 GB per volume localmente bloccata o la condivisione (aggiungendo 10% di prenotazione locale a ottenere le dimensioni di 300 GB viene completato il provisioning)

Lo spazio totale richiesto a livello locale finora: 240 GB + 120 GB + 330 GB = 690 GB.

In secondo luogo, è necessario almeno quantità di spazio a livello locale come la prenotazione singola più grande. Questo importo supplementare viene usato nel caso in cui è necessario ripristinare da uno snapshot cloud. In questo esempio, la più grande prenotazione locale è 330 GB (inclusa la caratteristica di prenotazione per file system), in modo che si aggiungerebbe che per il 690 GB: 690 + 330 GB in = 1020 GB.
Se è stata eseguita successivi ripristini aggiuntivi, è sempre possibile liberare spazio dall'operazione di ripristino precedente.

Infine, è necessario 15% del totale spazio locale finora per archiviare istantanee locale, in modo che solo 85% che ne è disponibile. In questo esempio, che sarà intorno 1020 GB = 0.85&ast;dati provisioning disco TB. Pertanto, il disco di provisioning dati sarebbe (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (arrotondamento al più vicina quartile)

Separazione crescita imprevista e ripristino nuovo, si deve effettuare il provisioning di disco locale del intorno 1.25-1,5 TB.

> [AZURE.NOTE] È anche consigliabile che eseguono effettuato il provisioning del disco locale. Questo suggerimento è perché lo spazio di ripristino è necessario solo quando si desidera ripristinare i dati antecedenti a cinque giorni. Ripristino a livello di elemento consente di ripristinare i dati degli ultimi cinque giorni senza lo spazio aggiuntivo per il ripristino.

#### <a name="example-2"></a>Esempio 2: 
Nella matrice virtuale si desidera essere in grado di 

- effettuare il provisioning di 2 TB a più livelli volume
- effettuare il provisioning di un volume 300 GB aggiunte localmente

In base a 12% di prenotazione dello spazio locale per volumi/condivisioni a più livelli e 10% per volumi/condivisioni bloccate in locale, è necessario

- Prenotazione locale 240 GB (per 2 TB a volume/Condividi livelli)
- 330 GB per volume localmente bloccata o la condivisione (aggiungendo 10% di prenotazione locale per lo spazio di 300 GB viene completato il provisioning)

Spazio totale richiesto a livello locale è: 240 GB + 330 = 570 GB in

Lo spazio locale minimo necessario per il ripristino è 330 GB. 

15% del totale disco viene utilizzato per archiviare istantanee in modo che solo 0.85 è disponibile. Pertanto, le dimensioni del disco rigido sono (900&ast;(1/0.85)) = TB 1,06 ~ 1,25 TB (arrotondamento al più vicina quartile) 

Suddivisione in qualsiasi crescita imprevista, è possibile effettuare il provisioning di un disco locale 1.25-1,5 TB.


### <a name="group-policy"></a>Criteri di gruppo

Criteri di gruppo sono un'infrastruttura che consente di implementare configurazioni specifiche per utenti e computer. Impostazioni di criteri di gruppo sono contenute nel gruppo oggetti criteri (), che sono collegati ai seguenti contenitori di servizi di dominio Active Directory (AD DS): siti, domini o unità organizzative (OU). 

Se la matrice virtuale dominio, oggetti Criteri di gruppo possono essere applicate a tale. Questi oggetti Criteri di gruppo è possibile installare applicazioni, ad esempio un software antivirus che possono influire negativamente sull'operazione della matrice virtuale StorSimple.

Pertanto, è consigliabile che è:

-   Assicurarsi che la matrice virtuale nella propria organizzazione unità Organizzativa di Active Directory. 

-   Assicurarsi che nessun gruppo oggetti criteri () vengono applicati per la matrice virtuale. È possibile bloccare l'ereditarietà per assicurarsi che la matrice virtuale (nodo figlio) non eredita automaticamente gli oggetti Group Policy dall'elemento padre. Per ulteriori informazioni, vedere per [bloccare l'ereditarietà](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Rete

Configurazione della rete per la matrice virtuale viene eseguita tramite l'interfaccia web locale. Un'interfaccia virtuali è abilitata tramite hypervisor in cui viene eseguito il provisioning in forma di matrice virtuale. Utilizzare la pagina [Impostazioni di rete](storsimple-ova-deploy3-fs-setup.md) per configurare l'indirizzo IP interfaccia virtuali, alla subnet e gateway.  È anche possibile configurare i server DNS primario e secondario, le impostazioni di tempo e le impostazioni del proxy facoltativo del dispositivo. La maggior parte della configurazione della rete è una copia unica configurazione. Verificare i [requisiti di rete StorSimple](storsimple-ova-system-requirements.md#networking-requirements) prima di distribuire la matrice virtuale.

Quando si distribuisce il virtuale in forma di matrice, è consigliabile eseguire queste procedure consigliate:

-   Assicurarsi che la rete in cui la matrice virtuale viene distribuita sempre con la capacità di dedicare 5 della larghezza di banda Mbps Internet (o più). 

    -   Necessità di larghezza di banda Internet variabile a seconda le caratteristiche di carico di lavoro e la percentuale di modifica dei dati.

    -   La modifica di dati che può essere gestita è direttamente proporzionale la larghezza di banda Internet. Ad esempio quando si esegue una copia di backup, una larghezza di banda Mbps 5 può contenere un cambio di dati di circa 18 GB 8 ore. Con quattro volte larghezza di banda (20 Mbps), è possibile gestire più quattro volte modifica dei dati (72 GB). 

-   Assicurarsi che sia sempre disponibile la connettività a Internet. Origini non affidabili o sporadiche connessioni Internet per i dispositivi potrebbero causare la perdita di accesso ai dati nel cloud e possono consentire una configurazione non supportata.

-   Se si prevede di distribuire il dispositivo come server iSCSI: 
    -   È consigliabile disattivare l'opzione **automaticamente un indirizzo IP ottenere** (DHCP). 
    -   Configurare gli indirizzi IP statici. È necessario configurare un primario e un server DNS secondario.

    -   Se la definizione di più interfacce di rete sul virtuale matrice, la prima interfaccia rete (per impostazione predefinita, questa interfaccia è **Ethernet**) contattarle nel cloud. Per controllare il tipo di traffico, è possibile creare più interfacce virtuali sulla matrice virtuale (configurata come server iSCSI) e connettere tali interfacce a subnet diverse.

-   Per limitare solo la larghezza di banda cloud (utilizzato da matrice virtuale), configurare la limitazione o al router o il firewall. Se si definisce limitazione l'hypervisor, esso verrà limitare tutti i protocolli inclusi iSCSI e piccole e medie imprese anziché solo la larghezza di banda cloud. 

-   Assicurarsi che la sincronizzazione dell'ora per hypervisor sono abilitato. Se tramite Hyper-V, selezionare la matrice virtuale in Hyper-V Manager, passare a **impostazioni &gt; servizi di integrazione**e verificare che sia selezionata **la sincronizzazione dell'ora** .

### <a name="storage-accounts"></a>Account di archiviazione

Matrice virtuale StorSimple può essere associato a un account di archiviazione singola. Questo account di archiviazione può essere un account di archiviazione generato automaticamente un account nella stessa sottoscrizione del servizio, o un account di archiviazione relativi a un'altra sottoscrizione. Per ulteriori informazioni, vedere come [gestire gli account di archiviazione per la matrice virtuale](storsimple-ova-manage-storage-accounts.md).

Utilizzare i seguenti suggerimenti per gli account di spazio di archiviazione associati la matrice virtuale.

-   Quando si collegano più matrici virtuale con un account di archiviazione singola, suddividere la capacità massima (64 TB) per una matrice virtuale e le dimensioni massime (500 TB) per un account di archiviazione. Consente di limitare il numero di matrici virtuale ingrandita che possono essere associati a tale account di archiviazione di circa 7.

-   Quando si crea un nuovo account di archiviazione
    -   È consigliabile che venga creato nell'area più vicina alla remote office/filiale nel punto in cui il StorSimple virtuale viene distribuito per ridurre al minimo latenza.

    -   Tenere presente che non è possibile spostare un account di archiviazione nelle diverse aree. Non è possibile anche spostare un servizio abbonamenti.

    -   Usare un account di archiviazione che implementa ridondanza tra i Data Center. Spazio di archiviazione geografico ridondanti (GRS), zona ridondanti dello spazio di archiviazione (ZRS) e lo spazio di archiviazione ridondanti in locale (LRS) sono supportati per l'utilizzo con la matrice virtuale. Per ulteriori informazioni sui diversi tipi di account di archiviazione, passare alla [replica di archiviazione Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Condivisioni e volumi

Per la matrice virtuale StorSimple, è possibile effettuare il provisioning di condivisioni quando viene configurato come un file server e volumi quando configurato come server iSCSI. Le procedure consigliate per la creazione di condivisioni e volumi riguardano le dimensioni e il tipo di configurato.

#### <a name="volumeshare-size"></a>Dimensioni del volume/Condividi

Nella matrice virtuale, è possibile effettuare il provisioning di condivisioni quando viene configurato come un file server e volumi quando configurato come server iSCSI. Le procedure consigliate per la creazione di condivisioni e volumi riguardano le dimensioni e il tipo di configurato. 

Tenere presente le procedure consigliate seguenti durante il provisioning di condivisione o volume sul dispositivo virtuale.

-   Le dimensioni dei file rispetto alla dimensione provisioning di una condivisione a più livelli può influire sulle prestazioni collegamento a livelli. Utilizzo dei file di grandi dimensioni potrebbe causare un livello lento indietro. Quando si lavora con file di grandi dimensioni, è consigliabile che il file più grande è minore di 3% delle dimensioni della condivisione.

-   Un massimo di volumi 16/condivisioni può essere creato nella matrice virtuale. Se in locale è bloccato, volumi/condivisioni può essere compreso tra 50 GB a 2 TB. Se a più livelli, volumi/condivisioni deve essere compreso tra 500 GB a 20 TB. 

-   Quando si crea un volume, suddividere il consumo di dati previsti, nonché la crescita futura. Mentre non è possibile espandere il volume in un secondo momento, è sempre possibile ripristinare un volume più grande.

-   Dopo aver creato il volume, non sarà possibile ridurre le dimensioni del volume su StorSimple.
   
-   Quando si scrive in un volume a più livelli su StorSimple, quando i dati del volume raggiunge una determinata soglia (relativamente spazio locale riservata il volume), l'IO è limitato. Continuare a scrivere il volume rallentamento l'IO in modo significativo. Anche se è possibile scrivere un volume a più livelli oltre la propria capacità di provisioning (abbiamo non attivamente impedire all'utente la scrittura oltre la capacità di provisioning), è visualizzato un avviso per l'effetto che dispone di stata eseguita l'oversubscription. Una volta che viene visualizzato l'avviso, è essenziale misure correttive, ad esempio elimina i dati del volume o ripristinare il volume su un volume più grande (espansione volume non è attualmente supportato).

-   Per casi di utilizzo ripristino di emergenza, come il numero di azioni/volumi consentiti è 16 e il numero massimo di azioni/volumi che possono essere elaborati in parallelo anche 16, il numero di azioni/volumi non ha incidono rilasciato e RTOs. 

#### <a name="volumeshare-type"></a>Tipo di volume/condivisione

StorSimple supporta due tipi di volume/Condividi basati sull'utilizzo: localmente bloccate e a più livelli. Locale bloccate volumi/condivisioni thickly viene effettuato il provisioning che eseguono viene completato il provisioning condivisioni di volumi a più livelli. 

È consigliabile implementare le procedure consigliate seguenti quando si configura StorSimple volumi/azioni:

-   Identificare il tipo di volume in base a carichi di lavoro che si desidera distribuire prima di creare un volume. Utilizzare volumi localmente bloccati per carichi di lavoro che richiedono garanzie locale dei dati (anche durante un periodo di inattività cloud) e che richiedono latenza bassa cloud. Dopo aver creato un volume sulla matrice virtuale, non è possibile modificare il tipo di volume da localmente aggiunte a più livelli o *viceversa*. Ad esempio, creare volumi localmente bloccati durante la distribuzione di SQL carichi di lavoro o carichi di lavoro che ospita macchine (); utilizzare volumi a più livelli per carichi di lavoro Condivisione file.

-   Selezionare l'opzione per meno archiviazione dati utilizzati di frequente quando si gestiscono le dimensioni di file di grandi dimensioni. Quando questa opzione è attivata per accelerare il trasferimento di dati nel cloud, viene utilizzata una dimensione maggiore per il blocco deduplication di 512 KB.

#### <a name="volume-format"></a>Formato volume

Dopo aver creato volumi StorSimple nel server iSCSI, è necessario inizializzare, installare e formattare i volumi. Questa operazione nell'host connessi al dispositivo StorSimple. Procedure consigliate seguenti sono consigliabile quando il collegamento e formattazione di volumi nell'host dei StorSimple.

-   Eseguire una formattazione rapida su tutti i volumi StorSimple.

-   Quando si formatta un volume StorSimple, utilizzare una dimensione di unità di assegnazione (Australia) di 64 KB (valore predefinito è 4 KB). 64 KB Australia si basa su test eseguiti internamente per carichi di lavoro StorSimple comuni e altri carichi di lavoro.

-   Quando si utilizza la matrice virtuale StorSimple configurato come server iSCSI, non utilizzare spanning o dischi dinamici come questi volumi o dischi non sono supportati da StorSimple.

#### <a name="share-access"></a>Condividere l'accesso

Quando si creano condivisioni in un file server virtuale in forma di matrice, seguire queste linee guida:

-   Quando si crea una condivisione, assegnare un gruppo di utenti come un amministratore della condivisione invece di un singolo utente.

-   È possibile gestire le autorizzazioni dopo aver creata la condivisione modificando le quote tramite Esplora risorse.

#### <a name="volume-access"></a>Accesso al volume

Quando si configurano i volumi iSCSI della matrice virtuale StorSimple, è importante controllare l'accesso ovunque sia necessario. Per determinare quali server host consente di accedere volumi, creare e associare volumi StorSimple record di controllo di accesso (ACRs).

Usare le procedure consigliate seguenti quando si configura ACRs per indicare i volumi StorSimple:

-   Sempre associare almeno ACR un volume.

-   Definire ACRs più solo in un ambiente cluster.

-   Quando si assegna più ACR a un volume, assicurarsi che il volume non viene esposto in modo in cui possono accedere contemporaneamente da più host non cluster. Se è stato assegnato più ACRs a un volume, un messaggio di avviso appare per consentirne la verifica della configurazione.

### <a name="data-security-and-encryption"></a>Crittografia e protezione dei dati

La matrice virtuale StorSimple include funzionalità di protezione e crittografia di dati che garantire la riservatezza e l'integrità dei dati. Quando si utilizza queste funzionalità, è consigliabile eseguire queste procedure consigliate: 

-   Definire una chiave di crittografia di spazio di archiviazione cloud per generare la crittografia AES 256 prima che i dati vengono inviati dalla matrice virtuale nel cloud. Questo tasto non è necessario se i dati vengono crittografati per iniziare. La chiave può generata e protetto tramite un sistema di gestione delle chiavi, ad esempio [archivio chiave Azure](../key-vault/key-vault-whatis.md).

-   Quando si configura l'account di archiviazione tramite il servizio di gestione StorSimple, verificare che si attiva la modalità SSL creare un canale sicuro per la comunicazione di rete tra il dispositivo StorSimple e nel cloud.

-   Rigenerare le chiavi per gli account di archiviazione (accedere al servizio di archiviazione Azure) periodicamente all'account accedere a eventuali modifiche in base all'elenco di amministratori modificato.

-   Dati della matrice virtuale sono compresso e deduplicated prima di inviarlo per Azure. Non è consigliabile utilizzare il servizio di ruolo Deduplication dati sull'host Windows Server.


## <a name="operational-best-practices"></a>Procedure consigliate per

Le procedure consigliate per alcune linee guida che devono essere seguite durante l'operazione di matrice virtuale o gestione giornaliera. Queste procedure illustrate le attività di gestione specifiche, ad esempio eseguendo il backup, ripristino da un set di backup, eseguire caso di errore, la disattivazione ed eliminazione di matrice, il monitoraggio dell'integrità e l'uso di sistema e l'esecuzione di virus analisi sulla matrice virtuale.

### <a name="backups"></a>Esecuzione di backup

I dati della matrice virtuale viene eseguito il backup nel cloud in due modi, predefinito automatizzato backup giornaliero del dispositivo intero partire 22:30 o tramite un backup manuale su richiesta. Per impostazione predefinita, il dispositivo viene creato automaticamente giornaliera snapshot cloud di tutti i dati presenti su di esso. Per ulteriori informazioni, visitare [la matrice virtuale StorSimple il backup](storsimple-ova-backup.md).

Non è possibile modificare la frequenza e conservazione associato a backup predefiniti, ma è possibile configurare l'ora di inizio in cui il backup giornalieri sono ogni giorno. Quando si configura l'ora di inizio per i backup automatici, è consigliabile che:

-   Pianificare le copie di backup per le ore. Ora di inizio backup non dovrebbe coincidere con numerosi host IO.

-   Avviare un backup manuale su richiesta quando si prevede di eseguire un dispositivo failover o precedente nella finestra di manutenzione, per proteggere i dati della matrice virtuale.

### <a name="restore"></a>Ripristinare

È possibile ripristinare da un backup impostato in due modi: ripristinare in un altro volume o la condivisione o il ripristino di un livello di elemento (disponibile solo in una matrice virtuale configurata come un file server). Ripristino a livello di elemento consente di eseguire un ripristino granulare dei file e cartelle da un backup cloud di tutte le quote sul dispositivo StorSimple. Per ulteriori informazioni, vedere ripristinare [da un backup](storsimple-ova-restore.md).

Quando si esegue un ripristino, tenere presente quanto segue:

-   La matrice virtuale StorSimple non supporta ripristino sul posto. Può tuttavia essere facilmente ottenuto in due fasi: liberare spazio virtuale matrice e quindi si ripristina un altro volume/Condividi.

-   Quando il ripristino da un volume locale, prendere in considerazione il ripristino saranno un'operazione di esecuzione prolungata. Se il volume rapidamente può essere accompagnato online, i dati continuano a essere Alluminosilicato in background.

-   Il tipo di volume rimarrà invariato durante il processo di ripristino. Ripristino di un volume a più livelli in un altro volume a più livelli e un volume locale aggiunto a un altro localmente aggiunte volume.

-   Quando si tenta di ripristinare un volume o da una condivisione di un set di backup, se il processo di ripristino non riesce, volume di destinazione o condivisione ancora potrebbe essere creati nel portale. È importante eliminare il volume di destinazione inutilizzato o condividere nel portale per ridurre al minimo eventuali problemi futuri derivanti da questo elemento.

### <a name="failover-and-disaster-recovery"></a>Failover e ripristino di emergenza

Caso di errore dispositivo consente di eseguire la migrazione dei dati da un dispositivo di *origine* nel centro dati a un altro dispositivo di *destinazione* che si trova nello stesso documento o una posizione geografica diversa. Failover dispositivo riguarda l'intera unità. Durante il failover, per l'origine dati cloud diventa la proprietà che il dispositivo di destinazione.

Per la matrice virtuale StorSimple, può solo eseguire il a un'altra matrice virtuale gestita dal servizio di gestione StorSimple stesso. Caso di errore in un dispositivo 8000 serie o una matrice gestita da un servizio di gestione StorSimple diverso (quello per il dispositivo di origine) non è consentita. Per ulteriori informazioni sull'utilizzo di failover, passare ai [Prerequisiti per failover dispositivo](storsimple-ova-failover-dr.md).

Quando si esegue una hanno esito negativo su per la matrice virtuale, tenere presente quanto segue:

-   Per pianificati in caso di errore, si tratta di una procedura consigliata da seguire tutte le volumi/condivisioni non in linea prima di iniziare il failover. Seguire le istruzioni specifiche del sistema operativo per accettare volumi/condivisioni non in linea nell'host prima e quindi accettare quelle non in linea in un dispositivo virtuale.

-   Per un file server ripristino di emergenza (DR), è consigliabile aggiungere il dispositivo di destinazione al dominio stesso come origine, in modo che le autorizzazioni di condivisione vengono risolte automaticamente. In questa versione è supportato solo l'il controllo a un dispositivo di destinazione nello stesso dominio.

-   Al termine di DR, viene eliminato automaticamente il dispositivo di origine. Se il dispositivo non è più disponibile, la macchina virtuale che è stato effettuato il provisioning nel sistema host è ancora tempo le risorse. È consigliabile eliminare la macchina virtuale dal sistema di host per impedire che i costi attribuiti.

-   Si noti che anche se il failover ha esito negativo, **i dati siano sempre sicuri nel cloud**. Considerare i seguenti tre scenari in cui il failover non è stato completato:

    -   Errore nella fase iniziale del failover, ad esempio quando i pre-controlli DR da eseguire. In questo caso, il dispositivo di destinazione è ancora utilizzabile. È possibile ritentare failover nello stesso dispositivo di destinazione.

    -   Si è verificato un errore durante il processo di failover effettivo. In questo caso, il dispositivo di destinazione è inutilizzabile. È necessario eseguire il provisioning e configurare un'altra matrice virtuale di destinazione e utilizzarla per failover.

    -   Il failover completato dopo che il dispositivo di origine è stato eliminato, ma il dispositivo di destinazione ha problemi e non è possibile accedere ai dati. I dati sono ancora sicuri nel cloud e possono essere recuperati facilmente con la creazione di un'altra matrice virtuale e quindi utilizzarla come dispositivo di destinazione per il DR.

### <a name="deactivate"></a>Disattivare

Quando si disattiva una matrice virtuale StorSimple, si server la connessione tra il dispositivo e il servizio di gestione StorSimple corrispondente. La disattivazione è un'operazione **permanente** e non può essere annullata. Un dispositivo disattivato non è registrato con il servizio di gestione StorSimple nuovamente. Per ulteriori informazioni, visitare [disattivare ed eliminare la matrice virtuale StorSimple](storsimple-deactivate-and-delete-device.md).

Quando la disattivazione della matrice virtuale, tenere presente le procedure consigliate seguenti:

-   Creare uno snapshot cloud di tutti i dati prima di disattivare un dispositivo virtuale. Quando si disattiva una matrice virtuale, tutti i dati locali dispositivo vengono persi. Un'istantanea cloud consentono di recuperare i dati in una fase successiva.

-   Prima di disattivare una matrice virtuale StorSimple, assicurarsi di interrompere o eliminare i client e a ospitare che dipendono da tale dispositivo.

-   Eliminare un dispositivo disattivato se si usa non è più in modo che esso non Attribuzione costi.

### <a name="monitoring"></a>Monitoraggio

Per assicurarsi che la matrice virtuale StorSimple si trovi in uno stato integro continuo, è necessario eseguire il monitoraggio della matrice e assicurarsi di ricevere informazioni dal sistema inclusi gli avvisi. Per controllare lo stato generale della matrice virtuale, implementare le procedure consigliate seguenti:

- Configurare il monitoraggio per tenere traccia dell'utilizzo del disco del disco di dati in forma di matrice virtuale, nonché il disco del sistema operativo. Se è in esecuzione Hyper-V, è possibile utilizzare una combinazione di System Center Virtual Machine Manager (SCVMM) e System Center Operations Manager (SCOM) per monitorare l'host di virtualizzazione.   

- Configurare notifiche tramite posta elettronica la matrice virtuale per inviare gli avvisi di determinati livelli di utilizzo.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Virus e ricerca nell'indice delle applicazioni di analisi

Una matrice virtuale StorSimple automaticamente a livelli di dati dal livello locale nel cloud di Microsoft Azure. Quando un'applicazione, ad esempio una ricerca nell'indice o un programma antivirus viene utilizzata per analizzare i dati archiviati in StorSimple, è necessario prestare attenzione dati cloud non ottenere accesso e recapitati torna a livello locale.

È consigliabile implementare le procedure consigliate seguenti quando si configura la scansione dell'indice di ricerca o virus nella matrice virtuale:

-   Disattivare tutte le operazioni di analisi completa configurazione automatica.

-   Per indicare i volumi a più livelli, configurare l'applicazione di analisi ricerca o virus indice per eseguire un'analisi incrementale. Questa operazione da eseguire la scansione solo i nuovi dati probabile che si trovano a livello locale. I dati a più livelli nel cloud non è accessibile durante un'operazione incrementale.

-   Assicurarsi che i filtri di ricerca corretto e le impostazioni sono configurate in modo che vengano analizzati solo i tipi di file desiderati. Ad esempio immagini file (JPEG, GIF e TIFF) e i disegni di progettazione non deve essere analizzato durante la ricostruzione dell'indice incrementale o completo.

In Windows processo di indicizzazione, seguire queste linee guida:

-   Non utilizzare l'indicizzatore di Windows per indicare i volumi a più livelli come richiama grandi quantità di dati (TB) dal cloud se è necessario ricostruire spesso l'indice. La ricostruzione dell'indice verranno recuperati tutti i tipi di file per indicizzare il relativo contenuto.

-   Utilizzare le finestre del processo per indicare i volumi localmente bloccati di indicizzazione come se si trattasse di solo i dati in livelli locali per creare l'indice (dati cloud non sarà accessibile).

### <a name="byte-range-locking"></a>Blocco di intervallo byte

Applicazioni è possono bloccare un intervallo specificato di byte all'interno dei file. Se il blocco di byte intervallo è attivato per le applicazioni di scrivono per il StorSimple, quindi il collegamento a livelli non funziona nella matrice virtuale. Per il collegamento a livelli per usare, tutte le aree di file accessibili devono essere sbloccate. Il blocco di byte intervallo non è supportato con i volumi a più livelli della matrice virtuale.

Le misure consigliate per ridurre il blocco di intervallo byte includono:

-   Disattivare l'intervallo di byte blocco la logica dell'applicazione.

-   Uso localmente aggiunte volumi (invece che a livelli) per i dati associati all'applicazione. Volumi localmente bloccati non livello nel cloud.

-   Quando tramite localmente bloccato volumi con byte intervallo blocco attivato il volume può provenire online prima del ripristino. In questi casi, è necessario attendere che il ripristino per il completamento.

## <a name="multiple-arrays"></a>Più matrici

Più matrici virtuale potrebbero essere necessario distribuito all'account per un crescente working set di dati che potrebbero soppressione nel cloud in questo modo sulle prestazioni del dispositivo. In questi casi è preferibile scala dispositivi come crescita working set. È necessario uno o più dispositivi da aggiungere nell'interfaccia di dati in locale. Quando si aggiungono i dispositivi, è possibile:

-   Dividere il set di dati corrente.
-   Distribuire nuovi carichi di lavoro in nuovi dispositivi.
-   Se si distribuisce più matrici virtuali, si consiglia di bilanciamento del carico prospettiva, distribuire la matrice tra host hypervisor diverso.

-  Più matrici virtuale (se configurato come un file server o su un server iSCSI) possono essere distribuite in un Namespace di Distributed File System. Per istruzioni dettagliate, vedere [Distributed File System Namespace soluzione Guida alla distribuzione di spazio di archiviazione Cloud ibrida](https://www.microsoft.com/download/details.aspx?id=45507). Attualmente distribuito replica di sistema di File non è consigliabile per l'utilizzo con matrice virtuale. 


## <a name="see-also"></a>Vedere anche
Informazioni su come [amministrare la matrice virtuale StorSimple](storsimple-ova-manager-service-administration.md) tramite il servizio di gestione StorSimple.
