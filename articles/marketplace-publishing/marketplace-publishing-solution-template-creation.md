<properties
   pageTitle="Guida alla creazione di un modello di soluzione per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un modello di soluzione macchine Virtuali con più immagini per acquistare in Azure Marketplace."
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
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guida per creare un modello di soluzione per Azure Marketplace
Dopo aver completato il passaggio 1, [registrazione e la creazione di Account][link-acct-creation], procedura guidata è al momento della creazione di un modello di soluzione compatibile con Azure in [tecnici prerequisiti per la creazione di un modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md). Ora esamineremo è la procedura per creare un modello di soluzione per più macchine virtuali nel [Portale di pubblicazione] [ link-pubportal] per Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Creare l'offerta di modello soluzione nel portale di pubblicazione
Passare a [https://publish.windowsazure.com](http://publish.windowsazure.com). Quando si accede per la prima volta al [Portale di pubblicazione](https://publish.windowsazure.com/), utilizzare lo stesso account con cui è stato registrato il venditore profilo aziendale. In un secondo momento, è possibile aggiungere tutti i dipendenti dell'azienda come co-amministratore nel portale di pubblicazione.

### <a name="1-select-solution-templates"></a>1. selezionare "Modelli di soluzioni"

  ![disegno][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. creare un nuovo modello di soluzione

  ![disegno][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. avviare con topologie
Un modello di soluzione è un "parent" a tutte le topologie. È possibile definire più topologie in un modello di offerta/soluzione. Quando viene inserita un'offerta di gestione temporanea, viene inserito con tutti i relative topologie. Eseguire la procedura seguente per definire l'offerta:     

- Crea una topologia: "Identificatore topologia" è in genere il nome della topologia per il modello di soluzione. L'identificatore topologia viene utilizzato nell'URL, come illustrato di seguito:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Portale di Azure: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Aggiungere una nuova versione.

### <a name="4-get-your-topology-versions-certified"></a>4. ottenere le versioni di topologia Certificate
Caricare un file zip contenente tutti i file necessari per eseguire il provisioning di quella versione specifica della topologia. Il file zip deve contenere le operazioni seguenti:

- file *mainTemplate.json* e *createUiDefinition.json* nella directory radice.
- Tutti i modelli collegati e gli script di tutte le necessarie.

  > [AZURE.TIP] Mentre gli sviluppatori usare la creazione della soluzione topologie modello e del loro certified, business, i reparti marketing e/o persone della società possono lavorare sul contenuto marketing e legale.

## <a name="next-steps"></a>Passaggi successivi
Ora che si crea il modello di soluzione e caricata il file zip, seguire le istruzioni nella [Guida di contenuti di marketing Marketplace](marketplace-publishing-push-to-staging.md) prima di spostarle dell'offerta di gestione temporanea. Per visualizzare l'insieme completo di marketplace pubblicare articoli, visitare [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md).

Può inoltre essere utile in questi articoli correlati:

- Immagini di macchine Virtuali: [Sulle immagini macchina virtuale di Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Estensioni macchine Virtuali: [agente macchine Virtuali e macchine Virtuali estensioni Panoramica](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [Azure macchine Virtuali estensioni e funzionalità](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- Manager delle risorse Azure: [creazione di modelli di Azure ARM](../resource-group-authoring-templates.md) e [ARM semplice modello esempi](https://github.com/rjmax/ArmExamples)
- Account di archiviazione limita: [come Monitor per la limitazione all'Account di spazio di archiviazione](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [archiviazione aggiuntivo Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
