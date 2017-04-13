<properties
   pageTitle="Ottenere i valori necessari per l'autenticazione di un'applicazione di Database SQL di accedere dal codice | Microsoft Azure"
   description="Creare un'entità servizio per accedere al Database di SQL dal codice."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Ottenere i valori necessari per l'autenticazione di un'applicazione di Database SQL di accedere dal codice

Per creare e gestire Database SQL dal codice è necessario registrare l'app nel dominio di Azure Active Directory (AAD) in cui sono state create le risorse Azure nella sottoscrizione.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Creare un servizio principale per accedere alle risorse da un'applicazione

È necessario disporre di più recente [PowerShell di Azure](https://msdn.microsoft.com/library/mt619274.aspx) , installato e in esecuzione. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Il seguente script di PowerShell crea l'applicazione di Active Directory (AD) e l'identità di servizio che è necessario eseguire l'autenticazione applicazione c#. Lo script restituisce i valori requisiti per l'esempio c# precedente. Per informazioni dettagliate, vedere [Usare PowerShell Azure per creare un servizio principale per accedere alle risorse](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Vedere anche

- [Creare un database SQL con c#](sql-database-get-started-csharp.md)
- [La connessione al Database SQL mediante l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md)


