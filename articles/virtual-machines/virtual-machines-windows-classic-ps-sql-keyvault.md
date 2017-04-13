<properties
    pageTitle="Configurare l'integrazione di Azure archivio chiave per SQL Server in macchine virtuali Azure (classica)"
    description="Informazioni su come automatizzare la configurazione di crittografia di SQL Server per l'utilizzo con Azure chiave archivio. In questo argomento viene illustrato come utilizzare l'integrazione di archivio di Azure chiave con SQL Server macchine virtuali di creare nel modello di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurare l'integrazione di Azure archivio chiave per SQL Server in macchine virtuali Azure (classica)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-ps-sql-keyvault.md)
- [Classica](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Panoramica
Sono disponibili più funzioni di crittografia di SQL Server, ad esempio [la crittografia dei dati trasparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (Cancella)](https://msdn.microsoft.com/library/ms173744.aspx)e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Questi tipi di crittografia è necessario gestire e memorizzare la crittografia che utilizzare per la crittografia. Il servizio di Azure chiave archivio (AKV) è progettato per migliorare la sicurezza e la gestione di questi tasti in un percorso sicuro e disponibilità. Il [Connettore di SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server per utilizzare questi tasti dall'archivio chiave Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Se si esegue SQL Server con computer locale, sono disponibili [procedure per accedere a Azure chiave archivio dal computer locale di SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ma per SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo utilizzando la funzionalità di *Integrazione di archivio di Azure chiave* . Con alcuni dei cmdlet di PowerShell Azure per abilitare questa funzionalità, è possibile automatizzare la configurazione necessaria per una VM SQL accedere all'archivio chiave.

Quando è attivata, automaticamente installa il connettore di SQL Server, consente di configurare il provider EKM per accedere a Azure chiave archivio e crea le credenziali per consentire l'insieme di credenziali di accesso. Se attiva la procedura descritta nella documentazione locale descritto in precedenza, è possibile vedere questa caratteristica consente di automatizzare i passaggi 2 e 3. L'unica che è comunque necessario eseguire manualmente consiste nel creare l'archivio chiave e chiavi. A questo punto, l'intera impostazione le VM SQL è automatica. Quando questa funzionalità viene completato il programma di installazione, è possibile eseguire istruzioni Transact-SQL per iniziare la crittografia del database o backup come di consueto.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurare l'integrazione di AKV
Usare PowerShell per configurare l'integrazione di archivio di Azure chiave. Nelle sezioni seguenti offrono una panoramica dei parametri obbligatori e quindi uno script di PowerShell di esempio.

### <a name="install-the-sql-server-iaas-extension"></a>Installare l'estensione IaaS SQL Server

Prima di tutto, [installare l'estensione IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprendere i parametri di input
La tabella seguente elenca i parametri necessari per eseguire lo script di PowerShell nella sezione successiva.

|Parametro|Descrizione|Esempio|
|---|---|---|
|**$akvURL**|**L'URL di archivio chiave**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nome dell'entità servizio**|"5-4e11-af04eb07b669ccf2 fde2b411 - 33d"|
|**$spSecret**|**Segreto principale del servizio**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM ="|
|**$credName**|**Nome delle credenziali**: integrazione AKV crea una credenziale all'interno di SQL Server, consentendo macchine Virtuali di accedere all'archivio chiave. Scegliere un nome per queste credenziali.|"mycred1"|
|**$vmName**|**Nome macchina virtuale**: il nome di una VM SQL creata in precedenza.|"myvmname"|
|**$serviceName**|**Nome del servizio**: nome nel servizio Cloud associato VM SQL.|"mycloudservicename"|

### <a name="enable-akv-integration-with-powershell"></a>Attivare l'integrazione di AKV con PowerShell
Il cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un oggetto di configurazione per la funzionalità di integrazione di archivio di Azure chiave. **Set AzureVMSqlServerExtension** configura l'integrazione con il parametro **KeyVaultCredentialSettings** . La procedura seguente viene illustrato come utilizzare questi comandi.

1. PowerShell di Azure, prima di tutto configurare i parametri di input con i valori specifici come descritto nelle sezioni precedenti di questo argomento. Lo script seguente è illustrato un esempio.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Usare quindi il seguente script per configurare e attivare l'integrazione con AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L'estensione agente IaaS SQL aggiornerà VM SQL con questa nuova configurazione.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
