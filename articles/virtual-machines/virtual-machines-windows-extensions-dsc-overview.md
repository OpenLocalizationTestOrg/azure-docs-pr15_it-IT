<properties
   pageTitle="Bene accolta configurazione dello stato per Panoramica Azure | Microsoft Azure"
   description="Panoramica per l'utilizzo del gestore di estensione Microsoft Azure per configurazione dello stato bene accolta PowerShell. Tra i prerequisiti, architettura, i cmdlet.."
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

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduzione al gestore estensione configurazione dello stato bene accolta Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L'agente di macchine Virtuali di Azure e le estensioni associate fanno parte di Microsoft Azure infrastruttura Services. Estensioni macchine Virtuali sono componenti di software che estendono le funzionalità di macchine Virtuali e semplificano le operazioni di gestione delle varie macchine Virtuali. Ad esempio, l'estensione VMAccess può essere usata per reimpostare la password dell'amministratore oppure l'estensione di Script personalizzato può essere utilizzato per eseguire uno script sul macchina virtuale.

In questo articolo vengono presentati l'estensione di PowerShell bene accolta stato configurazione (DSC) per macchine virtuali di Azure come parte di Azure PowerShell SDK. È possibile utilizzare nuovi cmdlet per caricare e applicare una configurazione DSC PowerShell in una macchina virtuale Azure abilitato con estensione DSC PowerShell. DSC PowerShell estensione chiamate in DSC PowerShell per attivare la configurazione di DSC ricevuta nella macchina virtuale. Questa funzionalità è disponibile anche tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti di ##
**Computer locale** Per interagire con l'estensione macchine Virtuali di Azure, è necessario usare il portale di Azure o Azure PowerShell SDK. 

**Agente di guest** La macchina virtuale Azure configurate dalla configurazione DSC deve essere un sistema operativo che supporta Windows Management Framework (WMF) 4.0 o 5.0. L'elenco completo delle versioni del sistema operativo supportate sono disponibili la [Cronologia delle versioni estensione DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termini e dei concetti ##
Questa guida presuppone familiarità con i concetti seguenti:

Configurazione: un documento di configurazione DSC. 

Nodo: una destinazione per una configurazione DSC. In questo documento "nodo" fa sempre riferimento a una macchina virtuale Azure.

Dati di configurazione - un .psd1 file contenente dati dell'ambiente per una configurazione

## <a name="architectural-overview"></a>Cenni preliminari ##

L'estensione di Azure DSC utilizza framework agente macchine Virtuali di Azure recapitare, seguire e creare un report sul configurazioni DSC in esecuzione in macchine virtuali di Azure. L'estensione DSC prevede un file ZIP che contiene almeno un documento di configurazione e un set di parametri specificato Azure PowerShell SDK o mediante il portale di Azure.

Quando si chiama l'estensione per la prima volta, viene eseguito il processo di installazione. Questo processo viene installata una versione di Windows Management Framework (WMF) utilizzando la logica seguente:

1. Se il sistema operativo macchine Virtuali di Azure Windows Server 2016, viene eseguita alcuna azione. Windows Server 2016 con la versione più recente di PowerShell installato.
2. Se il `wmfVersion` proprietà specificata, tale versione del file WMF è installato a meno che non è compatibile con sistema operativo della macchina virtuale.
3. Se non `wmfVersion` proprietà specificata, è installata la versione più recente applicabile del file WMF.

Installazione di file WMF richiede il riavvio. Dopo il riavvio, l'estensione download di file con estensione zip specificato nella finestra di `modulesUrl` proprietà. Se questo percorso di archiviazione blob Azure, è possibile specificare un token di SA nel `sasToken` proprietà per accedere al file. Dopo la ZIP il download e decompresso, la funzione di configurazione definita in `configurationFunction` viene eseguita per generare il file MOF. L'estensione viene eseguita quindi `Start-DscConfiguration -Force` nel file MOF generato. L'estensione acquisisce output e scrive nuovamente per il canale di stato di Azure. Da questo momento, MCM DSC gestisce il monitoraggio e correzione come di consueto. 

## <a name="powershell-cmdlets"></a>Cmdlet di PowerShell ##

Cmdlet di PowerShell utilizzabili con ARM o ASM per creare un pacchetto, pubblicare e monitorare le distribuzioni di estensione DSC. I cmdlet seguenti elencati sono i moduli ASM, ma può essere sostituito "Azure" con "AzureRm" come utilizzare il modello ARM. Ad esempio `Publish-AzureVMDscConfiguration` utilizza ASM, in cui `Publish-AzureRmVMDscConfiguration` utilizza ARM. 

`Publish-AzureVMDscConfiguration`accetta in un file di configurazione, ricerca le risorse DSC dipendenti e crea un file zip contenente la configurazione e le risorse DSC necessarie per attivare la configurazione. Anche possibile creare il pacchetto localmente utilizzando la `-ConfigurationArchivePath` parametro. In caso contrario, pubblica il file con estensione zip a archiviazione blob Azure e sicura con un token di SA.

Il file con estensione ZIP creato da questo cmdlet ha lo script di configurazione ps1 nella radice della cartella di archiviazione. Risorse per la cartella di modulo nella cartella di archiviazione. 

`Set-AzureVMDscExtension`Inserisce le impostazioni necessarie dall'estensione DSC PowerShell in un oggetto di configurazione macchine Virtuali, quindi può essere applicato a una macchina virtuale Azure con `Update-AzureVM`.

`Get-AzureVMDscExtension`Recupera lo stato di estensione DSC di una macchina virtuale specifico. 

`Get-AzureVMDscExtensionStatus`Recupera lo stato della configurazione DSC all'azione per il gestore di estensione DSC. Questa azione può essere eseguita in un singolo macchine Virtuali o un gruppo di macchine virtuali.

`Remove-AzureVMDscExtension`Rimuove il gestore di estensione da una macchina virtuale specificata. Questo cmdlet **non** rimuove la configurazione, disinstallare file WMF o modificare le impostazioni applicate nella macchina virtuale. Rimuove solo il gestore di estensione. 

**Principali differenze tra i cmdlet ASM e ARM**

- Cmdlet ARM sono icona del. Cmdlet ASM sono asincrone.
- ResourceGroupName, VMName, ArchiveStorageAccountName, versione e posizione sono tutti i nuovi parametri necessari.
- ArchiveResourceGroupName è un nuovo parametro facoltativo per ARM. È possibile specificare questo parametro quando l'account di archiviazione appartiene a un gruppo di risorse diversa da quella in cui viene creata la macchina virtuale.
- ConfigurationArchive è chiamato ArchiveBlobName in ARM
- Nomecontenitore è chiamato ArchiveContainerName in ARM
- StorageEndpointSuffix è chiamato ArchiveStorageEndpointSuffix in ARM
- L'opzione di aggiornamento automatico è stata aggiunta a ARM per abilitare gli aggiornamenti automatici del gestore estensione per la versione più recente come e quando è disponibile. Questo parametro è in grado di causare noti riavvia nella macchina virtuale quando viene rilasciata una nuova versione del file WMF. 


## <a name="azure-portal-functionality"></a>Funzionalità portale Azure ##
Passare a una macchina virtuale classica. In impostazioni-di > fare clic su generale di "Estensioni." Viene creato un nuovo riquadro. Fare clic su "Aggiungere" e selezionare DSC PowerShell.

Il portale necessita di input.
**Moduli per la configurazione o Script**: questo campo è obbligatorio. Richiede un file ps1 contenente uno script di configurazione o un file ZIP con uno script di configurazione ps1 radice e tutte le risorse dipendenti nelle cartelle di modulo nello zip. Possono essere creata con la `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet incluse in Azure PowerShell SDK. Il file con estensione ZIP viene caricato nello spazio di archiviazione blob utente protetto da un token di SA. 

**File di configurazione dati PSD1**: questo campo è facoltativo. Se la configurazione richiede un file di dati di configurazione in .psd1, utilizzare questo campo per selezionarlo e caricare allo spazio di archiviazione blob utente, in cui è protetto da un token di SA. 
 
**Configurazione del nome di Module-Qualified**: file ps1 possono avere più funzioni di configurazione. Immettere il nome dello script ps1 configurazione seguito da un '\' e il nome della funzione di configurazione. Ad esempio, se lo script ps1 ha il nome "configuration.ps1" e la configurazione è "IisInstall", immettere:`configuration.ps1\IisInstall`

**Gli argomenti di configurazione**: se la funzione di configurazione accetta argomenti, immetterli qui nel formato `argumentName1=value1,argumentName2=value2`. Nota che questo formato è un formato diverso da come argomenti di configurazione vengono accettati tramite i cmdlet di PowerShell o modelli Manager delle risorse. 

## <a name="getting-started"></a>Guida introduttiva ##

L'estensione di Azure DSC accetta nei documenti di configurazione DSC e attivarne loro in macchine virtuali di Azure. Un semplice esempio di una configurazione segue. Salvarlo localmente come "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

La procedura seguente inserisce lo script IisInstall.ps1 nella macchina virtuale specificata, esegue la configurazione e rimandato allo stato di avanzamento.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Registrazione ##

Vengono inseriti:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[numero di versione]

## <a name="next-steps"></a>Passaggi successivi ##

Per ulteriori informazioni su DSC PowerShell, [visitare l'area documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Esaminare il [modello di gestione risorse di Azure per l'estensione DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Per trovare altre funzionalità è possibile gestire con PowerShell DSC, [Selezionare la raccolta di PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) per altre risorse DSC.

Per informazioni dettagliate sul passaggio parametri riservati in configurazioni, vedere [gestire le credenziali in modo sicuro con il gestore di estensione DSC](virtual-machines-windows-extensions-dsc-credentials.md).
