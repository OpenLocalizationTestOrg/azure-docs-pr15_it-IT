<properties 
    pageTitle="Utilizzo di regressione lineare in apprendimento | Microsoft Azure" 
    description="Confronto dei modelli di regressione lineare in Excel e in Azure Machine Learning Studio" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Utilizzo di regressione lineare in apprendimento Azure

> *Kate Baroni* e *Ben Boatman* sono progettisti di soluzioni aziendali in dati approfondimenti centro di eccellenza Microsoft. In questo articolo vengono descritti l'esperienza la migrazione di un gruppo di analisi della regressione esistente a una soluzione basata su cloud con apprendimento Azure.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Obiettivo

Iniziare con due obiettivi del progetto:  

1. Utilizzare analitica previsione per migliorare la precisione delle previsioni dei ricavi mensile dell'organizzazione  
2. Consente di Azure ML confermare, ottimizzare e aumentare la velocità e scala i risultati.  

Ad esempio molte aziende azienda attraversa un ricavi mensili processo di previsione. Il nostro team piccoli di analisti è stato il compito di utilizzo di apprendimento per supportare il processo e migliorare la precisione della previsione.  Il team dedicato ad alcuni mesi la raccolta di dati da più origini e l'esecuzione degli attributi di dati tramite analisi statistica identificare gli attributi della chiavi i contenuti pertinenti per la previsione delle vendite di servizi.  Passaggi successivi è stato iniziare modelli statistiche di regressione la creazione di prototipi sui dati in Excel.  Poche settimane era un modello di regressione di Excel che è stata superare il campo corrente e finanze processi di previsione. È diventato il risultato di stima previsto.  


È quindi richiesto il passaggio successivo per lo spostamento di nostro analitica previsione a ML Azure per scoprire come Azure ML potrebbe migliorare sulle prestazioni di Office.


## <a name="achieving-predictive-performance-parity"></a>Per ottenere uniformità stima delle prestazioni

Per ottenere uniformità tra i modelli di regressione Azure ML ed Excel è stato il nostro la massima priorità.  Se gli stessi dati esattamente la stessa suddivisione per la formazione e verifica dei dati vogliamo uniformità stima prestazioni tra Excel e Azure ML.   Inizialmente non è riuscita. Modello di Excel ha superato il modello di Azure ML.   L'errore è stato per mancanza di comprensione dell'impostazione dello strumento base ML Azure. Dopo la sincronizzazione con il team del prodotto ML Azure, abbiamo acquisita comprendere meglio la base impostazione necessaria per il set di dati e ottenere uniformità tra i due modelli.  

### <a name="create-regression-model-in-excel"></a>Creare il modello di regressione in Excel
Il nostro regressione Excel utilizzato il modello di regressione lineare standard nell'analisi Excel. 

Abbiamo calcolato *significa % assoluto errore* e utilizzato come la misurazione delle prestazioni per il modello.  Impiegato 3 mesi per raggiungere un modello di lavoro tramite Excel.  È aggiunta la maggior parte delle risorse nella prova di Azure ML che infine era utile nelle informazioni sui requisiti.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Creare prova comparabile in apprendimento Azure  
Sono stati seguiti questi passaggi per creare il nostro prova in Azure ML:  

1.  Caricare il set di dati in un file csv per Azure ML (file molto piccolo)
2.  Creata una nuova prova e [Selezionare colonne nel set di dati] di utilizzo[ select-columns] modulo per selezionare le stesse funzionalità di dati utilizzate in Excel   
3.  Utilizzare i [Dati divisi] [ split] modulo (con la modalità di *Espressione relativa* ) per dividere i dati in esatta set treno stesso come dovuto in Excel  
4.  Sperimenta [Regressione lineare] [ linear-regression] modulo (solo opzioni predefinite), descritte e confrontare i risultati per il modello di regressione di Excel

### <a name="review-initial-results"></a>Rivedere i risultati iniziali
Innanzitutto il modello di Excel buoni chiaramente il modello di Azure ML:  

|   |Excel|ML Azure|
|---|:---:|:---:|
|Prestazioni|   |  |
|<ul style="list-style-type: none;"><li>R al quadrato corretto</li></ul>| 0,96 |N/D|
|<ul style="list-style-type: none;"><li>Coefficiente di <br />Determinazione</li></ul>|N/D|   0,78<br />(precisione bassa)|
|Significato Absolute Error |  $9. 5M|  $ 19.4 M|
|Significato Absolute Error (%)|   6.03%|  12.2%

Quando si esegue il processo e i risultati per sviluppatori e scienziati dati i membri del team ML Azure, vengono fornite rapidamente alcuni suggerimenti utili.  

* Quando si utilizza la [Regressione lineare] [ linear-regression] modulo ML Azure, sono disponibili due metodi:
    *  Discesa sfumatura online: Potrebbe essere più adatta per i problemi di larga scala
    *  Normale minimi quadrati: Si tratta del metodo che pensare maggior parte delle persone quando viene emesso regressione lineare. Per set di dati piccoli normali minimi quadrati può essere più ottimale.
*  Valutare la possibilità di modificare il parametro L2 Regularization peso per migliorare le prestazioni. È impostato su 0,001 per impostazione predefinita e per le piccole set di dati, è impostato su 0.005 per migliorare le prestazioni.    

### <a name="mystery-solved"></a>Mystery soluzione!
Quando sono applicati i suggerimenti, abbiamo raggiunto le stesse prestazioni di previsione in Azure ML come con Excel:   

|| Excel|Azure ML (iniziale)|Azure ML con ai minimi quadrati|
|---|:---:|:---:|:---:|
|Valore etichetta  |Valori effettivi (numerico)|stesso|stesso|
|Per studenti  |Excel -> dati Analysis -> regressione|Regressione lineare.|Regressione lineare|
|Opzioni per studenti|N/D|Impostazioni predefinite|normali minimi quadrati<br />L2 = 0.005|
|Set di dati|26 righe, 3 caratteristiche ed 1 etichetta.   Valore numerico tutti.|stesso|stesso|
|Dividi: treno|Excel qualificato nella prima 18 righe, su ultima 8 righe.|stesso|stesso|
|Dividi: Test|Formula di regressione applicata per ultima 8 righe di Excel|stesso|stesso|
|**Prestazioni**||||
|R al quadrato corretto|0,96|N/D||
|Coefficiente di determinazione|N/D|0,78|0.952049|
|Significato Absolute Error |$9. 5M|$ 19.4 M|$9. 5M|
|Significato Absolute Error (%)|<span style="background-color: 00FF00;">6.03%</span>|12.2%|<span style="background-color: 00FF00;">6.03%</span>|

Inoltre, i coefficienti di Excel confrontare anche spessori caratteristica nel modello di esperti Azure:

||Coefficienti di Excel|Caratteristica Azure spessori|
|---|:---:|:---:|
|Intercetta/deviazione|19470209.88|19328500|
|Caratteristica risposte|0.832653063|0.834156|
|Funzionalità B|11071967.08|11007300|
|Funzionalità C|25383318.09|25140800|

## <a name="next-steps"></a>Passaggi successivi

Si desidera utilizzare il servizio web ML Azure all'interno di Excel.  Il nostro business analyst si basano su Excel ed è necessario un modo per chiamare il servizio web ML Azure con una riga di dati di Excel e che restituisce il valore previsto in Excel.   

Abbiamo anche voluto ottimizzare il modello, utilizzando le opzioni e algoritmi disponibili in Azure ML.

### <a name="integration-with-excel"></a>Integrazione con Excel
La soluzione era rendono operativi il modello di regressione ML Azure mediante la creazione di un servizio web dal modello di esperti.  Nell'arco di qualche minuto, è possibile chiamare direttamente da Excel per restituire un valore dei ricavi previsti stato creato il servizio web.    

La sezione *Del Dashboard di servizi Web* include una cartella di lavoro di Excel scaricabile.  La cartella di lavoro viene fornito pre-formattato con l'API e schema informazioni sui servizi web incorporati.   Quando si fa clic su *Scarica cartella di lavoro Excel*, è possibile salvarlo nel computer locale viene aperta.    

![][1]
 
Con la cartella di lavoro aperta, copiare i parametri predefiniti nella sezione parametro blu, come illustrato di seguito.  Dopo che sono stati immessi i parametri, Excel viene visualizzato al servizio web AzureML e il valore previsto totalizza etichette verranno visualizzato nell'area valori previsti verde.  La cartella di lavoro continuerà a creare le stime per i parametri in base a un modello di formazione per tutti gli elementi di riga immessa in parametri.   Per ulteriori informazioni su come utilizzare questa caratteristica, vedere [utilizzo di un servizio Web di Azure Machine Learning da Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Ottimizzazione e ulteriormente esperimenti
Ora che si dispone di una previsione con il modello di Excel, sono spostate avanti ottimizzare il modello di regressione lineare Azure ML.  È stato usato il modulo [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] per migliorare la selezione di dati iniziali elementi ed è permesso ottenere un miglioramento delle prestazioni del 4.6% allo Absolute Error.   Questa caratteristica risparmio Contattaci settimane in scorrere gli attributi di dati per trovare il set di caratteristiche da usare per modellazione destro verrà usata per i progetti futuri.  

Successivamente si prevede di includere algoritmi aggiuntivi come [Bayes] [ bayesian-linear-regression] o [Aumentata decisionali] [ boosted-decision-tree-regression] nel nostro test per confrontare le prestazioni.    

Se si desidera sperimentare regressione, un set di dati utile per provare è il set di dati di esempio di regressione l'efficienza energetica, che sono disponibili molti degli attributi numerici. Il set di dati viene fornito come parte del set di dati di esempio ML Studio.  È possibile utilizzare una varietà di moduli di formazione per stimare carico riscaldamento o raffreddamento carico.  Il grafico seguente è che un confronto delle prestazioni di regressione diversi le apprende contro la previsione energetica set di dati per la variabile di destinazione raffreddamento carico: 

|Modello|Significato Absolute Error|Media radice al quadrato errore|Errore relativo a assoluto|Relativo al quadrato errore|Coefficiente di determinazione
|---|---|---|---|---|---
|Albero decisionale invece un incremento|0.930113|1.4239|0.106647|0.021662|0.978338
|Regressione lineare (sfumatura discesa)|2.035693|2.98006|0.233414|0.094881|0.905119
|Rete neurale regressione|1.548195|2.114617|0.177517|0.047774|0.952226
|Regressione lineare (normale minimi quadrati)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Materiale chiave 

Si è appreso molto da regressione di Excel in esecuzione e apprendimento Azure esperimenti in parallelo. Creazione di modelli di base in Excel e il confronto ai modelli utilizzando Azure ML [Regressione lineare] [ linear-regression] utile Contattaci informazioni su Azure ML e individuato opportunità per migliorare le prestazioni di selezione e il modello di dati.         

Trovati è consigliabile utilizzare [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] per accelerare progetti stima futura.  Tramite l'applicazione di selezione delle caratteristiche ai dati, è possibile creare un modello migliorato in Azure ML con prestazioni migliori. 

La possibilità di trasferire la stima analitico la previsione da Azure ML in Excel sistemico consente di aumentare la possibilità di fornire correttamente i risultati a un pubblico utente generali dell'azienda.     


## <a name="resources"></a>Risorse
Sono elencate alcune risorse utili per cui si collabora regressione risoluzione:  

* Regressione in Excel.  Se, dopo aver provato mai regressione in Excel, questa esercitazione è semplice: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Vs regressione la previsione.  Collaborazione tra Tyler Chessman scritto un articolo di blog che spiega come ora serie la previsione in Excel, che contiene la descrizione utile principianti di regressione lineare. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-Forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Normale minimi quadrati regressione lineare: Difetti, problemi e risoluzione dei problemi.  Per un'introduzione e discussione di regressione: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
