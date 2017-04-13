<properties
   pageTitle="Guida alla creazione di un servizio dati per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un servizio dati per acquistare in Azure Marketplace."
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

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Servizio dati pubblicazione per Azure Marketplace

>[AZURE.IMPORTANT] **In questo momento sono onboarding qualsiasi nuovi autori di servizio dati. Nuovo dataservices non ottenere approvato per l'elenco.** Se si dispone di un'applicazione aziendale SaaS si desidera pubblicare nella AppSource è possibile trovare altre informazioni [di seguito](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o servizio sviluppo da pubblicare in Azure Marketplace è possibile trovare ulteriori informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).

Dopo aver completato il passaggio 1, [registrazione e la creazione di Account](marketplace-publishing-accounts-creation-registration.md), è la procedura guidata è tramite [Generale non tecnici](marketplace-publishing-pre-requisites.md) e di [Requisiti tecnici](marketplace-publishing-data-service-creation-prerequisites.md) di un'offerta di servizi di dati in Azure Marketplace. Ora esamineremo è la procedura per creare un'offerta di servizi di dati nel [Portale di pubblicazione] [ link-pubportal] per Azure Marketplace.

## <a name="1---login-to-the-publishing-portal"></a>1. eseguire l'accesso per la pubblicazione del portale.

Passare a [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Per primo accesso al portale di pubblicazione, utilizzare lo stesso account con cui è stato registrato il venditore profilo aziendale nel centro per sviluppatori.**  (In un secondo momento è possibile aggiungere tutti i dipendenti dell'azienda come co-amministratore nel portale di pubblicazione).

Fare clic sul riquadro **pubblica una Data Services** se questo è il primo accesso al portale di pubblicazione.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. scegliere **I servizi di dati** nel menu di spostamento sul lato sinistro.

  ![disegno](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. creare un nuovo servizio dati

Inserire il titolo per il nuovo servizio dati offrono e fare clic su "+" a destra.

  ![disegno](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. revisione sottomenu nel servizio dati appena creato nel menu di spostamento.

Fare clic sulla scheda **procedura dettagliata** e rivedere tutte le procedure necessarie per pubblicare correttamente il servizio di dati in Azure Marketplace.

> [AZURE.TIP] È sempre possibile fare clic sui collegamenti nella pagina "Procedura dettagliata" o utilizzare le schede dal sottomenu dell'offerta di servizi di dati, sul lato sinistro.

## <a name="5---create-a-new-plan"></a>5. creare un nuovo piano.

### <a name="offers-plans-transactions"></a>Offre, piani, le transazioni.

Ogni offerta può avere più piani, ma deve avere almeno uno (1) piano. Quando gli utenti finali sottoscrive l'offerta eseguita la sottoscrizione per un piano dell'offerta. Ogni piano consente di definire come gli utenti finali saranno in grado di utilizzare il servizio.

Azure Marketplace supportano attualmente solo in base a transazione abbonamento mensile modello per i servizi di dati, vale a dire gli utenti finali a pagare tariffa mensile in base al prezzo di piano specifico che sottoscritto e saranno in grado di utilizzare ogni numero di mesi di transazione definito dal piano.

Ogni transazione in genere definito come numero di record che verrà restituito il servizio di dati in base alla query inviata al servizio. Il valore predefinito è 100. Numero di transazioni restituito a ogni query sarà numero di record diviso per 100 e arrotondato per eccesso all'intero più vicino.

È responsabilità di livello di servizio di Azure Marketplace per monitorare (contatore) numero delle transazioni consumata da ogni query.

> [AZURE.IMPORTANT] Utenti finali che raggiunto il limite di transazione durante il mese non potrà continuare a usare il servizio fino a fine il ciclo di abbonamento mensile.

> Il piano o uno dei piani possibile (ma non necessario) includono un numero illimitato delle transazioni.

### <a name="create-a-plan"></a>Creare un piano.
1. Fare clic su **"+"** accanto a "Aggiungi un nuovo piano".

2. Scegliere una delle opzioni: l'uso di **illimitate** o **limitate** per il piano.  Se limitata quindi specifica il numero di transazione il piano consente di utilizzare in un mese.

    ![disegno](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portale di pubblicazione suggerisce anche "Piano identificatore", che verrà utilizzato per comunicare con gli utenti finali il nome del piano nell'interfaccia utente e anche utilizzato dal servizio di mercato per identificare il piano. Se si desidera, è possibile modificare "Identificatore piano".

    > [AZURE.NOTE] "Piano identificatore" deve essere univoco nell'ambito di ogni offerta. Come molti altri identificatori nell'identificatore pianificare portale pubblicazione verrà bloccato dopo la pubblicazione prima di produzione e non si potranno essere modificati questo identificatore.

3. Fare clic su per confermare la scelta.

4. Quindi verrà chiesto alcune altre domande relative al piano appena creato.

    ![disegno](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Domanda|Peso|
|----|----|
|**Il piano è gratuito ed è disponibile tutto il mondo?**|È possibile creare un piano completamente gratuiti di-gratuito. Se il piano solo per questa offerta, significa che si desidera pubblicare "Offerta gratuita" sul mercato. Se è solo per un (pochi) piano, l'it offre un'opzione per offrire agli utenti finali per ulteriori informazioni sul servizio con un numero relativamente delle transazioni al mese.  Se la risposta è "Sì", non verranno richiesto altre domande.|

> [AZURE.NOTE] Gli utenti finali sempre possibile eseguire l'aggiornamento per i piani di pagamento.

|Domanda|Peso|
|----|----|
|**È disponibile il periodo di valutazione gratuita?**|È possibile scegliere tra "Versione di valutazione n" tutto o fornire un'opzione per utilizzare il piano per "Mese". Per utilizzare questa opzione per fornire agli utenti finali la possibilità di comprendere i vantaggi dell'offerta gratuitamente per un mese, ad esempio autori.|

> [AZURE.IMPORTANT] Gli utenti finali solo saranno possibile acquistare una versione di valutazione gratuita se hanno stabilito strumento di pagamento, ad esempio carta di credito, contratto enterprise.

> Dopo un mese dalla versione di valutazione gratuita, Azure Marketplace inizierà addebito ai clienti il prezzo al momento della sottoscrizione, a meno che il cliente avviata annullamento della sottoscrizione. Non sarà fornito alcuna notifica speciale per gli utenti finali.

|Domanda|Peso|
|----|----|
|**Prevede un codice di promozione per l'acquisto?**| Gli autori dispongono di un'opzione per limitare l'accesso per i piani di servizio fornendo un codice speciale, denominato "Codice promozione A" ai clienti specifici. Solo gli utenti finali che avranno il codice promozione saranno possibile effettuare la sottoscrizione al piano. Se si sceglie "No", si accettano tutti gli utenti dall'area in cui l'offerta è disponibile (vedere [Guida contenuto Marketing Marketplace](marketplace-publishing-push-to-staging.md) per altri dettagli) sarà in grado di eseguire la sottoscrizione al piano. Non verranno richiesto altre domande.|
|**Nascondere anche il piano da tutti gli utenti che non è disponibile un codice di promozione valida?**|Se la risposta alla domanda precedente è "Sì" di Publisher include un'opzione per rimuovere completamente il piano che venga visualizzato nell'interfaccia utente del mercato. Significa, clienti non verranno visualizzato il piano nella pagina dei dettagli dell'offerta. Utenti finali che verranno visualizzato un codice di promozione per acquistare, sarà possibile sottoscrivere utilizzando questo codice promozione.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. creare il Marketplace contenuti di marketing
Per informazioni su come specificare le informazioni richieste nelle schede **Marketing, prezzi, supporto e le categorie** , vedere [Guida contenuto Marketing Marketplace](marketplace-publishing-push-to-staging.md) comune a tutti gli elementi pubblicate in Azure Marketplace.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. l'offerta la connessione al servizio (SQL Azure in base a o in base a un servizio Web).

Fare clic su nel sottomenu **ODS** .

Nella parte superiore della pagina verrà chiesto di specificare dell'offerta **Namespace**.  

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.png)

Di seguito domanda verranno definite come editore verrà offerta espongono appena creato a Azure Marketplace. (Per ulteriori informazioni, vedere la [Guida di prerequisiti tecnico servizi dati](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Pubblicazione del servizio di Database in base a**

Fare clic sul **Database**. Verrà visualizzata la pagina seguente:

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.3.png)

Per creare un mapping CSDL per il set di dati in base a SQL Azure DB:

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.4.png)

E quindi per ogni tabella

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se il servizio Web

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Per istruzioni dettagliate ed esempi per la creazione di un servizio Web CSDL, leggere [il Mapping di un servizio web esistente a OData tramite CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) .

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato l'offerta di servizi di dati, verificare le istruzioni nella [Guida di contenuto Marketing Marketplace](marketplace-publishing-push-to-staging.md) prima di spostare in avanti alla [verifica del servizio di dati di gestione temporanea](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
- Se si è interessati comprendere il processo di mapping OData e lo scopo complessivo, leggere questo articolo [Mapping OData del servizio di dati](marketplace-publishing-data-service-creation-odata-mapping.md) per verificare le definizioni, strutture e le istruzioni.
- Se si è interessati apprendimento e la comprensione nodi specifici e i parametri, leggere questo articolo [Nodi di Mapping OData servizio dati](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) per le definizioni e spiegazioni, esempi e utilizzare contesto maiuscole e minuscole.
- Se si è interessati a esaminare gli esempi, leggere questo articolo [Esempi di Mapping OData servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md) per visualizzare il codice di esempio e informazioni di contesto e sintassi dei codici.


[link-pubportal]:https://publish.windowsazure.com
