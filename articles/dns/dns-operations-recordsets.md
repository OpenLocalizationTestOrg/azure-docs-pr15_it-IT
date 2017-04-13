<properties
   pageTitle="Gestione record e set di record DNS tramite il portale di Azure | Microsoft Azure"
   description="Gestione di set di record DNS e i record DNS di Azure durante l'hosting del dominio DNS Azure. Tutti i comandi di PowerShell per le operazioni sui set di record e i record."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gestire i record DNS e set di record tramite PowerShell



> [AZURE.SELECTOR]
- [Portale di Azure](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



In questo articolo viene illustrato come gestire set di record e record per l'area DNS con Windows PowerShell.

È importante conoscere la differenza tra i set di record DNS e singoli record DNS. Un set di record è l'insieme dei record in un'area che hanno lo stesso nome e sono dello stesso tipo. Per ulteriori informazioni, vedere [set di record DNS di creare e record tramite il portale di Azure](dns-getstarted-create-recordset-portal.md).

Per gestire il set di record e record, è necessario l'ultima versione dei cmdlet PowerShell di Azure Manager delle risorse. Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per ulteriori informazioni sull'utilizzo di PowerShell, vedere [Uso di PowerShell Azure Gestione risorse Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Creare un nuovo set di record e un record

Per creare un record impostato tramite PowerShell, vedere [set di record DNS di creare e record tramite PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Ottenere un set di record

Per recuperare un set di record esistente, utilizzare `Get-AzureRmDnsRecordSet`. Specificare che il record impostata relativo nome, il tipo di record e il fuso orario.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Come con `New-AzureRmDnsRecordSet`, il nome del record deve essere un nome relativo, ovvero è necessario escludere il nome di zona.

È possibile specificare l'area utilizzando il nome della zona e nome gruppo di risorse o utilizzando un oggetto fuso orario:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Restituisce un oggetto locale che rappresenta il set di record che è stato creato in Azure DNS.

## <a name="list-record-sets"></a>Set di record di elenco

È inoltre possibile utilizzare`Get-AzureRmDnsRecordSet` a set di record elenco se si omette il *-nome* e/o *-RecordType* parametri.

### <a name="to-list-all-record-sets"></a>Per visualizzare un elenco di tutti i set di record

In questo esempio restituisce tutti i set di record, indipendentemente dal nome o tipo di record:

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Per set di record di elenco di un determinato tipo di record

In questo esempio restituisce tutti i set di record che corrispondono al tipo di record specificato. In questo caso, il record set che è restituito è "a" record:

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

È possibile specificare l'area utilizzando *– NomeZona* e *– ResourceGroupName* parametri della (come illustrato) o specificando un oggetto fuso orario:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Aggiungere un record a un set di record

Aggiungere record a set di record utilizzando la `Add-AzureRmDnsRecordConfig` cmdlet. Questa è un'operazione non in linea. Solo l'oggetto locale che rappresenta il set di record viene modificato.

I parametri per l'aggiunta di record a un set di record possono variare a seconda del tipo di set di record. Ad esempio, quando si utilizza un set di record di tipo "A", è possibile solo specificare record con il parametro *-IPv4Address*.

Altri record possono essere aggiunti a ogni record ulteriori chiamate per l'impostazione `Add-AzureRmDnsRecordConfig`. È possibile aggiungere fino a 20 record a un set di record. Tuttavia, set di record di tipo "CNAME" può contenere al massimo un record e un set di record non può contenere due record identici. Set di record vuoto (con zero records) possono essere create, ma non vengono visualizzate nel server dei nomi di Azure DNS.

Dopo il set di record contiene la raccolta desiderata dei record, è necessario eseguire il commit utilizzando il `Set-AzureRmDnsRecordSet` cmdlet. Dopo un set di record è stato eseguito il commit, sostituisce il record esistente impostato in Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Per creare un record a impostare un singolo record

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza di operazioni per creare un record può anche essere *reindirizzato*, vale a dire che si passa l'oggetto del set di record utilizzando la barra verticale anziché passando come parametro. Per esempio:

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Esempi di tipo di record aggiuntivi

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificare i set di record esistenti

La procedura per la modifica di un set di record esistenti è simile a passaggi eseguiti durante la creazione dei record. La sequenza di operazioni è il seguente:

1.  Recuperare il record esistente impostato utilizzando `Get-AzureRmDnsRecordSet`.

2.  Modificare il record impostato dall'aggiunta di record, la rimozione dei record, la modifica dei parametri di record oppure modificare il record impostare time to live (TTL). Questa è un'operazione non in linea. Solo l'oggetto locale che rappresenta il set di record viene modificato.

3.  Salvare le modifiche tramite il `Set-AzureRmDnsRecordSet` cmdlet. Sostituisce il record esistente impostato in Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Per aggiornare un record in un set di record esistente

In questo esempio abbiamo modificare l'indirizzo IP del record "a" di esistente:

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

Il `Set-AzureRmDnsRecordSet` cmdlet utilizza etag controlli per garantire che le modifiche simultanee non vengono sovrascritti. Utilizzare la *-sovrascrivere* contrassegna l'esclusione di questi controlli. Per ulteriori informazioni, vedere [informazioni sulla eTag e tag](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Per modificare un record SOA

Non è possibile aggiungere o rimuovere i record da impostare all'apice zona il record SOA creato automaticamente (nome = "@"). Tuttavia, è possibile modificare i parametri all'interno del record SOA (ad eccezione di "Host") e il record set TTL.

Nell'esempio seguente viene illustrato come modificare la proprietà di *posta elettronica* del record SOA:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Per modificare i record NS all'apice zona

È possibile aggiungere, per rimuovere o modificare i record creato automaticamente NS set di record al vertice zona (nome = "@"). L'unica differenza che sia consentito consiste nel modificare il set di record TTL.

Nell'esempio seguente viene illustrato come modificare la proprietà TTL del set di record NS:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Per aggiungere record a un set di record esistente

In questo esempio, si aggiunge due altri record MX per il set di record esistente:

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Rimuovere un record da un set di record esistente

Record possono essere rimossi da un record impostato utilizzando `Remove-AzureRmDnsRecordConfig`. Il record è stato rimosso deve essere una corrispondenza esatta con un record esistente in tutti i parametri. Devono eseguire il commit delle modifiche tramite `Set-AzureRmDnsRecordSet`.

La rimozione dell'ultimo record da un set di record non viene eliminato il set di record. Per ulteriori informazioni, vedere [eliminare un set di record](#delete-a-record-set) .


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La sequenza di operazioni per rimuovere un record da un set di record possa anche essere reindirizzata, vale a dire che si passa l'oggetto del set di record utilizzando la barra verticale anziché passando come parametro. Per esempio:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Rimuovere un record AAAA da un set di record

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Rimuovere un record CNAME da un set di record

Poiché un set di record CNAME può contenere al massimo un record, la rimozione di tale record lascia un set di record vuoto.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Rimuovere un record MX da un set di record

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Rimuovere un record NS del set di record

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Rimuovere un record SRV da un set di record

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Rimuovere un record TXT da un set di record

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Eliminare un set di record

Set di record possono essere eliminati utilizzando il `Remove-AzureRmDnsRecordSet` cmdlet. Non è possibile eliminare la SOA e set di record NS all'apice zona (nome = "@") che sono stati creati automaticamente quando è stata creata l'area. Verranno eliminati automaticamente se l'area viene eliminata.

Per rimuovere un set di record, utilizzare uno dei tre metodi seguenti:

### <a name="specify-all-the-parameters-by-name"></a>Specificare il nome di tutti i parametri

Facoltativa *-forza* parametro può essere utilizzato per eliminare la richiesta di conferma.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Specificare il set di record per nome e tipo e l'area dall'oggetto

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Specificare il record impostato dall'oggetto

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Quando si specifica il record impostato utilizzando un oggetto, in questo modo etag controlli per verificare che le modifiche simultanee non vengono eliminate. Facoltativa *-sovrascrivere* contrassegno Elimina questi controlli. Per ulteriori informazioni, vedere [ETag e tag](dns-getstarted-create-dnszone.md#tagetag) .

L'oggetto del set di record possa anche essere reindirizzato anziché passati come parametro:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure DNS, vedere [Panoramica di Azure DNS](dns-overview.md). Per informazioni sull'automazione DNS, vedere [creazione DNS zone e record imposta utilizzando .NET SDK](dns-sdk.md).

Per ulteriori informazioni sui record DNS inverso, vedere [come gestire i record DNS inverso per i servizi tramite PowerShell](dns-reverse-dns-record-operations-ps.md).
