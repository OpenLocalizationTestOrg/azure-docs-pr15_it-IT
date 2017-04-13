<properties 
   pageTitle="Monitorare l'utilizzo e statistiche in un servizio di ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
   description="Tenere traccia delle dimensioni di consumi e indice delle risorse per la ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Monitorare l'utilizzo e le statistiche in un servizio di ricerca di Azure

Verifica la crescita di indici e le dimensioni del documento consentono di regolare tempestiva capacità prima di raggiungere il limite massimo stabilito per il servizio. 

Per monitorare l'utilizzo delle risorse, conteggi e le statistiche per il servizio facilmente vengono visualizzate nel [Portale di Azure](https://portal.azure.com), ma è inoltre possibile ottenere le informazioni a livello di programmazione se si sta creando uno strumento di amministrazione di servizio personalizzato. In questo articolo è descritta la procedura per entrambe le tecniche.

È anche possibile utilizzare la nuova caratteristica analitica il traffico di ricerca per informazioni dettagliate in attività a livello di indice. Visitare [Analitica il traffico di ricerca per la ricerca di Azure](search-traffic-analytics.md) per iniziare.

##<a name="view-counts-and-metrics-in-the-portal"></a>Visualizzare i conteggi e metriche nel portale 

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Aprire il dashboard di servizio del servizio di ricerca di Azure. I riquadri per il servizio possono essere trovati nella Home page o al servizio, è possibile passare da Sfoglia nella JumpBar. Per istruzioni dettagliate, vedere [creare un servizio](search-create-service-portal.md) .

Sezione uso include un indicatore di cui viene indicato quale parte delle risorse disponibili sono attualmente in uso.

  ![][1]

Tenere presente che il servizio condiviso con un massimo di una replica e partizione ciascuno. Inoltre, supporta 10.000 documenti solo in totale o 50 MB di dati, subiranno.

##<a name="get-index-statistics-using-the-rest-api"></a>Ottenere le statistiche di indice analitico utilizzando l'API REST

API REST ricerca Azure e .NET SDK fornire l'accesso alle metriche di servizio.  Se si utilizza [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) per caricare un indice da Database SQL Azure o DocumentDB, un'ulteriore API è disponibile per ottenere numeri di cui che si desidera. 

  + [Ottenere le statistiche di indice analitico](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Numero di documenti](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Ottenere lo stato dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Passaggi successivi

Rivedere le [capacità e limiti](search-limits-quotas-capacity.md) per determinare la combinazione di partizioni e repliche è necessario se non è sufficiente capacità esistente. 

Per ulteriori informazioni sull'amministrazione del servizio, vedere [gestire il servizio di ricerca in Microsoft Azure](search-manage.md) .

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
