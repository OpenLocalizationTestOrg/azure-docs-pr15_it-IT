<properties
   pageTitle="Gestire set di record DNS e i record DNS di Azure usa CLI Azure | Microsoft Azure"
   description="Gestione di set di record DNS e i record DNS di Azure durante l'hosting del dominio DNS Azure. Tutti i comandi CLI per le operazioni sui set di record e i record."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gestire i record DNS e set di record tramite CLI


> [AZURE.SELECTOR]
- [Portale di Azure](dns-operations-recordsets-portal.md)
- [CLI Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


In questo articolo viene illustrato come gestire set di record e record per l'area DNS tramite l'interfaccia di riga di comando Azure multipiattaforma (CLI).

È importante conoscere la differenza tra i set di record DNS e singoli record DNS. Un set di record è un insieme di record in un'area che hanno lo stesso nome e sono dello stesso tipo. Per ulteriori informazioni, vedere [record e set di record di informazioni](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurare CLI Azure multipiattaforma

Azure DNS è un servizio di gestione risorse sola Azure. Non è un API di gestione del servizio di Azure. Assicurarsi che CLI Azure sia configurata per usare la modalità di gestione risorse, utilizzando il `azure config mode arm` comando.

Se viene visualizzato **errore: "dns" non è un comando azure**, è probabile poiché si sta utilizzando Azure CLI in modalità di gestione dei servizi di Azure, non in modalità di gestione risorse.

## <a name="create-a-new-record-set-and-record"></a>Creare un nuovo set di record e record

Per creare un nuovo record impostare nel portale di Azure, vedere [creare un set di record e record](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Recuperare un set di record

Per recuperare un set di record esistente, utilizzare `azure network dns record-set show`. Specificare il gruppo di risorse, nome zona record impostare relativo nome e tipo di record. Utilizzare l'esempio seguente, sostituire i valori con uno personalizzato.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Set di record di elenco

È possibile elencare tutti i record in una zona DNS utilizzando il `azure network dns record-set list` comando. È necessario specificare il nome del gruppo di risorse e area.

### <a name="to-list-all-record-sets"></a>Per visualizzare un elenco di tutti i set di record

In questo esempio restituisce tutti i set di record, indipendentemente dal nome o tipo di record:

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>Per set di record di elenco di un determinato tipo

In questo esempio restituisce tutti i set di record che corrispondono al tipo di record specificato (in questo caso, "a" record):

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Aggiungere un record a un set di record

Aggiungere record a set di record utilizzando la `azure network dns record-set add-record`comando. I parametri per l'aggiunta di record a un set di record possono variare a seconda del tipo di record da impostare. Ad esempio, quando si utilizza un set di record di tipo "A", è possibile specificare solo record con il parametro `-a <IPv4 address>`.

Per creare un set di record, utilizzare la `azure network dns record-set create`comando. Specificare il gruppo di risorse, nome zona record impostare relativo nome, tipo di record e ora a live (TTL). Se il `--ttl` parametro non è definita, il valore (in secondi) predefinito è quattro.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Dopo aver creato "Un" set di record, aggiungere l'indirizzo IPv4 utilizzando il `azure network dns record-set add-record`comando.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Negli esempi seguenti viene illustrato come creare un set di record di ogni tipo di record. Ogni set di record contiene un singolo record.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Aggiornare un record in un set di record

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Per aggiungere un altro indirizzo IP (1.2.3.4) a un record "a" a esistente per impostare ("www"):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Per rimuovere un valore esistente da un set di record
Usare `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Rimuovere un record da un set di record

Record possono essere rimossi da un record impostato utilizzando `azure network dns record-set delete-record`. Il record è stato rimosso deve essere una corrispondenza esatta con un record esistente in tutti i parametri.

La rimozione dell'ultimo record da un set di record non viene eliminato il set di record. Per ulteriori informazioni, vedere la sezione di questo articolo, [eliminare un set di record](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Rimuovere un record AAAA da un set di record

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Rimuovere un record CNAME da un set di record

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Rimuovere un record MX da un set di record

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Rimuovere un record NS del set di record

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Rimuovere un record PTR da un set di record
In questo caso ' personale-arpa-zone.com' rappresenta l'area ARPA che rappresenta l'intervallo di indirizzi IP.  Ogni record PTR impostare in quest'area corrisponde a un indirizzo IP all'interno dell'intervallo di indirizzi IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Rimuovere un record SRV da un set di record

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Rimuovere un record TXT da un set di record

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Eliminare un set di record

Set di record possono essere eliminati utilizzando il `Remove-AzureRmDnsRecordSet` cmdlet. Non è possibile eliminare la SOA e set di record NS all'apice zona (nome = "@") che sono stati creati automaticamente quando è stata creata l'area. Verranno eliminati automaticamente se l'area viene eliminata.

Nell'esempio seguente "il un" set di record "prova a" verrà rimosso dall'area DNS "contoso.com":

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

Il parametro facoltativo *- q* può essere utilizzato per eliminare la richiesta di conferma.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure DNS, vedere [Panoramica di Azure DNS](dns-overview.md). Per informazioni sull'automazione DNS, vedere [creazione DNS zone e record imposta utilizzando .NET SDK](dns-sdk.md).

Se si desidera utilizzare i record DNS inverso, vedere [come gestire i record DNS inverso per i servizi tramite CLI Azure](dns-reverse-dns-record-operations-cli.md).
