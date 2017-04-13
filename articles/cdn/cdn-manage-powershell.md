<properties
    pageTitle="Gestire CDN Azure con PowerShell | Microsoft Azure"
    description="Informazioni su come utilizzare i cmdlet di Azure PowerShell per gestire CDN Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="casoper"/>


# <a name="manage-azure-cdn-with-powershell"></a>Gestire CDN Azure con PowerShell

PowerShell fornisce uno dei metodi più flessibili per gestire i profili di rete CDN di Azure e i punti finali.  È possibile utilizzare in modo interattivo o tramite la scrittura di script di PowerShell per automatizzare le attività di gestione.  Di seguito vengono illustrati in questa esercitazione le attività più comuni possono essere svolte con PowerShell per gestire i profili di rete CDN di Azure e i punti finali.

## <a name="prerequisites"></a>Prerequisiti

Per usare PowerShell per gestire i profili di rete CDN di Azure e i punti finali, è necessario disporre del modulo PowerShell per Azure installato.  Per informazioni su come installare PowerShell Azure e connettersi all'uso di Azure il `Login-AzureRmAccount` cmdlet, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] È necessario accedere con `Login-AzureRmAccount` prima di poter eseguire i cmdlet di PowerShell Azure.

## <a name="listing-the-azure-cdn-cmdlets"></a>I cmdlet di Azure CDN di elenco

Elencare tutti i cmdlet di Azure CDN utilizzando il `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Visualizzazione della Guida

È possibile ottenere assistenza con uno di questi cmdlet usando il `Get-Help` cmdlet.  `Get-Help`offre l'utilizzo e sintassi e facoltativamente mostra esempi.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Elenco profili Azure CDN esistenti

Il `Get-AzureRmCdnProfile` cmdlet senza parametri recupera tutti i profili di rete CDN esistenti.

```powershell
Get-AzureRmCdnProfile
```

L'output può essere reindirizzato al cmdlet per enumerazione.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

È inoltre possibile restituire un singolo profilo specificando il gruppo di risorse e nome profilo.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] È possibile disporre di più profili CDN con lo stesso nome, a condizione che si trovano in gruppi di risorse diversi.  Se si omette il `ResourceGroupName` parametro restituisce tutti i profili con un nome corrispondente.

## <a name="listing-existing-cdn-endpoints"></a>Elenco endpoint CDN esistenti

`Get-AzureRmCdnEndpoint`recuperare un singolo endpoint o tutti gli endpoint su un profilo.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Creazione di profili CDN e i punti finali

`New-AzureRmCdnProfile`e `New-AzureRmCdnEndpoint` vengono utilizzati per creare i profili di rete CDN e i punti finali.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Verifica disponibilità nome endpoint

`Get-AzureRmCdnEndpointNameAvailability`Restituisce un oggetto che indica se è disponibile un nome dell'endpoint.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Aggiungere un dominio personalizzato

`New-AzureRmCdnCustomDomain`Aggiunge un nome di dominio personalizzato per un endpoint esistente.

>[AZURE.IMPORTANT] È necessario impostare il record CNAME con il proprio provider DNS come descritto [come eseguire il mapping di dominio personalizzati a endpoint di contenuti rete (CDN)](./cdn-map-content-to-custom-domain.md).  È possibile testare il mapping prima di modificare l'endpoint utilizzando `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modifica di un estremo

`Set-AzureRmCdnEndpoint`modifica un endpoint esistente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Eliminazione/Pre-loading CDN beni

`Unpublish-AzureRmCdnEndpointContent`Elimina cache beni, mentre `Publish-AzureRmCdnEndpointContent` pre-carica risorse sugli endpoint supportati.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Endpoint di avvio e l'arresto CDN
`Start-AzureRmCdnEndpoint`e `Stop-AzureRmCdnEndpoint` può essere utilizzato per avviare e arrestare endpoint singoli o gruppi di punti finali.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Eliminazione di risorse CDN

`Remove-AzureRmCdnProfile`e `Remove-AzureRmCdnEndpoint` può essere utilizzato per rimuovere i profili e i punti finali.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come automatizzare CDN Azure con [.NET](./cdn-app-dev-net.md) o [Node](./cdn-app-dev-node.md).

Per informazioni sulle funzionalità di rete CDN, vedere [Panoramica CDN](./cdn-overview.md).


