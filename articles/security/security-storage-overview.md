<properties
   pageTitle="Cenni preliminari sulla protezione di archiviazione Azure | Microsoft Azure"
   description=" Spazio di archiviazione Azure è la soluzione di archiviazione cloud per moderne applicazioni basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. In questo articolo viene fornita una panoramica delle principali funzionalità di sicurezza di Azure che può essere utilizzata con lo spazio di archiviazione di Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Cenni preliminari sulla protezione di archiviazione Azure

Spazio di archiviazione Azure è la soluzione di archiviazione cloud per moderne applicazioni basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Spazio di archiviazione Azure fornisce una serie completa di funzionalità di sicurezza:

- Account di archiviazione possono essere protetti tramite controllo dell'accesso basato sui ruoli e Azure Active Directory.
- Proteggere i dati in transito tra un'applicazione e Azure utilizzando la crittografia lato Client, HTTPS o 3.0 piccole e medie imprese.
- Possono impostare i dati da crittografare automaticamente quando scritta allo spazio di archiviazione di Azure utilizzando la crittografia del servizio di archiviazione.
- Vengano crittografati mediante la crittografia del Azure, è possono impostare il sistema operativo e dati dischi utilizzati da macchine virtuali.
- Uso delle firme di Access condiviso, è possibile concedere l'accesso delegato agli oggetti dati in archiviazione Azure.
- Metodo di autenticazione utilizzato da un utente durante l'accesso a archiviazione può essere rilevato tramite analitica lo spazio di archiviazione.

Per informazioni più dettagliate alla sicurezza in archiviazione Azure, vedere la [Guida sulla sicurezza di archiviazione Azure](../storage/storage-security-guide.md). Questa guida fornisce approfondimenti sulle funzionalità di protezione di spazio di archiviazione di Azure, ad esempio tasti singoli account lo spazio di archiviazione, la crittografia dei dati in transito e presso riposo e analitica lo spazio di archiviazione.

In questo articolo viene fornita una panoramica delle funzionalità di sicurezza di Azure che può essere utilizzata con lo spazio di archiviazione di Azure. Non sono collegamenti ad articoli che forniscono i dettagli di ogni caratteristica riuscito per ulteriori informazioni.

Ecco le funzionalità di base per contenute in questo articolo:

- Controllo dell'accesso basato sui ruoli
- Delega per l'accesso a oggetti di archiviazione
- Crittografia durante il transito
- Crittografia resto/la crittografia del servizio di archiviazione
- Crittografia del Azure
- Archivio chiave Azure

## <a name="role-based-access-control-rbac"></a>Controllo dell'accesso basato sui ruoli (RBAC)

È possibile proteggere il tuo account di archiviazione con controllo dell'accesso basato sui ruoli (RBAC). Limitazione dell'accesso basato sui principi di sicurezza [utili](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege) è fondamentale per le organizzazioni che vogliono applicare i criteri di sicurezza per l'accesso ai dati. Questi diritti di accesso sono concessi mediante l'assegnazione di ruolo RBAC appropriato per i gruppi e le applicazioni in un determinato ambito. [Ruoli RBAC incorporati](../active-directory/role-based-access-built-in-roles.md), ad esempio lo spazio di archiviazione Account collaboratore, è possibile utilizzare per assegnare i privilegi agli utenti.

Ulteriori informazioni:

- [Controllo dell'accesso basato sui ruoli Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Delega per l'accesso a oggetti di archiviazione

Una firma di accesso condiviso (SA) consente di delega per l'accesso alle risorse nell'account di archiviazione. Le SA significa che è possibile concedere che autorizzazioni agli oggetti nel proprio account di archiviazione un client limitate per un determinato periodo di tempo e con un determinato set di autorizzazioni. È possibile concedere le autorizzazioni limitate senza che sia necessario condividere i tasti di scelta account. Le SA sono un URI che includa nei relativi parametri query tutte le informazioni necessarie per l'accesso autenticato a una risorsa di archiviazione. Per accedere alle risorse di archiviazione con le associazioni di protezione, il client deve solo passare al metodo o costruttore appropriato le associazioni di protezione.

Ulteriori informazioni:

- [Informazioni sul modello di SA](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Creare e utilizzare un sa con archiviazione Blob](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Crittografia durante il transito
La crittografia durante il transito è un meccanismo di protezione dei dati quando vengono trasmessi in rete. Con lo spazio di archiviazione di Azure è possibile proteggere i dati utilizzando:

- [Crittografia a livello di trasporto](../storage/storage-security-guide.md#encryption-in-transit), ad esempio HTTPS quando si trasferiscono dati o all'esterno di archiviazione Azure.
- [Crittografia](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), ad esempio la crittografia di piccole e medie imprese 3.0 per condivisioni File Azure.
- [La crittografia lato client](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), per crittografare i dati prima di essere trasferiti in spazio di archiviazione e decrittografare i dati dopo il trasferimento fuori dello spazio di archiviazione.

Ulteriori informazioni sulla crittografia lato client:

- [Crittografia lato client per lo spazio di archiviazione di Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Protezione cloud controlla serie: la crittografia dei dati in transito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Crittografia inattivi

Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è obbligatoria verso la privacy dei dati, conformità e sovranità dei dati. Esistono tre caratteristiche Azure che forniscono la crittografia dei dati che si trova "al resto":

- [La crittografia del servizio di archiviazione](../storage/storage-security-guide.md#encryption-at-rest) consente di richiedere che il servizio di archiviazione crittografare automaticamente i dati quando si scrive allo spazio di archiviazione Azure.
- [La crittografia lato client](../storage/storage-security-guide.md#client-side-encryption) vengono forniti anche la funzionalità di crittografia inattivi.
- [La crittografia del Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) consente di crittografare il dischi del sistema operativo e dischi dati utilizzati da una macchina virtuale IaaS.

Ulteriori informazioni sulla crittografia del servizio di archiviazione:

- [La crittografia del servizio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) è disponibile per [L'archiviazione Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Per informazioni dettagliate su altri tipi di archiviazione Azure, vedere [File](https://azure.microsoft.com/services/storage/files/), [su disco (Premium archiviazione)](https://azure.microsoft.com/services/storage/premium-storage/), [tabella](https://azure.microsoft.com/services/storage/tables/)e [coda](https://azure.microsoft.com/services/storage/queues/).
- [Crittografia del servizio di archiviazione Azure per dati inattivi](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Crittografia del Azure

Crittografia disco Azure per macchine () consente di protezione dell'organizzazione di indirizzi e i requisiti di conformità crittografando macchine Virtuali dischi (inclusi dischi di avvio e di dati) con chiavi e i criteri di che controllo in [Azure chiave archivio](https://azure.microsoft.com/services/key-vault/).

Crittografia disco per macchine virtuali funziona per sistemi operativi Linux e Windows. Utilizza anche archivio chiave che consentono di proteggere, gestire e controllare l'utilizzo delle chiavi di crittografia disco. Tutti i dati dischi macchine Virtuali è crittografato inattivi utilizzando la tecnologia di crittografia standard di settore negli account di archiviazione Azure. La soluzione di crittografia disco per Windows è basata su [Unità BitLocker di Microsoft](https://technet.microsoft.com/library/cc732774.aspx)e la soluzione Linux è basata sulla [crittografia di data mining](https://en.wikipedia.org/wiki/Dm-crypt).

Ulteriori informazioni:

- [Crittografia di Azure disco per Windows e macchine virtuali Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Archivio chiave Azure

La crittografia disco Azure utilizza [Azure chiave archivio](https://azure.microsoft.com/services/key-vault/) consentono di controllare e gestire le chiavi di crittografia disco e informazioni riservate nel proprio abbonamento archivio chiave, assicurandosi che tutti i dati nei dischi macchina virtuale vengono crittografati inattivi in archiviazione Azure. È necessario utilizzare archivio chiave per controllare le chiavi e l'uso dei criteri.

Ulteriori informazioni:

- [Che cos'è archivio chiave Azure?](../key-vault/key-vault-whatis.md)
- [Guida introduttiva di Azure chiave archivio](../key-vault/key-vault-get-started.md)
