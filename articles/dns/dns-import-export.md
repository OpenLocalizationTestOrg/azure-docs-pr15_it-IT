<properties
   pageTitle="Importare ed esportare un file di zona del dominio in DNS Azure utilizzando CLI | Microsoft Azure"
   description="Informazioni su come importare ed esportare un file di zona DNS in Azure DNS utilizzando CLI Azure"
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

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importare ed esportare un file di zona DNS utilizzando CLI Azure


In questo articolo verrà descritto come importare ed esportare file di zona DNS per il DNS Azure usa CLI Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introduzione alla migrazione zona DNS

Un file di zona DNS è un file di testo che contiene i dettagli di tutti i record nell'area sistema DNS (Domain Name). Segua un formato standard, rendendo utilizzabili per trasferire i record DNS tra sistemi DNS. L'uso di un file di zona è un modo rapido, affidabile e comodo per trasferire una zona DNS interno o all'esterno Azure DNS.

Azure DNS supporta l'importazione ed esportazione di file di zona utilizzando l'interfaccia della riga di comando Azure (CLI). CLI Azure è uno strumento della riga di comando multipiattaforma utilizzato per la gestione dei servizi di Azure. È disponibile per le piattaforme di Windows, Mac e Linux dalla [pagina Download Azure](https://azure.microsoft.com/downloads/). Supporto multipiattaforma è particolarmente importante per importazione ed esportazione di file di zona, perché il software di server di nome più comune, [ASSOCIARE](https://www.isc.org/downloads/bind/), in genere viene eseguito su Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Ottenere il file di zona DNS esistente

Prima di importare un file di zona DNS nel DNS Azure, sarà necessario ottenere una copia del file di zona. L'origine del file dipenderà nel punto in cui la zona DNS è attualmente ospitata.

- Se la zona DNS è ospitata da un servizio partner (ad esempio un registrar, dedicato provider di hosting DNS o provider alternativo cloud), tale servizio deve fornire la possibilità di scaricare il file di zona DNS.

- Se la zona DNS è ospitata su DNS di Windows, la cartella predefinita per i file di zona è **%systemroot%\system32\dns**. Il percorso completo per ogni file di zona viene visualizzata anche nella scheda **Generale** della console di gestione di servizi DNS.

- Se la zona DNS è ospitata tramite associazione, il percorso del file di zona per ogni area viene specificato nel file di configurazione associazione **named. conf**.

**Utilizzo di file di zona da GoDaddy**<BR>
File di zona scaricati da GoDaddy hanno un formato leggermente non standard. È necessario risolvere il problema prima di importare questi file di zona in Azure DNS. Nomi DNS in RData di ogni record DNS vengono specificati come nomi completi, ma non hanno un termine "." Di conseguenza, che vengono interpretati da altri sistemi DNS come nomi relativi. È necessario modificare il file di zona per aggiungere il termine "." per i nomi prima di importarli in Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importare un file di zona DNS nel DNS Azure


Importare un file di zona creerà un nuovo orario in Azure DNS se non ne esiste già. Se l'area esiste già, il set di record nel file di zona devono essere unite con il set di record esistenti.

### <a name="merge-behavior"></a>Unire comportamento

- Per impostazione predefinita, vengono uniti set di record nuovo o esistente. Record identici all'interno di un set di record unito sono deselezionare duplicato.

- In alternativa, specificando il `--force` opzione, il processo di importazione esistenti verrà sostituite set di record con nuovo set di record. Set di record esistenti che non dispongono di un record corrispondente nel file di zona importato non vengono rimosse.

- Durante l'unione di set di record, viene utilizzato il tempo necessario per live (TTL) del set di record preesistenti. Quando `--force` è utilizzato, viene utilizzato il valore TTL del nuovo set di record.

- Inizio parametri autorità (SOA) (ad eccezione di `host`) sempre applicato dal file di zona importati, indipendentemente dal fatto che `--force` viene utilizzato. Analogamente, per i record server dei nomi impostato al vertice area, il valore TTL viene accettato sempre dal file di zona importati.

- Un record CNAME importato non sostituire un record CNAME con lo stesso nome a meno che il `--force` parametro specificato.

- Quando si verifica un conflitto tra un record CNAME e un altro record con lo stesso nome ma di tipo diverso (indipendentemente dal fatto che esistente o nuovo), viene mantenuto il record esistente. Si tratta indipendente dell'utilizzo di `--force`.

### <a name="additional-information-about-importing"></a>Ulteriori informazioni sull'importazione

Le note di seguito offrono ulteriori dettagli tecnici sull'area di processo di importazione.

- Il `$TTL` direttiva è facoltativa e questa caratteristica è supportata. Quando non `$TTL` direttiva viene assegnata, record senza un TTL espliciti verrà importato insieme a un valore predefinito TTL di 3.600 secondi. Quando due record nello stesso set di record specificare TTLs diverso, viene utilizzato il valore più basso.

- Il `$ORIGIN` direttiva è facoltativa e questa caratteristica è supportata. Quando non `$ORIGIN` è impostata, il valore predefinito utilizzato è il nome di zona come specificato nella riga di comando (e di chiusura ".").

- Il `$INCLUDE` e `$GENERATE` direttive non sono supportate.

- Sono supportati questi tipi di record: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

- Record SOA viene creato automaticamente da Azure DNS quando si crea una zona. Quando si importa un file di zona, tutti i parametri SOA provengono dal zone file *ad eccezione* di `host` parametro. Questo parametro utilizzerà il valore fornito da Azure DNS. Ciò avviene perché questo parametro deve fare riferimento a name server primario fornite da Azure DNS.

- Record server dei nomi impostare al vertice zona viene anche creato automaticamente da Azure DNS quando viene creato l'area. Viene importato solo il valore TTL di questo set di record. Questi record contengono i nomi dei server di nome fornite da Azure DNS. I dati del record non viene sovrascritta dai valori contenuti nel file di zona importati.

- Durante l'anteprima pubblica, Azure DNS supporta solo i record TXT singola stringa. Record TXT multistringa verranno concatenate e troncati a 255 caratteri.

### <a name="cli-format-and-values"></a>Valori e il formato CLI


Il formato del comando CLI Azure per importare una zona DNS è:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valori:

- `<resource group>`è il nome del gruppo di risorse per l'area in Azure DNS.
- `<zone name>`è il nome dell'area.
- `<zone file name>`è il percorso del file di zona da importare.

Se una zona con questo nome non esiste nel gruppo di risorse, verrà creato automaticamente. Se l'area esiste già, il set di record importati verranno unite con set di record esistenti. Per sovrascrivere il set di record esistenti, utilizzare la `--force` opzione.

Per verificare il formato di un file di zona senza effettivamente l'importazione, utilizzare la `--parse-only` opzione.

### <a name="step-1-import-a-zone-file"></a>Passaggio 1. Importare un file di zona

Per importare un file di zona per lo zona **contoso.com**.

1. Accedere al proprio abbonamento Azure tramite CLI Azure.

        azure login

2. Selezionare l'abbonamento in cui si desidera creare la nuova zona DNS.

        azure account set <subscription name>

3. Azure DNS è un servizio di gestione risorse sola Azure, pertanto CLI Azure deve essere aggiornata alla modalità di gestione risorse.

        azure config mode arm

4. Prima di utilizzare il servizio DNS Azure, è necessario registrare l'abbonamento per utilizzare il provider di risorse Microsoft.Network. (Si tratta di un'operazione che si per ogni abbonamento).

        azure provider register Microsoft.Network

5. Se non hai uno già, è necessario creare un gruppo di risorse Manager delle risorse.

        azure group create myresourcegroup westeurope

6. Per importare la zona **contoso.com** dal file **contoso.com.txt** in una nuova zona DNS nel gruppo di risorse **myresourcegroup**, eseguire il comando `azure network dns zone import`.<BR>Questo comando caricare il file di zona e analisi essere. Il comando verrà eseguito una serie di comandi per creare l'area del servizio di Azure DNS e tutto il record imposta nell'area. Il comando riporterà anche lo stato di avanzamento nella finestra della console, insieme a eventuali errori o avvisi. Dato set di record vengono creati in serie, possono richiedere alcuni minuti per importare un file di zona di grandi dimensioni.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Passaggio 2. Verificare l'area

Per verificare l'area DNS dopo aver importato il file, è possibile usare uno dei metodi seguenti:

- È possibile elencare i record utilizzando il seguente comando CLI Azure.

        azure network dns record-set list myresourcegroup contoso.com

- È possibile elencare i record utilizzando il cmdlet di PowerShell `Get-AzureRmDnsRecordSet`.

- È possibile utilizzare `nslookup` per verificare la risoluzione dei nomi per i record. Perché l'area non delegata ancora, sarà necessario specificare in modo esplicito i server dei nomi di Azure DNS corretto. Nell'esempio seguente viene illustrato come recuperare i nomi di server nome assegnati all'area. IT viene inoltre descritto come eseguire una query utilizzando il record "www" `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Passaggio 3. Aggiornare la delega DNS

Dopo aver verificato che l'area è stato importato correttamente, sarà necessario aggiornare la delega di DNS in modo che puntino ai server dei nomi di Azure DNS. Per ulteriori informazioni, vedere l'articolo [aggiornare la delega DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Esportare un file di zona DNS da Azure DNS

Il formato del comando CLI Azure per importare una zona DNS è:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valori:

- `<resource group>`è il nome del gruppo di risorse per l'area in Azure DNS.
- `<zone name>`è il nome dell'area.
- `<zone file name>`è il percorso del file di zona da esportare.

Come con l'importazione di zona, è innanzitutto necessario effettuare l'accesso, scegliere l'abbonamento e configurare CLI Azure per usare la modalità di gestione risorse.

### <a name="to-export-a-zone-file"></a>Per esportare un file di zona


1. Accedere al proprio abbonamento Azure tramite CLI Azure.

        azure login

2. Selezionare l'abbonamento in cui si desidera creare la nuova zona DNS.

        azure account set <subscription name>

3. Azure DNS è un servizio di gestione risorse sola Azure. CLI Azure deve essere aggiornata alla modalità di gestione risorse.

        azure config mode arm

4. Per esportare il di zona DNS Azure di esistente **contoso.com** nel gruppo di risorse **myresourcegroup** per il file **contoso.com.txt** (nella cartella corrente), eseguire `azure network dns zone export`. Questo comando chiama il servizio di Azure DNS per enumerare set di record nell'area ed esportare i risultati in un file di zona compatibile con l'associazione.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

