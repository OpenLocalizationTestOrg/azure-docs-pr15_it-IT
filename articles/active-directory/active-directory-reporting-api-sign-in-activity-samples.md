<properties
    pageTitle="Esempi di report API di Azure Active Directory accesso attività | Microsoft Azure"
    description="Come iniziare con Azure Active Directory Reporting API di"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Esempi di report API di Azure Active Directory attività di accesso

In questo argomento fa parte di un insieme di argomenti su Azure Active Directory API di segnalazione.  
Reporting di Azure Active Directory offre un'API che consente di accedere ai dati di attività di accesso tramite codice o gli strumenti correlati.  
L'ambito di questo argomento è necessario fornirgli codice di esempio per l' **attività di accesso API**.

Vedere:

- [Log di controllo](active-directory-reporting-azure-portal.md#audit-logs) per ulteriori informazioni
- [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md) per ulteriori informazioni sull'API dei report.

Per eventuali domande, problemi o commenti e suggerimenti, contattare il [Report consente AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Prerequisiti
Prima di poter usare gli esempi in questo argomento, è necessario soddisfare i [Prerequisiti per accedere a Azure AD API di segnalazione](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>Script di PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Eseguire lo script
Dopo aver apportato le modifiche lo script, eseguirlo e verificare che i dati previsti dall'Audit accesso report viene restituiti.

Lo script restituisce output dal report accesso nel formato JSON. Crea anche un `SigninActivities.json` file con lo stesso risultato. È possibile sperimentare modificando lo script per restituire i dati da altri report e commento i formati di output che non è necessaria.



## <a name="next-steps"></a>Passaggi successivi

- Si desidera personalizzare gli esempi in questo argomento? Estrarre [Azure Active Directory accesso attività riferimento all'API](active-directory-reporting-api-sign-in-activity-reference.md). 

- Se si desidera visualizzare una panoramica dell'uso di Azure Active Directory API di segnalazione completa, vedere [Guida introduttiva di Azure Active Directory API di segnalazione](active-directory-reporting-api-getting-started.md).

- Per saperne di più sui report di Azure Active Directory, vedere [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  