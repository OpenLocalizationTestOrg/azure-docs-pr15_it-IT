## <a name="setting-up-powershell-for-resource-manager-templates"></a>La configurazione di PowerShell per i modelli di Manager delle risorse

Prima di poter utilizzare PowerShell Azure con Gestione risorse, sarà necessario dispongono destra Windows PowerShell e le versioni di Azure PowerShell.

### <a name="verify-powershell-versions"></a>Verificare le versioni di PowerShell

Verificare di che disporre di Windows PowerShell versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare il comando al prompt dei comandi di Windows PowerShell.

    $PSVersionTable

Verrà visualizzato il seguente tipo di informazioni:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Verificare che il valore di **PSVersion** 3.0 o 4.0. In caso contrario, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Configurare l'account Azure e la sottoscrizione

Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno alto per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Aprire un prompt dei comandi PowerShell di Azure e accedere a Azure con questo comando.

    Login-AzureRmAccount

Se si hanno più abbonamenti Azure, è possibile elencare le sottoscrizioni Azure con questo comando.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Per ulteriori informazioni sull'account e abbonamenti Azure, vedere [come: connettersi al proprio abbonamento](powershell-install-configure.md#Connect).
