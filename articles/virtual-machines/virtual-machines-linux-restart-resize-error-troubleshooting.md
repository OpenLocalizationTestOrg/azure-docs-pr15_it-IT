<properties
   pageTitle="Macchine Virtuali riavviare o problemi di ridimensionamento | Microsoft Azure"
   description="Risoluzione dei problemi di distribuzione di Manager delle risorse con il riavvio o ridimensionamento di una macchina virtuale Linux esistente in Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Risoluzione dei problemi di distribuzione di Manager delle risorse con il riavvio o ridimensionamento di una macchina virtuale Linux esistente in Azure

Quando si tenta di avviare un ha smesso di Azure virtuale macchina o ridimensionare una macchina virtuale Azure esistente, l'errore comune che si verificherà un errore di allocazione. Questo errore verifica quando l'area geografica o il cluster non dispongono di risorse disponibili o non è in grado di supportare le dimensioni di macchine Virtuali richieste.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registri di controllo raccogliere

Per avviare la risoluzione dei problemi, raccogliere i registri di controllo per identificare l'errore associato il problema. I collegamenti seguenti contengono informazioni dettagliate sul processo di:

[Risoluzione dei problemi distribuzioni di gruppo di risorse con portale Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Controllare le operazioni con Gestione risorse](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Errore quando si avvia una macchina virtuale ha smesso di

Si prova ad avviare una macchina virtuale ha smesso di ma si riceve un errore di allocazione.

### <a name="cause"></a>Causa

La richiesta per avviare la macchina virtuale ha smesso di deve essere tentativi ripetuti cluster originale che ospita il servizio cloud. Tuttavia, il cluster non dispone di spazio libero disponibile per soddisfare la richiesta.

### <a name="resolution"></a>Risoluzione

*   Interrompere tutte le macchine virtuali nel set di disponibilità e quindi riavviare ogni macchina virtuale.

  1. Fare clic su **gruppi di risorse** > _il gruppo di risorse_ > **risorse** > _Imposta la propria disponibilità_ > **macchine virtuali** > _macchina virtuale_ > **interrompere**.

  2. Dopo tutte le macchine virtuali interrompere, selezionare ognuna delle macchine virtuali arrestate e fare clic su Start.

*   Ripetere la richiesta di riavvio in un secondo momento.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Errore durante il ridimensionamento di una macchina virtuale esistente

Si prova a ridimensionare una macchina virtuale esistente ma si riceve un errore di allocazione.

### <a name="cause"></a>Causa

La richiesta per ridimensionare la macchina virtuale deve essere tentativi ripetuti cluster originale che ospita il servizio cloud. Tuttavia, il cluster non supporta la dimensione di memoria virtuale richiesta.

### <a name="resolution"></a>Risoluzione

* Ripetere la richiesta tramite una versione ridotta macchine Virtuali.

* Se non è possibile modificare le dimensioni della macchina virtuale richiesta:

  1. Interrompere tutte le macchine virtuali nel set di disponibilità.

    * Fare clic su **gruppi di risorse** > _il gruppo di risorse_ > **risorse** > _Imposta la propria disponibilità_ > **macchine virtuali** > _macchina virtuale_ > **interrompere**.

  2. Dopo che tutte le macchine virtuali interrompere, ridimensionare macchine Virtuali desiderato di una dimensione maggiore.
  3. Selezionare la macchina virtuale più grande e fare clic su **Start**e quindi si avvia ognuna delle macchine virtuali ha smesso di.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi quando si crea una nuova VM Linux in Azure, vedere [risoluzione dei problemi di distribuzione con la creazione di una nuova macchina virtuale Linux in Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
