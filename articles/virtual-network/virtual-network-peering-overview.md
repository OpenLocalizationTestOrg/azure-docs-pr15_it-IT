
<properties
   pageTitle="Azure virtuali peering | Microsoft Azure"
   description="Informazioni sulle VNet peering in Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>Peering VNet

Peering VNet è un meccanismo che connette due reti virtuale (VNets) nella stessa regione tramite la rete dorsale Azure. Una volta peered, le due reti virtuale vengono visualizzati come uno per tutti gli scopi di integrazione applicativa. Se vengono comunque gestiti come risorse distinte, ma macchine virtuali in queste reti virtuali possano comunicare con loro direttamente tramite indirizzi IP privati.

Il traffico tra macchine virtuali nelle reti virtuali peered instradato l'infrastruttura di Azure così come viene effettuato il traffico tra macchine virtuali nella stessa rete virtuale. Alcuni dei vantaggi dell'uso delle VNet peering includono:

- Connessione di bassa latenza, larghezza di banda elevata tra le risorse in reti virtuali diversi.
- La possibilità di usare le risorse, ad esempio dispositivi di rete e gateway VPN come punti di transito in un VNet peered.
- La possibilità di connettersi a una rete virtuale che utilizza il modello di gestione di risorse Azure a una rete virtuale che utilizza il modello di distribuzione classica e abilita la connettività completa tra le risorse in queste reti virtuale.

Requisiti e aspetti principali della VNet peering:

- Le due reti virtuale che sono peered dovrebbero essere presenti nell'area di Azure stesso.
- Le reti virtuali sono peered devono avere spazi di indirizzi IP non sovrapposti.
- VNet peering è compresa tra due reti virtuali e non è alcuna relazione transitivi derivata. Ad esempio, se sono peered virtuali risposte con virtuali B e se virtuali B sono peered con virtuali C, non convertire a virtuale rete un peered con virtuali C.
- È possibile stabilire peering tra reti virtuali in due diversi abbonamenti come tempo un utente con privilegi di entrambe le sottoscrizioni autorizza il peering e le sottoscrizioni sono associate a stesso tenant di Active Directory. 
- Peering tra virtuali nel modello di gestione delle risorse e il modello di distribuzione classica, è necessario che il VNets deve essere nello stesso abbonamento.
- Una rete virtuale che usa il modello di distribuzione di Manager delle risorse può peered con un'altra rete virtuale che usa il modello o con una rete virtuale che usa il modello di distribuzione classica. Tuttavia, reti virtuali che utilizzano il modello di distribuzione classica non possono essere peered tra loro.
- Anche se le comunicazioni tra macchine virtuali in reti virtuali peered non sono restrizioni di larghezza di banda aggiuntiva, estremità della larghezza di banda in base alle dimensioni di macchine Virtuali è ancora valida.


![Peering VNet base](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connettività
Dopo che sono peered due reti virtuali, una macchina virtuale (ruolo del web/lavoro) nella rete virtuale direttamente in grado di altre macchine virtuali nella rete virtuale peered. Queste due reti hanno il livello di IP completo connettività.

La latenza di rete per un round trip tra due macchine virtuali di reti virtuali peered è la stessa un round trip all'interno di una rete virtuale locale. La velocità di rete è basata sulla larghezza di banda consentito per la macchina virtuale mantenendo le proporzioni. Non è presente nessuna restrizione aggiuntiva sulla larghezza di banda.

Il traffico tra le macchine virtuali di reti virtuali peered è indirizzato direttamente tramite l'infrastruttura di back-end Azure e non attraverso un gateway.

Macchine virtuali in una rete virtuale possono accedere l'interno bilanciamento del carico (ILB) i punti finali della rete virtuale peered. In uno dei due virtuali per bloccare l'accesso ad altre reti virtuali o subnet se lo si desidera, è possono utilizzare gruppi di sicurezza di rete (NSGs).

Quando gli utenti di configurare peering, possono aprire o chiudere le regole NSG tra le reti virtuali. Se si sceglie di aprire completa connettività tra reti virtuali peered (che l'opzione predefinita), quindi consente NSGs in subnet specifiche o macchine virtuali di bloccare o negare l'accesso specifico.

Condizione Azure interno DNS la risoluzione dei nomi per macchine virtuali non funziona reti virtuale peered. Macchine virtuali hanno nomi DNS interni che sono possibile risolvere solo all'interno della rete virtuale locale. Tuttavia, gli utenti possono configurare macchine virtuali che sono in esecuzione in reti virtuali peered come server DNS per una rete virtuale.

## <a name="service-chaining"></a>Servizio concatenazione
Gli utenti possono configurare le tabelle di route definite dall'utente che puntano alle macchine virtuali in reti virtuali peered come "hop successivo" indirizzo IP, come illustrato nella figura più avanti in questo articolo. In questo modo gli utenti ottenere assistenza concatenazione, tramite cui possono reindirizzare il traffico da una rete virtuale a un dispositivo virtuale è in esecuzione in una rete virtuale peered tramite tabelle di route definite dall'utente.

Gli utenti possono inoltre efficace creare ambienti di tipo hub e raggio nel punto in cui l'hub può ospitare componenti dell'infrastruttura, ad esempio un dispositivo virtuale di rete. Tutte le reti virtuali raggio possono quindi peer con essa, nonché un sottoinsieme del traffico a dispositivi che eseguono la rete virtuale hub. In breve, VNet peering consente l'indirizzo IP di hop successivo nella 'tabella definita dall'utente route' sia l'indirizzo IP di una macchina virtuale nella rete virtuale peered.

## <a name="gateways-and-on-premises-connectivity"></a>Connettività di gateway e locale
Ogni virtuali, indipendentemente dal fatto che sia peered con un altro virtuali, possono ancora è il proprio gateway e usarlo per connettersi a locale. Gli utenti possono anche configurare [VNet-VNet connessioni](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) tramite gateway, anche se le reti virtuali sono peered.

Quando sono configurate entrambe le opzioni per interconnettività virtuali, il traffico tra le reti virtuali scorre tramite la configurazione peering (ovvero, tramite dorsale Azure).

Quando sono peered reti virtuali, gli utenti possono inoltre configurare il gateway nella rete virtuale peered come punto di transito nella distribuzione locale. In questo caso, la rete virtuale che utilizza un gateway remoto non è possibile specificare il proprio gateway. Una rete virtuale può contenere solo un gateway. Può essere un gateway locale o un gateway remoto (in peered rete virtuale), come illustrato nella figura seguente.

Transito gateway non è supportata in relazione peering tra reti virtuali utilizzando il modello di Manager delle risorse e quelle che utilizzano il modello di distribuzione classica. Entrambe le reti virtuali inclusi nella relazione peering necessario usare il modello di distribuzione Manager delle risorse per la trasmissione di un gateway.

Quando le reti virtuali che condividono una singola connessione Azure ExpressRoute sono peered, il traffico tra di esse attraversa la relazione peering (ovvero, tramite la rete dorsale Azure). Gli utenti possono comunque usare gateway locale in ogni virtuali a cui connettersi circuito locale. In alternativa, possono utilizzare un gateway condiviso e configurare transito per la connettività locale.

![Transito peering VNet](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Il provisioning
VNet peering è un'operazione con privilegi. È una funzione distinta nello spazio dei nomi VirtualNetworks. Un utente può essere specificato diritti specifici per autorizzare peering. Un utente che ha accesso in lettura / scrittura alla rete virtuale eredita questi diritti automaticamente.

Un utente che sia un amministratore o un utente con privilegi di possibilità peering possibile avviare un'operazione peering in un'altra VNet. Se è stata richiesta corrispondente per peering su altro lato e altri requisiti, il peering verranno stabilite.

Fare riferimento ad articoli nella sezione "Passaggi successivi" per ulteriori informazioni su come stabilire VNet peering tra due reti virtuale.

## <a name="limits"></a>Limiti
Esistono limiti al numero di peerings consentiti per una singola rete virtuale. Per ulteriori informazioni, consultare [limiti di reti Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Prezzi
VNet peering sarà gratuitamente durante il periodo di revisione. Dopo il rilascio, in ingresso e in uscita il traffico che utilizza la peering verrà applicata una tariffa nominale. Per ulteriori informazioni, vedere [prezzi pagina](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Passaggi successivi
- [Configurare peering tra reti virtuali](virtual-networks-create-vnetpeering-arm-portal.md).
- Informazioni sulle [NSGs](virtual-networks-nsg.md).
- Informazioni sulle [route definite dall'utente e inoltro IP](virtual-networks-udr-overview.md).
