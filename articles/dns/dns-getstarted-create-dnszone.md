<properties
   pageTitle="Guida introduttiva a DNS Azure | Microsoft Azure"
   description="Informazioni su come creare zone DNS per il DNS Azure. Si tratta di un passaggio per passaggio per ottenere il primo zona DNS creato per avviare l'hosting del dominio DNS tramite PowerShell."
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

# <a name="create-a-dns-zone-using-powershell"></a>Creare una zona DNS tramite Powershell

> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)

In questo articolo consentono all'utente la procedura per creare una zona DNS tramite PowerShell. È anche possibile creare una zona DNS utilizzando CLI o il portale di Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Relative eTag e tag

### <a name="etags"></a>ETag

Si supponga di due persone o due processi tentano di modificare un record DNS nello stesso momento. Quali occorrenza ha? E il vincitore riconosce i contenuti che è appena sovrascritti modifiche create da un altro utente?

Azure DNS utilizza eTag per gestire in modo sicuro modifiche simultanee per la stessa risorsa. Ogni risorsa DNS (zona o set di record) è un Etag è associato. Quando viene recuperata una risorsa, il relativo Etag verranno recuperati anche. Quando si aggiorna una risorsa, disponibile l'opzione per passare Etag in modo Azure DNS verificare che l'Etag su corrispondenze server. Poiché ogni aggiornamento a una risorsa determina Etag in corso la rigenerazione, mancata corrispondenza Etag indica che simultanee è stata modificata. ETag utilizzati anche quando si crea una nuova risorsa per assicurarsi che la risorsa non esiste già.

Per impostazione predefinita, Azure DNS PowerShell utilizza eTag per impedire modifiche simultanee per le aree e i set di record. Facoltativa *-sovrascrivere* parametro può essere utilizzato eliminare i controlli Etag, nel qual caso eventuali modifiche simultanee che si sono verificati sovrascritti.

Livello di API REST DNS Azure eTag vengono specificati utilizzando le intestazioni HTTP.  Il comportamento è data nella tabella seguente:

|Intestazione|Comportamento|
|------|--------|
|Nessuno|INSERIRE sempre ha avuto esito positivo (nessun controllo Etag)|
|Se Confronta<etag>|Inserire avrà esito positivo solo se la risorsa è disponibile e trova le corrispondenze Etag|
|Se Confronta *     | Inserire avrà esito positivo solo se la risorsa è disponibile|
|Se nessuno-corrispondenza * |  Inserire avrà esito positivo solo se non esiste alcun delle risorse|

### <a name="tags"></a>Tag

Tag sono diversi dai ETag. Tag sono un elenco di coppie di nome e vengono utilizzati da Gestione risorse di Azure alle risorse etichetta ai fini della fatturazione o di raggruppamento. Per ulteriori informazioni sui tag, vedere [usare i contrassegni per organizzare le risorse Azure](../resource-group-using-tags.md).

Azure PowerShell DNS supporta i tag zone e set di record specificato utilizzando le opzioni di `-Tag` parametro.


## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione.

- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- È necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure (1.0 o versioni successive). Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

## <a name="step-1---sign-in"></a>Passaggio 1 - accesso

Aprire la console di PowerShell e connettersi all'account. Per ulteriori informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Utilizzare nell'esempio seguente viene per la connessione:

    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

Specificare l'abbonamento a cui si desidera utilizzare.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Passaggio 2 - creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Tuttavia, poiché tutte le risorse DNS sono globale, non regionali, la scelta della posizione di gruppo di risorse non influisce sulla Azure DNS.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Passaggio 3 - registro

Il servizio DNS Azure viene gestito tramite il provider di risorse Microsoft.Network. L'abbonamento Azure deve essere registrato per utilizzare il provider di risorse prima di poter usare Azure DNS. Si tratta in operazione una sola volta per ogni sottoscrizione.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Passaggio 4 - creazione di una zona DNS

Viene creata una zona DNS utilizzando il `New-AzureRmDnsZone` cmdlet. Sono disponibili esempi riportati di seguito per creare una zona DNS con o senza tag. Per ulteriori informazioni sui tag, vedere la sezione [tag](#tags) in questo articolo.

>[AZURE.NOTE] Nel sistema DNS Azure, specificare i nomi delle aree senza un termini **'.'**. Ad esempio, '**contoso.com**' anziché '**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>Per creare una zona DNS

Nell'esempio seguente viene creata una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*. L'esempio può essere utilizzato per creare una zona DNS, sostituendo i valori per il proprio.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Per creare una zona DNS con tag

Nell'esempio seguente viene illustrato come creare una zona DNS con due tag *progetto = demo* e *env = test*. L'esempio può essere utilizzato per creare una zona DNS, sostituendo i valori per il proprio.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Visualizzare i record

Creare una zona DNS crea anche i record DNS seguenti:

- Record *Di autorità* (SOA). È presenta nella radice di ogni zona DNS.

- I record server (dei nomi NS) nome rilevanti. Mostrano quali i server dei nomi sono hosting della zona. Azure DNS utilizza un pool di server dei nomi e i server dei nomi in modo diverso, è possibile applicare in aree diverse in Azure DNS. Vedere [ad Azure DNS un dominio](dns-domain-delegation.md) per ulteriori informazioni.

Per visualizzare questi record, utilizzare `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Set di record principale (o *apice*) di un impiego di zona DNS **@** come nome del set di record.


## <a name="test"></a>Test

È possibile testare lo zona DNS utilizzando strumenti DNS, ad esempio nslookup, Scavare o il [cmdlet di PowerShell Risolvi DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non hai ancora delega il dominio da utilizzare la nuova zona DNS Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per l'area. I server dei nomi per l'area figurano nei record NS come indicato da `Get-AzureRmDnsRecordSet` sopra. Assicurarsi di sostituire i valori corretti per l'area nel comando riportato di seguito.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset.md) per avviare la risoluzione dei nomi del dominio Internet.

