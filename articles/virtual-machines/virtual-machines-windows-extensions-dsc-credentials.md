<properties
   pageTitle="Passaggio di credenziali Azure con DSC | Microsoft Azure"
   description="Panoramica sul passaggio in modo sicuro le credenziali in macchine virtuali Azure utilizzando Configurazione dello stato bene accolta PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Il passaggio delle credenziali per il gestore di estensione DSC Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

In questo articolo è descritta l'estensione bene accolta configurazione dello stato per Azure. Una panoramica del gestore estensione DSC sono disponibili in [Introduzione al gestore estensione configurazione dello stato bene accolta Azure](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Passaggio di credenziali
Come parte del processo di configurazione, è possibile necessarie per configurare l'account utente, accedere ai servizi oppure installare un programma in un contesto utente. Per eseguire queste operazioni, è necessario specificare le credenziali. 

DSC consente per le configurazioni parametri in cui le credenziali sono passate nella configurazione e sicuro archiviate in file MOF. Il gestore di estensione Azure semplifica la gestione delle credenziali mediante la gestione automatica dei certificati. 

Considerare il seguente script di configurazione DSC che consente di creare un account utente locali con la password specificata:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

È importante includere *host locale nodo* come parte della configurazione. Se questa istruzione non è presente, la procedura seguente non funzionano come il gestore di estensione specificamente consente di cercare l'istruzione host locale nodo. È anche importante includere il cast di tipo *[PsCredential]*, in quanto questo tipo specifico di attiva l'estensione per crittografare le credenziali. 

Pubblicare questo script a archiviazione blob:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Impostare l'estensione di Azure DSC e specificare le credenziali:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Come vengono protetti credenziali
Eseguire il codice richiede le credenziali. Una volta fornita, questa viene archiviata in memoria brevemente. Quando viene pubblicato con `Set-AzureVmDscExtension` cmdlet, viene trasmesso HTTPS per la macchina virtuale, dove Azure memorizza crittografate su disco, utilizzando il certificato di macchine Virtuali locale. Quindi è brevemente decrittografare in memoria e nuovamente crittografato per passare a DSC.

Questo comportamento risulta diverso rispetto [all'utilizzo di configurazioni protette senza il gestore di estensione](https://msdn.microsoft.com/powershell/dsc/securemof). Ambiente di Azure offre un modo per la trasmissione dei dati di configurazione in modo sicuro tramite certificati. Quando si utilizza il gestore di estensione DSC, non è necessario specificare $CertificatePath o un $CertificateID / $Thumbprint voce ConfigurationData.


## <a name="next-steps"></a>Passaggi successivi ##

Per ulteriori informazioni sul gestore estensione DSC Azure, vedere [Introduzione al gestore estensione configurazione dello stato bene accolta Azure](virtual-machines-windows-extensions-dsc-overview.md). 

Esaminare il [modello di gestione risorse di Azure per l'estensione DSC](virtual-machines-windows-extensions-dsc-template.md).

Per ulteriori informazioni su DSC PowerShell, [visitare l'area documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Per trovare altre funzionalità è possibile gestire con PowerShell DSC, [Selezionare la raccolta di PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) per altre risorse DSC.
