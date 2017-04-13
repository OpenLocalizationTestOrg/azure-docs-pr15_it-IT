<properties
    pageTitle="Importare dati in computer risorse Studio da un file locale | Microsoft Azure"
    description="Informazioni su come importare i dati di formazione su Azure Machine Learning Studio da un file locale."
    keywords="importare dati, il formato dati, i tipi di dati, origini dati, dati di formazione"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importare i dati di formazione in Azure Machine Learning Studio da un file locale

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Per usare i propri dati in computer risorse Studio, è possibile caricare un file di dati anticipo dal disco rigido locale per creare un modulo di set di dati nell'area di lavoro. 


## <a name="import-data-from-a-local-file"></a>Importare dati da un file locale

È possibile importare dati da un disco rigido locale eseguendo le operazioni seguenti:

1. Fare clic su **+ Nuovo** nella parte inferiore della finestra di Studio risorse computer.
2. Selezionare **set di dati** e **FILE locale da**.
3. Nella finestra di dialogo **Carica un nuovo set di dati** , individuare il file da caricare
4. Immettere un nome, identificare il tipo di dati e facoltativamente immettere una descrizione. È consigliabile una descrizione: consente di registrare tutte le caratteristiche relative ai dati da ricordare quando si utilizzano i dati in futuro.
5. La casella di controllo **questo è la nuova versione di un set di dati esistente** consente di aggiornare un set di dati esistenti con i nuovi dati. Basta fare clic su questa casella di controllo e quindi immettere il nome di un set di dati esistente.

Durante il caricamento, verrà visualizzato un messaggio che viene caricato un file. Caricare ora dipenderà le dimensioni dei dati e la velocità della connessione al servizio.
Se si conosce che il file richiederà molto tempo, è possibile eseguire altre operazioni all'interno di Studio risorse computer durante l'attesa. Tuttavia, chiudere il browser si verificherà il caricamento di dati avere esito negativo.

Al caricamento, i dati memorizzato in un modulo di set di dati ed è disponibile per qualsiasi prova nell'area di lavoro.
Quando si modifica una prova, è possibile trovare i set di dati che è stata creata nell'elenco **Set di dati personali** sotto l'elenco di **Set di dati salvati** nella tavolozza modulo. È possibile trascinare e rilasciare il set di dati nell'area di lavoro di prova quando si desidera utilizzare il set di dati per l'ulteriore analitica e apprendimento.



