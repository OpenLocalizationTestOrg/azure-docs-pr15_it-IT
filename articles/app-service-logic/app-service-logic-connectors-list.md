<properties
    pageTitle="Elenco di disponibili connettori e le API App | Servizio di Microsoft Azure App"
    description="Informazioni sui connettori e App API nel servizio App Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Elenco dei connettori e le API app da usare nelle applicazioni logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema. Per la versione di logica App generale disponibilità (GA), vedere [Nuovo elenco di connettori](../connectors/apis-list.md).

Informazioni sui tutti i connettori disponibili e App API creato da Microsoft per usare l'app di logica.

Per informazioni sui prezzi e un elenco di cosa è incluso in ogni livello di servizio, vedere [Azure App servizio prezzi](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Per iniziare a usare App logica prima di iscriversi a un account Azure, passare [all'App di logica di prova](https://tryappservice.azure.com/?appservice=logic). È possibile creare un'app di logica starter breve immediatamente nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="core-connectors"></a>Connettori core
Nella tabella seguente elenca tutti i connettori disponibili e App API creato da Microsoft sono disponibili come connettori principali:

Nome | Descrizione
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utilizzare Bing per tradurre il testo in un'altra lingua.
[HTTP](app-service-logic-connector-http.md) | Comunicare ascoltatore HTTP viene aperto un endpoint che funge da un server HTTP e ascolta le richieste in arrivo HTTP o HTTPS. L'azione HTTP non richiede un'App API ed è supportato a livello nativo all'interno di logica di App.
[Margine di flessibilità](app-service-logic-connector-slack.md) | Connettersi a un margine di flessibilità e inviare messaggi al margine di flessibilità canali.


## <a name="enterprise-integration-connectors"></a>Connettori integrazione Enterprise
Nella tabella seguente elenca tutti i connettori e App API creato da Microsoft disponibili come Enterprise integrazione connettori disponibili:

Nome  | Descrizione
------------- | -------------
[Regole di BizTalk](app-service-logic-use-biztalk-rules.md) | Usare le regole di BizTalk per definire e controllare la regola business all'interno dell'organizzazione. È possibile aggiornare i criteri aziendali senza ricompilare o senza ridistribuire applicazioni associate.
[Estrazione da XPath BizTalk](app-service-logic-xpath-extract.md) | Cerca ed estrae i dati dal contenuto XML in base a un'espressione XPath scelto.
[Connettore DB2](app-service-logic-connector-db2.md) | Si connette a un IBM DB2 database locale e in un computer virtuale Azure con sistema operativo Windows. Associare operazioni API Web e API OData ai comandi di Informix Structured Query Language. <br/><br/>Nessun trigger. Azioni includono tabella selezionare, Inserisci, Aggiorna, Elimina e istruzione personalizzata<br/><br/>Il connettore include anche il Client Microsoft per DRDA per connettersi a un server di Informix in una rete TCP/IP.
[File](app-service-logic-connector-file.md) | Usa il connettore, è possibile connettersi al sistema di file locale o rete e attività completo file diverso, inclusi il caricamento, l'eliminazione, Elenca i file e così via.
[Informix](app-service-logic-connector-informix.md) | Si connette a un database IBM Informix, in locale e in un computer virtuale Azure con sistema operativo Windows. Associare operazioni API Web e API OData ai comandi di Informix Structured Query Language.<br/><br/>Nessun trigger. Azioni includono tabella selezionare Inserisci, Aggiorna, Elimina e istruzione personalizzata.<br/><br/>Quando si usa locale, è possibile utilizzare VPN o Azure ExpressRoute. Il connettore include anche un Client Microsoft per DRDA per connettersi a un server di Informix in una rete TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Si connette al locale SQL Server o un Database SQL Azure. È possibile creare, aggiornare, ottenere ed eliminare voci in una tabella di database SQL.
MQ | Si connette al Server IBM MQ WebSphere versione 8, in locale e in un computer virtuale Azure con sistema operativo Windows. Quando si usa locale, è possibile utilizzare VPN o Azure ExpressRoute. Il connettore include anche il Client Microsoft per MQ.<br/><br/>Nessun trigger. Nessuna azione.<br/><br/>**Nota** Attualmente non può essere utilizzata con logica di App.

## <a name="connectors-as-triggers"></a>Connettori come trigger
Connettori diverse offrono trigger per App logica. I trigger sono disponibili due tipi:

1. Sondaggio trigger: I trigger sondaggio al servizio una frequenza specificata per verificare la presenza di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'App logica con i dati come input. Per evitare che gli stessi dati utilizzata più volte, il trigger potrebbe pulizie dati leggere e passati all'App logica. Esempi di tali connettori sono File, SQL e lo spazio di archiviazione di Azure.
2. Trigger push: Ascoltano i trigger per i dati in un punto finale o per un evento si verifichi. Attivare una nuova istanza di un'App di logica. Esempi di tali connettori sono comunicare ascoltatore HTTP e Twitter.

## <a name="connectors-as-actions"></a>Connettori come azioni
Connettori possono anche essere utilizzati come azioni all'interno di un'applicazione di logica. Azioni sono utili per la ricerca di dati interno dell'App logica possono quindi usata in esecuzione. Ad esempio, potrebbe essere necessario cercare dati da un database SQL per ulteriori informazioni su un cliente durante l'elaborazione di un ordine. In alternativa, potrebbe essere necessario scrivere, aggiornare o eliminare i dati di una destinazione. È possibile eseguire questa operazione utilizzando le azioni disponibili per i connettori. Azioni mappare alle operazioni nelle App API (come definito mediante i relativi metadati Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Creare i connettori e API App
[Connettori e API App riferimento](http://aka.ms/appservicesconnectorreference)  
[Azure API del servizio App app trigger](../app-service-api/app-service-api-dotnet-triggers.md)  
[Guida di riferimento App logica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Altre informazioni su connettori e le API App
[Cosa sono i connettori e BizTalk API App](app-service-logic-what-are-biztalk-api-apps.md)  
[Utilizzando la gestione di connessione ibrido nel servizio App Azure](app-service-logic-hybrid-connection-manager.md)  
[Gestire e monitorare le app API e i connettori incorporati](app-service-logic-monitor-your-connectors.md)
