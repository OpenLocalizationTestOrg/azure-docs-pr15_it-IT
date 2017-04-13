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

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>Esempi di configurazione router per configurare e gestire NAT

Questa pagina vengono forniti esempi di configurazione NAT per router serie ASA Cisco e SRX Juniper. Questi sono progettati per essere esempi a scopo informativo e non deve essere utilizzati come. È possibile lavorare con il fornitore per sorgere con configurazioni appropriate per la rete. 

>[AZURE.IMPORTANT] Negli esempi inclusi in questa pagina sono dovrebbero essere puramente per indicazioni. È necessario collaborare con il team di vendita / tecnica del fornitore e al team di rete sorgere con configurazioni appropriate in base alle esigenze. Microsoft non supporta i problemi relativi ai configurazioni elencate in questa pagina. Per problemi di supporto, è necessario contattare il fornitore del dispositivo.

Esempi di configurazione router che seguono si applicano a peerings Azure pubbliche e a Microsoft. Non è necessario configurare NAT per peering privato Azure. Esaminare [requisiti NAT ExpressRoute](expressroute-nat.md) e [peerings ExpressRoute](expressroute-circuit-peerings.md) per altri dettagli.

**Nota:** È necessario utilizzare pool IP NAT diversi per la connessione a internet ed ExpressRoute. Con lo stesso pool IP NAT su internet oppure ExpressRoute genereranno asimmetrici routing e perdita di connettività.

## <a name="cisco-asa-firewalls"></a>Firewall ASA Cisco

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configurazione di Elena per il traffico di rete dei clienti a Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configurazione di Elena per il traffico da Microsoft alla rete dei clienti

#### <a name="interfaces-and-direction"></a>Interfacce e la direzione:
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>Configurazione:
Pool NAT:

    object network outbound-PAT
        host <NAT-IP>

Server di destinazione:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Gruppo di oggetti per gli indirizzi IP cliente

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Comandi NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Router di serie SRX Juniper 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. creare ridondanti interfacce Ethernet per il cluster

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. creare due aree di protezione

 - Area di protezione per rete interna e incluso nell'area per la rete esterna affiancate router bordo
 - Assegnare interfacce appropriate alle aree
 - Consentire servizi sulle interfacce


    protezione {zone {area di protezione protezione {host in ingresso-traffico {-servizi di sistema {ping;                  } protocolli {bgp;                  interfacce}} {reth0.100;              }} area di protezione incluso {host in ingresso-traffico {-servizi di sistema {ping;                  } protocolli {bgp;                  interfacce}} {reth1.100;              }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3. creare criteri di sicurezza tra le aree
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. configurare i criteri di NAT
 - Creare due pool NAT. Uno da utilizzare per il traffico NAT in uscita a Microsoft e altre risorse al cliente.
 - Creare regole per NAT il traffico rispettivo

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. configurare BGP per annunciare prefissi selettivi in entrambe le direzioni

Fare riferimento a esempi in pagina [degli esempi di configurazione di Routing](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. creare criteri

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Passaggi successivi

Vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) per altri dettagli.
