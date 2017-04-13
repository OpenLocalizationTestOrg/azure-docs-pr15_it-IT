<properties
    pageTitle="Creare un'immagine di macchine Virtuali da una macchina virtuale Azure | Microsoft Azure"
    description="Informazioni su come creare un'immagine di macchine Virtuali GRG da una macchina virtuale Azure esistente creato nel modello di distribuzione di Manager delle risorse"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Scaricare il modello per una macchina virtuale

Quando si crea una macchina virtuale in Azure tramite il portale e PowerShell, viene creato automaticamente un modello di Manager delle risorse. È possibile utilizzare questo modello per duplicare rapidamente una distribuzione. Il modello contiene informazioni su tutte le risorse in un gruppo di risorse. Per una macchina virtuale, significa che i contenitori di modello tutto ciò che viene creato per macchine Virtuali di tale gruppo di risorse, incluse le risorse di rete.

## <a name="download-the-template-using-the-portal"></a>Scaricare il modello tramite il portale

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Un menu hub selezionare **macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
5. Selezionare **script di automazione**.
6. Fare clic **su Download** e salvare il file con estensione zip nel computer locale.
7. Aprire il file ZIP ed estrarre i file in una cartella. Il file con estensione zip conterrà:
    
    - Deploy.ps1
    - Deploy.sh 
    - deployer.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - template.JSON

Il file .json è il modello.
    
## <a name="download-the-template-using-powershell"></a>Scaricare il modello di utilizzo di PowerShell

È anche possibile scaricare il file del modello .json utilizzando il cmdlet [AzureRMResourceGroup di esportazione](https://msdn.microsoft.com/library/mt715427.aspx) . È possibile utilizzare il `-path` parametro per fornire il nome del file e il percorso del file .json. In questo esempio viene illustrato come scaricare il modello per il gruppo di risorse denominato **myResourceGroup** nella cartella **C:\users\public\downloads** sul computer locale.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla distribuzione risorse uso dei modelli, vedere [procedura dettagliata modello Manager delle risorse](../resource-manager-template-walkthrough.md).