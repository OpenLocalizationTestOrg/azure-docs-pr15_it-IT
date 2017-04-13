<properties
    pageTitle="Informazioni sulle immagini per macchine virtuali di Windows | Microsoft Azure"
    description="Informazioni sull'utilizzo delle immagini con macchine virtuali di Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>Informazioni sulle immagini per macchine virtuali di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Utilizzo delle immagini

È possibile utilizzare il modulo di Azure PowerShell per gestire le immagini disponibili per l'abbonamento Azure. È inoltre possibile utilizzare il portale classico Azure per alcune attività di immagine, ma la riga di comando fornisce più opzioni.


-   **Ottenere tutte le immagini**:`Get-AzureVMImage`restituisce un elenco di tutte le immagini disponibili in abbonamento corrente: le immagini, nonché quelli forniti da Azure o partner. Di conseguenza, che è possibile che venga visualizzato un elenco di grandi dimensioni. Negli esempi successivi viene illustrato come ottenere un elenco più breve.
-   **Ottenere le famiglie di immagine**:`Get-AzureVMImage | select ImageFamily` Ottiene un elenco delle famiglie di immagine mostrando stringhe **ImageFamily** proprietà.
-   **Ottenere tutte le immagini in un gruppo specifico**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Trovare immagini di macchine Virtuali**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` funziona filtrando la proprietà DataDiskConfiguration, si applica solo alle immagini macchine Virtuali. In questo esempio viene inoltre l'output viene filtrato solo il nome di etichetta e immagine.
-   **Salvare un'immagine GRG**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Salvare un'immagine specifico**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Il parametro OSState è necessario se si desidera creare un'immagine macchine Virtuali, che include i dischi dei dati, nonché il disco di sistema operativo. Se non si usa il parametro, il cmdlet crea un'immagine del sistema operativo. Il valore del parametro indica se l'immagine generalized o specializzata, in base a se il disco di sistema operativo è stato preparato per il riutilizzo.
-   **Eliminare un'immagine**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Passaggi successivi

È anche possibile [creare un computer Windows tramite il portale classico](virtual-machines-windows-classic-tutorial.md)

