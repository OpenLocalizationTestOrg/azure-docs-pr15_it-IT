<properties
    pageTitle="Associazione di certificati SSL tramite PowerShell"
    description="Informazioni su come eseguire il binding certificati SSL all'app web tramite PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure App servizio SSL certificato di associazione tramite PowerShell #

Con la versione di Microsoft Azure PowerShell versione 1.1.0 è stato aggiunto un nuovo cmdlet da fornire all'utente la possibilità di associare i certificati SSL nuovi o esistenti a un'App Web esistente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Per informazioni sull'uso di gestione risorse di Azure in base a PowerShell Azure cmdlet per gestire le app Web controllare [i comandi di PowerShell per Azure Web App in base a Gestione risorse di Azure](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Durante il caricamento e l'associazione di un nuovo certificato SSL ##

Scenario: L'utente desidera associare un certificato SSL a uno dei suoi App web.

Si conosce il nome del gruppo risorse che contiene l'applicazione web, il nome dell'app web, il percorso del file. pfx certificato nel computer dell'utente, la password per il certificato e il nome host personalizzato, è possibile usare il seguente comando di PowerShell per creare tale associazione SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Si noti prima di aggiungere un'associazione SSL per un'app web, è necessario che un nome host (dominio personalizzato) già configurato. Se non è configurato il nome host, quindi si verificherà un errore durante l'esecuzione di nuovo AzureRmWebAppSSLBinding non esiste "hostname". È possibile aggiungere un nome host direttamente dal portale o tramite PowerShell Azure. Frammento di PowerShell riportato di seguito è possibile configurare il nome host prima di eseguire di nuovo AzureRmWebAppSSLBinding.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
È importante tenere presente che il cmdlet Set-AzureRmWebApp sovrascrive nomi host per l'applicazione web. Pertanto il frammento di PowerShell sopra viene aggiunta all'elenco dei nomi host per l'applicazione web esistente.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Durante il caricamento e l'associazione di un certificato SSL esistente ##

Scenario: L'utente desidera associare un certificato SSL precedentemente caricato a uno dei suoi App web.

È possibile ottenere l'elenco dei certificati già caricato in un determinato gruppo di risorse utilizzando il comando seguente

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Si noti che i certificati sono locali rispetto a una posizione specifica e un gruppo di risorse, è necessario caricare nuovamente il certificato se l'app web configurata viene eseguita in un percorso diverso e gruppo di risorse altri che l'utente che del certificato di base alle esigenze 

Si conosce il nome del gruppo risorse che contiene l'applicazione web, il nome dell'app web, identificazione personale del certificato e hostname personalizzato, è possibile usare il seguente comando di PowerShell per creare tale associazione SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Eliminazione di un'associazione SSL esistente  ##

Scenario: L'utente desidera eliminare un'associazione SSL esistente.

Si conosce il nome del gruppo risorse che contiene l'applicazione web, il nome dell'app web e hostname personalizzato, è possibile utilizzare il comando di PowerShell seguente per rimuovere l'associazione SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Si noti che, se l'ultima associazione tramite il certificato in posizione, per impostazione predefinita il certificato viene eliminata se l'utente desidera mantenere il certificato che è possibile utilizzare l'opzione DeleteCertificate per mantenere il certificato è stato rimosso associazione di SSL

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Riferimenti ###
- [Azure Manager delle risorse in base a comandi PowerShell per Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Introduzione all'ambiente di servizio di App](app-service-app-service-environment-intro.md)
- [Utilizzo di PowerShell Azure Azure Gestione risorse](../powershell-azure-resource-manager.md)
