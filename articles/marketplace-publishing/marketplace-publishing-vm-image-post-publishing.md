<properties
   pageTitle="Gestire l'immagine di macchina virtuale in Azure Marketplace | Microsoft Azure"
   description="Guida dettagliata su come gestire l'immagine di macchina virtuale in Azure Marketplace al termine della pubblicazione iniziale."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guida di post-produzione per le offerte di macchina virtuale in Azure Marketplace

Questo articolo spiega come è possibile aggiornare un'offerta di macchina virtuale live in Azure Marketplace. Inoltre, Guida l'utente del processo di aggiunta di uno o più ai nuovi SKU a un'offerta esistente e rimuovere un'offerta di macchina virtuale o SKU live da Azure Marketplace.

Una volta un'offerta/SKU viene gestita nel [Portale di Azure](http://portal.azure.com), non è possibile modificare i campi riportati di seguito:

- **Offrono identificatore:** [Portale di pubblicazione -> macchine virtuali -> Seleziona l'offerta -> scheda immagini macchine Virtuali -> identificatore offrono]
- **Identificatore SKU:** [Portale di pubblicazione -> macchine virtuali -> Seleziona l'offerta -> SKU -> scheda aggiungere uno SKU]
- **Namespace publisher:** [Portale di pubblicazione -> macchine virtuali -> procedura dettagliata scheda -> Contattaci sull'azienda (disponibile nella sezione "Passaggio 2 eseguire la registrazione della propria azienda") -> comunicare Publisher Namespace -> Namespace]

Una volta offerta/SKU è elencato in [Azure Marketplace](http://azure.microsoft.com/marketplace), non è possibile modificare i campi riportati di seguito:

- **Offrono identificatore:** [Portale di pubblicazione -> macchine virtuali -> Seleziona l'offerta -> scheda immagini macchine Virtuali -> identificatore offrono]
- **Identificatore SKU:** [Portale di pubblicazione -> macchine virtuali -> Seleziona l'offerta -> SKU -> scheda aggiungere uno SKU]
- **Namespace publisher:** [Portale di pubblicazione -> macchine virtuali -> procedura dettagliata -> scheda Namespace Publisher informare Contattaci della propria azienda (disponibili in passaggio 2 registrare) -> Namespace]
- **Porte** [Portale di pubblicazione -> macchine virtuali -> Seleziona l'offerta -> scheda immagini macchine Virtuali -> Apri porte]
- **Modifica prezzo di SKU(s) nell'elenco**
- **Modifica del modello di SKU(s) nell'elenco di fatturazione**
- **Rimozione delle aree di SKU(s) nell'elenco di fatturazione**
- **Modificare il numero di disco di dati di SKU(s) nell'elenco**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. la procedura per aggiornare i dettagli tecnici di uno SKU

È possibile aggiungere una nuova versione a SKU elencati e pubblicare di nuovo l'offerta seguendo la procedura indicata di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **Immagini macchine Virtuali** .
4. Nella sezione **SKU** della scheda **Macchine Virtuali immagini** individuare lo SKU che si desidera aggiornare.
5. Successivamente, aggiungere un nuovo numero di versione dello SKU e fare clic sul pulsante **"+"** . La nuova versione deve essere di formato X.Y.Z dove X, Y e Z sono numeri interi. Modifiche alla versione solo dovrebbe essere incrementali.
6. Nella casella **URL disco rigido virtuale del sistema operativo** , aggiungere la firma di accesso condiviso che URI creato per il sistema operativo disco rigido virtuale e salvare le modifiche.

    >[AZURE.IMPORTANT] È possibile incremento/decremento il conteggio del disco di dati di uno SKU elencato. È necessario creare un nuovo SKU in questo caso. Fare riferimento alla sezione [3. Come aggiungere un nuovo SKU in un'offerta elencato](#3-how-to-add-a-new-sku-under-a-live-offer) per istruzioni dettagliate.

7. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
8. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. come aggiornare i dettagli non tecniche di un'offerta o un SKU

È possibile aggiornare la comprensione (marketing, legali, supporto, categorie) i dettagli dell'offerta live o SKU di Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 aggiornare l'offerta descrizione e logo

È possibile aggiornare i dettagli offerta e pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING** .
4. Fare clic sul pulsante **Inglese (Stati Uniti)** .
5. Dal menu sul lato sinistro fare clic sulla scheda **Dettagli** . Nella sezione *Descrizione* della scheda **Dettagli** è possibile aggiornare il titolo di offerta, offrire riepilogo, offrire riepilogo lungo e salvare le modifiche.

    >[AZURE.NOTE] Prestare attenzione delle operazioni seguenti durante l'aggiornamento i dettagli SKU.
    **Non immettere testo duplicato in Descrizione offerta e la descrizione della SKU. Non immettere testo duplicato sotto il titolo SKU e l'offerta tempo riepilogo. Non immettere testo duplicato sotto il titolo SKU e riepilogo offerta.**

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Nella sezione *logo* della scheda **Dettagli** , è possibile aggiornare il logo. Tuttavia, assicurarsi che il logo seguire [le istruzioni di Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (fare riferimento alla sezione Passaggio 1: contenuti di marketing fornire Marketplace -> dettagli -> Azure Marketplace Logo Guidelines).

    >[AZURE.NOTE] Icona di immagine è facoltativo. È possibile scegliere di non caricare un'icona di immagine. Tuttavia, quando viene caricato l'icona di immagine, quindi non sarà necessario eliminare dalla pubblicazione portal. In questo caso, è necessario seguire [le istruzioni sull'icona di immagine](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (fare riferimento alla sezione Passaggio 1: contenuti di marketing fornire Marketplace -> dettagli -> indicazioni aggiuntive per intestazione con il logo eroe).

7. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea consultare questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
8. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Aggiornare la descrizione SKU

È possibile aggiornare i dettagli SKU e pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING** .
4. Fare clic sul pulsante **Inglese (Stati Uniti)** .
5. Dal menu sul lato sinistro fare clic sulla scheda **plan di messaggistica unificata** . Nella sezione *SKU* della scheda **plan di messaggistica unificata** è possibile aggiornare il titolo SKU, SKU riepilogo e dettagli descrizione SKU e salvare le modifiche.

    >[AZURE.NOTE] Prestare attenzione delle operazioni seguenti durante l'aggiornamento i dettagli SKU. **Non immettere testo duplicato in Descrizione offerta e la descrizione della SKU. Non immettere testo duplicato in titolo del SKU e l'offerta tempo riepilogo. Non immettere testo duplicato sotto il titolo SKU e riepilogo offerta.**

6. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo collegamento
7. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 modificare i collegamenti esistenti o aggiungere nuovi collegamenti

È possibile modificare i collegamenti esistenti o aggiungere nuovi collegamenti e quindi pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING** .
4. Fare clic sul pulsante **Inglese (Stati Uniti)** .
5. Dal menu sul lato sinistro fare clic sulla scheda **collegamenti** .
6. Se si desidera aggiungere un nuovo collegamento, quindi in *collegamenti* sezione fare clic sul pulsante **Aggiungi collegamento** . Verrà aperta la finestra di dialogo *"Aggiungi collegamento"* . Nella finestra di dialogo, è possibile aggiungere il collegamento del titolo e URL campi e salvare le modifiche. È possibile immettere un collegamento che contiene informazioni che possono aiutare i clienti.
7. Se si desidera aggiornare o eliminare un collegamento esistente, quindi selezionare il collegamento appropriato e fare clic sul pulsante Modifica o eliminazione di conseguenza.

    >[AZURE.NOTE] Assicurarsi che i collegamenti che è stato immesso in questa sezione funzionino correttamente, questi collegamenti vengono convalidati durante il processo di richiesta di produzione.

8. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea consultare questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2,4 modificare un'immagine di esempio esistente o aggiungere una nuova immagine di esempio

È possibile modificare un immagini di esempio esistenti o aggiungere nuove immagini di esempio e quindi pubblicare di nuovo l'offerta seguendo la procedura seguente:

>[AZURE.NOTE] Immagine di un solo campione viene visualizzata in [https://portal.azure.com](https://portal.azure.com).

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING** .
4. Fare clic sul pulsante **Inglese (Stati Uniti)** .
5. Dal menu sul lato sinistro fare clic sulla scheda **Immagini di esempio** .
6. Se si desidera aggiungere una nuova immagine di esempio, nella sezione *Immagini di esempio* fare clic sul pulsante **Carica una nuova immagine** e quindi salvare le modifiche.

    >[AZURE.NOTE] Includere un'immagine di esempio è un passaggio facoltativo.

7. Se si desidera aggiornare o eliminare un'immagine di esempio esistente, quindi individuare l'immagine di esempio appropriato e quindi fare clic sul pulsante **Sostituisci immagine** o sul pulsante Elimina conseguenza.

8. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea consultare questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md).
9. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2,5 aggiornare la protezione del contenuto

È possibile aggiornare il contenuto legale e pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **MARKETING** .
4. Fare clic sul pulsante **Inglese (Stati Uniti)** .
5. Dal menu sul lato sinistro fare clic sulla scheda **legali** . Nella sezione *legali* è possibile aggiornare i criteri/condizioni per l'utilizzo. Immettere o incollare i criteri/termini nella casella *Condizioni per l'utilizzo* di testo e salvare le modifiche.
6. Il limite di caratteri per le note legali di utilizzo è 1.000.000 caratteri.
7. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
8. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 aggiornare le informazioni di supporto

È possibile aggiornare le informazioni di supporto e pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **supporto** .
4. Nella sezione *Contattare ingegneristica* della scheda **supporto** è possibile aggiornare le informazioni di contatto. Questi dettagli vengono utilizzati per la comunicazione interna solo tra il partner e Microsoft.
5. Nella sezione *Servizio di supporto* della scheda **supporto** è possibile aggiornare le informazioni di contattare il supporto ad esempio **nome, il messaggio di posta elettronica, telefono** e **URL di supporto**. Questi dettagli vengono utilizzati per la comunicazione interna solo tra il partner e Microsoft.

    >[AZURE.NOTE] Se si desidera fornire solo supporto tramite posta elettronica, fornire un numero di telefono fittizio nella sezione **Supporto clienti** . In questo caso, verrà utilizzata la posta elettronica fornita.

6. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
7. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 aggiornare le categorie

È possibile aggiornare la sezione categorie per l'offerta e pubblicare di nuovo l'offerta seguendo la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com)
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **categorie** .
4. Nella sezione *categorie* è possibile aggiornare le categorie per l'offerta e salvare le modifiche. È possibile selezionare un massimo di cinque categorie per la raccolta di Azure Marketplace.
5. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
6. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. come aggiungere un nuovo SKU in un'offerta elencato

È possibile aggiungere un nuovo SKU sotto l'offerta live seguendo la procedura indicata di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU** . Dopo il clic sul pulsante **Aggiungi A SKU**.  Verrà aperta una nuova finestra di dialogo. Immettere un identificatore SKU in lettere minuscole. Selezionare la casella di controllo per visualizzare-your-proprietari fatturazione model(BYOL) se si desidera pubblicare SKU nuovo modello di fatturazione BYOL. In caso contrario, deselezionare la casella di controllo per BYOL. Dopo tale fare clic sul segno di graduazione nella finestra di dialogo per creare un nuovo SKU. Se non si aderire per il modello di fatturazione BYOL per SKU nuovo, quindi il modello di fatturazione verrà automaticamente impostare ogni ora per i nuovi SKU. Se si desidera abilitare la versione di valutazione gratuita di 30days per orario modello fatturazione, quindi scegliere l'opzione "Mese" per "è una versione di valutazione gratuita disponibile?". In caso contrario selezionare "Periodo di valutazione n". [Nota: l'opzione "è una versione di valutazione gratuita?" viene visualizzata solo se è stato selezionato non BYOL nella finestra di dialogo durante la creazione di nuovi SKU.]

    >[AZURE.IMPORTANT] L'opzione "Nascondere questa SKU di Marketplace perché deve sempre essere acquistato tramite un modello di soluzione" deve essere contrassegnato come "Sì" solo se richiesta l'approvazione per la pubblicazione di un'offerta di modello soluzione in Azure Marketplace. In caso contrario, questa opzione deve sempre essere contrassegnata come "NO".

4. A questo punto dal menu sul lato sinistro fare clic sulla scheda **Immagini macchine Virtuali** e Scopri la nuova SKU creata.
5. Per impostare lo SKU di nuovo, fare riferimento al passaggio 5 di questo [collegamento](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) per indicazioni.
6. Per aggiungere il materiale di marketing per SKU nuovo, fare riferimento alla sezione Passaggio 1: contenuti di marketing fornire Marketplace -> dettagli -> numeri a virgola 2 a 5 di questo [collegamento](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Per aggiungere le informazioni sui prezzi per SKU nuovo, fare riferimento alla sezione 2.1. Impostare i prezzi macchine Virtuali di questo [collegamento](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Dopo aver apportato le modifiche, passare alla scheda **pubblica** e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
9. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. come modificare il numero di disco dati per un elenco SKU

È possibile incremento/decremento il conteggio del disco di dati di uno SKU elencato. È necessario creare un nuovo SKU in questo caso. Fare riferimento alla sezione [3. Come aggiungere un nuovo SKU in un'offerta di live](#3-how-to-add-a-new-sku-under-a-live-offer) per istruzioni dettagliate.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. come eliminare un'offerta nell'elenco di Azure Marketplace

Esistono diversi aspetti che devono essere gestiti in caso di una richiesta per rimuovere un'offerta di live. Seguire la procedura seguente per ottenere istruzioni dal team di supporto per rimuovere un'offerta nell'elenco di Azure Marketplace:

1.  Generare un ticket di supporto con questo [collegamento](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Selezionare il tipo di problema come **"Gestione offerte"** e selezionare categoria come **"Modifica di un'offerta e/o SKU già in produzione"**
3.  Inviare la richiesta

Il team di supporto consentirà il processo di eliminazione offerta/SKU.

>[AZURE.NOTE] È sempre possibile eliminare l'offerta mentre è nello stato bozza (ad esempio, non in gestione temporanea o produzione) facendo clic sul pulsante **Elimina bozza** nella scheda **cronologia** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. come eliminare uno SKU elencato da Azure Marketplace

È possibile eliminare uno SKU elencato da Azure Marketplace seguendo la procedura indicata di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal riquadro sul lato sinistro, fare clic sulla scheda **SKU** .
4. Selezionare lo SKU che si desidera eliminare e fare clic sul pulsante Elimina rispetto a quella SKU.
5. Al termine, passare alla scheda pubblica di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.
6. Dopo l'offerta ottiene nuovamente pubblicata in Azure Marketplace, lo SKU verrà eliminato da Azure Marketplace e il portale di Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. come eliminare la versione corrente di uno SKU elencato da Azure Marketplace

È possibile eliminare la versione corrente di uno SKU elencato da Azure Marketplace seguendo la procedura indicata di seguito. Al termine del processo, lo SKU verrà ripristinato la versione precedente.

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2.  Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3.  Dal riquadro sul lato sinistro, fare clic sulla scheda **Immagini macchine Virtuali** .
4.  Selezionare lo SKU cui versione corrente che si desidera eliminare e fare clic sul pulsante Elimina con tale versione.
5.  Al termine, passare alla scheda **pubblica** di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.
6.  Dopo l'offerta ottiene nuovamente pubblicata in Azure Marketplace, la versione corrente di SKU nell'elenco verrà eliminata da Azure Marketplace e il portale di Azure. Lo SKU verrà ripristinato alla versione precedente.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. come ripristinare il prezzo di elenco di valori di produzione
Ho modificato il prezzo di un elenco SKU (o è stato rimosso le aree di fatturazione di uno SKU elencato). Poiché non è supportata in Azure Marketplace, desidera annullare le modifiche con i valori di produzione. Come ottenere che?

Seguire la procedura indicata di seguito:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **costi** .
4. Nella scheda prezzi selezionare un'area di cui si desidera reimpostare prezzi.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. In caso di SKU oraria modello di fatturazione, reimpostare i prezzi per tutti i core si tratta di produzione per l'area selezionata. Per gli SKU modello fatturazione BYOL, deve essere lo SKU disponibile nell'area selezionando la casella di controllo contro lo SKU nella sezione disponibilità SKU EXTERNALLY-LICENSED (BYOL) (vedere la schermata seguente).

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Fare clic sul pulsante **AUTOPRICE altri mercati in base a via prezzi IN Stati Uniti**.

    >[AZURE.NOTE] Etichetta del pulsante può essere diverso a seconda dell'area del è stato selezionato. Dal momento che durante la creazione di questo documento è stato selezionato negli Stati Uniti, quindi sul pulsante contrassegnato come "Automatico prezzo altri mercati in base ai prezzi negli Stati Uniti" nella schermata seguente.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. Verrà aperta la procedura guidata prezzo automatico. La prima pagina viene visualizzata la selezione per mercato base. Verificare la sezione e passare alla pagina successiva, fare clic sul pulsante **"->"** .

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Opzione per la selezione core e dei piani di verrà visualizzati nella pagina 2. Selezionare il piano desiderato e i core e fare clic su "->" pulsante.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Pagina 3 Visualizza mercati/aree geografiche. Fare clic sul pulsante Mostra/Nascondi tutto per selezionare tutte le aree o manualmente selezionare le caselle per area geografica. Fare clic sul pulsante "->" per passare alla pagina successiva.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Pagina 4 consente di visualizzare le tariffe di exchange. Fare clic sul pulsante Fine per completare la procedura. La procedura guidata reimpostano prezzi in base alla selezione.

11. A questo punto, passare alla scheda prezzo e fare clic sul pulsante "Visualizzazione Riepilogo e delle modifiche".
Selezionare "la parola Bozza" nella sezione "Visualizza la versione" e "Produzione" nella sezione "Confrontare con" (vedere la schermata seguente). Se viene non visualizzata alcuna differenza prezzo, significa prezzi è stato ripristinato i valori di produzione correttamente.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Dopo aver apportato le modifiche, passare alla scheda pubblica e fare clic sul pulsante **PUSH di gestione temporanea**. Per istruzioni dettagliate sul testing l'offerta nell'ambiente di gestione temporanea fare riferimento a questo [collegamento](marketplace-publishing-vm-image-test-in-staging.md)
13. Dopo aver verificato l'offerta di gestione temporanea, passare alla scheda pubblica di pubblicazione portale e fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. le procedure per modificare il modello fatturazione ai valori di produzione
Ho modificato il modello di fatturazione di uno SKU nell'elenco. Poiché non è supportata in Azure Marketplace, desidera annullare le modifiche con i valori di produzione. Come ottenere che?

Eseguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU** .
4. Fare clic su pulsante di modifica per ripristinare il modello di fatturazione. Verrà aperta una finestra. Selezionare o deselezionare la casella di controllo **'fatturazione e gestione delle licenze viene eseguita esternamente da Azure (o visualizzare il proprio licenza)'** a seconda dei casi.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Una volta, consultare la risposta della domanda 8 in questo documento per ripristinare i prezzi.
6. Dopo che consentono di passare alla scheda **pubblica** di pubblicazione portale e push dell'offerta di gestione temporanea per eseguire il test. Una volta completato con l'offerta di test quindi fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. come ripristinare l'impostazione della visibilità di uno SKU elencato per il valore di produzione

Eseguire la procedura seguente:

1. Accedere al [portale di pubblicazione](https://publish.windowsazure.com).
2. Passare alla scheda **macchine VIRTUALI** e selezionare l'offerta.
3. Dal menu sul lato sinistro fare clic sulla scheda **SKU** .
4. Selezionare lo SKU e ripristinare le impostazioni di visibilità di SKU al valore di produzione.

    ![disegno](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Una volta lavorare con le modifiche, quindi fare clic sul pulsante **Richiedere l'approvazione per PUSH di produzione** per pubblicare di nuovo l'offerta in Azure Marketplace

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
- [Informazioni sulle informazioni dettagliate sui venditore dei report](marketplace-publishing-report-seller-insights.md)
- [Informazioni sulla creazione di report di pagamento](marketplace-publishing-report-payout.md)
- [Come modificare l'incentivi rivenditore Provider di soluzioni Cloud](marketplace-publishing-csp-incentive.md)
- [Risoluzione dei problemi comuni di pubblicazione del Marketplace](marketplace-publishing-support-common-issues.md)
- [Ottenere supporto come un autore](marketplace-publishing-get-publisher-support.md)
- [Creazione di un'immagine di macchine Virtuali locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creare una macchina virtuale che esegue Windows nel portale di anteprima Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
