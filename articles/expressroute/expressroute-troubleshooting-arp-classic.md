<properties
   pageTitle="Guida alla risoluzione dei problemi di ExpressRoute: Guida ARP tabelle | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per ottenere il ARP tabelle per un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guida alla risoluzione dei problemi di ExpressRoute: Guida ARP tabelle nel modello di distribuzione classica

> [AZURE.SELECTOR]
[PowerShell - Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classico](expressroute-troubleshooting-arp-classic.md)

In questo articolo vengono illustrati i passaggi per ottenere le tabelle di risoluzione ARP (Address Protocol) per il circuito Azure ExpressRoute.

>[AZURE.IMPORTANT] In questo documento è destinato per diagnosticare e risolvere i problemi di semplice. Non è deve essere pensata come sostitutivo di supporto tecnico Microsoft. Se è possibile risolvere il problema usando le linee guida seguenti, aprire una richiesta di assistenza con [la Guida di Microsoft Azure + supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Indirizzo risoluzione protocollo ARP () e ARP tabelle
ARP è un protocollo di livello 2 definiti nel [RFC 826](https://tools.ietf.org/html/rfc826). ARP viene utilizzato per eseguire il mapping un Ethernet indirizzo (MAC) a un indirizzo IP.

Una tabella ARP fornisce un mapping dell'indirizzo IPv4 e MAC per un particolare peering. La tabella ARP di un circuito ExpressRoute peering fornisce le informazioni seguenti per ogni interfaccia (primario e secondario):

1. Mapping di un indirizzo IP di locale router interfaccia a un indirizzo MAC
2. Mapping di un indirizzo IP di ExpressRoute router interfaccia a un indirizzo MAC
3. L'età del mapping

Tabelle ARP consente di convalida della configurazione di livello 2 e sulla risoluzione dei problemi di connettività di livello 2 base.

Di seguito è illustrato un esempio di una tabella ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La sezione seguente fornisce informazioni su come visualizzare le tabelle ARP che siano visibili dai router ExpressRoute bordo.

## <a name="prerequisites-for-using-arp-tables"></a>Prerequisiti per l'utilizzo di tabelle ARP

Verificare di disporre le operazioni seguenti prima di continuare:

 - Circuito ExpressRoute valido configurata con almeno un peering. Il circuito deve essere completamente configurato dal provider di integrazione applicativa. Si (o il provider di integrazione applicativa) necessario configurare almeno un peerings (pubblico privato, Azure Azure o Microsoft) su questo circuito.

 - Intervalli di indirizzi IP utilizzati per la configurazione peerings (pubblico privato, Azure Azure e Microsoft). Esaminare gli esempi di assegnazione indirizzo IP nella [pagina requisiti routing ExpressRoute](expressroute-routing.md) per ottenere la comprensione del mapping di indirizzi IP e le interfacce il aise e al lato ExpressRoute. È possibile ottenere informazioni sulla configurazione peering consultando l'argomento della [pagina Configurazione peering ExpressRoute](expressroute-howto-routing-classic.md).

 - Informazioni dal provider del team o connettività di rete indirizzi MAC le interfacce vengono utilizzate con questi indirizzi IP.

 - Modulo di Windows PowerShell più recente per Azure (1,50 o versione successiva).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelle ARP per il circuito ExpressRoute
In questa sezione vengono fornite istruzioni su come visualizzare le tabelle ARP per ogni tipo di peering tramite PowerShell. Prima di continuare, è necessario configurare i peering l'utente o il provider di integrazione applicativa. Ogni circuito include due percorsi (primari e secondari). È possibile controllare la tabella ARP per ogni percorso in modo indipendente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelle ARP per peering privato Azure
Il seguente cmdlet prevede l'ARP tabelle Azure peering privato:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Di seguito è riportato output di esempio per uno dei percorsi:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelle di ARP per Azure peering pubblico:
Il seguente cmdlet prevede l'ARP tabelle Azure peering pubblico:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Di seguito è riportato output di esempio per uno dei percorsi:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Di seguito è riportato output di esempio per uno dei percorsi:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelle ARP per Microsoft peering
Il cmdlet seguente vengono fornite le ARP tabelle per peering Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Output di esempio è illustrato di seguito per uno dei percorsi:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Come usare queste informazioni
La tabella ARP di un peering può essere utilizzata per convalidare la connettività e configurazione di livello 2. In questa sezione viene fornita una panoramica dell'aspetto delle tabelle ARP in scenari diversi.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabella ARP quando un circuito è in stato operativo (previsto)

 - La tabella ARP dispone di una voce per il lato locale con un indirizzo IP e MAC valido e una voce simile per il lato Microsoft.
 - L'ultimo ottetto dell'indirizzo IP locale è sempre un numero dispari.
 - L'ultimo ottetto dell'indirizzo IP Microsoft è sempre un numero pari.
 - Sul lato Microsoft per tutte le tre peerings (primario e secondario) viene visualizzato lo stesso indirizzo MAC.


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabella ARP quando è composto locale o quando il lato provider di integrazione applicativa con problemi a livello

 Nella tabella ARP viene visualizzata solo una voce. Viene illustrato il mapping tra l'indirizzo MAC e l'indirizzo IP utilizzato sul lato Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Se si verifica un problema …, aprire una richiesta di assistenza con il provider di integrazione applicativa per risolvere il problema.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabella ARP quando lato Microsoft con problemi a livello

 - Non verrà visualizzata una tabella ARP visualizzata per un peering se sono presenti problemi sul lato Microsoft.
 -  Aprire una richiesta di assistenza con [la Guida di Microsoft Azure + supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Specificare che si verifica un problema con la connettività di livello 2.

## <a name="next-steps"></a>Passaggi successivi

 - Convalidare le configurazioni di livello 3 per il circuito ExpressRoute:
     - È possibile ottenere una route di riepilogo per determinare lo stato delle sessioni BGP.
     - È possibile ottenere una tabella di route per determinare quali prefissi vengono inseriti in ExpressRoute.
 - Convalidare i trasferimenti di dati consultando byte avanti e indietro.
 - Aprire una richiesta di assistenza con [la Guida di Microsoft Azure + supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se si verificano ancora problemi.
