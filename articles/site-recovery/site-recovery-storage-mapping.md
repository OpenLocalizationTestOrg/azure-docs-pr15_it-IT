<properties
    pageTitle="Eseguire il mapping di archiviazione in Azure sito ripristino della replica di macchina virtuale Hyper-V tra i data center locale | Microsoft Azure"
    description="Preparare il mapping di spazio di archiviazione per la replica di macchina virtuale Hyper-V tra due Data Center locale con il ripristino del sito di Azure."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Preparare il mapping di spazio di archiviazione per la replica di macchina virtuale Hyper-V tra due Data Center locale con il ripristino del sito di Azure


Il ripristino del sito Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Questo articolo vengono illustrati i mapping di spazio di archiviazione, che consente di eseguire ottimali utilizzano di spazio di archiviazione quando si usa il ripristino del sito per replicare macchine virtuali Hyper-V tra i Data Center VMM due locale.

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="overview"></a>Panoramica

Mapping di spazio di archiviazione è rilevante solo quando si attiva la replica macchine virtuali di Hyper-V che si trovano nel cloud VMM da un centro dati principale a un Data Center secondario replica Hyper-V Replica o SAN, come indicato di seguito:


- **Locale per la replica locale con Hyper-V Replica)** Si imposta lo spazio di archiviazione mapping da mapping classificazioni di spazio di archiviazione su un'origine e VMM server target per eseguire le operazioni seguenti:

    - **Identificare lo spazio di archiviazione destinazione per macchine virtuali di replica**, ovvero macchine virtuali di replicare a una destinazione di archiviazione (piccole e medie imprese condividere o cluster volumi condivisi (CSVs)) che si è scelto.
    - **Posizionamento macchina virtuale duplicata**, viene utilizzato il mapping di spazio di archiviazione per posizionare in modo ottimale macchine virtuali di replica sui server host Hyper-V. Macchine virtuali di replica verrà inserite in host che possono accedere la classificazione di archiviazione mappate.
    - **Nessun mapping di spazio di archiviazione**, ovvero se ma non si configura il mapping di spazio di archiviazione, macchine virtuali da replicare alla posizione di archiviazione predefinito specificata nel server host Hyper-V associato alla macchina virtuale duplicata.

- **Locale per la replica locale con SAN**, si imposta lo spazio di archiviazione mapping da mapping pool di archiviazione matrici su un'origine e destinazione server VMM.
    - **Specificare pool**: consente di specificare quali pool di archiviazione secondario riceve i dati di replica dal pool di principale.
    - **Pool di archiviazione di destinazione di identificare**, ovvero garantisce che LUN in un gruppo di replica di origine vengono replicate al pool di archiviazione mappato destinazione preferito.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurare la classificazione di spazio di archiviazione per la replica di Hyper-V

Quando si usa Hyper-V Replica replicare con il ripristino del sito, si esegue il mapping tra classificazioni di spazio di archiviazione in origine e destinazione server VMM, o su un singolo server VMM se due siti sono gestiti dallo stesso server VMM. Si noti che:

- Quando il mapping è configurato correttamente ed è attivata la replica, disco rigido virtuale della macchina virtuale in corrispondenza della posizione principale da replicare allo spazio di archiviazione nel percorso di destinazione mappate.
- Classificazioni di spazio di archiviazione devono essere disponibile per i gruppi di host si trova in aree di origine e destinazione.
- Classificazioni non è necessario avere lo stesso tipo di spazio di archiviazione. Ad esempio, è possibile mappare una classificazione di origine che contiene le azioni di piccole e medie imprese da una classificazione di destinazione che contiene CSVs.
- Altre informazioni su [come creare classificazioni di spazio di archiviazione in VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Esempio

Se classificazioni siano configurate correttamente in VMM quando si seleziona l'origine e destinazione server VMM durante il mapping di spazio di archiviazione, verranno visualizzate le classificazioni di origine e destinazione. Ecco un esempio di classificazioni per un'organizzazione con due posizioni New York e Chicago e nelle condivisioni file lo spazio di archiviazione.

**Posizione** | **Server VMM** | **Condivisione di file (origine)** | **Classificazione (origine)** | **Mappare** | **Condivisione di file (destinazione)**
---|---|--- |---|---|---
New York | VMM_Source| SourceShare1 | ORO | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | ARGENTO | SILVER_TARGET | TargetShare2
 | | SourceShare3 | MINIMO | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Non mappati |
| | | SILVER_TARGET | Non mappati |
 | | | BRONZE_TARGET | Non mappati

Nella pagina delle **risorse** del portale del ripristino del sito, è opportuno configurare questi nella scheda **Archiviazione Server** .

![Configurare il mapping di spazio di archiviazione](./media/site-recovery-storage-mapping/storage-mapping1.png)

Con questo esempio:
- Quando un viene creata una macchina virtuale di replica per qualsiasi macchina virtuale in archiviazione oro (SourceShare1), che possa essere replicato a un archivio GOLD_TARGET (TargetShare1).
- Creazione di una macchina virtuale duplicata per ogni macchina virtuale in archiviazione argento (SourceShare2), da replicare a un archivio SILVER_TARGET (TargetShare2) e così via.

Condivisioni file effettivo e classificazioni assegnate in VMM vengono visualizzati nella schermata successiva.

![Classificazioni di spazio di archiviazione in VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Più percorsi di archiviazione

Se la classificazione di destinazione è assegnata a più piccole e medie imprese condivisioni o CSVs, la posizione di archiviazione ottimale verrà selezionata automaticamente quando la macchina virtuale è protetto. Se lo spazio di archiviazione non destinazione appropriata sono disponibile con la classificazione specificata, il percorso di archiviazione predefinito specificato nell'host Hyper-V viene utilizzato per inserire i dischi rigidi virtuali duplicata.

Nella tabella seguente mostra come la classificazione di spazio di archiviazione e volumi cluster condivisi siano configurati in questo esempio.

**Posizione** | **Classificazione** | **Archiviazione associato**
---|---|---
New York | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | ARGENTO | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

In questa tabella sono riepilogate il comportamento quando si abilita la protezione per macchine virtuali (VM1 - VM5) in questo ambiente di esempio.

**Macchina virtuale** | **Spazio di archiviazione di origine** | **Classificazione di origine** | **Spazio di archiviazione di destinazione mappati**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Entrambi GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Entrambi GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | ARGENTO | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | ARGENTO |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Entrambi SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/D | Nessun mapping, in modo che viene utilizzato il percorso di archiviazione predefinito dell'host di Hyper-V

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una migliore comprensione del mapping di spazio di archiviazione, [preparare distribuire il ripristino del sito di Azure](site-recovery-best-practices.md).
