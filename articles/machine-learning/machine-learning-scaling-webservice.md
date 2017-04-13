<properties
   pageTitle="Ridimensionamento di servizio web | Microsoft Azure"
   description="Informazioni su come ridimensionare un servizio web aumentando concorrenza e aggiunta di nuovi endpoint."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure apprendimento automatico, servizi web, operationalization, il ridimensionamento, endpoint, concorrenza"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Ridimensionamento di un servizio Web

>[AZURE.NOTE] In questo argomento vengono descritte le tecniche applicabili a un servizio Web di apprendimento Machine classica. 

Per impostazione predefinita, ogni servizio Web pubblicato è configurato per supportare 20 richieste simultanee e può essere più alto 200 richieste simultanee. Mentre il portale classico Azure consente di impostare questo valore, apprendimento Azure ottimizza automaticamente l'impostazione per offrire le massime prestazioni per il servizio web e il valore portale viene ignorato. 

Se si prevede di chiamare l'API con un carico maggiore rispetto a un valore di chiamate simultanee numero massimo di 200 supporterà, è necessario creare più endpoint del servizio Web stesso. È possibile in modo casuale distribuire il carico tra tutti i bordi.

## <a name="add-new-endpoints-for-same-web-service"></a>Aggiungere nuovi endpoint per stesso servizio web

Ridimensionamento di un servizio Web è un'attività comune. Alcuni motivi per ridimensionare sono e supporta più di 200 richieste simultanee, aumentare la disponibilità tramite più endpoint o fornire endpoint separato per il servizio web. È possibile aumentare la scala mediante l'aggiunta di altri endpoint del servizio Web stesso tramite [Azure portal classico](https://manage.windowsazure.com/) o il portale del [Servizio Web di Azure Machine Learning](https://services.azureml.net/) .

Per ulteriori informazioni sull'aggiunta di nuovi endpoint, vedere [Creazione di endpoint](machine-learning-create-endpoint.md).

Tenere presente che con un numero elevato concorrenza può essere negativo se non si sta chiamando l'API con una frequenza elevata. È possibile che timeout sporadiche e/o picchi la latenza se si inserisce un carico relativamente basso in un'API configurata per condizioni di carico elevato.

Icona del API utilizzate in genere in situazioni in cui si desidera una latenza bassa. Latenza qui implica il tempo impiegato per l'API completare una richiesta e non includere le eventuali ritardi di rete. Supponiamo che è disponibile un'API con una latenza 50 ms. Per utilizzare completamente la capacità disponibile con il livello di limitazione ad alta e le chiamate simultanee Max = 20, è necessario chiamare l'API 20 * 1000 / 50 = 400 volte al secondo. Si estende questa ulteriormente, un numero massimo di chiamate simultanee 200 consente di chiamare volte 4000 API al secondo, presupponendo che una latenza 50 ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
