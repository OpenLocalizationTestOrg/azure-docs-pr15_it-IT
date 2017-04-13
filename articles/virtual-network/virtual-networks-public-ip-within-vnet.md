<properties 
   pageTitle="Come usare gli indirizzi IP pubblici in una rete virtuale"
   description="Informazioni su come configurare una rete virtuale per l'utilizzo di indirizzi IP pubblici"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Spazio di indirizzi IP pubblico in una rete virtuale (VNet)

Reti virtuali (VNets) possono contenere spazi di indirizzi IP (blocchi di indirizzi RFC 1918) pubblici e privati. Quando si aggiunge un indirizzo IP pubblico, verrà considerato come parte dello spazio indirizzo IP VNet privato è raggiungibile all'interno di VNet, interconnessi VNets e dal percorso di locale.

Nell'immagine seguente mostra un VNet che include spazi di indirizzi IP pubblici e privati.

![IP pubblico concettuale](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Come è possibile aggiungere un indirizzo IP pubblico?

Aggiungere un indirizzo IP pubblico allo stesso modo che si aggiungerebbe un intervallo di indirizzi IP privato; utilizzando un file di *netcfg* o aggiungendo la configurazione del [portale di Azure](http://portal.azure.com). È possibile aggiungere un indirizzo IP pubblico quando si crea la VNet oppure è possibile tornare indietro e aggiungere in un secondo momento. Nell'esempio seguente viene pubblici e privati spazi di indirizzi IP configurati VNet stesso.

![Indirizzo IP pubblico nel portale](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Esistono limitazioni?

Esistono alcuni intervalli di indirizzi IP non consentiti:

- 224.0.0.0/4 (Multicast)

- 255.255.255.255/32 (trasmissione)

- 127.0.0.0/8 (loopback)

- 169.254.0.0/16 (locale)

- 168.63.129.16/32 (DNS interno)

## <a name="next-steps"></a>Passaggi successivi

[Come gestire i server DNS utilizzati da un VNet](virtual-networks-manage-dns-in-vnet.md)