<properties
   pageTitle="Panoramica della disponibilità configurazioni con gateway VPN Azure | Microsoft Azure"
   description="In questo articolo viene fornita una panoramica delle opzioni di configurazione disponibilità usando gateway VPN Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Disponibilità tra locale e la connettività di VNet-VNet

In questo articolo viene fornita una panoramica delle opzioni di configurazione disponibilità per il cross locale e la connettività VNet a VNet tramite gateway VPN Azure.

## <a name = "activestandby"></a>Sulle ridondanza gateway VPN Azure

Ogni gateway VPN Azure è costituito da due istanze di una configurazione standby attivo. Per ogni interventi di manutenzione pianificata o interruzione non pianificata che succede all'istanza attiva, l'istanza standby subentrare (failover) automaticamente e riprendere la S2S VPN o connessioni VNet a VNet. Il parametro su si verificherà una breve interruzione. Per la manutenzione pianificata, la connettività dovrebbe essere ripristinata entro 10-15 secondi. Per problemi non pianificati, il ripristino di connessione sarà più lungo, circa un minuto a 1 e mezzo minuti nel caso peggiore. Per le connessioni client VPN P2S al gateway, le connessioni P2S saranno disconnessi e gli utenti dovranno riconnettersi dal computer client.

![Standby attivo](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Connettività locale tra disponibilità

Per fornire migliore disponibilità per le connessioni tra locale, sono disponibili due opzioni:

- Più dispositivi VPN locale
- Gateway di Azure VPN attivo attivo
- Combinazione di entrambi

### <a name = "activeactiveonprem"></a>Più dispositivi VPN locale

È possibile utilizzare più dispositivi VPN alla rete locale per la connessione al gateway VPN Azure, come illustrato nella figura seguente:

![Più locale VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Questa configurazione fornisce più tunnel attivi dal gateway VPN Azure stesso per i dispositivi in locale nella stessa posizione. Esistono alcuni requisiti e vincoli:

1. È necessario creare più connessioni VPN S2S dai dispositivi VPN in Azure. Quando ci si connette più dispositivi VPN dalla stessa rete locale in Azure, è necessario creare un gateway di rete locale per ogni dispositivo VPN e una connessione da gateway VPN Azure al gateway di rete locale.

2. Il gateway di rete locale corrispondente per i dispositivi VPN deve avere indirizzi IP pubblici univoci nella proprietà "GatewayIpAddress".

3. BGP è necessario per la configurazione. Ogni gateway di rete locale che rappresenta un dispositivo VPN deve disporre BGP peer l'indirizzo IP specificato nella proprietà "BgpPeerIpAddress".

4. Il campo proprietà AddressPrefix in ogni gateway di rete locale deve non si sovrappongono. È necessario specificare "BgpPeerIpAddress" in /32 formato CIDR nel campo AddressPrefix, ad esempio 10.200.200.254/32.

5. È necessario utilizzare BGP per pubblicizzare la stessa prefissi dello stesso locale prefissi di rete per il gateway VPN Azure e il traffico verrà inoltrato tramite questi tunnel contemporaneamente.

6. Ogni connessione viene conteggiata rispetto al numero massimo di tunnel per il gateway VPN Azure, 10 per SKU Standard e Basic e 30 per HighPerformance SKU. 

In questa configurazione, il gateway VPN Azure è ancora in modalità standby attivo, in modo che lo stesso failover comportamento e breve interruzione si verifica come descritto [sopra](#activestandby). Ma il programma di installazione protegge errori o interruzioni in rete locale e dispositivi VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Gateway di Azure VPN attivo attivo

È ora possibile creare un gateway VPN Azure in una configurazione attivo attivo in entrambe le istanze di gateway macchine virtuali definiranno tunnel VPN S2S al dispositivo VPN locale, come illustrato nel diagramma seguente:

![Attivo attivo](./media/vpn-gateway-highlyavailable/active-active.png)

In questa configurazione, ogni istanza di Azure gateway avrà un indirizzo IP pubblico univoco e ogni definiranno un tunnel IPsec/IKE S2S VPN al dispositivo VPN locale specificato nel gateway di rete locale e la connessione. Si noti che entrambi tunnel VPN fanno parte della stessa connessione. Sarà comunque necessario configurare il dispositivo VPN locale per accettare o stabilire due tunnel VPN S2S a questi due Azure VPN gateway indirizzi IP.

Poiché le istanze di gateway Azure configurazione attivo attivo, il traffico di rete virtuale Azure alla rete locale instradato entrambi Galleria contemporaneamente, anche se il dispositivo VPN locale può favorire un tunnel ciascuna di esse. Nota Se lo stesso flusso TCP o UDP sarà sempre scorrere le stesse tunnel percorso, a meno che non si verifica un evento di manutenzione su una delle istanze.

Quando una manutenzione pianificata o un evento imprevisto accade all'istanza di un gateway, il gateway da quell ' istanza dispositivo VPN locale saranno disconnessi. Indirizza corrispondente nei dispositivi VPN eliminato o ritirata automaticamente in modo che il traffico passa a altri tunnel IPsec attivi. Sul lato Azure, il passaggio verrà eseguito automaticamente dall'istanza interessato per l'istanza attiva.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Doppio ridondanza: gateway VPN attivo attivo per le reti di Azure e locale

L'opzione più affidabile consiste nel combinare i gateway attivo attivo nella rete e Azure, come illustrato nella figura seguente.

![Immagine del doppio fuso ridondanza](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Creare e configurare il gateway VPN Azure in una configurazione attivo attivo e creare due gateway di rete locale e due connessioni per i due locale dispositivi VPN come descritto in precedenza. Il risultato è una perfettamente la connettività di 4 tunnel IPsec tra la rete virtuale Azure e la rete locale.

Tutti i gateway e tunnel sono attivi relativi al lato Azure, in modo che il traffico verrà distribuito tra 4 tunnel tutte contemporaneamente, anche se ogni flusso TCP o UDP seguiranno nuovamente il tunnel stesso o il percorso dal lato Azure. Anche se per la distribuzione del traffico, venga visualizzato leggermente migliori velocità su tunnel IPsec, lo scopo principale della configurazione è per la disponibilità elevata. E della diffusione la natura statistiche difficile da fornire che la misurazione funzionamento di diversi condizioni di traffico applicazione viene influenzata dalla velocità di aggregata.

Questa topologia richiederà due gateway di rete locale e due connessioni per supportare la coppia di dispositivi VPN locale e BGP è necessario per consentire le due connessioni alla stessa rete locale. Questi requisiti sono le stesse l' [sopra](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Disponibilità connettività VNet a VNet tramite gateway VPN Azure

La stessa configurazione attivo attivo inoltre possibile applicare alle connessioni Azure VNet a VNet. È possibile creare gateway VPN attivo attivo per entrambe le reti virtuali e connetterle insieme alla forma la stessa connettività perfettamente di 4 tunnel tra due VNets, come illustrato nella figura seguente:

![VNet-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

In questo modo ci sono sempre una coppia di tunnel tra le due reti virtuale per tutti gli eventi interventi di manutenzione pianificata, disponibilità ancora migliore. Anche se la stessa topologia per tra locale connettività richiede due connessioni, la topologia di VNet-VNet sopra sarà necessario solo una connessione per ogni gateway. Inoltre, BGP è facoltativo, a meno che non transito il routing tramite la connessione VNet-VNet è necessario.


## <a name="next-steps"></a>Passaggi successivi

Vedere [Configurazione di gateway VPN attivo attivo per le connessioni VNet a VNet e Cross-locale](vpn-gateway-activeactive-rm-powershell.md) per la procedura per configurare attivo attivo tra locale e connessioni VNet a VNet.
