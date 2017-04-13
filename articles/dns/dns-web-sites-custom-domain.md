<properties
   pageTitle="Creare record DNS personalizzati per un'app web | Microsoft Azure  "
   description="Come creare record DNS per app web utilizzando il DNS Azure dominio personalizzato."
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

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Creare record DNS per un'app web in un dominio personalizzato

È possibile utilizzare Azure DNS per ospitare un dominio personalizzato per le applicazioni web. Ad esempio, si sta creando un'app web Azure e si desidera agli utenti di accedere a una utilizzando contoso.com o www.contoso.com come un nome di dominio completo.

A questo scopo è necessario creare due record:

- Un record principale "A" che fa riferimento a contoso.com
- Un record "CNAME" per il nome www che punta al record

Tenere presente che, se si crea un record a per un'app web di Azure, deve essere manualmente il record aggiornati se l'indirizzo IP sottostante di tutte le app web.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario creare una zona DNS nel DNS Azure prima di tutto e delegare l'area del registrar in Azure DNS.

1. Per creare una zona DNS, attenersi alla procedura descritta in [creare una zona DNS](dns-getstarted-create-dnszone.md).
2. Per delegare il DNS per DNS Azure, seguire la procedura di [delega di dominio DNS](dns-domain-delegation.md).

Dopo aver creato una zona e delega di Azure DNS, è possibile creare i record per il dominio personalizzato.


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. creare un record per il dominio personalizzato

Un record viene utilizzato per eseguire il mapping di un nome per il proprio indirizzo IP. Nell'esempio seguente si assegna @ come un record a un indirizzo IPv4:

### <a name="step-1"></a>Passaggio 1

Creare un record e assegnare a una variabile $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>Passaggio 2

Aggiungere il valore di IPv4 al set di record creato in precedenza "@" utilizzando la variabile $rs assegnata. Il valore di IPv4 assegnato sarà l'indirizzo IP per un'app web.

Per trovare l'indirizzo IP per un'app web, seguire i passaggi descritti in [Configura un nome di dominio personalizzato in Azure App servizio](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address).

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>Passaggio 3

Applicare le modifiche al set di record. Usare `Set-AzureRMDnsRecordSet` per caricare le modifiche al record impostato su Azure DNS:

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. creare un record CNAME per il dominio personalizzato

Se il dominio è già gestito da Azure DNS (vedere [delega di dominio DNS](dns-domain-delegation.md), è possibile utilizzare il seguente esempio per creare un record CNAME per contoso.azurewebsites.net.

### <a name="step-1"></a>Passaggio 1

Aprire di PowerShell e creare un nuovo set di record CNAME e assegnare a una variabile $rs. In questo esempio verrà creato un tipo di set di record CNAME con "time to live" di 600 secondi nel zona DNS denominato "contoso.com".

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Passaggio 2

Dopo aver creato il set di record CNAME, è necessario creare un valore alias che farà riferimento all'app web.

Utilizzo della variabile assegnata in precedenza "$rs" è possibile utilizzare il comando PowerShell riportato di seguito per creare l'alias per il contoso.azurewebsites.net app web.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Passaggio 3

Il commit delle modifiche tramite il `Set-AzureRMDnsRecordSet` cmdlet:

    Set-AzureRMDnsRecordSet -RecordSet $rs

È possibile convalidare il record è stato creato correttamente eseguendo "www.contoso.com" utilizzo di nslookup, come illustrato di seguito:

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>Creare un record "awverify" per le applicazioni web


Se si decide di usare un record per un'app web, è necessario accedere tramite un processo di verifica per assicurarsi di che essere proprietari del dominio personalizzato. Questa fase di verifica viene eseguita creando un record CNAME speciale denominato "awverify". In questa sezione si applica a solo un record.


### <a name="step-1"></a>Passaggio 1

Creare il record "awverify". Nell'esempio seguente, verrà creato il record "aweverify" per contoso.com verificare la proprietà del dominio personalizzato.

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>Passaggio 2

Dopo aver creato il set di record "awverify", assegnare il record CNAME impostato alias. Nell'esempio seguente, si verrà assegnato il record CNAMe alias impostato su awverify.contoso.azurewebsites.net.

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>Passaggio 3

Il commit delle modifiche tramite il `Set-AzureRMDnsRecordSet cmdlet`, come illustrato nel comando riportato di seguito.

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>Passaggi successivi

Seguire i passaggi della [configurazione di un nome di dominio personalizzato per il servizio di App](../app-service-web/web-sites-custom-domain-name.md) per configurare un'applicazione web per l'utilizzo di un dominio personalizzato.








