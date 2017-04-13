<properties
   pageTitle="Più siti su Gateway applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del supporto di più siti di Gateway di applicazioni."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-multiple-site-hosting"></a>Gateway di applicazioni più hosting di siti

Hosting di siti più consente di configurare più di un'applicazione web nella stessa istanza di gateway di applicazione. Questa caratteristica consente di configurare una topologia più efficace per le distribuzioni mediante l'aggiunta di siti Web fino a 20 al gateway di un'applicazione. Ogni sito Web possono essere indirizzati al proprio pool di back-end. Nell'esempio seguente gateway applicazione serve il traffico verso contoso.com e fabrikam.com da due pool di server di back-end denominata ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Le richieste di http://contoso.com vengono instradate alle ContosoServerPool e http://fabrikam.com viene instradata alle FabrikamServerPool.

Analogamente due sottodomini dello stesso dominio padre possono essere ospitati nella stessa distribuzione gateway dell'applicazione. Esempi di utilizzo sottodomini possono includere http://blog.contoso.com e http://app.contoso.com ospitati in una distribuzione di gateway singola applicazione.

## <a name="host-headers-and-server-name-indication-sni"></a>Le intestazioni host e indicazione di nome Server (SNI)

Esistono tre meccanismi comuni per l'attivazione di hosting di più siti nella stessa infrastruttura.

1. Ospitare più applicazioni web ogni su un indirizzo IP univoco.
2. Consente di più applicazioni web sullo stesso indirizzo IP nome host.
3. Utilizzare porte diverse per più applicazioni web sullo stesso indirizzo IP.

Attualmente un gateway di applicazione ottiene un solo indirizzo IP pubblico su cui in attesa per il traffico. Che supporta più applicazioni, ognuna con il proprio indirizzo IP non è supportato. Gateway di applicazione supporta più applicazioni ogni porte diverse in ascolto ma questo scenario richiede le applicazioni per accettare il traffico sulle porte non standard e non è spesso una configurazione desiderata. Gateway di applicazioni si basa su intestazioni host HTTP 1.1 per ospitare più di un sito Web stesso indirizzo IP pubblico e porta. I siti ospitati in gateway applicazione inoltre possono offload SSL supporto con estensione TLS indicazione di nome Server (SNI). Questo scenario significa che la farm di web browser e back-end client deve supportare HTTP/1.1 ed estensione TLS come definito nella RFC 6066.

## <a name="listener-configuration-element"></a>Elemento di configurazione di comunicare ascoltatore

Elemento di configurazione HTTPListener esistente è stato migliorato per supportare host nome nome indicazione gli elementi e server, che viene utilizzato il gateway di applicazioni per instradare il traffico al pool di back-end appropriati. Nell'esempio seguente è il frammento di codice dell'elemento HttpListeners da file del modello.

    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener1",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                        "HostName": "contoso.com",
                        "RequireServerNameIndication": "true"
                    }
                },
                {
                    "name": "appGatewayHttpListener2",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                        "HostName": "fabrikam.com",
                        "RequireServerNameIndication": "false"
                    }
                }
            ],




È possibile visitare [il modello di gestione risorse utilizza l'hosting di siti più](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) per una distribuzione to end basato sul modello.

## <a name="routing-rule"></a>Regola di routing

Non esiste nessuna modifica richiesta la regola di routing. La regola di routing 'Basic' continueranno a scelta per collegare comunicare ascoltatore sito appropriato per il pool di indirizzi back-end corrispondente.

    "requestRoutingRules": [
    {
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    },
    {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }
    ]

## <a name="next-steps"></a>Passaggi successivi

Dopo la formazione sull'hosting più siti, passare a [creare un gateway di applicazioni utilizza l'hosting più siti](application-gateway-create-multisite-azureresourcemanager-powershell.md) per creare un gateway di applicazioni con possibilità di supportare più di un'applicazione web.
