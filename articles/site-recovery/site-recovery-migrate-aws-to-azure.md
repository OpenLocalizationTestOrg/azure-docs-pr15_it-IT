<properties
    pageTitle="Eseguire la migrazione di macchine virtuali di Windows da servizi Web di Amazon in Azure con il ripristino del sito | Microsoft Azure"
    description="In questo articolo viene descritto come eseguire la migrazione di macchine virtuali di Windows in esecuzione in Amazon Web Services (AWA) in Azure utilizzando il ripristino del sito di Azure."
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
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Eseguire la migrazione di macchine virtuali di Windows in servizi Web di Amazon (AWS) in Azure con il ripristino del sito di Azure

## <a name="overview"></a>Panoramica

Introduzione al ripristino del sito Azure. In questo articolo consente di eseguire la migrazione di istanze di Windows in esecuzione in AWS in Azure con il ripristino del sito. Prima di iniziare, tenere presente che:

- Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione. La procedura di base per la migrazione è gli stessi se si sta configurando il ripristino del sito in Gestione risorse o classica. Tuttavia le istruzioni di interfaccia utente e le schermate contenute in questo articolo sono rilevanti per il portale di Azure.
- **Attualmente è possibile solo eseguire la migrazione da AWS in Azure. Può avere esito negativo su macchine virtuali da AWS in Azure, ma non si non riesce loro tornare nuovamente. Non esiste alcun replica in corso.**
- Le istruzioni di migrazione in questo articolo si basano le istruzioni per la replica di un computer fisico in Azure. Include collegamenti per la procedura descritta in [macchine virtuali VMware replicare o server fisici in Azure](site-recovery-vmware-to-azure.md), che descrive come replicare un server fisico nel portale di Azure.
- Se sta impostando il ripristino del sito nel portale di classico, seguire le istruzioni dettagliate in [questo articolo](site-recovery-vmware-to-azure-classic.md). **Non è necessario utilizzare** le istruzioni fornite in questo [articolo legacy](site-recovery-vmware-to-azure-classic-legacy.md).

Registrare commenti o domande nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Prerequisiti

Ecco cosa occorre per la distribuzione

- **Server di configurazione**: una macchina virtuale locale che eseguono Windows Server 2012 R2 che funge dal server di configurazione. Si installa anche altri componenti il ripristino del sito (incluso il processo server destinazione master) in questa macchina virtuale. Per ulteriori [architettura scenario](site-recovery-vmware-to-azure.md#scenario-architecture) e [i prerequisiti di configurazione server](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Le istanze di macchine Virtuali EC2**: le istanze in esecuzione Windows si vuole eseguire la migrazione.

## <a name="deployment-steps"></a>Passaggi di distribuzione

In questa sezione vengono descritti i passaggi di distribuzione nel portale di Azure nuovo. Se è necessario questi passaggi di distribuzione per il ripristino del sito nel portale di classica, fare riferimento al [presente articolo](site-recovery-vmware-to-azure-classic.md).

1. [Creare un archivio](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Distribuire un server di configurazione](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Dopo aver distribuito il server di configurazione, verificare che è possibile comunicare con macchine virtuali che si desidera eseguire la migrazione.
4. [Configurare le impostazioni di replica](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Creare un criterio di replica e assegnare al server di configurazione.
5. [Installare il servizio di mobilità](site-recovery-vmware-to-azure.md#step-6-replication-application). Ogni macchina virtuale che si desidera proteggere deve installato il servizio di mobilità. Il servizio invia dati al server di processo. Il servizio mobilità può essere installato manualmente o inserito e installato automaticamente dal server di processo quando è abilitata la protezione per la macchina virtuale. Le regole firewall su istanze EC2 che si desidera eseguire la migrazione devono essere configurate per consentire l'installazione push del servizio. Il gruppo di sicurezza per le istanze di EC2 deve avere le regole seguenti:

    ![regole firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Abilitare la replica](site-recovery-vmware-to-azure.md#enable-replication). Abilitare la replica per macchine virtuali di cui eseguire la migrazione. È possibile scoprire le varianti di EC2 utilizzando gli indirizzi IP privati, è possibile ottenere dalla console di EC2.
7. [Eseguire un failover non pianificato](site-recovery-failover.md#run-an-unplanned-failover). Una volta completata la replica iniziale, è possibile eseguire un failover non pianificato da AWS in Azure per ogni macchina virtuale. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato per eseguire la migrazione di più macchine virtuali da AWS in Azure. [Altre informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su altri scenari di replica in [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)
