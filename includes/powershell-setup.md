<properties services="virtual-machines" title="Setting up PowerShell" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="05/12/2015"
   ms.author="rasquill" />

## <a name="setting-up-powershell"></a>La configurazione di PowerShell

Prima di poter usare PowerShell di Azure, seguire questa procedura.

### <a name="verify-powershell-versions"></a>Verificare le versioni di PowerShell

Prima di poter utilizzare Windows PowerShell, è necessario disporre di Windows PowerShell, versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare il comando al prompt dei comandi di Windows PowerShell.

    $PSVersionTable

Verrà visualizzato simile al seguente.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Verificare che il valore di **PSVersion** 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

È inoltre Azure PowerShell versione 0.8.0 o versione successiva. È possibile controllare la versione di PowerShell Azure installati con questo comando al prompt dei comandi PowerShell di Azure.

    Get-Module azure | format-table version

Verrà visualizzato simile al seguente.

    Version
    -------
    0.8.16.1

Per istruzioni e un collegamento all'ultima versione, vedere [come installare e configurare PowerShell Azure](powershell-install-configure.md).


### <a name="set-your-azure-account-and-subscription"></a>Configurare l'account Azure e la sottoscrizione

Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno alto per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Aprire un prompt dei comandi PowerShell di Azure e accedere a Azure con questo comando.

    Add-AzureAccount

Se si hanno più abbonamenti Azure, è possibile elencare le sottoscrizioni Azure con questo comando.

    Get-AzureSubscription

Verrà visualizzato il seguente tipo di informazioni:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

È possibile impostare la sottoscrizione di Azure corrente eseguendo questi comandi al prompt dei comandi PowerShell di Azure. Sostituire tutti gli elementi all'interno di offerte, incluse le < e > caratteri, con il nome corretto.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current 

Per ulteriori informazioni sull'account e abbonamenti Azure, vedere [come: connettersi al proprio abbonamento](powershell-install-configure.md#Connect).
