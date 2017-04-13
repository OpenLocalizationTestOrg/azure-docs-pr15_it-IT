<properties
   pageTitle="Esplora risorse Azure | Microsoft Azure"
   description="Vengono descritti Esplora risorse Azure e come può essere utilizzato per visualizzare e aggiornare le distribuzioni tramite Gestione risorse di Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Esplora risorse Azure consente di visualizzare e modificare le risorse
[Esplora risorse Azure](https://resources.azure.com) è un ottimo strumento per l'analisi risorse già creata nell'abbonamento. Tramite questo strumento, è possibile comprendere come le risorse sono strutturate e visualizzare le proprietà assegnate a ogni risorsa. Per informazioni sulle operazioni API REST e i cmdlet di PowerShell disponibili per un tipo di risorsa e comandi tramite l'interfaccia. Esplora risorse possono essere particolarmente utile durante la creazione di modelli di Manager delle risorse, perché consente di visualizzare le proprietà di risorse esistenti.

L'origine per lo strumento di Esplora risorse è disponibile in [github](https://github.com/projectkudu/ARMExplorer), che fornisce un riferimento molto utile se è necessario implementare un comportamento simile nelle applicazioni.

## <a name="view-resources"></a>Visualizzazione risorse
Passare a [https://resources.azure.com](https://resources.azure.com) e accedere con le stesse credenziali che si utilizzerebbero per il [Portale di Azure](https://portal.azure.com).

Dopo aver caricato, visualizzazione struttura sul lato sinistro consente di drill-down sottoscrizioni e i gruppi di risorse:

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Come si analizza un gruppo di risorse è possibile vedere i provider in cui sono presenti le risorse di tale gruppo:

![provider di servizi](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Da qui è possibile iniziare il drill-down le istanze di risorse. Nella schermata seguente è possibile visualizzare il `sltest` istanza di SQL Server nella visualizzazione struttura. Sul lato destro è possibile visualizzare informazioni sulle richieste API REST che è possibile usare con quella risorsa. Passando al livello di nodo per una risorsa, Resource Explorer prende automaticamente la richiesta GET per recuperare le informazioni sulle risorse. Nell'area di testo di grandi dimensioni sotto l'URL, verrà visualizzata la risposta dall'API. 

Dopo avere acquisito familiarità con i modelli di Manager delle risorse il contenuto del corpo inizia a familiare! La sezione **proprietà** della risposta corrisponde ai valori che immessi nella sezione **proprietà** del modello.

![SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Esplora risorse consente di mantenere il drill-down per esplorare le risorse figlio, nel caso dei Server di Database SQL, sono disponibili risorse figlio per le operazioni, ad esempio database e le regole del firewall.

Esplorazione di un database Mostra le proprietà per il database. Nella figura seguente è possibile vedere che il database `edition` è `Standard` e `serviceLevelObjective` (o livello database) è `S1`.

![database SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Modificare le risorse

Dopo essere passati a una risorsa, è possibile selezionare il pulsante Modifica per rendere il contenuto JSON modificabile. È quindi possibile utilizzare Esplora risorse per modificare il JSON e inviare una richiesta di caricamento per modificare la risorsa. Ad esempio, l'immagine seguente viene mostrato il livello di database modificato in `S0`:

![database - richiesta di caricamento](./media/resource-manager-resource-explorer/are-05-database-put.png)

**Selezionando** di inviare la richiesta. 

Una volta è stata inviata la richiesta di Esplora risorse inoltra una nuova richiesta GET per aggiornare lo stato. In questo caso è possibile vedere che la `requestedServiceObjectiveId` è stato aggiornato ed è la differenza tra il `currentServiceObjectiveId` che indica che è un'operazione di ridimensionamento in corso. È possibile fare clic sul pulsante RECUPERA per aggiornare manualmente lo stato.

![database - GET richiesta2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Esecuzione di operazioni sulle risorse

Scheda **Azioni** consente di vedere ed eseguire altre operazioni di resto. Ad esempio, dopo aver selezionato una risorsa del sito web, sulla scheda azioni presenta un lungo elenco di operazioni disponibili, alcune delle quali illustrati di seguito.

![Web - richiesta POST](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Richiamare l'API tramite PowerShell
La scheda PowerShell in Esplora risorse Mostra i cmdlet da utilizzare per interagire con la risorsa che sono attualmente esplorazione. A seconda del tipo di risorsa è stata selezionata, lo script di PowerShell visualizzato può essere compreso tra i cmdlet semplici (ad esempio `Get-AzureRmResource` e `Set-AzureRmResource`) a più complicato cmdlet (ad esempio lo scambio bande orarie in un sito web). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Per ulteriori informazioni sui cmdlet di PowerShell di Azure, vedere [Uso di PowerShell Azure Gestione risorse Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Riepilogo
Quando si lavora con Gestione risorse, Esplora risorse può essere uno strumento estremamente utile. È un'ottima soluzione per trovare modi per usare PowerShell per eseguire query e apportare le modifiche. Se si sta lavorando con l'API REST è un'ottima soluzione per iniziare e testare rapidamente chiamate API prima di iniziare a scrivere codice. E se si sta scrivendo modelli che può essere un'ottima soluzione per comprendere la gerarchia delle risorse e trovare la posizione in cui inserire configurazione - è possibile apportare modifiche nel portale e quindi individuare le voci corrispondenti in Esplora risorse.

Per ulteriori informazioni, guardare [9 canale video con Scott Hanselman e David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


