<properties
   pageTitle="Supporto di Gateway WebSocket applicazioni | Microsoft Azure"
   description="Questa pagina offre una panoramica del supporto applicazione Gateway WebSocket."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Supporto di Gateway WebSocket applicazioni

Gateway di applicazioni fornisce supporto nativo per WebSocket tra tutte le dimensioni di gateway. Non esiste nessuna impostazione configurabile dall'utente per attivare o disattivare il supporto WebSocket. È possibile continuare a usare un HTTPListener standard all'indirizzo 80/443 per ricevere il traffico WebSocket. Il traffico WebSocket quindi è indirizzato al server di back-end abilitato WebSocket utilizzando il pool di back-end appropriati come specificato nelle regole di gateway di applicazione. Protocollo WebSocket standardizzato nella [RFC6455](https://tools.ietf.org/html/rfc6455) consente una comunicazione completa fronte retro tra client e server tramite una connessione TCP lunga. Questa caratteristica consente una comunicazione più interattiva tra server web e client, che può essere bidirezionale senza la necessità di polling come necessari in implementazioni basate su HTTP.  WebSocket dispone bassa sovraccarico diversamente da quanto succede HTTP e riutilizzare la stessa connessione TCP per più richiesta/risposte risultante in un utilizzo più efficiente delle risorse. I protocolli WebSocket sono progettati per essere attraverso le porte HTTP tradizionale di 80 e 443.

Il server di back-end deve rispondere a ricerche di gateway applicazione, descritte nella sezione [overview verifica dell'integrità](application-gateway-probe-overview.md) . Applicazione gateway integrità ricerche sono HTTP/HTTPS solo, ciò significa che tutti i server di back-end devono rispondere le ricerche HTTP per gateway di applicazioni instradare il traffico WebSocket al server.

## <a name="listener-configuration-element"></a>Elemento di configurazione di comunicare ascoltatore

HTTPListener esistente può essere utilizzato per supportare WebSocket. Di seguito è riportato un frammento dell'elemento HttpListeners da un file di modello di esempio. È necessario listener HTTP e HTTPS per supportare WebSocket e proteggere il traffico WebSocket. In modo analogo è possibile utilizzare il [portale](application-gateway-create-gateway-portal.md) e [PowerShell](application-gateway-create-gateway-arm.md) per creare un gateway di applicazioni con listener all'indirizzo 80/443 per supportare il traffico WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
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
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configurazione delle regole BackendAddressPool, BackendHttpSetting e percorso

BackendAddressPool dovrebbe essere usata per definire un pool di back-end con i server WebSocket abilitato. BackendHttpSetting devono essere definiti con back-end porta 80/443 solo. Proprietà affinità basate su cookie e requestTimeouts non sono i contenuti pertinenti per il traffico WebSocket. Non esiste nessuna modifica richiesta nella regola di routing. Regola di routing "Base" continuerà a essere utilizzata per collegare comunicare ascoltatore appropriato per il pool di indirizzi back-end corrispondente. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>Back-end WebSocket abilitata

Il back-end deve avere un server web HTTP/HTTPS nella proprietà configurata porta (in genere 80/443) per WebSocket per l'uso. Questo requisito è valido perché protocollo WebSocket richiede sincronizzazione iniziale da HTTP con l'aggiornamento a protocollo WebSocket come un campo di intestazione.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Un altro motivo è che verifica dell'integrità di applicazione gateway back-end supporta solo protocolli HTTP/HTTPS. Se il server di back-end non risponde a HTTP/HTTPS le ricerche, adottate tutte le potenzialità back-end pool e non le richieste incluse quelle WebSocket, potrebbero raggiungere questo back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo la formazione sulle WebSocket supportate, passare a [creare un gateway di applicazioni](application-gateway-create-gateway.md) per iniziare a utilizzare un'applicazione web WebSocket abilitato.
