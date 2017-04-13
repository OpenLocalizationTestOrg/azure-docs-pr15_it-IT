<properties
  pageTitle="Azure e lo spazio di archiviazione di macchine Virtuali Linux | Microsoft Azure"
  description="Descrive Azure Standard e lo spazio di archiviazione Premium con macchine virtuali Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Archiviazione di Azure e Linux VM

Spazio di archiviazione Azure è la soluzione di archiviazione cloud per moderne applicazioni basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti.  Oltre a consentendo agli sviluppatori di creare applicazioni su larga scala per supportare nuovi scenari, lo spazio di archiviazione di Azure fornisce anche la base dello spazio di archiviazione per macchine virtuali di Azure.

## <a name="azure-storage-standard-and-premium"></a>Spazio di archiviazione Azure: Standard e Premium

Azure macchine Virtuali possono essere creato al momento dell'archiviazione standard dischi o dischi di archiviazione premium.  Quando si utilizza il portale per scegliere la macchina virtuale è necessario attivare o disattivare un elenco a discesa nella schermata di base per la visualizzazione standard e premium dischi.  Nella schermata seguente evidenzia il menu Mostra/Nascondi.  Quando attivata o disattivata per SSD, solo premium lo spazio di archiviazione macchine virtuali abilitate, verranno visualizzate tutte supportate dalle SSD unità.  Quando impostata su disco rigido, macchine virtuali unità disco che fa ruotare un backup, verrà visualizzata insieme archiviazione premium macchine virtuali supportate da SSD attiva l'archiviazione standard.

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Quando si crea una macchina virtuale dalla `azure-cli` è possibile scegliere tra standard e premium quando si sceglie la dimensione di memoria virtuale tramite il `-z` o `--vm-size` cli contrassegno.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Creare una macchina virtuale con standard macchine Virtuali di archiviazione sul cli

Il contrassegno cli `-z` sceglie Standard_A1 con A1 da un archivio standard in base a Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Creare una macchina virtuale con archiviazione premium su cli

Il contrassegno cli `-z` sceglie Standard_DS1 con DS1 un'archiviazione premium basato Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Spazio di archiviazione standard

Archiviazione Standard Azure è il tipo di spazio di archiviazione predefinito.  Archiviazione standard è conveniente la comunque efficienti.  

## <a name="premium-storage"></a>Spazio di archiviazione Premium

Lo spazio di archiviazione di Azure Premium offre supporto disco prestazioni ad alta, bassa latenza per macchine virtuali in esecuzione è/O-che richiede significative attività carichi di lavoro. Dischi macchine () che utilizzano lo spazio di archiviazione Premium archiviare dati in (SSD). È possibile eseguire la migrazione di dischi macchine Virtuali dell'applicazione allo spazio di archiviazione di Azure Premium per sfruttare la velocità e prestazioni di questi dischi.

Caratteristiche Premium di spazio di archiviazione:

- Dischi di archiviazione Premium: Lo spazio di archiviazione di Azure Premium supporta dischi macchine Virtuali che è possono allegare alle serie DS, DSv2 o GS macchine virtuali di Azure.

- Blob di pagine Premium: Archiviazione Premium supporta BLOB di pagine Azure, vengono utilizzati per contenere dischi persistenti per Azure macchine ().

- Spazio di archiviazione in locale ridondante Premium: Un account di archiviazione Premium solo supporta localmente ridondanti dello spazio di archiviazione (LRS) come opzione di replica e continua tre copie dei dati in una singola regione.

- [Spazio di archiviazione Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Spazio di archiviazione Premium supportati macchine virtuali

Archiviazione Premium supporta serie DS, serie di DSv2 serie GS e serie di ADFS Azure macchine (). È possibile utilizzare dischi di archiviazione Standard e Premium con archiviazione Premium supportate delle macchine virtuali. Ma non è possibile utilizzare dischi di archiviazione Premium con serie di macchine Virtuali, non sono Premium lo spazio di archiviazione compatibili.

Ecco le distribuzioni Linux è convalidati con lo spazio di archiviazione Premium.

| Distribuzione | Versione                 | Kernel supportati    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7, 8                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| Centos       | 6.5, 6.6, 6,7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6,8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Spazio di archiviazione file

Spazio di archiviazione File Azure offre condivisioni file nel cloud usando il protocollo piccole e medie imprese standard. Con i file di Azure, è possibile eseguire la migrazione di applicazioni aziendali che si basano su file server in Azure. Le applicazioni in esecuzione in Azure possono installare facilmente condivisioni file da Azure macchine virtuali che eseguono Linux. E con la versione più recente di archiviazione dei File, è inoltre possibile collegare una condivisione file da un'applicazione locale che supporta 3.0 piccole e medie imprese.  Poiché nelle condivisioni file sono condivisioni piccole e medie imprese, è possibile accedere tramite API di sistema di file standard.

Spazio di archiviazione file viene generato sulla stessa tecnologia di archiviazione Blob, tabella e coda, l'archiviazione dei File offre la disponibilità, durata, scalabilità e ridondanza geografico integrato nella piattaforma di archiviazione Azure. Per informazioni dettagliate sulle destinazioni prestazioni di spazio di archiviazione File e limiti, vedere Destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure.

- [Utilizzo di spazio di archiviazione File Azure con Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Spazio di archiviazione attivo

Il livello di archiviazione attivo Azure è ottimizzato per l'archiviazione dei dati che si accede frequentemente.  Archiviazione attivo è il tipo di spazio di archiviazione predefinito per blob stores.

## <a name="cool-storage"></a>Spazio di archiviazione avanzata

Il livello di archiviazione interessanti Azure è ottimizzato per l'archiviazione dei dati che sono raramente accessibile e lunga durata. Casi di utilizzo di esempio per l'archiviazione interessanti includono backup contenuti multimediali, dati scientifici, conformità e archiviazione dati. In generale, i dati che viene utilizzata raramente è un candidato ideale per lo spazio di archiviazione avanzata.

|                             | Livello di spazio di archiviazione attivo      | Livello di spazio di archiviazione avanzate     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilità                | 99,9%                 | 99%                   |
| Disponibilità (legge RA GRS) | 99,99%                | 99,9%                 |
| Costi di utilizzo               | Costi più elevati lo spazio di archiviazione  | Ridurre i costi di spazio di archiviazione   |
|                             | Accesso inferiore          | Accesso superiore         |
|                             | e i costi di transazione | e i costi di transazione |


## <a name="redundancy"></a>Ridondanza

I dati nel proprio account di archiviazione di Microsoft Azure viene sempre replicati per garantire la durata e disponibilità, contratto di servizio di archiviazione Azure anche in caso di errori hardware temporaneo della riunione.

Quando si crea un account di archiviazione, è necessario selezionare una delle seguenti opzioni di replica:

- Spazio di archiviazione in locale ridondante (LRS)
- Spazio di archiviazione zona ridondanti (ZRS)
- Spazio di archiviazione ridondanti geografico (GRS)
- Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)

### <a name="locally-redundant-storage"></a>Spazio di archiviazione in locale ridondante

Spazio di archiviazione in locale ridondante (LRS) replica i dati all'interno dell'area in cui è stato creato l'account di archiviazione. Per ottimizzare la durata, tutte le richieste inviate rispetto ai dati nel proprio account di archiviazione viene replicata tre volte. Questi tre ogni repliche, in domini di guasto separata e aggiornamento.  Una richiesta restituisce correttamente solo dopo che sono stati scritti per ogni tre replica.

### <a name="zone-redundant-storage"></a>Spazio di archiviazione zona ridondanti

Spazio di archiviazione zona ridondanti (ZRS) replica i dati tra due o tre strutture, in una singola regione o tra due aree, fornendo durata superiore a LRS. Se l'account di archiviazione ha ZRS abilitata, i dati sono permanenti anche in caso di errore in una delle strutture.

### <a name="geo-redundant-storage"></a>Spazio di archiviazione geografico ridondanti

Spazio di archiviazione ridondanti geografico (GRS) replica i dati in un'area secondaria centinaia di miglia fuori area principale. Se l'account di archiviazione ha GRS abilitata, i dati sono permanenti anche in caso di un'interruzione di internazionali completa o un danno in cui non è reversibile area principale.

### <a name="read-access-geo-redundant-storage"></a>Spazio di archiviazione ridondanti geografico di accesso in lettura

Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS) ingrandisce disponibilità per il proprio account di archiviazione, fornendo accesso in sola lettura per i dati nel percorso secondario, oltre a replica tra due aree forniti da GRS. Nel caso in cui dati non sono disponibili nell'area principale, l'applicazione possibile leggere i dati dall'area di secondario.

Per approfondimenti archiviazione Azure ridondanza vedere:

- [Azure replica di spazio di archiviazione](../storage/storage-redundancy.md)

## <a name="scalability"></a>Scalabilità

Archiviazione Azure è market scalable, pertanto è possibile archiviare e processo centinaia di terabyte di dati per supportare gli scenari di dati necessari affinché analisi scientifica, finanziari e le applicazioni multimediali. Oppure è possibile archiviare piccole quantità di dati necessari per un sito Web small business. Nel punto in cui possono essere suddivise alle proprie esigenze, pagando solo per i dati che è archiviato. Archiviazione Azure attualmente archivia le decine di trilioni di oggetti customer univoci e gestisce milioni di richieste al secondo in Media.

Per gli account di archiviazione standard: un account di archiviazione standard con una velocità massima totale delle richieste di 20.000 IOPS. Totale IOPS in tutti i dischi macchina virtuale di un account di archiviazione standard non deve superare questo limite.

Per gli account di archiviazione premium: un account di archiviazione premium ha una velocità totale massimo di 50 GB/s. La velocità totale per tutti i dischi macchine Virtuali non deve superare questo limite.

## <a name="availability"></a>Disponibilità

È garantire che almeno 99,99% (99,9% per livello di accesso interessanti) del tempo, è correttamente elaborerà richieste per leggere i dati dagli account di accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS), purché tentativi per leggere i dati dall'area principale vengono riprovati sull'area di secondario.

È garantire che almeno 99,9% (99% per livello di accesso interessanti) del tempo, è correttamente elaborerà richieste per leggere i dati da localmente ridondanti dello spazio di archiviazione (LRS), area ridondanti dello spazio di archiviazione (ZRS) e account geografico ridondanti dello spazio di archiviazione (GRS).

È garantire che almeno 99,9% (99% per livello di accesso interessanti) del tempo, è correttamente elaborerà richieste per scrivere dati in locale ridondanti dello spazio di archiviazione (LRS), zona ridondanti dello spazio di archiviazione (ZRS) e account geografico ridondanti dello spazio di archiviazione (GRS) e gli account di accesso in lettura geografico archiviazione ridondanti (RA GRS).

- [Contratto di servizio Azure per lo spazio di archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Aree

Azure è disponibile in genere 30 aree di tutto il mondo e ha annunciato piani per 4 ulteriori regioni. Espansione geografica priorità per Azure perché consente ai clienti di migliorare le prestazioni e supporta i propri requisiti e le preferenze relative ai dati posizione.  Area geografica più recente di azures per avviare è in Germania.

Germania Cloud Microsoft offre un'opzione di applicazione ai servizi Cloud Microsoft già disponibili in Europa, creazione maggiori opportunità di innovazione e crescita economica per altamente regolamentato partner e clienti in Germania, dell'Unione europea (UE) e l'associazione europea di libero scambio (EFTA).

Dati dei clienti in questa nuova data center privato, Magdeburg e Francoforte, viene gestiti tramite il controllo di un dominio trusted dati, T-Systems internazionale, una società tedesca indipendente e filiale di Deutsche Telekom. Commerciale servizi cloud di Microsoft in questi Data Center rispettare norme di gestione dei dati tedesco e offrire ai clienti le modalità di elaborazione dei dati scelte.


- [Eseguire il mapping delle aree geografiche Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sicurezza

Spazio di archiviazione Azure fornisce una serie completa di funzionalità di sicurezza che insieme consentono agli sviluppatori di creare applicazioni protette. È possibile proteggere l'account di archiviazione tramite controllo dell'accesso basato sui ruoli e Azure Active Directory. Proteggere i dati in transito tra un'applicazione e Azure utilizzando la crittografia lato Client, HTTPS o 3.0 piccole e medie imprese. Dati impostare automaticamente la crittografia quando scritta allo spazio di archiviazione di Azure tramite lo spazio di archiviazione servizio di crittografia (SSE). Vengano crittografati mediante la crittografia del Azure, è possono impostare il sistema operativo e dati dischi utilizzati da macchine virtuali. Uso delle firme di Access condiviso, è possibile concedere l'accesso delegato agli oggetti dati in archiviazione Azure.

### <a name="management-plane-security"></a>Protezione di piano di gestione

Il piano di gestione include le risorse utilizzate per gestire il proprio account di archiviazione. In questa sezione verrà illustrato il modello di distribuzione di gestione di risorse Azure e su come utilizzare controllo dell'accesso basato sui ruoli (RBAC) per controllare l'accesso agli account di archiviazione. Anche soffermarsi sulla gestione delle chiavi account lo spazio di archiviazione e come rigenerare loro.

### <a name="data-plane-security"></a>Protezione dei dati piano

In questa sezione vengono illustrate che consente l'accesso agli oggetti dati effettivi nel proprio account di archiviazione, ad esempio BLOB, file, code e tabelle, utilizzando le firme di Access condiviso e archiviati i criteri di accesso. Verranno illustrate le caratteristiche associazioni di protezione a livello di servizio e associazioni di protezione a livello di account. Vedremo anche come limitare l'accesso a uno specifico indirizzo IP (o intervallo di indirizzi IP), come limitare il protocollo utilizzato per HTTPS e come revocare una firma di Access condiviso senza attendere che la scadenza.

## <a name="encryption-in-transit"></a>Crittografia durante il transito

In questa sezione viene descritto come proteggere i dati quando si trasferisce o all'esterno di archiviazione Azure. Verranno fornite informazioni sull'utilizzo consigliato di HTTPS e la crittografia utilizzata da piccole e medie imprese 3.0 per condivisioni File Azure. Verrà inoltre osservare la crittografia lato Client, che consente di crittografare i dati prima di essere trasferiti in archiviazione nelle applicazioni client e decrittografare i dati dopo il trasferimento fuori dello spazio di archiviazione.

## <a name="encryption-at-rest"></a>Crittografia inattivi

Si parla lo spazio di archiviazione servizio di crittografia (SSE) e come è possibile abilitarla per un account di archiviazione, causando il BLOB di blocco BLOB di pagine e aggiungere BLOB da crittografare automaticamente quando scritto allo spazio di archiviazione Azure. Esamineremo anche come è possibile utilizzare la crittografia del Azure e informazioni sulle differenze di base e casi di crittografia disco e SSE e la crittografia lato Client. Esamineremo brevemente la conformità FIPS per i computer governo degli Stati Uniti.

- [Guida di sicurezza di spazio di archiviazione Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Riduzione dei costi

- [Costo di archiviazione](https://azure.microsoft.com/pricing/details/storage/)

- [Calcolatrice il costo di archiviazione](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limiti di archiviazione

- [Limiti di servizio di archiviazione](../azure-subscription-service-limits.md#storage-limits)
