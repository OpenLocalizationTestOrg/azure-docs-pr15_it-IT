<properties 
   pageTitle="Specificare le impostazioni DNS in un file di configurazione del servizio | Microsoft Azure"
   description="Specifica delle impostazioni DNS personalizzate usando il file di configurazione del servizio per la rete virtuale"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Specificare le impostazioni DNS in un File di configurazione del servizio

## <a name="dns-elements"></a>Elementi del DNS

Un file di configurazione del servizio può contenere un elemento DnsServers con un elenco di indirizzi IPv4 per il server di sistema DNS (Domain Name) che verrà utilizzato il servizio. File di configurazione del servizio avranno la precedenza su impostazioni nel file di configurazione di rete. Per ulteriori informazioni, vedere [Dello Schema di configurazione del servizio di Azure (cscfg File)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] L'attributo **name** nell'elemento **ServerDNS** viene utilizzato solo come un nome di riferimento. Non rappresenta il nome host per il server DNS. Ogni valore dell'attributo **ServerDNS** deve essere univoco nell'intera sottoscrizione di Microsoft Azure.

## <a name="see-also"></a>Vedere anche

[Schema di configurazione del servizio Azure (cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schema di configurazione virtuali Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurare una rete virtuale utilizzando il file di configurazione di rete](http://go.microsoft.com/fwlink/?LinkId=248094)

[Informazioni sulle impostazioni di rete virtuale nel portale di gestione](http://go.microsoft.com/fwlink/?LinkId=248092)

