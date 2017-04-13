<properties
   pageTitle="Creare un set di record e record per una zona DNS tramite PowerShell | Microsoft Azure"
   description="Come creare record host per il DNS Azure. Impostazione dei record imposta e record tramite PowerShell"
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



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Creare record di set di record DNS e tramite PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI Azure](dns-getstarted-create-recordset-cli.md)

In questo articolo illustra il processo di creazione di record e i set di record mediante Windows PowerShell. Dopo aver creato la zona DNS, aggiungere i record DNS per il dominio. A tale scopo, è innanzitutto necessario conoscere i record DNS e set di record.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Verificare di avere l'ultima versione di PowerShell

Verificare di avere installato la versione più recente dei cmdlet di Manager delle risorse PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

## <a name="create-a-record-set-and-record"></a>Creare un set di record e record

In questa sezione viene descritto come creare un set di record e record.


### <a name="1-connect-to-your-subscription"></a>1. connettersi al proprio abbonamento

Aprire la console di PowerShell e connettersi all'account. Utilizzare nell'esempio seguente viene per la connessione:

    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

Specificare l'abbonamento a cui si desidera utilizzare.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Per ulteriori informazioni sull'utilizzo di PowerShell, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. creare un set di record

Per creare set di record, utilizzare la `New-AzureRmDnsRecordSet` cmdlet. Quando si crea un set di record, è necessario specificare che i record del set di nome, l'area, la durata (TTL) e il tipo di record.

Per creare un set di record in apice dell'area (in questo caso, "contoso.com"), usare il nome del record "@", incluse le virgolette. Questa è una convenzione DNS comune.

Nell'esempio seguente viene creato un set di record con il relativo nome "www" in DNS Zone "contoso.com". Il nome completo del set di record è "www.contoso.com". Il tipo di record è "A" e il valore TTL è 60 secondi. Dopo aver completato questo passaggio, sarà disponibile un set di record vuoto "www" che sono state assegnate a variabile *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Se un record imposta già esistente

Se esiste un record già impostato, il comando non riesce a meno che il *-sovrascrivere* parametro viene utilizzato. Il *-sovrascrivere* opzione attiva una richiesta di conferma, può essere eliminata utilizzando il *-forza* passare.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


In questo esempio, specificare l'area utilizzando il nome della zona e nome gruppo di risorse. In alternativa, è possibile specificare un oggetto di fuso orario, come restituito da `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Restituisce un oggetto locale che rappresenta il set di record che è stato creato in Azure DNS.

### <a name="3-add-a-record"></a>3. aggiungere un record

Per utilizzare il set di record appena creato "www", è necessario aggiungere record. È possibile aggiungere IPv4 *record al record "www"* impostare utilizzando l'esempio seguente. In questo esempio si basa su variabili *$rs* impostate nel passaggio precedente.

Aggiunta di record a un record impostata utilizzando `Add-AzureRmDnsRecordConfig` è un'operazione non in linea. Solo il locale variabile *$rs* viene aggiornato.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. il commit delle modifiche

Applicare le modifiche al set di record. Usare `Set-AzureRmDnsRecordSet` per caricare le modifiche al record impostato su Azure DNS.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. recuperare il set di record

È possibile recuperare il set di record da Azure DNS utilizzando `Get-AzureRmDnsRecordSet` come illustrato nell'esempio seguente.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Per ottenere tramite query il nuovo set di record, è possibile utilizzare lo strumento nslookup o altri strumenti DNS.

Se il dominio per i server dei nomi di Azure DNS non è ancora stato delegato, è necessario specificare il nome, il server e l'indirizzo per l'area.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Creare un set di record di ogni tipo con un singolo record


Negli esempi seguenti viene illustrato come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Passaggi successivi

[Come gestire le aree DNS tramite PowerShell](dns-operations-dnszones.md)

[Gestire i record DNS e set di record tramite PowerShell](dns-operations-recordsets.md)

[Automatizzare operazioni Azure con .NET SDK](dns-sdk.md)
