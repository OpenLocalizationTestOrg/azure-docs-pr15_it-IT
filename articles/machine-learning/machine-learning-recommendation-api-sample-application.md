<properties 
    pageTitle="Operazioni comuni dell'API di consigli risorse computer | Microsoft Azure" 
    description="Applicazione di esempio Recommendation ML Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Procedura dettagliata dell'applicazione di esempio API consigli

>[AZURE.NOTE]È necessario iniziare a usare il servizio di Cognitive API consigli invece di questa versione. Il servizio Cognitive consigli andrà a sostituire il servizio e tutte le nuove funzionalità da sviluppare sono. Offre nuove funzionalità, come il batch di supporto, un migliore API Explorer, un esperienza di superficie, più coerente l'iscrizione/fatturazione API chiaro e così via.
> Ulteriori informazioni sulla [migrazione al nuovo servizio cognitiva](http://aka.ms/recomigrate)

##<a name="purpose"></a>Scopo

In questo documento illustra l'uso dell'API di Azure Machine Learning consigli tramite un' [applicazione di esempio](https://code.msdn.microsoft.com/Recommendations-144df403).

Questa applicazione non deve includere tutte le funzionalità, né vengono usati tutte le API. Vengono illustrate alcune operazioni da eseguire quando si desidera riprodurre con recommendation service apprendimento prima di tutto. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introduzione al servizio recommendation apprendimento

Consigli tramite recommendation service apprendimento vengono attivati quando si creazione un modello di recommendation in base ai dati seguenti:

* Un archivio di elementi che si desidera consigliabile, noto anche come un catalogo
* Dati che rappresenta l'utilizzo di elementi per un utente o sessione (questa operazione può essere acquisita nel tempo tramite acquisizione dei dati, non come parte di applicazione di esempio)

Una volta creato un modello di suggerimento, è possibile utilizzare per prevedere gli elementi che un utente potrebbe essere desiderano, in base a un set di elementi (o un singolo elemento) l'utente seleziona.

Per abilitare lo scenario precedente, eseguire le operazioni seguenti in recommendation service di apprendimento:

* Creare un modello: si tratta di un contenitore logico che contiene i dati (catalogo e l'uso) e i modelli di previsione. Contenitore ogni modello viene identificato tramite un ID univoco, allocato quando viene creato. Questo ID viene definito l'ID di modello e viene utilizzato dalla maggior parte delle API. 
* Carica in catalogo: quando viene creato un contenitore di modello, è possibile associare a esso un catalogo.

**Nota**: creazione di un modello e il caricamento di un catalogo vengono in genere eseguite una sola volta per ciclo di vita del modello.

* Caricare l'uso: aggiunge i dati di utilizzo al contenitore di modello.
* Creazione di un modello di suggerimento: dopo aver ottenuto dati sufficienti, è possibile creare il modello di recommendation. Questa operazione utilizza superiore algoritmi di apprendimento per creare un modello di recommendation. Ogni compilazione è associata a un ID univoco. È necessario tenere traccia di questo ID poiché è necessario per la funzionalità di alcune API.
* Monitorare il processo di creazione: creare un modello recommendation è un'operazione asincrona e può essere necessaria da alcuni minuti a diverse ore, a seconda della quantità di dati (catalogo e l'uso) e i parametri di compilazione. È necessario monitorare la compilazione. Creazione di un modello di recommendation solo se la compilazione associata viene completata correttamente.
* (Facoltativo) Scegliere un modello incorporato recommendation attivo: questo passaggio è necessario solo se si dispone di più di un modello di recommendation incorporato nel contenitore di modello. Qualsiasi richiesta per ottenere suggerimenti senza che indica il modello active recommendation viene reindirizzata automaticamente dal sistema per la compilazione attiva predefinito. 

**Nota**: produzione pronti è di un modello di recommendation attiva ed è basata su carico di lavoro di produzione. Questo è diverso da un modello di recommendation non attivo, rimane in un ambiente di test (a volte chiamato gestione temporanea).

* Ottenere suggerimenti: dopo aver ottenuto un modello di suggerimento, è possibile attivare i suggerimenti per un singolo elemento o un elenco di elementi selezionati. 

Richiamare in genere il suggerimento visualizzato per un determinato periodo di tempo. Durante questo periodo di tempo, è possibile reindirizzare i dati di utilizzo al sistema di recommendation apprendimento, che consente di aggiungere questi dati al contenitore di modello specificato. Dopo aver abbastanza dati di utilizzo, è possibile creare un nuovo modello recommendation che include i dati di utilizzo aggiuntive. 

##<a name="prerequisites"></a>Prerequisiti

* Visual Studio 2013
* Accesso a Internet 
* Abbonamento a consigli API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Soluzione di app esempio apprendimento Azure

Questa soluzione contiene il codice sorgente, esempio di utilizzo, file di catalogo e le istruzioni per scaricare i pacchetti necessari per la compilazione.

##<a name="the-apis-used"></a>API utilizzate

L'applicazione utilizza la funzionalità di recommendation apprendimento tramite un sottoinsieme dell'API disponibili. Nell'applicazione vengono illustrate le API seguenti:

* Creare modello: creare un contenitore logico per mettere in attesa dei modelli di dati e recommendation. Un modello è identificato da un nome e non è possibile creare più di un modello con lo stesso nome.
* Caricare file catalogo: consente di caricare dati del catalogo.
* Caricare file di utilizzo: consente di caricare dati di utilizzo.
* Impostare un trigger genera: consente di creare un modello di recommendation.
* Monitorare l'esecuzione di compilazione: consente di controllare lo stato di un modello incorporato recommendation.
* Scelta di un modello predefinito per il suggerimento: consente di indicare il modello di recommendation da usare per impostazione predefinita per un determinato contenitore di modello. Questo passaggio è necessario solo se si dispone di più di un modello di recommendation e si desidera attivare una compilazione non attivo come modello di recommendation attivo.
* Ottenere recommendation: utilizzare per recuperare elementi consigliati in base a un singolo elemento specificato o un insieme di elementi. 

Per una descrizione completa delle API, vedere la documentazione di Microsoft Azure Marketplace. 

**Nota**: un modello può avere diverse generazioni nel tempo (non contemporaneamente). Ogni compilazione viene creato con lo stesso o un catalogo aggiornato e dati di utilizzo aggiuntive.

## <a name="common-pitfalls"></a>Problemi comuni

* È necessario specificare il nome utente e la chiave account principale di Microsoft Azure Marketplace per eseguire l'applicazione di esempio.
* Esecuzione dell'app esempio consecutivamente avrà esito negativo. Il flusso di applicazione include creazione, durante il caricamento, il monitor e ottenere suggerimenti da un modello predefinito. di conseguenza, non funziona sull'esecuzione consecutivi se non si modifica il nome del modello tra le chiamate.
* Consigli potrebbero restituire senza dati. Applicazione di esempio viene utilizzato un file di catalogo e l'utilizzo molto piccolo. Di conseguenza, alcuni elementi dal catalogo non avrà alcun elementi consigliati.

## <a name="disclaimer"></a>Dichiarazione di non responsabilità
Applicazione di esempio non deve essere eseguita in un ambiente di produzione. I dati forniti nel catalogo sono molto piccoli e non fornirà un modello di recommendation significativo. I dati viene forniti una dimostrazione. 
 
