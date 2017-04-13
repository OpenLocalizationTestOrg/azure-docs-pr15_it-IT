<properties
   pageTitle="Gestire le aree DNS tramite PowerShell | Microsoft Azure"
   description="È possibile gestire zone tramite Powershell Azure. Come aggiornare, eliminare e creare zone DNS nel DNS Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="how-to-manage-dns-zones-using-powershell"></a>Modalità di gestione DNS Zones tramite PowerShell

> [AZURE.SELECTOR]
- [CLI Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



In questo articolo illustra come gestire la zona DNS tramite PowerShell. Per utilizzare questa procedura, è necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure (1.0 o versioni successive). Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .


## <a name="create-a-new-dns-zone"></a>Creare una nuova zona DNS

Per creare una zona DNS, vedere [creare una zona DNS tramite PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Ottenere una zona DNS

Per recuperare una zona DNS, utilizzare la `Get-AzureRmDnsZone` cmdlet. Questa operazione restituisce un oggetto di zona DNS corrispondente a una zona esistente nel sistema DNS Azure. L'oggetto contiene i dati sull'area (ad esempio il numero di set di record), ma non contiene il set di record se stessi.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Elenco DNS zone

Omettendo il nome della zona `Get-AzureRmDnsZone`, è possibile enumerare tutte le aree in un gruppo di risorse. Questa operazione restituisce una matrice di oggetti fuso orario.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Aggiornare una zona DNS

Le modifiche apportate a una risorsa di zona DNS possono essere eseguite con `Set-AzureRmDnsZone`. Questa operazione non aggiorna il set di record DNS all'interno dell'area (vedere [Manage DNS record](dns-operations-recordsets.md)). Viene utilizzato solo per aggiornare le proprietà della risorsa zona stessa. Questo è attualmente limitato a Gestione risorse Azure 'tag' per la risorsa di zona. Per ulteriori informazioni, vedere [ETag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags) .

Usare una delle operazioni seguenti due modi per zona DNS aggiornamento:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Specificare l'area utilizzando il gruppo di risorse e nome zona

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare l'area di utilizzo di un oggetto $zone

Specificare l'area di utilizzo di un oggetto $zone da `Get-AzureRmDnsZone`. Quando si usa `Set-AzureRmDnsZone` con un oggetto $zone Etag controlli verranno utilizzati per assicurarsi che le modifiche simultanee non vengono sovrascritti. È possibile utilizzare facoltativa *-sovrascrivere* passa a eliminare questi controlli. Per ulteriori informazioni, vedere [ETag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags) .


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Eliminare una zona DNS

Zone DNS possono essere eliminate utilizzando il cmdlet AzureRmDnsZone Rimuovi.

Prima di eliminare una zona DNS nel DNS Azure, sarà necessario eliminare tutti i set di record, fatta eccezione per i record NS e SOA nella radice della zona che sono stati creati automaticamente quando è stata creata l'area.

Utilizzare uno dei due modi per rimuovere una zona DNS seguenti:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Specificare l'area utilizzando il nome di zona e il nome del gruppo di risorse

Questa operazione è facoltativa *-forza* opzione che elimina la richiesta di confermare la rimozione della zona DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Specificare l'area di utilizzo di un oggetto $zone

Specificare l'area di utilizzo di un oggetto $zone da `Get-AzureRmDnsZone`. Questa operazione è facoltativa *-forza* opzione che elimina la richiesta di confermare la rimozione della zona DNS. Come con `Set-AzureRmDnsZone`, che specifica l'area utilizzando un oggetto $zone Abilita i controlli Etag per assicurarsi che le modifiche simultanee non vengono eliminate. <BR>
Facoltativa *-sovrascrivere* contrassegno Elimina questi controlli. Per ulteriori informazioni, vedere [ETag e tag](dns-getstarted-create-dnszone.md#Etags-and-tags) .

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



Oggetto zona possa anche essere reindirizzato anziché passati come parametro:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset.md) per avviare la risoluzione dei nomi del dominio Internet.