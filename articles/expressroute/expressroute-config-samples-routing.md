<properties
   pageTitle="Esempi di configurazione router cliente ExpressRoute | Microsoft Azure"
   description="Questa pagina vengono forniti esempi di configurazione router per router Cisco e Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Esempi di configurazione router per configurare e gestire il routing

In questa pagina vengono interfaccia e dallo stesso routing esempi di configurazione per router serie Cisco IOS-XE e MX Juniper. Questi sono progettati per essere esempi a scopo informativo e non deve essere utilizzati come. È possibile lavorare con il fornitore per sorgere con configurazioni appropriate per la rete. 

>[AZURE.IMPORTANT] Negli esempi inclusi in questa pagina sono dovrebbero essere puramente per indicazioni. È necessario collaborare con il team di vendita / tecnica del fornitore e al team di rete sorgere con configurazioni appropriate in base alle esigenze. Microsoft non supporta i problemi relativi ai configurazioni elencate in questa pagina. Per problemi di supporto, è necessario contattare il fornitore del dispositivo.

Esempi di configurazione router che seguono si applicano a tutte le peerings. Esaminare [ExpressRoute peerings](expressroute-circuit-peerings.md) e i [requisiti di routing ExpressRoute](expressroute-routing.md) per ulteriori informazioni sul routing.

## <a name="cisco-ios-xe-based-routers"></a>XE con Cisco IOS in base a router

Negli esempi di questa sezione richiedere qualsiasi router con sistema operativo della famiglia OS XE IOS.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. configurazione interfacce e secondari

Si richiede un'interfaccia sub per peering in ciascun router che connettersi a Microsoft. Un'interfaccia sub può essere identificata con un ID VLAN o una coppia in pila di ID VLAN e un indirizzo IP.

#### <a name="dot1q-interface-definition"></a>Definizione dell'interfaccia Dot1Q

In questo esempio fornisce la definizione dell'interfaccia secondaria per l'interfaccia secondario con un ID VLAN. L'ID VLAN sia univoco per peering. L'ultimo ottetto dell'indirizzo IPv4 viene mantenuto un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Definizione dell'interfaccia QinQ

In questo esempio fornisce la definizione dell'interfaccia secondaria per l'interfaccia secondario con un ID VLAN due. L'ID VLAN esterno (s-tag), se utilizzato rimarrà invariato in tutti i peerings. L'ID VLAN interna (tag c) sia univoco per peering. L'ultimo ottetto dell'indirizzo IPv4 viene mantenuto un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. la configurazione di sessioni eBGP

Impostare una sessione BGP con Microsoft per ogni peering. Nell'esempio seguente consente di configurare una sessione BGP con Microsoft. Se l'indirizzo IPv4 che è stato utilizzato per l'interfaccia sub era a.b.c. d, l'indirizzo IP della risorsa adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 dell'adiacente BGP viene mantenuto un numero pari.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. impostazione prefissi da annunciare nella sessione BGP

È possibile configurare il router per annunciare prefissi select a Microsoft. È possibile eseguire questa operazione utilizzando l'esempio riportato di seguito.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. mappe route

È possibile utilizzare la distribuzione mappe e prefisso gli elenchi e prefissi filtro propagati alla rete aziendale. Nell'esempio seguente è possibile utilizzare per il completamento dell'attività. Assicurarsi di avere il programma di installazione di prefisso appropriato elenchi.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Router serie Juniper MX 

Negli esempi di questa sezione richiedere qualsiasi router serie Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. configurazione interfacce e secondari

#### <a name="dot1q-interface-definition"></a>Definizione dell'interfaccia Dot1Q

In questo esempio fornisce la definizione dell'interfaccia secondaria per l'interfaccia secondario con un ID VLAN. L'ID VLAN sia univoco per peering. L'ultimo ottetto dell'indirizzo IPv4 viene mantenuto un numero dispari.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Definizione dell'interfaccia QinQ

In questo esempio fornisce la definizione dell'interfaccia secondaria per l'interfaccia secondario con un ID VLAN due. L'ID VLAN esterno (s-tag), se utilizzato rimarrà invariato in tutti i peerings. L'ID VLAN interna (tag c) sia univoco per peering. L'ultimo ottetto dell'indirizzo IPv4 viene mantenuto un numero dispari.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. la configurazione di sessioni eBGP

Impostare una sessione BGP con Microsoft per ogni peering. Nell'esempio seguente consente di configurare una sessione BGP con Microsoft. Se l'indirizzo IPv4 che è stato utilizzato per l'interfaccia sub era a.b.c. d, l'indirizzo IP della risorsa adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 dell'adiacente BGP viene mantenuto un numero pari.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. impostazione prefissi da annunciare nella sessione BGP

È possibile configurare il router per annunciare prefissi select a Microsoft. È possibile eseguire questa operazione utilizzando l'esempio riportato di seguito.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. mappe route

È possibile utilizzare la distribuzione mappe e prefisso gli elenchi e prefissi filtro propagati alla rete aziendale. Nell'esempio seguente è possibile utilizzare per il completamento dell'attività. Assicurarsi di avere il programma di installazione di prefisso appropriato elenchi.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Passaggi successivi

Vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) per altri dettagli.
