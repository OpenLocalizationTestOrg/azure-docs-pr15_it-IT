<properties 
   pageTitle="Guida alla risoluzione dei problemi ExpressRoute - recupero delle tabelle ARP | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni su come ottenere il ARP tabelle per un circuito ExpressRoute"
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

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guida per la risoluzione dei problemi di ExpressRoute - tabelle Guida ARP nel modello di distribuzione Manager delle risorse

> [AZURE.SELECTOR]
[PowerShell - Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classico](expressroute-troubleshooting-arp-classic.md)

In questo articolo vengono illustrati i passaggi per scoprire tabelle ARP per il circuito ExpressRoute. 

>[AZURE.IMPORTANT] In questo documento è destinato per diagnosticare e risolvere i problemi di semplice. Non è deve essere pensata come sostitutivo di supporto tecnico Microsoft. Se non si riesce a risolvere il problema seguendo le indicazioni fornite descritta di seguito, è necessario aprire un ticket di supporto con [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Indirizzo risoluzione protocollo ARP () e ARP tabelle
Risoluzione ARP (Address Protocol) è un protocollo di livello 2 definito nel [RFC 826](https://tools.ietf.org/html/rfc826). ARP viene utilizzato per eseguire il mapping di Ethernet indirizzo (MAC) con un indirizzo ip.

La tabella ARP fornisce un mapping dell'indirizzo ipv4 e MAC per un particolare peering. La tabella ARP per un circuito ExpressRoute peering contiene le informazioni seguenti per ogni interfaccia (primario e secondario)

1. Mapping di indirizzo ip di interfaccia locale router all'indirizzo MAC
2. Mapping di indirizzi ip di ExpressRoute router interfaccia all'indirizzo MAC
3. Età del mapping

Tabelle ARP consigliabile eseguirla per convalidare la configurazione di livello 2 e risoluzione dei problemi di base di livello 2 problemi di connettività. 

Tabella di esempio ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La sezione seguente fornisce informazioni su come è possibile visualizzare le tabelle ARP visualizzate router ExpressRoute bordo. 

## <a name="prerequisites-for-learning-arp-tables"></a>Prerequisiti per l'apprendimento di tabelle ARP

Verificare di disporre le operazioni seguenti prima sull'avanzamento ulteriormente

 - Circuito ExpressRoute valido configurato con almeno un peering. Il circuito deve essere completamente configurato dal provider di integrazione applicativa. Si (o il provider di integrazione applicativa) che sia configurata almeno uno dei peerings (pubblico privato, Azure Azure e Microsoft) su questo circuito.
 - Intervalli di indirizzi IP utilizzati per la configurazione peerings (pubblico privato, Azure Azure e Microsoft). Esaminare gli esempi di assegnazione indirizzo ip nella [pagina requisiti routing ExpressRoute](expressroute-routing.md) per ottenere la comprensione del mapping di indirizzi ip e interfacce sul lato "i" e sul lato ExpressRoute. È possibile ottenere informazioni sulla configurazione di peering consultando l'argomento della [pagina Configurazione peering ExpressRoute](expressroute-howto-routing-arm.md).
 - Informazioni dal team di rete / provider di servizi di integrazione applicativa sugli indirizzi MAC delle interfacce usato con questi indirizzi IP.
 - È necessario disporre del modulo PowerShell per più recente per Azure (versione 1,50 o versioni successive).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Recupero di ARP tabelle per il circuito ExpressRoute
In questa sezione fornisce istruzioni su come è possibile visualizzare le tabelle ARP per peering tramite PowerShell. Si o il provider di integrazione applicativa che sia configurata peering prima dell'elaborazione ulteriormente. Ogni circuito include due percorsi (primari e secondari). È possibile controllare la tabella ARP per ogni percorso in modo indipendente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelle ARP per peering privato Azure
Il seguente cmdlet prevede l'ARP tabelle Azure peering privato

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Output di esempio è illustrato di seguito per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelle ARP per peering pubblica Azure
Il seguente cmdlet fornisce il ARP tabelle per peering pubblica Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Output di esempio è illustrato di seguito per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelle ARP per Microsoft peering
Il seguente cmdlet fornisce il ARP tabelle per Microsoft peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Output di esempio è illustrato di seguito per uno dei percorsi

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Come usare queste informazioni
La tabella ARP di un peering può essere utilizzata per determinare convalidare configurazione di livello 2 e la connettività. In questa sezione viene fornita una panoramica dell'aspetto tabelle ARP in scenari diversi.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabella ARP quando un circuito è in stato operativo (stato previsto)

 - La tabella ARP avrà una voce per il lato locale con un indirizzo IP e l'indirizzo MAC e una voce simile per il lato Microsoft. 
 - L'ultimo ottetto dell'indirizzo ip locale sarà sempre un numero dispari.
 - L'ultimo ottetto dell'indirizzo ip Microsoft viene mantenuto un numero pari.
 - Lo stesso indirizzo MAC verrà visualizzato sul lato Microsoft per tutto 3 peerings (principale / secondaria). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabella ARP locale / lato provider la connettività con problemi a livello

 - Verrà visualizzata solo una voce nella tabella ARP. Questa operazione verrà visualizzato il mapping tra l'indirizzo MAC e l'indirizzo IP utilizzato sul lato Microsoft. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Aprire una richiesta di assistenza con il provider di integrazione applicativa per eseguire il debug di questi problemi. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabella ARP quando lato Microsoft con problemi a livello

 - Non verrà visualizzata una tabella ARP visualizzata per un peering se sono presenti problemi sul lato Microsoft. 
 -  Aprire un ticket di supporto con [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Specificare che si verifica un problema con la connettività di livello 2. 

## <a name="next-steps"></a>Passaggi successivi

 - Convalidare le configurazioni di livello 3 per il circuito ExpressRoute
     - Ottenere la distribuzione riepilogo per determinare lo stato delle sessioni BGP 
     - Ottenere la tabella route per determinare quali prefissi vengono inseriti in ExpressRoute
 - Convalidare i trasferimenti di dati consultando byte in / out
 - Se si verificano ancora problemi, aprire un ticket di supporto con [supporto Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
