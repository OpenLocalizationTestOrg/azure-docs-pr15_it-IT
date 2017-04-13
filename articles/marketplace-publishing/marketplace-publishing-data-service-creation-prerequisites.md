<properties
   pageTitle="Tecnici prerequisiti per la creazione di un servizio dati per il Marketplace | Microsoft Azure"
   description="Informazioni sui requisiti per la creazione di un servizio di dati per distribuire e vendere su Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Tecnici prerequisiti per la creazione di un servizio dati l'offerta per Azure Marketplace

>[AZURE.IMPORTANT] **In questo momento sono onboarding qualsiasi nuovi autori di servizio dati. Nuovo dataservices non ottenere approvato per l'elenco.** Se si dispone di un'applicazione aziendale SaaS si desidera pubblicare nella AppSource è possibile trovare altre informazioni [di seguito](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o servizio sviluppo da pubblicare in Azure Marketplace è possibile trovare ulteriori informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).

Accuratamente prima di iniziare il processo di leggere e comprendere dove e perché venga eseguita ogni passaggio. Il più possibile, si deve preparare le informazioni sulla società e altri dati, scaricare gli strumenti necessari e/o creare componenti tecnici prima di iniziare il processo di creazione di offerta.

È necessario disporre gli elementi seguenti pronti prima di iniziare il processo:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Decidere quali tecnologia verrà utilizzata per pubblicare l'offerta di servizio dati

Un autore può decidere tra più tecnologie per la pubblicazione di servizio dati di Azure Marketplace. Le principali tecnologie supportate descritte di seguito. Indipendentemente dal fatto che la tecnologia viene usata per pubblicare il servizio di dati, l'utente finale implica l'uso di dati tramite il **feed OData** esposti dal servizio di Azure Marketplace. Informazioni complete sul servizio OData è possibile trovare nella [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure Database

Set di dati pronto in SQL Azure è responsabilità di Publisher. È necessario sottoscrivere Azure, effettuare il provisioning di dimensione appropriata del Database e caricare i dati in SQL Azure DB. Publisher è responsabile anche per mantenere il proprio dati sempre aggiornati. Ulteriori informazioni sull'abbonamento a servizi di Windows Azure è possibile trovare nella [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Quando si spostano i dati in SQL Azure, è possibile le tabelle e viste vengono esposte Azure Marketplace. Impostazioni di pubblicazione è possibile specificare le tabelle o viste e le colonne vengono esposti all'utente finale. Ulteriormente il provider di contenuti inoltre possibile specificare le colonne che possono essere richiesta l'utente finale e quelle che vengono restituiti solo nel payload. In questo modo un livello elevato di flessibilità rispetto alle esporre i dati nel database. Le colonne che possono essere eseguite query devono essere eseguito da una o più indici di database.

## <a name="rest-based-web-service"></a>Servizio web REST in base a

Protocolli supportati: **HTTPS solo**

Servizi resto in base a esistenti possono essere esposti tramite Azure Marketplace. Poiché il set di dati sempre esposto all'utente finale come un feed OData, le esigenze di servizio di Azure Marketplace per poter eseguire il mapping del servizio a un OData in base a servizio. Per eseguire in modo che il resto in base a endpoint necessari per esporre tutti i parametri come parametri HTTP.

Il payload deve essere in una maschera che possa essere mappata in una risposta ATOM. Pertanto la risposta da servizi deve essere in formato XML e può solo contenere un elemento ripetuto che contiene i valori di payload (ad esempio set di record). Il servizio di Azure Marketplace verrà mappate il nodo ripetuto per il nodo della voce ATOM e i valori di payload in nodi di proprietà all'interno del nodo di voce.

Informazioni sulle autorizzazioni (ad esempio API chiave, autenticazione token, ecc.) deve essere fornita come parametro HTTP o nell'intestazione HTTP (coppia valore chiave) – è supportata anche l'autenticazione di base. È necessario specificare una chiave valida e tutte le richieste tramite Azure Marketplace vengono apportate a tale tasto. Utente monitoraggio e la fatturazione accade al livello di Azure Marketplace.

Errori restituiti dal servizio devono essere mappati in codici di stato HTTP. Nel caso in cui il servizio restituisce un file XML che contiene l'errore che si sta per eseguire il mapping dal servizio di Azure Marketplace per i codici di stato HTTP.

## <a name="soap-based-web-services"></a>Servizi web SOAP in base a

Protocollo: **HTTPS solo**

I requisiti sono che gli stessi visualizzati nel resto in base a sezione servizio. L'unica differenza è possano anche fornire parametri nel corpo di un XML registrati del servizio di Publisher con tutte le richieste inviate tramite Azure Marketplace. Questo errore indica che i parametri HTTP che l'utente fornisce in front-end vengono vengono convertiti in elementi XML di un documento XML registrate con la richiesta di servizio web del provider di contenuti.

## <a name="odata-based-web-services"></a>Servizi web OData in base a

Protocollo: **HTTPS solo**

Dati possono essere esposto come servizio OData a Azure Marketplace. Il sistema sta per passare al servizio tramite e sostituisce la radice del servizio con la radice del servizio di Azure Marketplace: per assicurarsi che tutte le chiamate successive elaborata Azure Marketplace.

Servizi OData non sufficiente per l'invio in un database back-end. OData supporta qualsiasi tipo di spazio di archiviazione o la regola business per guidare il servizio.


## <a name="next-steps"></a>Passaggi successivi
Ora che si esaminato i prerequisiti e completare le attività necessarie, è possibile spostare in avanti alla creazione dell'offerta di servizi di dati come descritto nella [Guida alla pubblicazione di servizio dati](marketplace-publishing-data-service-creation.md).

Oppure, se si desidera esaminare il processo complessivo e i rispettivi articoli per ognuna delle fasi di pubblicazione, consultare l'articolo [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
