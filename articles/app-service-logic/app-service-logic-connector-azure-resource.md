<properties
   pageTitle="Usando il connettore risorsa Azure nelle App logica | Servizio di Microsoft Azure App"
   description="Come creare e configurare l'app Azure risorsa connettore o API e usarla in un'app di logica nel servizio App Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Iniziare a utilizzare il connettore risorsa Azure e aggiungere logica all'applicazione in uso
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Utilizzare il connettore risorsa Azure per gestire facilmente le risorse di Azure all'interno di un'applicazione di logica.

## <a name="create-the-azure-resource-connector"></a>Creare il connettore risorsa Azure
Per usare l'app di Azure risorsa connettore API, è necessario prima di tutto creare un'istanza. Questa può essere eseguita inline durante la creazione di un'app logica oppure selezionando l'app di Azure risorsa Manager connettore API di Azure Marketplace.

Per configurarlo, si verifica è necessario impostare la identità di servizio con le autorizzazioni per eseguire le operazioni che si desidera eseguire in Azure. Tutte le chiamate verranno effettuate per conto di capitale questo servizio che ha configurato. In questo modo è possibile definire l'ambito del connettore per utilizzare solo esattamente quali si desidera eseguire e nulla altre.

David Ebbo è scritto [un post di blog interessanti](http://blog.davidebbo.com/2014/12/azure-service-principal.html) su come impostare. Seguire le istruzioni sono e ottenere **l'ID di Tenant**, **l'ID Client** e **segreto**. Questi tre campi, oltre l' **ID abbonamento**sono quali sono necessarie per configurare il connettore.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Tramite il connettore risorsa Azure in logica App designer
### <a name="trigger"></a>Trigger
Esistono due trigger supportate nel connettore:

Nome | Descrizione
---- | -----------
Si verifica | Condizione di attivazione quando si verifica un evento a una risorsa nell'abbonamento.
Unità di misura metriche supera soglia |  Trigger quando una metrica soddisfa una determinata soglia.

### <a name="action"></a>Azione

Analogamente, è possibile fornire un numero elevato di azioni all'interno di abbonamento Azure:

Per i **gruppi di risorse** che è possibile:

Nome | Descrizione
---- | -----------
Elenco di gruppi di risorse | Elencare tutti i gruppi di risorse nella sottoscrizione.
Ottenere gruppo di risorse | È possibile ottenere un gruppo di risorse dal relativo id.
Creare gruppo di risorse | Creare o aggiornare un gruppo di risorse.
Eliminare il gruppo di risorse | Eliminare un gruppo di risorse.

Per **le risorse** è possibile:

Nome | Descrizione
---- | -----------
Elenco risorse | Elencare le risorse nell'abbonamento a diversi tipi di filtri.
Ottenere la risorsa | Ottenere una singola risorsa per la risorsa ID.
Creazione o aggiornamento delle risorse | Creare una risorsa o aggiornare una risorsa esistente. È necessario fornire tutte le proprietà per la risorsa.
Azione di risorse |  Eseguire altre azioni su una risorsa. È necessario conoscere il nome dell'azione e payload che questa azione consente di accedere (se presente).
Eliminazione di risorsa | Eliminare una risorsa.

Per i **Provider di risorse** è possibile:

Nome | Descrizione
---- | -----------
Provider di elenco risorse | Elencare tutti i provider di servizi di risorse disponibili nella sottoscrizione.

Per **Le distribuzioni di gruppo di risorse** è possibile:

Nome | Descrizione
---- | -----------
Distribuzioni di elenco | Elenco di tutte le distribuzioni in un gruppo di risorse.
Ottenere la distribuzione | È possibile ottenere una distribuzione modello dal relativo id.
Creare distribuzione | Creare una nuova distribuzione di gruppo di risorse tramite un modello.

Per **gli eventi** sulle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere gli eventi | È possibile ottenere gli eventi a un abbonamento o in una risorsa.

Per **metriche** sulle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere metriche | Ottenere una metrica per una risorsa ID.

## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere a un flusso di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se si desidera iniziare a utilizzare App Azure logica prima di iscriversi a un account Azure, passare all' [app di logica di prova](https://tryappservice.azure.com/?appservice=logic), in cui è possibile creare immediatamente un'app di logica starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

Visualizzare il riferimento all'API REST Swagger in [connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
