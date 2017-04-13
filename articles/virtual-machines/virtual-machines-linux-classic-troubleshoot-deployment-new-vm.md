<properties
   pageTitle="Risolvere i problemi di classica di distribuzione Linux VM | Microsoft Azure"
   description="Risoluzione dei problemi di distribuzione classica quando si crea una nuova macchina virtuale Linux in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Risoluzione dei problemi di distribuzione classica alla creazione di una nuova macchina virtuale Linux in Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registri di controllo raccogliere

Per avviare la risoluzione dei problemi, raccogliere i registri di controllo per identificare l'errore associato il problema.

Nel portale di Azure, fare clic su **Sfoglia** > **macchine virtuali** > *la macchina virtuale di Windows* > **Impostazioni** > **i log di controllo**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se il sistema operativo è Linux generalizzata e viene caricato e/o acquisita con l'impostazione GRG, quindi non sarà eventuali errori. Allo stesso modo, se il sistema operativo Linux specializzata, viene caricata e/o acquisita con l'impostazione specializzata, quindi non possono essere eventuali errori.

**Errori di caricamento:**

**N<sup>1</sup>:** Se il sistema operativo Linux generalizzata e viene caricato come specializzata, verrà visualizzato un errore di timeout provisioning perché la macchina virtuale è bloccata nella fase di provisioning.

**N<sup>2</sup>:** Se il sistema operativo Linux specializzate e viene caricato come generalized, verrà visualizzato un errore di provisioning perché la nuova macchina virtuale è in esecuzione con il nome del computer, il nome utente e la password originale.

**Risoluzione:**

Per risolvere entrambi questi errori, caricare il disco rigido virtuale originale, disponibile in locale, con la stessa impostazione come che per il sistema operativo (generalized/specializzati). Per caricare come generalized, è necessario ricordare di run - prima di tutto il deprovisioning. Per ulteriori informazioni, vedere [creare e caricare un disco rigido virtuale che contiene il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md) .

**Acquisire errori:**

**N<sup>3</sup>:** Se il sistema operativo Linux generalizzata e viene indicato come specializzata, verrà visualizzato un errore di timeout provisioning perché la macchina virtuale originale non è utilizzabile in quanto è contrassegnato come generalizzato.

**N<sup>4</sup>:** Se il sistema operativo Linux specializzate e che viene acquisito come generalized, verrà visualizzato un errore di provisioning perché la nuova macchina virtuale è in esecuzione con il nome del computer, il nome utente e la password originale. Inoltre, originale macchine Virtuali non sono possibile utilizzare perché è contrassegnato come specializzati.

**Risoluzione:**

Per risolvere entrambi questi errori, eliminare l'immagine corrente dal portale e [acquisire da dischi rigidi virtuali di correnti](virtual-machines-linux-classic-capture-image.md) con la stessa impostazione come che per il sistema operativo (generalized/specializzati).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizzata / raccolta / immagine marketplace. Errore di allocazione
Questo errore viene visualizzato nelle situazioni quando la nuova richiesta di macchine Virtuali viene inviata a un cluster che non dispongono di spazio libero disponibile per la richiesta o non è in grado di supportare le dimensioni di macchine Virtuali richiesta. Non è possibile combinare diverse serie di macchine virtuali nel servizio cloud stesso. Pertanto se si desidera creare una nuova macchina virtuale di una dimensione diversa rispetto al servizio cloud supportino, la richiesta di elaborazione avrà esito negativo.

In base ai vincoli del servizio cloud che consente di creare la nuova macchina virtuale, possono verificarsi un errore causato da uno dei due situazioni.

**Causare 1:** Il servizio cloud è bloccato a uno specifico cluster oppure è collegato a un gruppo di affinità e pertanto aggiunte a un cluster specifico in base alla progettazione. Pertanto nuova risorsa di elaborazione le richieste in tale gruppo affinità sono provate diverse soluzioni dello stesso cluster in cui sono ospitate risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportano le dimensioni di macchine Virtuali richieste o dispone di spazio disponibile insufficiente, causando un errore di allocazione. Questo è valido se le nuove risorse vengono create tramite un nuovo servizio cloud o un servizio cloud esistente.

**Soluzione 1:**

- Creare un nuovo servizio cloud e associare a un'area o una rete virtuale basata sull'area.
- Creare una nuova macchina virtuale in nuovo servizio cloud.
  Se viene visualizzato un messaggio di errore quando si tenta di creare un nuovo servizio cloud, Riprova in un secondo momento o modificare l'area per il servizio cloud.

> [AZURE.IMPORTANT] Se si sta cercando di creare una nuova macchina virtuale in un servizio cloud esistente, ma non è stato e di creare un nuovo servizio cloud per la nuova macchina virtuale, è possibile consolidare tutti nelle macchine virtuali nel servizio cloud stesso. A tale scopo, eliminare macchine virtuali nel servizio cloud esistente e acquisire di nuovo dai loro dischi nel nuovo servizio cloud. Tuttavia, è importante tenere presente che il nuovo servizio cloud avrà un nuovo nome e indirizzo VIP, pertanto è necessario aggiornare i per tutte le dipendenze che fanno uso queste informazioni per il servizio cloud esistente.

**Causa 2:** Il servizio cloud è associato a una rete virtuale è collegata a un gruppo di affinità, pertanto è bloccato a uno specifico cluster in base alla progettazione. Nuova risorsa di elaborazione tutte le richieste in tale gruppo affinità pertanto sono provate diverse soluzioni dello stesso cluster in cui sono ospitate risorse esistenti. Tuttavia, lo stesso cluster potrebbe non supportano le dimensioni di macchine Virtuali richieste o dispone di spazio disponibile insufficiente, causando un errore di allocazione. Questo è valido se le nuove risorse vengono create tramite un nuovo servizio cloud o un servizio cloud esistente.

**Soluzione 2:**

- Creare una nuova rete virtuale internazionali.
- Creare la nuova macchina virtuale nella nuova rete virtuale.
- [Connettere la rete virtuale esistente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) per la nuova rete virtuale. Vedere ulteriori informazioni su [reti virtuali internazionali](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). In alternativa, è possibile [eseguire la migrazione della rete virtuale basato su gruppo affinità a una rete locale](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e quindi si crea nuova macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi quando si avvia un'interruzione di Linux VM o ridimensionare un VM Linux esistente in Azure, vedere [risoluzione dei problemi di distribuzione classica con il riavvio o ridimensionamento di una macchina virtuale Linux esistente in Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md).
