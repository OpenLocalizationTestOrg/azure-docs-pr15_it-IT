<properties
    pageTitle="Passaggio 5: Distribuire il servizio Web di Microsoft Learning automatica | Microsoft Azure"
    description="Passaggio 5 della Develop una procedura dettagliata soluzione predittiva: distribuire un esperimento predittivo in computer Learning Studio come un servizio Web."
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Procedura dettagliata passaggio 5: Distribuire il servizio Web di apprendimento automatica di Azure

Si tratta del passaggio 5 della procedura dettagliata, [Develop una soluzione di stima analitica in Azure l'apprendimento](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creare un'area di lavoro di apprendimento](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.  [Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Formazione e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Distribuire il servizio Web**
6.  [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Per concedere ad altri la possibilità di utilizzare il modello predittivo che abbiamo sviluppato in questa procedura, è distribuirlo come un servizio Web in Azure.

Fino a questo punto è stata provato a formazione il nostro modello. Ma servizio distribuito è non è più possibile ottenere formazione: genera le previsioni per l'input dell'utente basato sul nostro modello di classificazione. Pertanto dobbiamo eseguire alcune operazioni di preparazione per la conversione di questo esperimento da un esperimento di ***formazione*** per una ***stima*** sperimentare. 

Questo è un processo in due fasi:  

1. Convertire la *formazione sperimentare* che abbiamo creato in un *esperimento predittiva*
2. Distribuire esperimento predittiva come un servizio Web

Tuttavia, per prima cosa, è necessario tagliare un po' questo esperimento verso il basso. Sono due diversi modelli in esperimento, ma si desidera solo un unico modello quando questo viene distribuita come un servizio Web.  

Si supponga che abbiamo deciso che il modello struttura innalzamento è stata migliore modello da utilizzare. In modo che il primo consiste nella rimuovere la [Classe due supporto vettoriale automatica] [ two-class-support-vector-machine] modulo e i moduli utilizzati per la formazione viene. È possibile creare innanzitutto una copia di esperimento facendo clic su **Salva con nome** nella parte inferiore dell'area di prova.

Sono necessari eliminare i moduli seguenti:  

- [Supporto di due classi vettoriale automatica][two-class-support-vector-machine]
- [Formazione modello] [ train-model] [Modello punteggio] e[ score-model] moduli di cui sono stati connessi
- [Normalizzare i dati] [ normalize-data] (entrambi gli elementi)
- [Valutazione del modello][evaluate-model]

Selezionare il modulo e premere il tasto CANC o destro del mouse sul modulo e scegliere **Elimina**.

A questo punto, siamo pronti per distribuire questo modello utilizzando l' [Albero delle decisioni aumentata due classe][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire un esperimento predittivo esperimento formazione

La conversione a un esperimento predittivo comporta tre passaggi:

1. Salvare il modello è stato addestrati e sostituire i moduli di formazione
2. Trim esperimento per rimuovere i moduli che sono necessarie solo per la formazione
3. Definire cui il servizio Web accetta l'input e dove verrà generato l'output

Fortunatamente, è possono eseguire tutti i tre passaggi facendo clic sul **servizio impostare Web** nella parte inferiore dell'area di prova (selezionare l'opzione **predittiva Web service** ).

Quando si fa clic su **servizio impostare Web**, si verificano eventi diversi:

- Il modello di formazione viene salvato come un singolo modulo di **Formazione modello** nella tavolozza di modulo a sinistra dell'area di prova (è possibile risulti in **Modelli di formazione**).
- Vengono rimossi i moduli utilizzati per la formazione. In modo specifico:
  - [Albero decisionale innalzamento di livello 2][two-class-boosted-decision-tree]
  - [Modello treno][train-model]
  - [Suddividere i dati][split]
  - il secondo [Eseguire Script R] [ execute-r-script] modulo utilizzato per i dati di test
- Il modello di formazione salvato viene aggiunto alla esperimento.
- Vengono aggiunti i moduli **Web service input** e **output del servizio Web** .

> [AZURE.NOTE] Esperimento è stato salvato in due parti in schede che sono stati aggiunti nella parte superiore dell'area di esperimento: esperimento formazione originale della scheda **formazione sperimentare**, e nel esperimento predittiva appena creato in **predittiva sperimentare**.

È necessario eseguire un passaggio aggiuntivo con questa particolare esperimento.
È stato aggiunto due [Eseguire Script R] [ execute-r-script] moduli per fornire una funzione di ponderazione ai dati per la formazione e il testing. Non è necessario eseguire questa operazione nel modello finale.

Macchina Learning Studio rimuovere uno [Script R eseguire] [ execute-r-script] modulo quando rimossa la [divisione] [ split] modulo. Ora è ora possibile rimuovere l'altra e connettersi [Metadati Editor] [ metadata-editor] direttamente al [Modello punteggio][score-model].    

Il nostro esperimento dovrebbe essere simile al seguente:  

![Punteggio il modello di formazione][4]  

> [AZURE.NOTE] Per comprendere il motivo per cui è stato lasciato il set di dati di dati di carta di credito tedesco UCI esperimento predittiva. Il servizio verrà per utilizzare i dati dell'utente, non il set di dati originale, perché lasciare invariata dataset originale nel modello?
>
>È vero che il servizio non ha necessità di dati di carta di credito. Ma è necessario che lo schema per i dati, che include informazioni quali il numero di colonne sono disponibili e le colonne sono valori numeriche. Queste informazioni sullo schema sono necessarie interpretare i dati dell'utente. Viene lasciare questi componenti connessi in modo che il modulo punteggio ha lo schema del set di dati quando viene eseguito il servizio. I dati non viene utilizzati, solo lo schema.  

Eseguire l'ultima volta esperimento, scegliere **Esegui**. Se si desidera verificare che il modello sia ancora funzionante, fare clic sull'output del [Modello di punteggio] [ score-model] modulo e selezionare **Visualizza risultati**. Verrà visualizzato che vengono visualizzati dati originali, con il valore di rischio di credito ("etichette di punteggio") e il valore di probabilità punteggio ("punteggio probabilità".) 

## <a name="deploy-the-web-service"></a>Distribuire il servizio Web

È possibile distribuire esperimento come servizio Web classico o un nuovo servizio Web basato su Gestione risorse di Azure.

### <a name="deploy-as-a-classic-web-service"></a>Distribuire come un servizio Web classico ###

Per distribuire un servizio Web classico derivato dall'esperimento, scegliere **Distribuzione di servizio Web** sotto l'area di disegno e selezionare **Distribuzione di servizio Web [classica]**. Studio di apprendimento automatica distribuita esperimento come servizio Web e consente di passare al dashboard per il servizio Web. Da qui è possibile tornare a esperimento (**Visualizza snapshot** o **visualizzare più recente**) ed eseguire un test semplice del servizio Web (vedere **Test del servizio Web** riportata di seguito). È disponibile anche informazioni per la creazione di applicazioni che possono accedere al servizio Web (ulteriori informazioni nel passaggio successivo di questa procedura dettagliata).

![Dashboard del servizio Web][6]

È possibile configurare il servizio facendo clic sulla scheda **configurazione** . In questo caso è possibile modificare il nome del servizio (è assegnato il nome esperimento per impostazione predefinita) e fornire una descrizione. È inoltre possibile assegnare più descrittivo etichette per le colonne di input e outpue.  

![Configurare il servizio Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Distribuire come un servizio Web nuovo

Per distribuire un servizio Web nuovo derivato da esperimento, fare clic su **Servizio Web di distribuire** sotto l'area di disegno e **Distribuzione di servizio Web [New]**. Studio di apprendimento automatica trasferisce pagina esperimento distribuire servizi Web Learning Machine Azure.

Immettere un nome per il servizio Web e selezionare un piano di prezzo. Se si dispone di un piano di prezzo esistente, che è possibile selezionare, in caso contrario è necessario creare un nuovo piano di prezzi per il servizio. 

1.  **Plan prezzo** dal menu a discesa selezionare un piano esistente o selezionare l'opzione **Selezionare nuovo piano** .
2.  In **Plan nome**digitare un nome che identifica il piano sulla fattura online.
3.  Selezionare uno dei **Livelli pianificare mensile**. Il valore predefinito livelli per il piano per i piani per l'area predefinita e il servizio Web viene distribuito in tale area.

Scegliere **Distribuisci** e la pagina **della Guida rapida** per l'apertura di servizio Web.

È possibile configurare il servizio dall'opzione di menu **Configura** . In questo caso è possibile modificare il nome del servizio (è assegnato il nome esperimento per impostazione predefinita) e fornire una descrizione. 

Per testare selezionare servizio Web, fare clic sull'opzione **Test** (vedere **Test del servizio Web** riportata di seguito). Per informazioni sulla creazione di applicazioni che possono accedere al servizio Web, fare clic sull'opzione di menu **consumare** (ulteriori informazioni nel passaggio successivo di questa procedura dettagliata).

> [AZURE.TIP] Dopo aver distribuito, è possibile aggiornare il servizio Web. Ad esempio, se si desidera modificare il modello, modificare esperimento formazione, modificare i parametri di modello e fare clic su **Servizio Web di distribuzione**. Selezionare **La distribuzione del servizio Web [classica]** o **Distribuire un servizio Web [New]**. Quando si distribuisce l'esperimento nuovamente, viene sostituito il servizio Web, utilizzando il modello aggiornato.  

## <a name="test-the-web-service"></a>Testare il servizio Web

### <a name="test-a-classic-web-service"></a>Test di un servizio Web classico

È possibile testare il servizio in computer Learning Studio o nel portale dei servizi Web Learning automatica di Azure. Verifica nel portale dei servizi Web di Azure automatica Learning offre il vantaggio di in modo da attivare 

**Testare in computer Learning Studio**

Nella pagina del **DASHBOARD** , fare clic sul pulsante **Test** in **Endpoint predefinito**. Una finestra di dialogo verrà visualizzati e disponibili chiedono i dati di input per il servizio. Queste sono le stesse colonne che veniva visualizzato nel dataset rischio credito tedesca originale.  

Immettere un blocco di dati e quindi fare clic su **OK**. 

**Testare nel portale dei servizi Web di Azure macchina apprendimento**

Nella pagina del **DASHBOARD** , fare clic sul collegamento di anteprima **Test** in **Endpoint predefinito**. La pagina di prova nel portale dei servizi Web di Azure automatica Learning per l'endpoint del servizio Web viene aperto e viene richiesto per i dati di input per il servizio. Queste sono le stesse colonne che veniva visualizzato nel dataset rischio credito tedesca originale.

Fare clic su **Test richiesta-risposta**. 

Nel servizio Web, i dati vengono immessi tramite la funzionalità di **input del servizio Web** , tramite l' [Editor di metadati] [ metadata-editor] modulo e per il [Modello di punteggio] [ score-model] modulo dove risulti. I risultati vengono quindi generati dal servizio Web tramite l' **output del servizio Web**.

**Testare un nuovo servizio Web**

Nel portale di servizi Web di Microsoft Learning automatica Azure, fare clic su **Test** nella parte superiore della pagina. Verrà visualizzata la pagina di **prova** e che è possibile inserire i dati per il servizio. I campi di input visualizzati corrispondono alle colonne che veniva visualizzato nel set di dati di rischio credito tedesca originale. 

Immettere un blocco di dati e quindi fare clic su **Test richiesta-risposta**.

I risultati del test verranno visualizzata sul lato destro della pagina nella colonna output. 

Durante il test nel portale dei servizi Web di Azure macchina apprendimento, è possibile abilitare i dati di esempio che è possibile utilizzare per testare il servizio di richiesta-risposta. Se è stato creato il servizio Web nel computer Learning Studio, i dati di esempio vengono recuperati dai dati l'usati per formare il modello.

> [AZURE.TIP] Come è stato esperimento predittiva configurata, l'intero risultati da [Modello punteggio] [ score-model] modulo vengono restituiti. Include tutti i dati di input e il valore di rischio di credito e la probabilità punteggio. Se si desidera restituire un valore diverso -, ad esempio solo il credito risk valore - quindi è possibile inserire un [Progetto colonne] [ project-columns] modulo tra [Modello punteggio] [ score-model] e l' **output del servizio Web** per eliminare le colonne non si desidera il servizio Web da restituire. 

## <a name="manage-the-web-service"></a>Gestire il servizio Web

**Gestione di un servizio Web classica**

Dopo che è stato distribuito il servizio Web classico, è possibile gestire dal [portale classica Azure](https://manage.windowsazure.com).

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Nel riquadro Servizi Microsoft Azure, fare clic su **Automatica LEARNING**.
3. Fare clic su area di lavoro.
4. Fare clic sulla scheda **servizi Web** .
5. Fare clic sul servizio Web che è stato creato.
6. Fare clic sull'endpoint "predefinita".

Da qui è possibile eseguire operazioni come monitorare l'attività come il servizio Web e creazione delle prestazioni ottimizzazioni modificandone simultanee quanti chiama il servizio in grado di gestire.
È anche possibile pubblicare il servizio Web in Azure Marketplace.

Per ulteriori informazioni, vedere:

- [Creazione di endpoint](machine-learning-create-endpoint.md)
- [Servizio Web di implementazione della scalabilità](machine-learning-scaling-webservice.md)
- [Pubblicare servizio Web di apprendimento di Azure automatica in Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gestire il servizio Web nel portale dei servizi Web Learning automatica di Azure**

Dopo aver distribuito il servizio Web, classica o nuovo, è possibile gestire dal [portale di servizi Web di Microsoft Learning automatica Azure](https://servics.azureml.net).

Per monitorare le prestazioni del servizio Web:

1. Accedere al [portale di servizi Web di Microsoft Learning automatica Azure](https://servics.azureml.net).
2. Fare clic su **servizi Web**.
3. Fare clic su servizio Web.
4. Fare clic su **Dashboard**.

----------

**Avanti: [accesso al servizio Web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/