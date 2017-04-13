<properties
   pageTitle="Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace | Microsoft Azure"
   description="Comprendere i requisiti per la creazione e distribuzione di un'immagine di macchina virtuale di Azure Marketplace per consentire ad altri per l'acquisto."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace
Accuratamente prima di iniziare il processo di leggere e comprendere dove e perché venga eseguita ogni passaggio. Il più possibile, si deve preparare le informazioni sulla società e altri dati, scaricare gli strumenti necessari e/o creare componenti tecnici prima di iniziare il processo di creazione di offerta. Questi elementi devono essere Cancella da revisione in questo articolo.  

## <a name="download-needed-tools--applications"></a>Scaricare gli strumenti necessari e applicazioni
È necessario disporre gli elementi seguenti pronti prima di iniziare il processo:

- A seconda del sistema operativo in uso, installare il [cmdlet di PowerShell Azure](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) o [Linux interfaccia riga di comando strumento](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) dalla pagina [Download Azure](https://azure.microsoft.com/downloads/) .
- Installare lo spazio di archiviazione Azure Explorer da CodePlex.
- Scaricare e installare lo strumento Test di certificazione per Azure Certified:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). È necessario un computer basato su Windows per eseguire lo strumento di certificazione. Se non si dispone di un computer basato su Windows disponibile, è possibile eseguire lo strumento utilizzando una macchina virtuale basato su Windows Azure.

## <a name="platforms-supported"></a>Piattaforme supportate
È possibile sviluppare basate su Azure macchine virtuali su Windows o Linux. Alcuni elementi del processo di pubblicazione, ad esempio la creazione di un compatibile con Azure disco rigido virtuale (disco rigido virtuale), utilizzare diversi strumenti e i passaggi a seconda del sistema operativo in uso:  

- Se si utilizza Linux, consultare la sezione "Creare un disco rigido virtuale compatibile con Azure (basato su Linux)" della [Guida alla pubblicazione di macchina virtuale immagine](marketplace-publishing-vm-image-creation.md).
- Se si utilizza Windows, consultare la sezione "Creare un disco rigido virtuale compatibile con Azure (basato su Windows)" della [Guida alla pubblicazione di macchina virtuale immagine](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] È necessario accedere a un computer basato su Windows per:
- Eseguire lo strumento di convalida di certificazione.
- Creare l'URL di firma disco rigido virtuale condiviso accesso per la presentazione di certificazione disco rigido virtuale.

## <a name="develop-your-vhd"></a>Sviluppare il disco rigido virtuale
È possibile sviluppare dischi rigidi virtuali Azure nel cloud o locale:

- Sviluppo basato su cloud indica che tutte le fasi di sviluppo vengono eseguite in modalità remota su un disco rigido virtuale residente in Azure.
- Sviluppo locale è necessario scaricare un disco rigido virtuale e lo sviluppo utilizzando infrastruttura locale. Sebbene sia possibile, non è consigliabile. Si noti che sviluppano applicazioni per Windows o SQL locale è necessario disporre le chiavi di licenza locali pertinenti. Non è possibile includere o installare SQL Server dopo aver creato una macchina virtuale. È anche necessario basare l'offerta in un'immagine SQL approvata dal portale di Azure. Se si decide di sviluppare locale, è necessario eseguire alcuni passaggi in modo diverso da se sviluppano nel cloud. Si possono trovare informazioni in [creare un'immagine di macchine Virtuali locale](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Passaggi successivi
Ora che si esaminato i prerequisiti e completare le attività necessarie, è possibile spostare in avanti alla creazione dell'offerta di immagine macchina virtuale come descritto nella [Guida alla pubblicazione di macchina virtuale immagine](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creare una macchina virtuale che esegue Windows nel portale di anteprima Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
