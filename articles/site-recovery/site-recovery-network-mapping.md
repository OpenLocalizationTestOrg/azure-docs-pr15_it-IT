<properties
    pageTitle="Preparare l'ambiente di connessione di rete per la protezione macchina virtuale Hyper-V con VMM nel ripristino del sito di Azure | Microsoft Azure"
    description="Configurare la connessione di rete per Hyper-V macchina virtuale la replica da un data center locale in Azure o a un sito secondario."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Preparare l'ambiente di connessione di rete per la protezione macchina virtuale Hyper-V con VMM nel ripristino del sito di Azure

Il ripristino del sito Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici.

In questo articolo vengono illustrati i mapping di rete, che consente di configurare impostazioni di rete in modo ottimale quando si usa il ripristino del sito per replicare macchine virtuali Hyper-V trovano nel cloud VMM tra due Data Center locale o tra un data center locale e Azure. Si noti che se si sta replicare macchine virtuali di Hyper-V senza un cloud VMM o replicare macchine virtuali VMware o server fisici, in questo articolo non è rilevante.

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.


## <a name="overview"></a>Panoramica

Quando il ripristino del sito di Azure viene distribuito replicare macchine virtuali Hyper-V in Azure o a un Data Center secondario, tramite la replica di Replica Hyper-V o SAN, viene utilizzato il mapping di rete.

- **La replica Hyper-V macchine virtuali nel cloud VMM tra due locale Data Center**, ovvero rete mappe mapping tra macchine Virtuali su un server di origine VMM e delle reti macchine Virtuali su un server VMM destinazione eseguire le operazioni seguenti:

    - **Macchine virtuali di connettersi dopo il failover**, ovvero garantisce che macchine virtuali verrà connesso reti appropriate dopo il failover. La macchina virtuale duplicata verrà connesso alla rete destinazione che sia associata alla rete origine.
    - **Macchine virtuali di replica di posizione nel server host**, in modo ottimale posizionare macchine virtuali di replica sui server host Hyper-V. Macchine virtuali di replica verrà inserite in host che possono accedere le reti macchine Virtuali mappate.
    - **Nessuna connessione di rete**, ovvero se ma non si configura connessione di rete, replicato macchine virtuali di non essere connesso a tutte le reti macchine Virtuali dopo il failover.

- **Macchine virtuali di replica Hyper-V in un VMM locale cloud in Azure**, ovvero rete mappe mapping tra le reti macchine Virtuali nel server VMM di origine e destinazione Azure reti per eseguire le operazioni seguenti:
    - **Connettersi macchine virtuali dopo il failover**, ovvero quali failover sulla stessa rete è possibile connettersi a altro, indipendentemente dalla quale piano di ripristino si trovano in tutti i computer.
    - **Gateway di rete**, ovvero se un gateway di rete è impostato sulla destinazione della rete Azure, macchine virtuali di connettersi a altre macchine virtuali locale.
    - **Nessuna connessione di rete**, ovvero se ma non si configura connessione di rete, solo macchine virtuali di tale failover nello stesso piano di ripristino sarà possibile connettersi tra loro dopo fail in Azure.


## <a name="network-mapping-example"></a>Esempio di mapping di rete

Connessione di rete può essere configurato tra le reti macchine Virtuali in due server VMM o in un unico server VMM se due siti sono gestiti dallo stesso server. Quando mapping è configurato correttamente e replica è attivata, una macchina virtuale in corrispondenza della posizione principale verrà connesso a una rete e la relativa replica in corrispondenza della posizione di destinazione verrà connessi alla rete mappata.

Se le reti impostate correttamente in VMM, quando si seleziona una rete macchine Virtuali di destinazione durante il mapping di rete, verrà visualizzato il cloud origine VMM che utilizzano la rete macchine Virtuali di origine, insieme le reti macchine Virtuali di destinazione disponibile sulle aree di destinazione che vengono utilizzate per la protezione.

Ecco un esempio per illustrare questo meccanismo. Diamo un'organizzazione con due posizioni New York e Chicago.

**Posizione** | **Server VMM** | **Reti macchine Virtuali** | **Mappare**
---|---|---|---
New York | VMM NewYork| VMNetwork1 NewYork | Mappare VMNetwork1 Chicago
 |  | VMNetwork2 NewYork | Non mappati
Chicago | VMM Chicago| VMNetwork1 Chicago | Mappare VMNetwork1 NewYork
 | | VMNetwork2 Chicago | Non mappati

Con questo esempio:

- Creazione di una macchina virtuale duplicata per ogni macchina virtuale che sia connesso a VMNetwork1 NewYork, verrà connesso VMNetwork1 Chicago.
- Creazione di una macchina virtuale duplicata per NewYork VMNetwork2 o VMNetwork2 Chicago, non essere connessi a una rete.

Ecco come cloud VMM siano configurati in azienda di esempio e le reti logiche associate il cloud.

### <a name="cloud-protection-settings"></a>Impostazioni di protezione cloud

**Cloud protetto** | **Protezione cloud** | **Rete logica (Reggio Emilia)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>ND</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Impostazioni di rete logiche e SV

**Posizione** | **Rete logica** | **Rete di macchine Virtuali associata**
---|---|---
New York | LogicalNetwork1 NewYork | VMNetwork1 NewYork
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-networks"></a>Reti di destinazione

In base a queste impostazioni, quando si seleziona rete macchine Virtuali di destinazione, nella tabella seguente mostra le opzioni che saranno disponibili.

**Selezionare** | **Cloud protetto** | **Protezione cloud** | **Rete di destinazione disponibile**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | Disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Non disponibile
 | GoldCloud1 | GoldCloud2 | Disponibile



## <a name="multiple-subnets"></a>Più subnet

Se la rete di destinazione dispone di più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine, quindi la macchina virtuale duplicata verrà connesso subnet destinazione dopo il failover. Se non vi è alcuna subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.


### <a name="failback"></a>Failback

Per vedere cosa accade nel caso dei failback (inverso replica), si supponga che VMNetwork1 NewYork sia mappato a VMNetwork1 Chicago, con le impostazioni seguenti.


**Macchina virtuale** | **Connessi alla rete macchine Virtuali**
---|---
VM1 | Rete VMNetwork1
VM2 (replica di VM1) | VMNetwork1 Chicago

Con queste impostazioni, esaminiamo cosa accade in un paio di scenari possibili.

**Scenario:** | **Risultato**
---|---
Nessuna modifica nelle proprietà della rete di macchine Virtuali 2 dopo il failover. | 1 macchine Virtuali rimane connessi alla rete origine.
Proprietà di rete di macchine Virtuali 2 vengono modificati dopo il failover ed è scollegato. | Macchine Virtuali-1 è scollegato.
Proprietà di rete di macchine Virtuali 2 vengono modificati dopo il failover e sia connesso a Chicago VMNetwork2. | Se non è mappato VMNetwork2 Chicago, verranno disconnessi macchine Virtuali-1.
Connessione di rete di Chicago VMNetwork1 viene modificato. | 1 macchine Virtuali verrà connesso alla rete ora assegnata alle VMNetwork1 Chicago.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una migliore comprensione della connessione di rete, [iniziare con la distribuzione il ripristino del sito](site-recovery-best-practices.md).
