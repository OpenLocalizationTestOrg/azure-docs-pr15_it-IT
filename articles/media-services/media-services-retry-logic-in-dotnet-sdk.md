<properties
    pageTitle="Riprovare logica in Media Services SDK per .NET | Microsoft Azure"
    description="L'argomento fornisce una panoramica della logica di Riprova in Media Services SDK di .NET."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Riprovare logica in Media Services SDK per .NET

Quando si lavora con i servizi di Microsoft Azure, possono verificarsi problemi temporanei. Se si verifica un errore temporaneo, nella maggior parte dei casi, dopo alcuni tentativi l'operazione ha avuto esito positivo. Media Services SDK per .NET implementata la logica riprova per gestire problemi temporanei associati eccezioni ed errori sono causati da richieste web, esecuzione di query, salvare le modifiche e le operazioni di archiviazione.  Per impostazione predefinita, Media Services SDK per .NET esegue quattro tentativi prima di generare nuovamente l'eccezione per l'applicazione. Il codice nell'applicazione quindi necessario gestire correttamente questa eccezione.  
  
 Di seguito è breve indicazione dei criteri di richiesta Web, lo spazio di archiviazione, Query e SaveChanges:  
  
-   Il criterio di archiviazione viene utilizzato per le operazioni di archiviazione blob (caricamento o download di file di risorse).  
  
-   Il criterio di richiesta Web viene utilizzato per le richieste di tipo generico web (ad esempio, per ottenere un token di autenticazione e la risoluzione dell'endpoint cluster di utenti).  
  
-   I criteri di Query viene utilizzato per eseguire la query entità dal resto (ad esempio mediaContext.Assets.Where(...)).  
  
-   Il criterio SaveChanges viene utilizzato per eseguire operazioni che modifica i dati all'interno del servizio (ad esempio la creazione di un'entità un'entità, la chiamata a una funzione di servizio per un'operazione di aggiornamento).  
  
 In questo argomento sono elencati i tipi di eccezione e i codici di errore che sono gestiti dal Media Services SDK per .NET riprovare logica.  
  
## <a name="exception-types"></a>Tipi di eccezione  

Nella tabella seguente descrive le eccezioni che Media Services SDK per .NET gestisce o non consente di gestire per alcune operazioni che possono causare problemi temporanei.  
  
Eccezione|Richiesta Web|Spazio di archiviazione|Query|SaveChanges
----|------|----|---|---
WebException<br/>Per ulteriori informazioni, vedere la sezione [codici di stato WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Sì|Sì|Sì|Sì  
DataServiceClientException<br/> Per ulteriori informazioni, vedere [codici di stato di errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sì|Sì|Sì
DataServiceQueryException<br/> Per ulteriori informazioni, vedere [codici di stato di errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sì|Sì|Sì  
DataServiceRequestException<br/> Per ulteriori informazioni, vedere [codici di stato di errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|No|Sì|Sì|Sì  
DataServiceTransportException|No|No|Sì|Sì
TimeoutException|Sì|Sì|Sì|No
SocketException|Sì|Sì|Sì|Sì  
StorageException|No|Sì|No|No 
Create|No|Sì|No|No
  
###  <a name="WebExceptionStatus"></a>Codici di stato WebException  

Nella tabella seguente mostra per i codici di errore WebException è implementata la logica di ripetizione. Enumerazione [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) definisce i codici di stato.  
  
Stato|Richiesta Web|Spazio di archiviazione|Query|SaveChanges  
-----|-----------------|-------------|-----------|----------  
Errore di connessione|Sì|Sì|Sì|Sì
NameResolutionFailure|Sì|Sì|Sì|Sì  
ProxyNameResolutionFailure|Sì|Sì|Sì|Sì  
SendFailure|Sì|Sì|Sì|Sì
PipelineFailure|Sì|Sì|Sì|No  
ConnectionClosed|Sì|Sì|Sì|No  
KeepAliveFailure|Sì|Sì|Sì|No  
UnknownError|Sì|Sì|Sì|No 
Errore di ricezione|Sì|Sì|Sì|No  
RequestCanceled|Sì|Sì|Sì|No  
Timeout|Sì|Sì|Sì|No
ProtocolError <br/>Riprova in ProtocolError verrà controllata gestendo il codice di stato HTTP. Per ulteriori informazioni, vedere [codici di stato di errore HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Sì|Sì|Sì|Sì|  
  
###  <a name="HTTPStatusCode"></a>Codici di stato di errore HTTP  

Quando le operazioni di Query e SaveChanges generano DataServiceClientException, DataServiceQueryException o DataServiceQueryException, viene restituito il codice di stato di errore HTTP nella proprietà StatusCode.  Nella tabella seguente mostra per i codici di errore è implementata la logica di ripetizione.  
  
 
Stato|Richiesta Web|Spazio di archiviazione|Query|SaveChanges 
---|----|----|----|----
401|No|Sì|No|No
403|No|Sì<br/>Gestione tentativi con più condizioni di attesa.|No|No  
408|Sì|Sì|Sì|Sì
429|Sì|Sì|Sì|Sì  
500|Sì|Sì|Sì|No  
502|Sì|Sì|Sì|No  
503|Sì|Sì|Sì|Sì  
504|Sì|Sì|Sì|No  
  
Se si desidera scrivere osservare l'esecuzione effettiva del Media Services SDK per .NET Riprova logica, vedere [azure sdk per media services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
