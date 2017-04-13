<properties
   pageTitle="Macchine Virtuali riavviare o problemi di ridimensionamento | Microsoft Azure"
   description="Risoluzione dei problemi di distribuzione classica con il riavvio o ridimensionamento di una macchina virtuale di Windows esistente in Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Risoluzione dei problemi di distribuzione classica con il riavvio di o ridimensionamento di una macchina virtuale di Windows esistente in Azure

> [AZURE.SELECTOR]
- [Classica](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [Manager delle risorse](../../virtual-machines-windows-restart-resize-error-troubleshooting.md)

Quando si tenta di avviare un ha smesso di Azure virtuale macchina o ridimensionare una macchina virtuale Azure esistente, l'errore comune che si verificherà un errore di allocazione. Questo errore verifica quando l'area geografica o il cluster non dispongono di risorse disponibili o non è in grado di supportare le dimensioni di macchine Virtuali richieste.
> [AZURE.IMPORTANT] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../../../resource-manager-deployment-model.md).  In questo articolo è descritta utilizzando il modello di distribuzione classica. Si consiglia di più nuove distribuzioni di utilizzare il modello di Manager delle risorse.

[AZURE.INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registri di controllo raccogliere

Per avviare la risoluzione dei problemi, raccogliere i registri di controllo per identificare l'errore associato il problema.

Nel portale di Azure, fare clic su **Sfoglia** > **macchine virtuali** > _la macchina virtuale di Windows_ > **Impostazioni** > **i log di controllo**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Errore quando si avvia una macchina virtuale ha smesso di

Si prova ad avviare una macchina virtuale ha smesso di ma si riceve un errore di allocazione.

### <a name="cause"></a>Causa

La richiesta per avviare la macchina virtuale ha smesso di deve essere tentativi ripetuti cluster originale che ospita il servizio cloud. Tuttavia, il cluster non dispone di spazio libero disponibile per soddisfare la richiesta.

### <a name="resolution"></a>Risoluzione

* Creare un nuovo servizio cloud e associare con un'area o una rete virtuale basata sull'area, ma non un gruppo di affinità.

* Eliminare la macchina virtuale ha smesso di.

* Ricreare la macchina virtuale nel nuovo servizio cloud utilizzando i dischi.

* Avviare la macchina virtuale ricreata.

Se viene visualizzato un messaggio di errore quando si tenta di creare un nuovo servizio cloud, Riprova in un secondo momento o modificare l'area per il servizio cloud.

> [AZURE.IMPORTANT] Il nuovo servizio cloud avrà un nuovo nome e indirizzo VIP, in modo che sarà necessario modificare le informazioni per tutte le dipendenze che utilizzano tali informazioni per il servizio cloud esistente.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Errore durante il ridimensionamento di una macchina virtuale esistente

Si prova a ridimensionare una macchina virtuale esistente ma si riceve un errore di allocazione.

### <a name="cause"></a>Causa

La richiesta per ridimensionare la macchina virtuale deve essere tentativi ripetuti cluster originale che ospita il servizio cloud. Tuttavia, il cluster non supporta la dimensione di memoria virtuale richiesta.

### <a name="resolution"></a>Risoluzione

Ridurre le dimensioni di macchine Virtuali richieste, quindi riprovare la richiesta di ridimensionamento.

* Fare clic su **Sfoglia tutti** > **(classica) macchine virtuali di** > _macchina virtuale_ > **Impostazioni** > **dimensioni**. Per informazioni dettagliate, vedere [ridimensionare la macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).

Se non è possibile ridurre le dimensioni di macchine Virtuali, procedere come segue:

  * Creare un nuovo servizio cloud, garantendo non è collegato a un gruppo di affinità e non associato a una rete virtuale è collegata a un gruppo di affinità.

  * Creare una macchina virtuale nuova, ingrandita.

È possibile consolidare tutte le macchine virtuali nel servizio cloud stesso. Se non è associato a una rete virtuale basata sull'area del servizio cloud esistente, è possibile connettersi al nuovo servizio cloud alla rete virtuale esistente.

Se il servizio cloud esistente non è associato a una rete virtuale basata sull'area, è necessario eliminare macchine virtuali nel servizio cloud esistente e ricreare nel nuovo servizio cloud dai loro dischi. Tuttavia, è importante tenere presente che il nuovo servizio cloud avrà un nuovo nome e indirizzo VIP, pertanto è necessario aggiornare i per tutte le dipendenze che fanno uso queste informazioni per il servizio cloud esistente.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi quando si crea una nuova macchina virtuale di Windows in Azure, vedere [risoluzione dei problemi di distribuzione con la creazione di una nuova macchina virtuale di Windows in Azure](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md).
