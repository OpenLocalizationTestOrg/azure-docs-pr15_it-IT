<properties 
   pageTitle="Specificare le impostazioni DNS in un file di configurazione della rete virtuale | Microsoft Azure"
   description="Come modificare le impostazioni del server DNS in una rete virtuale tramite un file di configurazione della rete virtuale nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Specificare le impostazioni DNS in un file di configurazione della rete virtuale

Un file di configurazione della rete sono presenti due elementi che è possibile utilizzare per specificare le impostazioni di sistema DNS (Domain Name): **DnsServers** e **DnsServerRef**. È possibile aggiungere un elenco dei server DNS specificando i relativi indirizzi IP e nomi di riferimento all'elemento **DnsServers** . È quindi possibile utilizzare un elemento **DnsServerRef** per specificare le voci di server DNS dall'elemento DnsServers vengono utilizzate per i siti di rete diversa all'interno della rete virtuale.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica.

File di configurazione della rete può contenere gli elementi seguenti. Il titolo di ogni elemento è collegato a una pagina che fornisce informazioni aggiuntive sulle impostazioni di valore dell'elemento.

>[AZURE.IMPORTANT] Per informazioni su come configurare il file di configurazione della rete, vedere [configurare una rete virtuale utilizzando un File di configurazione della rete](virtual-networks-using-network-configuration-file.md). Per informazioni su ogni elemento contenuto nel file di configurazione di rete, vedere [Azure virtuale Schema configurazione di rete](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Elemento DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] L'attributo **nome** nell'elemento **ServerDNS** viene utilizzato solo come riferimento per l'elemento **DnsServerRef** . Non rappresenta il nome host per il server DNS. Ogni valore dell'attributo **ServerDNS** deve essere univoco nell'intera sottoscrizione di Microsoft Azure

[Elemento di siti virtuali](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Per specificare questa impostazione per l'elemento di siti di rete virtuali, deve essere già definita nell'elemento del DNS. Il DnsServerRef *nome* nell'elemento siti rete virtuali deve fare riferimento a un valore di nome specificato nell'elemento del DNS per ServerDNS *nome*.

## <a name="next-steps"></a>Passaggi successivi

- Comprendere lo [Schema di configurazione di Azure virtuali](http://go.microsoft.com/fwlink/?LinkId=248093).
- Comprendere lo [Schema di configurazione del servizio di Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configurare una rete virtuale utilizzando il file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
