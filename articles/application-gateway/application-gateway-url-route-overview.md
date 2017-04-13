<properties
   pageTitle="Panoramica del routing contenuto basato su URL | Microsoft Azure"
   description="Questa pagina offre una panoramica del routing contenuto basato su URL di Gateway di applicazione, configurazione UrlPathMap e PathBasedRouting regola."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="url-path-based-routing-overview"></a>Panoramica di Routing basato su URL percorso

In base a Routing degli URL percorso consente per instradare il traffico al pool di server di back-end in base a percorsi URL della richiesta. Uno degli scenari è per indirizzare le richieste per diversi tipi di contenuto per il pool di server di back-end diversi.
Nell'esempio seguente Gateway applicazione serve il traffico verso contoso.com da tre pool di server di back-end, ad esempio: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Le richieste per http://contoso.com/video* viene instradata alle VideoServerPool e http://contoso.com/images* vengono indirizzate ImageServerPool. Se nessuno dei modelli percorso corrisponde, DefaultServerPool sia selezionata.

## <a name="urlpathmap-configuration-element"></a>Elemento di configurazione UrlPathMap

Elemento UrlPathMap viene utilizzato per specificare i modelli di percorso per il mapping di pool di server di back-end. Nell'esempio seguente è il frammento di codice dell'elemento urlPathMap da file del modello.

    "urlPathMaps": [
    {
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
    }
    

>[AZURE.NOTE] PathPattern: Questa impostazione è riportato un elenco di modelli di percorso per la corrispondenza. Ogni deve iniziare con / e il luogo solo un "*" è consentito in quanto segue fine un "/". Stringa notte matcher percorso non include il testo eventualmente contenuto dopo la prima? o # e i caratteri non consentiti. 

È possibile estrarre un [modello di gestione risorse utilizzando routing basato su URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) per ulteriori informazioni.

## <a name="pathbasedrouting-rule"></a>Regola PathBasedRouting

RequestRoutingRule di tipo PathBasedRouting viene utilizzato per associare un comunicare ascoltatore a un urlPathMap. Tutte le richieste ricevuti per comunicare questo ascoltatore vengono indirizzate in base ai criteri specificati in urlPathMap.
Frammento di PathBasedRouting regola:

    "requestRoutingRules": [
    {

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
    
## <a name="next-steps"></a>Passaggi successivi

Dopo l'apprendimento basato su URL distribuzione del contenuto, passare a [creare un gateway di applicazione tramite routing basato su URL](application-gateway-create-url-route-portal.md) per creare un gateway di applicazioni con regole di routing URL.
