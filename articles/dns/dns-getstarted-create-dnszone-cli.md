<properties
   pageTitle="Creare una zona DNS tramite CLI | Microsoft Azure"
   description="Informazioni su come creare zone DNS per il DNS Azure dettagliate per avviare l'hosting del dominio DNS utilizzando CLI"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Creare una zona DNS Azure usa CLI


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI Azure](dns-getstarted-create-dnszone-cli.md)


In questo articolo consentono all'utente la procedura per creare una zona DNS tramite CLI. È anche possibile creare una zona DNS tramite PowerShell o il portale di Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Prima di iniziare

Queste istruzioni utilizzano Microsoft Azure CLI. Assicurarsi di aggiornare la più recente di Azure CLI (0.9.8 o versioni successive) usare i comandi di Azure DNS. Tipo di `azure -v` per controllare la versione di Azure CLI attualmente installata nel computer.

## <a name="step-1---set-up-azure-cli"></a>Passaggio 1: configurare CLI Azure

### <a name="1-install-azure-cli"></a>1. installare CLI Azure

È possibile installare CLI per Windows Azure, Linux o Mac. La procedura seguente è necessario essere completato per poter gestire DNS Azure utilizzando CLI Azure. Ulteriori informazioni sono disponibili in [installare CLI Azure](../xplat-cli-install.md). I comandi DNS richiedono Azure CLI versione 0.9.8 o versione successiva.

Tutti i comandi di provider di rete nella CLI possono essere trovato utilizzando il comando seguente:

    azure network

### <a name="2-switch-cli-mode"></a>2. modalità CLI passaggio

Azure DNS utilizza Gestione risorse di Azure. Assicurarsi che cambio di modalità CLI per usare i comandi ARM.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. accedere al proprio account Azure

Verrà richiesto di eseguire l'autenticazione con le credenziali. Tenere presente che è possibile utilizzare solo gli account ORGID.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. selezionare l'abbonamento

Scegliere quale delle sottoscrizioni Azure da utilizzare.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. creare un gruppo di risorse

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Tuttavia, poiché tutte le risorse DNS sono globale, non regionali, la scelta della posizione di gruppo di risorse non influisce sulla Azure DNS.

Se si usa un gruppo di risorse esistente, è possibile ignorare questo passaggio.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. registro

Il servizio DNS Azure viene gestito tramite il provider di risorse Microsoft.Network. L'abbonamento Azure deve essere registrato per utilizzare il provider di risorse prima di poter usare Azure DNS. Si tratta in operazione una sola volta per ogni sottoscrizione.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Passaggio 2: creare una zona DNS

Viene creata una zona DNS utilizzando il `azure network dns zone create` comando. Facoltativamente, è possibile creare una zona DNS insieme tag. Tag sono un elenco di coppie di nome e vengono utilizzati da Gestione risorse di Azure alle risorse etichetta ai fini della fatturazione o di raggruppamento. Per ulteriori informazioni sui tag, vedere [usare i contrassegni per organizzare le risorse Azure](../resource-group-using-tags.md).

Nel sistema DNS Azure, specificare i nomi delle aree senza un termini **'.'**. Ad esempio, '**contoso.com**' anziché '**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>Per creare una zona DNS

Nell'esempio seguente viene creata una zona DNS denominata *contoso.com* nel gruppo di risorse denominato *MyResourceGroup*.

L'esempio può essere utilizzato per creare l'area DNS, sostituendo i valori per il proprio.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Per creare una zona DNS e tag.

Azure DNS CLI supporta i tag delle zone DNS utilizzando facoltativa *-Tag* parametro. Nell'esempio seguente viene illustrato come creare una zona DNS con due tag, progetti = demo ed env = test.

Nell'esempio seguente consente di creare una zona DNS e tag, sostituendo i valori per il proprio.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Visualizzare i record

Creare una zona DNS crea anche i record DNS seguenti:

- Record 'Di autorità' (SOA). È presenta nella radice di ogni zona DNS.

- I record server (dei nomi NS) nome rilevanti. Mostrano quali i server dei nomi sono hosting della zona. Azure DNS utilizza un pool di server dei nomi e i server dei nomi in modo diverso possono essere assegnati a diverse aree in Azure DNS. Per ulteriori informazioni, vedere [delegato un dominio di Azure DNS](dns-domain-delegation.md) .

Per visualizzare questi record, utilizzare `azure network dns-record-set show`.<BR>
*Sintassi: rete dns set di record Mostra < gruppo risorse >< nome zona dns > <name><type>*


Nell'esempio seguente, se si esegue il comando con delle risorse gruppo *myresourcegroup*, record impostare nome *"@"* (per un record principale) e digitare *SOA*, consente di ottenere l'output seguente:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Per visualizzare i record NS creati con l'area, utilizzare il comando seguente:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Set di record principale (o *apice*) di un impiego di zona DNS **@** come nome del set di record.

## <a name="test"></a>Test

È possibile testare lo zona DNS utilizzando strumenti DNS, ad esempio nslookup, SCAVARE, o `Resolve-DnsName` cmdlet di PowerShell.

Se non hai ancora delegato il dominio da utilizzare la nuova zona DNS Azure, è necessario indirizzare la query DNS direttamente a uno dei server dei nomi per l'area. I server dei nomi per l'area figurano nei record NS indicata da "Mostra di set di record dns di rete azure". Assicurarsi di sostituire i valori corretti per l'area nel comando riportato di seguito.

Nell'esempio seguente usa SCAVARE per il dominio contoso.com utilizzando i server dei nomi assegnati per l'area DNS della query. La query contiene in modo che puntino a un server dei nomi per il quale è stato usato * @ * e area nome utilizzando l'attività Scavare le.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset-cli.md) per avviare la risoluzione dei nomi del dominio Internet.
