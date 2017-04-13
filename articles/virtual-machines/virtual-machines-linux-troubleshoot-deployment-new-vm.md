<properties
   pageTitle="Risolvere i problemi di distribuzione-RM Linux VM | Microsoft Azure"
   description="Risolvere i problemi relativi alla distribuzione di Manager delle risorse quando si crea una nuova macchina virtuale Linux in Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Risoluzione dei problemi di distribuzione di Manager delle risorse alla creazione di una nuova macchina virtuale Linux in Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Registri di controllo raccogliere

Per avviare la risoluzione dei problemi, raccogliere i registri di controllo per identificare l'errore associato il problema. I collegamenti seguenti includono informazioni dettagliate sul processo da seguire.

[Risolvere i problemi di distribuzioni di gruppo di risorse con portale Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Controllare le operazioni con Gestione risorse](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se il sistema operativo è Linux generalizzata e viene caricato e/o acquisita con l'impostazione GRG, quindi non sarà eventuali errori. Allo stesso modo, se il sistema operativo Linux specializzata, viene caricata e/o acquisita con l'impostazione specializzata, quindi non possono essere eventuali errori.

**Errori di caricamento:**

**N<sup>1</sup>:** Se il sistema operativo Linux generalizzata e viene caricato come specializzata, verrà visualizzato un errore di timeout provisioning perché la macchina virtuale è bloccata nella fase di provisioning.

**N<sup>2</sup>:** Se il sistema operativo Linux specializzate e viene caricato come generalized, verrà visualizzato un errore di provisioning perché la nuova macchina virtuale è in esecuzione con il nome del computer, il nome utente e la password originale.

**Risoluzione:**

Per risolvere entrambi questi errori, caricare il disco rigido virtuale originale, disponibile in locale, con la stessa impostazione come che per il sistema operativo (generalized/specializzati). Per caricare come generalized, è necessario ricordare di run - prima di tutto il deprovisioning.

**Acquisire errori:**

**N<sup>3</sup>:** Se il sistema operativo Linux generalizzata e viene indicato come specializzata, verrà visualizzato un errore di timeout provisioning perché la macchina virtuale originale non è utilizzabile in quanto è contrassegnato come generalizzato.

**N<sup>4</sup>:** Se il sistema operativo Linux specializzate e che viene acquisito come generalized, verrà visualizzato un errore di provisioning perché la nuova macchina virtuale è in esecuzione con il nome del computer, il nome utente e la password originale. Inoltre, originale macchine Virtuali non sono possibile utilizzare perché è contrassegnato come specializzati.

**Risoluzione:**

Per risolvere entrambi questi errori, eliminare l'immagine corrente dal portale e [acquisire da dischi rigidi virtuali di correnti](virtual-machines-linux-capture-image.md) con la stessa impostazione come che per il sistema operativo (generalized/specializzati).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizzata / raccolta / immagine marketplace. Errore di allocazione
Questo errore viene visualizzato nelle situazioni quando la nuova richiesta di macchine Virtuali viene aggiunta a un cluster che non supporta la dimensione di memoria virtuale richiesta o non dispone di spazio libero disponibile per la richiesta.

**Causare 1:** Il cluster non è in grado di supportare le dimensioni di macchine Virtuali richieste.

**Soluzione 1:**

- Ripetere la richiesta tramite una versione ridotta macchine Virtuali.
- Se non è possibile modificare le dimensioni della macchina virtuale richiesta:
  - Interrompere tutte le macchine virtuali nel set di disponibilità.
  Fare clic su **gruppi di risorse** > *il gruppo di risorse* > **risorse** > *Imposta la propria disponibilità* > **macchine virtuali** > *macchina virtuale* > **interrompere**.
  - Dopo tutte le macchine virtuali interrompere, creare la nuova macchina virtuale nelle dimensioni desiderate.
  - Iniziare la nuova macchina virtuale prima di tutto, quindi selezionare ognuna delle macchine virtuali ha smesso di e fare clic su **Start**.

**Causa 2:** Il cluster non dispone di risorse gratuite.

**Soluzione 2:**

- In un secondo momento, ripetere la richiesta.
- Se la nuova macchina virtuale può far parte di un set di disponibilità diverso
  - Creare una nuova macchina virtuale in una diversa disponibilità imposta (nella stessa regione).
  - Aggiungere la nuova macchina virtuale alla stessa rete virtuale.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi quando si avvia un'interruzione di Linux VM o ridimensionare un VM Linux esistente in Azure, vedere [Gestione risorse di risoluzione dei problemi di distribuzione con il riavvio o ridimensionamento di una macchina virtuale Linux esistente in Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
