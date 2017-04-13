<properties
    pageTitle="Domande frequenti su Azure Stack | Microsoft Azure"
    description="Pila di Azure domande frequenti."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Domande frequenti su Stack di Azure

## <a name="deployment"></a>Distribuzione

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>È necessario formattare i dischi dati prima di avviare o riavviare l'installazione?

Dischi devono essere in formato non elaborato. Se la reinstallazione del sistema operativo, potrebbe essere necessario verificare se il vecchio pool di archiviazione è ancora presenta ed eliminare procedendo come segue:

1. Aprire Server Manager.
2. Selezionare il pool di archiviazione.
3. Verificare se un pool di archiviazione è elencato.
4. Se nell'elenco e abilitare in lettura / scrittura, fare clic su **pool di archiviazione** .
5. Destro del mouse sul **disco rigido virtuale** (angolo inferiore sinistro) e selezionare Elimina.
6. Il pulsante destro **Del Pool di archiviazione** e fare clic su Elimina.
7. Avviare nuovamente script dello Stack di Azure e verificare che la verifica del disco passa.

Facoltativamente, è possibile utilizzare lo script seguente:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>È possibile utilizzare tutti i dischi SSD il pool di archiviazione nell'installazione di prova?

Questa configurazione non è supportata in questa versione.  Per ulteriori informazioni, vedere la [Guida di requisiti](azure-stack-deploy.md) per altre informazioni.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>È possibile usare dischi dati NVMe per la prova pratica dello Stack di Microsoft Azure?

Mentre lo spazio di archiviazione spazi diretto supporta dischi NVMe, Stack di Azure supporta solo un sottoinsieme dei tipi di unità e le possibili combinazioni per lo spazio di archiviazione spazi diretto. 

### <a name="how-can-i-reinstall-azure-stack"></a>Come è possibile reinstallare dello Stack di Azure?
È possibile eseguire i passaggi indicati nella [Guida per la ridistribuzione](azure-stack-redeploy.md).  

## <a name="tenant"></a>Tenant

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>È possibile distribuire il proprio immagini come un tenant?

Sì, proprio come in Azure, un tenant può caricare le immagini in pila di Azure, oltre a utilizzare le immagini all'amministratore del servizio. Per informazioni generali, vedere [aggiunta di un'immagine di macchine Virtuali](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Test

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>È possibile utilizzare virtualizzazione annidate per testare la prova pratica dello Stack di Microsoft Azure?

Virtualizzazione annidata non è supportata né testata con Azure Stack Technical Preview 2.

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Pila di Azure supporta dischi dinamici per macchine virtuali?

Pila di Azure non supporta dischi dinamici.

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi](azure-stack-troubleshooting.md)
