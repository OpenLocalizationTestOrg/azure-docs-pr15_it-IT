<properties
    pageTitle="Convertire una prova di formazione di apprendimento in una prova di Office | Microsoft Azure"
    description="Come convertire una prova di formazione di apprendimento, utilizzato per il modello di previsione analitica, per una prova di Office che può essere distribuito come servizio web di formazione."
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
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Convertire una prova di formazione di apprendimento in una prova di Office

Apprendimento Azure consente di creare, testare e distribuire soluzioni analitica previsione.

Dopo aver creato e scorrere in una *formazione provare* per formare il modello di previsione analitica e si è pronti per usarlo per punteggio nuovi dati, è necessario per la preparazione e semplificare la prova per il punteggio. È possibile distribuire questo *prova stima* come un servizio web Azure in modo che gli utenti possano inviare dati a un modello e ricevere le stime del modello.

La conversione di una prova di Office, viene visualizzato il modello di esperto pronto per essere distribuito come servizio web. Gli utenti del servizio web invierà al modello di dati di input e il modello di restituire i risultati della previsione. Così come si converte una prova stima possibile da tenere in considerazione come si prevede che il modello che verrà utilizzato da altri utenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il processo di conversione di una prova di formazione per una prova stima prevede tre passaggi:

1.  Salvare il modello di apprendimento computer che è stata qualificato e quindi sostituire apprendimento algoritmo e il [Modello di treno] [ train-model] moduli con il modello di esperto salvato.
2.  Tagliare prova solo ai moduli necessari per il punteggio. Una prova di formazione include un numero di moduli che sono necessari per la formazione ma non sono necessarie dopo il modello è possibile utilizzare per il punteggio ed esperti.
3.  Definire nel test in cui si desidera accettare dati provenienti da utenti del servizio web e i dati verranno restituiti.

## <a name="set-up-web-service-button"></a>Pulsante Imposta servizio Web

Dopo avere eseguito la prova (pulsante**Esegui** nella parte inferiore dell'area di prova), il pulsante **Di servizio Web** (selezionare l'opzione di **Previsione servizio Web** ) eseguirà automaticamente i tre passaggi della conversione di prova la formazione a una prova di Office:

1.  Il modello di esperto vengono salvate in un modulo nella sezione **Modelli di esperti** della tavolozza modulo (a sinistra dell'area di prova), quindi sostituisce apprendimento algoritmo e il [Modello di treno] [ train-model] moduli con il modello di esperto salvato.
2.  Rimuove i moduli chiaramente non necessari. In questo esempio, inclusi i [Dati divisi][split], 2a [Modello punteggio][score-model]e il [Modello di valutare] [ evaluate-model] moduli.
3.  Crea input servizio Web e moduli di output e aggiungerli nei percorsi predefiniti nel test.

Prova seguente training, ad esempio, un modello di albero decisionale invece un incremento di due classe mediante dati campione:

![Prova di formazione][figure1]

I moduli in questo esercizio eseguano in cui sono disponibili funzioni differenti quattro:

![Funzioni del modulo][figure2]

Quando si converte questo prova di formazione per una prova di Office, alcuni di questi moduli non sono più necessari o che hanno uno scopo diverso:

- **Dati** - i dati in questo set di dati di esempio non viene utilizzata durante il punteggio: l'utente del servizio web fornirà i dati da valutare. Tuttavia, i metadati da questo set di dati, ad esempio tipi di dati, viene utilizzato il modello di esperto. È necessario mantenere il set di dati nel test previsione in modo da poter fornire metadati.

- **Prepara** , a seconda dei dati inviati per il punteggio, questi moduli possono o non possono essere necessari per elaborare i dati in arrivo.

    Ad esempio, in questo esempio il set di dati di esempio possono avere valori mancanti e include le colonne che non sono necessari per formare il modello. Così una [Corretta di dati mancanti] [ clean-missing-data] modulo è stato incluso in una [Selezione colonne nel set di dati] con valori mancanti e[ select-columns] modulo è stato incluso per escludere le colonne aggiuntive dal flusso di dati. Se si è sicuri che i dati inviati per il punteggio tramite il servizio web non avranno valori mancanti, quindi è possibile rimuovere i [Dati mancanti libera] [ clean-missing-data] modulo. Tuttavia, dal momento che le [Colonne selezionate in set di dati] [ select-columns] modulo consente di definire il set di caratteristiche da totalizza, tale modulo deve rimanere.

- **Treno** - dopo il modello è stato qualificato, si salvarlo come un modulo unico modello esperti. Sarà quindi possibile sostituire i singoli moduli con il modello di esperto salvato.

- In questo esempio, il modulo Dividi **punteggio** - viene utilizzato per suddividere il flusso di dati in un set di dati di test e formazione. In prova stima questo non è necessaria e può essere rimossa. Analogamente, 2a [Modello punteggio] [ score-model] modulo e il [Modello di valutare] [ evaluate-model] modulo vengono utilizzati per confrontare i risultati dai dati di test, questi moduli sono inoltre non necessari nella prova previsione. Rimanente [Modello punteggio] [ score-model] modulo, tuttavia, è necessario per restituire un risultato di punteggio tramite il servizio web.

Di seguito è illustrato l'aspetto di esempio dopo aver fatto clic **Su servizio Web**:

![Convertire prova previsione][figure3]

Questa operazione può essere sufficiente per preparare la prova per essere distribuito come servizio web. Tuttavia, può essere necessario eseguire alcune operazioni aggiuntive specifiche per la prova.

### <a name="adjust-input-and-output-modules"></a>Regolare i moduli di input e di output

Nel test di formazione utilizzato un set di dati di formazione e quindi ha alcune elaborazioni per l'accesso ai dati in un modulo che l'algoritmo di apprendimento necessari. Se i dati che si prevede di ricevere tramite il servizio web non saranno necessario questo tipo di elaborazione, è possibile spostare il **modulo di input del servizio Web** su un altro nodo nel test.

Ad esempio, per impostazione predefinita **Di servizio Web** inserisce il modulo di **input del servizio Web** nella parte superiore del flusso di dati, come illustrato nella figura precedente. Tuttavia, se i dati di input non saranno necessario questo tipo di elaborazione, quindi per posizionare manualmente l' **input di servizio Web** oltre i moduli di elaborazione dei dati:

![Spostamento di input del servizio web][figure4]

I dati di input forniti tramite il servizio web passerà direttamente nel modulo del modello di punteggio senza eventuali pre.

Analogamente, per impostazione predefinita **Di servizio Web** inserisce il modulo di output servizio Web nella parte inferiore del flusso di dati. In questo esempio, il servizio web restituirà all'utente l'output del [Modello di punteggio] [ score-model] risultati modulo che include il vettore di dati di input completo e la classificazione.

Tuttavia, se si preferisce restituire un contenuto diverso, ad esempio, solo i risultati punteggio e non il vettore intero dei dati di input, sarà possibile inserire una [Selezione colonne nel set di dati] [ select-columns] modulo per escludere tutte le colonne tranne i risultati punteggio. Quindi spostare il modulo di **output servizio Web** per l'output di [Selezione colonne nel set di dati] [ select-columns] modulo:

![Spostare l'output di servizio web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Aggiungere o rimuovere dati elaborazioni moduli

Se sono presenti altri moduli nel test che si è certi di non saranno necessarie durante il punteggio, è possibile rimuovere queste. Ad esempio, perché sono stati spostati il modulo di **input del servizio Web** su un punto dopo i moduli di elaborazione dei dati, ma può rimuovere i [Dati mancanti libera] [ clean-missing-data] modulo da prova previsione.

La stima prova a questo punto è simile alla seguente:

![Rimozione di moduli aggiuntivi][figure6]

### <a name="add-optional-web-service-parameters"></a>Aggiungere parametri facoltativi di servizio Web

In alcuni casi può essere necessario consentire all'utente del servizio web modificare il comportamento dei moduli quando si accede al servizio. *Parametri di servizio Web* consentono di eseguire questa operazione.

Impostazione di un tipico esempio [Importa dati] [ import-data] modulo in modo che l'utente del servizio web distribuito può specificare un'altra origine dati quando si accede al servizio web. O configurando [Esporta dati] [ export-data] modulo in modo che è possibile specificare una destinazione diversa.

È possibile definire i parametri di servizio Web e associarli a uno o più parametri di modulo e specificare se sono obbligatori o facoltativi. Quando si accede al servizio e le azioni modulo vengono modificate di conseguenza, l'utente del servizio web possa quindi fornire i valori per i parametri.

Per ulteriori informazioni sui parametri di servizio Web, vedere [Utilizzo di Azure Machine Learning Web servizio parametri ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>La distribuzione di prova stima come un servizio web

Ora che prova Office è stato preparato sufficientemente, è possibile distribuirlo come un servizio web Azure. Utilizzo del servizio web, gli utenti possono inviare dati al modello e il modello restituirà le previsioni.

Per ulteriori informazioni sul processo di distribuzione completo, vedere [distribuire un servizio web apprendimento Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
