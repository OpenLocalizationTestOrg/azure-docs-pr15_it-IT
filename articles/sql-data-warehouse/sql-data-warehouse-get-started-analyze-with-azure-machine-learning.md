<properties
   pageTitle="Analizzare i dati con apprendimento Azure | Microsoft Azure"
   description="Utilizzare Azure Machine formazione per creare un modello in base a dati archiviati in Data Warehouse di SQL Azure previsione di apprendimento."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/14/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="analyze-data-with-azure-machine-learning"></a>Analizzare i dati con apprendimento Azure

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprendimento Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

In questa esercitazione Usa Azure Machine apprendimento per creare un modello in base a dati archiviati in Data Warehouse di SQL Azure previsione di apprendimento. In particolare, questa si basa una destinazione campagna di marketing di Adventure Works, reparto bicicletta prevedere se un cliente è probabile che acquistare una bicicletta o meno.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]


## <a name="prerequisites"></a>Prerequisiti
Per esaminare in questa esercitazione, è necessario:

- SQL Data Warehouse precaricato con dati di esempio AdventureWorksDW. Per eseguire il provisioning questa operazione, vedere [creare un Data Warehouse SQL][] e scegliere per caricare i dati di esempio. Se è già presente un data warehouse ma non dispone di dati di esempio, è possibile [caricare i dati di esempio manualmente][].

## <a name="1-get-data"></a>1. recuperare dati
I dati sono nella visualizzazione dbo.vTargetMail nel database AdventureWorksDW. Per leggere i dati:

1. Accedere a [studio Azure apprendimento][] e fare clic su miei esperimenti.
2. Fare clic su **+ Nuovo** e selezionare **Prova vuoto**.
3. Immettere un nome per la prova: riservata Marketing.
4. Trascinare il modulo di **utilità per la lettura** dal riquadro moduli nell'area di lavoro.
5. Specificare i dettagli del database SQL Data Warehouse nel riquadro delle proprietà.
6. Specificare la **query** di database per leggere i dati di interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Eseguire la prova facendo clic su **Esegui** sotto l'area di prova.
![Eseguire la prova][1]


Al termine di prova eseguito correttamente, fare clic su porta di output nella parte inferiore del modulo di utilità per la lettura e selezionare **Visualizza** per visualizzare i dati importati.
![Visualizzare i dati importati][3]


## <a name="2-clean-the-data"></a>2. pulire i dati
Per eliminare i dati, rilasciare alcune colonne che non sono rilevanti per il modello. Per procedere come segue:

1. Trascinare il modulo **Progetto colonne** nell'area di lavoro.
2. Fare clic su **barra di avvio selettore di colonna** nel riquadro delle proprietà per specificare le colonne che si desidera eliminare.
![Colonne di progetto][4]

3. Escludere due colonne: CustomerAlternateKey e GeographyKey.
![Rimuovere le colonne non necessarie][5]


## <a name="3-build-the-model"></a>3. creare il modello
Si verranno dividere i dati 80-20: 80% per formare apprendimento modello e 20% per testare il modello. Verrà utilizzare degli algoritmi "Due classe" per questo problema di classificazione binario.

1. Trascinare il modulo **divisa** nell'area di lavoro.
2. Immettere 0,8 per frazione di righe nel primo set di dati di output nel riquadro delle proprietà.
![Dividere i dati in set di test e formazione][6]
3. Trascinare il modulo **Albero decisionale aumentata due classe** nell'area di lavoro.
4. Trascinare il modulo **Treno modello** nell'area di lavoro e specificare gli input. Nel riquadro delle proprietà, quindi fare clic su **Avvia selettore di colonna** .
      - Primo input: algoritmo ML.
      - Secondo input: dati per formare l'algoritmo in.
![Collegare il modulo treno modello][7]
5. Selezionare la colonna **BikeBuyer** come colonna prevedere.
![Selezionare una colonna in base a prevedere][8]


## <a name="4-score-the-model"></a>4. punteggio del modello
A questo punto, si verificherà come esegue il modello di dati di test. Confrontare l'algoritmo scelto con un algoritmo diverso per vedere quale prestazioni migliori.

1. Trascinare **Il modello di punteggio** modulo nell'area di lavoro.
    Primo input: formazione modello secondo input: verificare dati ![punteggio del modello][9]
2. Trascinare **Due Class Bayes punto Machine** nell'area di lavoro di prova. Confrontare come questo algoritmo esegue in confronto di due classe aumentata decisionale.
3. Copiare e incollare i moduli treno modello e punteggio nell'area di disegno.
4. Trascinare il modulo di **Valutare modello** nell'area di lavoro per confrontare due algoritmi.
5. **Eseguire** la prova.
![Eseguire la prova][10]
6. Fare clic su porta di output nella parte inferiore del modulo di valutare modello e fare clic su Visualizza.
![Visualizzare i risultati di valutazione][11]

Metrica forniti è la curva ROC, curva diagramma e ascensore richiamo precisione. Esaminando questi criteri, è possibile vedere che il primo modello eseguita meglio la seconda. Esaminare le informazioni del primo modello previsto, fare clic su porta di output del modello di punteggio e fare clic su effetti grafici.
![Visualizzare i risultati punteggio][12]

Verrà visualizzata aggiunte al set di dati test altre due colonne.

- Probabilità punteggio: la probabilità che un cliente è un acquirente bicicletta.
- Punteggio etichette: classificazione effettuata da modello – acquirente bicicletta (1) o meno (0). Questo limite di probabilità per le etichette è impostato su 50% e può essere modificato.

Confronto della colonna BikeBuyer (effettivi) con le etichette totalizza (stima), è possibile vedere come anche ha eseguito il modello. Come passaggi successivi, è possibile utilizzare questo modello per rendere le stime per i nuovi clienti e pubblicare questo modello come un servizio web o scrivere i risultati in SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di modelli di apprendimento previsione, vedere [Introduzione a computer formazione su Azure][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Studio apprendimento Azure]:https://studio.azureml.net/
[Introduzione a lavorare formazione su Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[caricare i dati di esempio manualmente]: sql-data-warehouse-load-sample-databases.md
[Creare un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
