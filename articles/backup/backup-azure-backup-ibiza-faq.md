<properties
   pageTitle="Servizi di recupero archivio domande frequenti su | Microsoft Azure"
   description="Questa versione di domande frequenti supporta la versione di anteprima pubblico del servizio di Backup di Azure. Risposte alle domande frequenti sull'agente di backup, backup e criteri di conservazione, ripristino, sicurezza e altre domande comuni sulla soluzione Azure Backup."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="soluzione di backup. servizio di backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Archivio di servizi di recupero - domande frequenti


In questo articolo fornisce informazioni specifiche per l'archivio di servizi di recupero e integra le [Domande frequenti sulla copia di Backup di Azure](backup-azure-backup-faq.md). Domande frequenti Backup Azure fornisce l'insieme completo di domande e risposte relative al servizio di Backup di Azure.  

È possibile porre domande sui Backup di Azure nella sezione Disqus di questo articolo o un articolo correlato. È anche possibile pubblicare domande relative al servizio di Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Archivi di servizi di recupero sono Manager delle risorse in base a. Archivi di Backup (modalità classica) ancora supportati? <br/>
Sì, archivi di Backup sono ancora supportati. Creare archivi di Backup nel [portale classica](https://manage.windowsazure.com). Creare archivi di servizi di recupero nel [portale di Azure](https://portal.azure.com). Tuttavia si consiglia di creare un archivio di servizi di ripristino come tutti i miglioramenti futuri saranno disponibile solo nell'archivio di servizi di recupero.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>È possibile migrare un archivio di Backup in un archivio di servizi di ripristino? <br/>
Purtroppo no, al momento è possibile eseguire la migrazione del contenuto di un archivio di Backup in un archivio di servizi di recupero. Stiamo lavorando sull'aggiunta di questa funzionalità, ma non è disponibile come parte dell'anteprima pubblica.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Servizi di recupero archivi supportano classiche macchine virtuali o Manager delle risorse in base a macchine virtuali? <br/>
Archivi di servizi di recupero supportano entrambi i modelli.  È possibile eseguire il backup una macchina virtuale creata nel portale di classica (che sono macchine virtuali modalità classica) oppure una macchina virtuale creata nel portale di Azure, che sono Manager delle risorse in base a, in un archivio di servizi di recupero.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Ho backup personale classiche macchine virtuali nell'archivio di backup. A questo punto si desidera eseguire la migrazione personale macchine virtuali dalla modalità classica alla modalità di gestione risorse.  Come è eseguire il backup loro nell'archivio di servizi di recupero?
Backup di classiche macchine virtuali di archivio di backup non è possibile eseguire la migrazione automaticamente all'archivio di servizi di recupero durante la migrazione macchine virtuali da classica alla modalità di gestione risorse. Seguire questa procedura per la migrazione di backup di macchine Virtuali:

1. Nell'archivio di backup, passare alla scheda **Elementi protetti** e selezionare la macchina virtuale. Fare clic su [Interrompi la protezione](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Lasciare *eliminare i dati di backup associati* opzione **deselezionata**.
2. Eseguire la migrazione di macchina virtuale dalla modalità classica alla modalità di gestione risorse. Assicurarsi che lo spazio di archiviazione e di rete corrispondente alla macchina virtuale siano state migrate anche alla modalità di gestione risorse.
3. Creare un archivio di servizi di recupero e configurare il backup nel computer virtuale migrati tramite azione nella parte superiore del dashboard di archivio di **Backup** . Altre informazioni su come attivare [il backup di archivio di servizi di recupero](backup-azure-vms-first-look-arm.md)
