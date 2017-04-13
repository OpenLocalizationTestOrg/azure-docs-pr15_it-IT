<properties
    pageTitle="Matrice di supporto per il ripristino del sito Azure | Microsoft Azure"
    description="Vengono riepilogati i sistemi operativi supportati e i componenti per il ripristino del sito di Azure"
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

# <a name="azure-site-recovery-support-matrix"></a>Matrice di supporto Azure il ripristino del sito

In questo articolo vengono riepilogati i sistemi operativi supportati e dei componenti per distribuzioni il ripristino del sito di Azure. Un elenco dei componenti supportati e i prerequisiti è disponibile per ogni scenario di distribuzione in ogni articolo distribuzione corrispondente e il documento contiene un riepilogo loro.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Sistemi operativi supportati per server di virtualizzazione


**Origine** | **Destinazione** | **Sistema operativo host**
---|---|---|--- 
**Host Hyper-V (senza VMM)** | Azure | Windows Server 2012 R2 con gli aggiornamenti più recenti
**Host Hyper-V (con VMM)** | Azure | Windows Server 2012 R2 con gli aggiornamenti più recenti
**Host Hyper-V (con VMM)** | Sito secondario VMM | Almeno Windows Server 2012 con gli aggiornamenti più recenti
**VMware hosts/vCenter** | Azure | vCenter 5.5 o 6.0 (supporto per 5,5 solo caratteristiche) <br/><br/> vSphere 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti
**VMware hosts/vCenter** | Sito secondario VMware | vCenter 5.5 o 6.0 (supporto per 5,5 solo caratteristiche) <br/><br/> vSphere 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti

## <a name="supported-requirements-for-replicated-machines"></a>Requisiti supportati per computer replicato

**Origine** | **Che cos'è replicato** | **Destinazione** | **Sistema operativo host**
---|---|---|--- 
**Macchine virtuali Hyper-V** | Qualsiasi carico di lavoro | Azure | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Macchine virtuali devono soddisfare [requisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Macchine virtuali di Hyper-V (con VMM)** | Qualsiasi carico di lavoro | Azure | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Macchine virtuali devono soddisfare [requisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Macchine virtuali di Hyper-V (con VMM)** | Qualsiasi carico di lavoro | Sito secondario VMM | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Macchine virtuali VMware** | Qualsiasi carico di lavoro in esecuzione in macchine Virtuali di Windows | Azure | 64 bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con in almeno SP1<br/><br/> Macchine virtuali devono soddisfare [requisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Macchine virtuali VMware** | Qualsiasi carico di lavoro in esecuzione su Linux VM | Azure | Red Hat Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Spazio di archiviazione necessario: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/> Macchine virtuali devono soddisfare [requisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Macchine virtuali VMware** | Qualsiasi carico di lavoro in esecuzione in macchine Virtuali di Windows | Sito secondario VMware | 64 bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con in almeno SP1
**Macchine virtuali VMware** | Qualsiasi carico di lavoro in esecuzione su Linux VM | Sito secondario VMware | Red Hat Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.
**Server fisici** | Qualsiasi carico di lavoro in esecuzione su Windows | Azure | 64 bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 con SP1 almeno
**Server fisici** | Qualsiasi carico di lavoro in esecuzione su Linux | Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.
**Server fisici** | Qualsiasi carico di lavoro in esecuzione su Windows | Sito secondario | 64 bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 con SP1 almeno
**Server fisici** | Qualsiasi carico di lavoro in esecuzione su Linux | Sito secondario | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Spazio di archiviazione necessario: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versioni di provider

**Nome** | **Descrizione** | **Versione più recente** | **Supporto tecnico** | **Dettagli**
---|---|---|---| ---
**Provider di ripristino del sito Azure** | Coordinate le comunicazioni tra computer server locale e siti di Azure/secondario <br/><br/> Se nessun server VMM installato nel server VMM locale o server Hyper-V | 5.1.1700 (disponibile dal portale) | [Caratteristiche e le correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Il ripristino del sito Azure unificata configurazione (VMware in Azure)** | Coordinate le comunicazioni tra computer server VMware locale e Azure <br/><br/> Installato in locale VMware server | 9.3.4246.1 (disponibile dal portale) | [Caratteristiche e le correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio di mobilità** | Replica di coordinate tra computer server locale VMware server/fisici e sito Azure/secondario | ND (disponibile dal portale) | Installato in ogni VMware VM o server fisico che si desidera replicare. **Agente di Microsoft Azure ripristino servizi (MARS)** | Replica di coordinate tra macchine virtuali di Hyper-V e Azure<br/><br/> Installata nel server di Hyper-V in locale (con o senza un server VMM) | 2.0.8689.0 (disponibile dal portale) | Questo agente viene utilizzato da Azure Backup e ripristino dei siti di Azure). [Ulteriori] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per la distribuzione](site-recovery-best-practices.md)

