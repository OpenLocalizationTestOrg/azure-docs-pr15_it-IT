<properties 
    pageTitle="Come usare la caratteristica controllo API tracciare chiamate in Azure API Management" 
    description="Informazioni su come tenere traccia delle chiamate tramite il controllo dell'API in Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Come usare la caratteristica controllo API tracciare chiamate in Azure API Management

Gestione dell'API è uno strumento di controllo API per agevolare il debug e risoluzione dei problemi l'API. Controllo API può essere utilizzato a livello di programmazione e possono anche essere utilizzato direttamente dal portale per sviluppatori. 

Oltre alle operazioni di analisi, controllo API analizza anche le valutazioni di [espressione di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Per una dimostrazione, vedere [177 episodio coprire Cloud: altre funzionalità di gestione dell'API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e invece 21:00.

Questa guida offre una panoramica dell'uso di API controllo.

>[AZURE.NOTE] API controllo tracce vengono generate solo e resi disponibili per le richieste contenenti chiavi abbonamento che appartengono all'account [administrator](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Usa controllo API per tenere traccia di una chiamata

Per utilizzare controllo API, aggiungere un **ocp apim traccia: true** richiedere l'intestazione per le chiamate in operazione, quindi scaricare e controllare la traccia mediante l'URL indicato dalla **posizione traccia di apim ocp** ombreggiatura carattere. Può essere eseguita a livello di programmazione e possono essere eseguita anche direttamente dal portale di sviluppo.

In questa esercitazione viene illustrato come utilizzare il controllo dell'API alle operazioni di analisi mediante le API calcolatrice configurati nell'esercitazione introduttiva recupero [Gestisci l'API prima](api-management-get-started.md) . Se è stata ancora completata tale esercitazione richiede solo alcuni istanti per importare le API calcolatrice oppure è possibile utilizzare un'altra API di propria scelta, ad esempio l'API eco. Ogni istanza del servizio di gestione delle API include preconfigurato con un'API eco che può essere utilizzato per sperimentare e informazioni sulla gestione dei API. L'API eco restituisce nuovamente qualsiasi input inviati. Per utilizzarla, è possibile richiamare qualsiasi verbo HTTP e il valore restituito sarà semplicemente ciò che è stato inviato. 



Per iniziare, fare clic su **portale per sviluppatori** nel portale Azure classico per il servizio di gestione dell'API. Operazioni possono essere chiamate direttamente dal portale di sviluppo che consente di visualizzare e testare le operazioni di un'API.

>Se è ancora stata creata un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

![Portale di gestione API per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** dal menu superiore e quindi fare clic su **Calcolo di base**.

![API eco][api-management-api]

Fare clic su **Provalo** per ripetere l'operazione di **aggiunta di due numeri interi** .

![Provare a][api-management-open-console]

Mantenere l'impostazione predefinita i valori dei parametri e selezionare la chiave di abbonamento per il prodotto che si desidera utilizzare dalla **sottoscrizione chiave** elenco a discesa.

Per impostazione predefinita nel portale di sviluppo **Ocp Apim traccia** intestazione è già impostata su **true**. Questa intestazione consente di configurare una traccia viene generata o meno.

![Invia][api-management-http-get]

Fare clic su **Invia** per richiamare l'operazione.

![Invia][api-management-send-results]

Nella risposta intestazioni sarà un **percorso traccia di apim ocp** con un valore simile al seguente.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

L'analisi può essere scaricato dalla posizione specificata ed esaminata come illustrato nel passaggio successivo.

## <a name="inspect-trace"> </a>Esaminare la traccia

Per esaminare i valori nella traccia, scaricare il file di traccia da URL **percorso traccia di apim ocp** . È un file di testo in formato JSON e contiene voci simili all'esempio seguente.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Passaggi successivi

-   Offre una dimostrazione di individua le espressioni di criteri in [Cloud coprire episodio 177: altre funzionalità di gestione dell'API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Passare rapidamente a 21:00 per visualizzare la demo.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 