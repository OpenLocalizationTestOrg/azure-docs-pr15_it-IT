<properties
    pageTitle="Proteggere con Active Directory e DNS con il ripristino del sito Azure | Microsoft Azure"
    description="In questo articolo viene descritto come implementare una soluzione di ripristino di emergenza per Active Directory utilizzando il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteggere con Active Directory e DNS con il ripristino del sito Azure

Applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP è dipendono da Active Directory e un'infrastruttura DNS per il corretto funzionamento. Quando si crea una soluzione di ripristino di emergenza, Applications Edition, è importante tenere presente che è necessario proteggere e ripristinare Active Directory e DNS prima di altri componenti dell'applicazione, per garantire che aspetti funzionano correttamente in caso di emergenza.

Il ripristino del sito è un servizio di Azure che consente di emergenza per coordinazione replica, failover e ripristino di macchine virtuali. Il ripristino del sito supporta diversi scenari di replica per la protezione in modo coerente e in modo facile macchine virtuali di failover e alle applicazioni di nuvole privato, pubblico o host.

Usa il ripristino del sito, è possibile creare un piano di ripristino di emergenza automatizzato completo per Active Directory. In caso di problemi, è possibile avviare caso di errore in pochi secondi da qualsiasi posizione e ottenere Active Directory operatività in pochi minuti. Se è stato distribuito Active Directory per più applicazioni, ad esempio SharePoint e SAP nel sito principale e si desidera eseguire il sito completato, potrebbe non riuscire su Active Directory prima di tutto utilizzando il ripristino del sito e quindi prova non superata sopra le altre applicazioni che utilizzano piani di ripristino specifico dell'applicazione.

In questo articolo viene spiegato come creare una soluzione di ripristino di emergenza per Active Directory, come eseguire pianificata e non pianificata e failover test con un piano di ripristino di un solo clic, le configurazioni supportate e i prerequisiti.  È necessario avere familiarità con Active Directory e il ripristino del sito di Azure prima di iniziare.

Sono disponibili due opzioni consigliate in base alla complessità del proprio ambiente.

### <a name="option-1"></a>Opzione 1

Se si dispone di un numero limitato di applicazioni e un controller di dominio e si vuole avere esito negativo sull'intero sito, è consigliabile utilizzare il ripristino del sito per replicare controller di dominio al sito secondario (se sta non funziona su Azure o un sito secondario). La stessa macchina virtuale replicata è utilizzabile per test failover troppo.

### <a name="option-2"></a>Opzione 2

Se si dispone di un numero elevato di applicazioni e in più di un controller di dominio nell'ambiente, o se si prevede di esito negativo su alcune applicazioni alla volta, è consigliabile che oltre la replica macchina virtuale controller di dominio con il ripristino del sito è anche necessario configurare un controller di dominio nel sito di destinazione (Azure o un data center locale secondario).

>[AZURE.NOTE] Anche in caso di implementazione opzione 2, per questo caso di errore di test è comunque necessario replicare controller di dominio utilizzando il ripristino del sito. [Considerazioni sul failover di test](#considerations-for-test-failover) per ulteriori informazioni, leggere.


Nelle sezioni seguenti viene illustrato come attivare la protezione per un controller di dominio nel ripristino del sito e come configurare un controller di dominio in Azure.


## <a name="prerequisites"></a>Prerequisiti

- Una distribuzione locale di Active Directory e DNS server.
- Un archivio di servizi di ripristino di Windows Azure sito in un abbonamento a Microsoft Azure.
- Se si sta la replica a portata di clic Azure lo strumento di valutazione di Azure macchina virtuale in macchine virtuali per garantire che siano compatibili con macchine virtuali di Azure e servizi di recupero del sito di Windows Azure.


## <a name="enable-protection-using-site-recovery"></a>Abilitare la protezione tramite il ripristino del sito


### <a name="protect-the-virtual-machine"></a>Proteggere la macchina virtuale

Attivare la protezione della macchina virtuale/DNS controller di dominio nel ripristino del sito. Configurare le impostazioni del ripristino del sito in base al tipo di macchina virtuale (Hyper-V o VMware). È consigliabile una frequenza di replica coerenti arresto anomalo di 15 minuti.

###<a name="configure-virtual-machine-network-settings"></a>Configurare le impostazioni di rete macchina virtuale

Per la macchina virtuale/DNS controller di dominio, configurare le impostazioni di rete nel ripristino del sito in modo che la macchina virtuale verrà collegata alla rete corretta dopo il failover. Ad esempio, se si sta replicare macchine virtuali di Hyper-V in Azure è possibile selezionare la macchina virtuale nel cloud VMM o del gruppo di protezione di configurare le impostazioni di rete, come illustrato di seguito

![Impostazioni di rete macchina virtuale](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteggere con Active Directory con replica di Active Directory

### <a name="site-to-site-protection"></a>Protezione del sito al sito

Creare un controller di dominio nel sito secondario e specificare il nome dello stesso dominio in uso nel sito principale quando si converte il server a un ruolo di controller di dominio. È possibile utilizzare lo snap-in **Active Directory siti e servizi Microsoft** per configurare le impostazioni oggetto collegamento di sito in cui vengono aggiunti i siti. Se si configura impostazioni su un collegamento di sito, è possibile controllare verifica della replica tra due o più siti e con quale frequenza. Per ulteriori informazioni, vedere [Pianificazione di replica tra siti](https://technet.microsoft.com/library/cc731862.aspx) .

###<a name="site-to-azure-protection"></a>Protezione dei siti di Azure

Seguire le istruzioni per [creare un controller di dominio in una rete virtuale Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando si converte il server a un ruolo di controller di dominio specificare lo stesso nome di dominio da utilizzata per il sito principale.

[Riconfigurare i server DNS per la rete virtuale](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), utilizzare il server DNS in Azure.

![Rete Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Considerazioni sul failover test

Test failover si verifica in una rete è isolati dalla rete produzione in modo che non ha alcun impatto sulla carichi di lavoro di produzione.

La maggior parte delle applicazioni richiedono anche la presenza di un controller di dominio e un server DNS a funzionare, in modo prima dell'applicazione non è riuscito sopra, un controller di dominio deve essere creati in rete isolata da utilizzare per test failover. Il modo più semplice per eseguire questa operazione è attiva protezione sul computer virtuale/DNS controller di dominio con il ripristino del sito ed eseguire test caso di errore della macchina virtuale prima di eseguire test caso di errore del piano di ripristino per l'applicazione. Ecco come è:

1. Attivare la protezione nel ripristino del sito per la macchina virtuale/DNS controller di dominio.
2. Creare una rete isolata. Qualsiasi virtuali creati in Azure per impostazione predefinita sono isolata dalle altre reti. È consigliabile che l'intervallo di indirizzi IP per la rete è uguale a quella di rete di produzione. Non consentono la connettività a siti di questa rete.
3. Fornire un indirizzo IP DNS nella rete creato, come l'indirizzo IP che si sta aspettando la macchina virtuale DNS per ottenere. Se si sta replicare in Azure, specificare l'indirizzo IP per la macchina virtuale che verrà utilizzata in caso di failover nell'impostazione di **Indirizzi IP di destinazione** nelle proprietà macchine Virtuali. Se sta replicate in un altro locale del sito e si usa Segui DHCP le istruzioni per [installare DNS e DHCP per failover test](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] L'indirizzo IP assegnato a una macchina virtuale durante caso di errore di test è uguale all'indirizzo IP che otterrebbe durante caso di errore pianificato o non pianificato, se l'indirizzo IP è disponibile nella rete failover test. In caso contrario, la macchina virtuale riceve un indirizzo IP diverso che è disponibile nella rete failover test.

4. Nella macchina virtuale controller di dominio eseguire test caso di errore che ne nella rete isolata. Consente di eseguire il test failover punto di ripristino coerente applicazioni disponibili più recente di macchina virtuale controller di dominio. 
5. Eseguire test caso di errore per il piano di ripristino dell'applicazione.
6. Dopo avere completato il test, contrassegnare il processo di failover test di macchina virtuale controller di dominio e del piano di ripristino 'Completamento' nella scheda **processi** nel portale del ripristino del sito.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controller di dominio su computer diversi

Se DNS non è nello stesso computer di virtuale controller di dominio che è necessario creare una VM DNS per il failover di test. Se si trovano nella stessa macchina virtuale, è possibile ignorare questa sezione.

È possibile utilizzare un server DNS aggiornato e creare tutte le aree necessari. Ad esempio, se il dominio di Active Directory è contoso.com, è possibile creare una zona DNS con il nome contoso.com. I movimenti corrispondente a Active Directory devono essere aggiornati nel sistema DNS, come indicato di seguito:

1. Assicurarsi che queste impostazioni sono attive prima insorge altre macchine virtuali nel piano del ripristino:

    - L'area deve essere denominato dopo il nome radice della foresta.
    - L'area deve essere eseguito il file.
    - L'area deve essere abilitata per gli aggiornamenti sicuri e non protetta.
    - Risoluzione di macchina virtuale controller di dominio deve fare riferimento all'indirizzo IP del computer virtuale DNS.

2. Eseguire il comando seguente nella macchina virtuale controller di dominio:

    `nltest /dsregdns`

3. Aggiungere una zona sul server DNS, Consenti aggiornamenti di non protetta e aggiungere la voce relativa a DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Passaggi successivi

Lettura [quali carichi di lavoro è possibile proteggere?](../site-recovery/site-recovery-workload.md) per ulteriori informazioni sulla protezione carichi di lavoro dell'organizzazione con il ripristino del sito di Azure.
