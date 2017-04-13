<properties
   pageTitle="Usare l'apprendimento Azure con SQL Data Warehouse | Microsoft Azure"
   description="Esercitazione per l'uso di apprendimento Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Usare l'apprendimento Azure con SQL Data Warehouse

Apprendimento Azure è un servizio analitica stima completamente gestita che è possibile utilizzare per creare modelli di Office con i dati in SQL Data Warehouse e quindi pubblica come servizi web pronto da utilizzare. Per informazioni di base della previsione analitica e apprendimento automatico leggendo [Introduzione a computer formazione su Azure][].  È possibile quindi come creare, formare, punteggio e testare un modello di apprendimento utilizzando l'opzione [Crea provare esercitazione][].

In questo articolo viene spiegato come effettuare le seguenti utilizzando [Azure Machine Learning Studio][]:

- Leggere i dati del database da creare, organizzare la formazione e punteggio un modello di stima
- Scrivere dati nel database


## <a name="read-data-from-sql-data-warehouse"></a>Leggere i dati da SQL Data Warehouse

Abbiamo leggerà i dati dalla tabella Product nel database AdventureWorksDW.

### <a name="step-1"></a>Passaggio 1

Avviare una nuova prova facendo clic su + Nuovo nella parte inferiore della finestra del computer risorse Studio selezionare prova e quindi selezionare provare vuoto. Selezionare il nome di prova predefinito nella parte superiore dell'area di disegno e rinominarla significativo, ad esempio, per bicicletta prezzo la funzione di completamento.

### <a name="step-2"></a>Passaggio 2

Cercare il modulo di utilità per la lettura nel riquadro di set di dati e moduli sul lato sinistro dell'area di prova. Trascinare il modulo all'area di lavoro di prova.
![][drag_reader]

### <a name="step-3"></a>Passaggio 3

Selezionare il modulo di utilità per la lettura e compilare il riquadro delle proprietà.

1. Selezionare Database SQL Azure come origine dati.
2. Nome del server di database: digitare il nome del server. È possibile usare il [portale di Azure][] per trovare.

![][server_name]

3. Nome del database: digitare il nome di un database nel server appena specificato.
4. Nome dell'account utente server: digitare il nome utente di un account dotato di autorizzazioni di accesso per il database.
5. Password dell'account utente server: immettere la password per l'account utente specificato.
6. Accettare un certificato server: usare questa opzione (meno sicura) se si desidera esaminare il certificato del sito prima di leggere i dati.
7. Query di database: immettere un'istruzione SQL che descriva i dati che si vuole leggere. In questo caso, si leggerà i dati dalla tabella prodotti con la query seguente.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Passaggio 4

1. Eseguire la prova facendo clic su Esegui sotto l'area di prova.
2. Al termine di prova, il modulo lettore avrà un segno di spunta verde per indicare che è stato completato. Si noti inoltre completato l'esecuzione di stato nell'angolo superiore destro.

![][run]

3. Per visualizzare i dati importati, fare clic su porta di output nella parte inferiore del set di dati veicoli e selezionare Visualizza.


## <a name="create-train-and-score-a-model"></a>Creare, organizzare la formazione e punteggio un modello

A questo punto è possibile utilizzare questo set di dati per:

- Creare un modello: elaborare dati e definire caratteristiche
- Formare il modello: scegliere e applicare un algoritmo di risorse
- Punteggio e testare il modello: prevedere nuovo prezzo per bicicletta


![][model]

Per altre informazioni su come creare, formare punteggio e testare un uso del modello di apprendimento [Crea provare esercitazione][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Scrivere i dati nell'archivio di dati di SQL Azure

Verrà scritto il risultato in ProductPriceForecast tabella nel database AdventureWorksDW.

### <a name="step-1"></a>Passaggio 1

Cercare il modulo Writer nel riquadro di set di dati e moduli sul lato sinistro dell'area di prova. Trascinare il modulo all'area di lavoro di prova.

![][drag_writer]

### <a name="step-2"></a>Passaggio 2

Selezionare il modulo Writer e compilare il riquadro delle proprietà.

1. Selezionare Database SQL Azure come destinazione dei dati.
2. Nome del server di database: digitare il nome del server. È possibile usare il [portale di Azure][] per trovare.
3. Nome del database: digitare il nome di un database nel server appena specificato.
4. Nome dell'account utente server: digitare il nome utente di un account dotato di autorizzazioni di scrittura per il database.
5. Password dell'account utente server: immettere la password per l'account utente specificato.
6. Accettare un certificato server (non protetta): selezionare questa opzione se non si vuole visualizzare il certificato.
7. Elenco delimitato da virgole delle colonne da salvare: specificare un elenco delle colonne set di dati o il risultato che si desidera visualizzare il risultato.
8. Nome tabella dati: specificare il nome della tabella di dati.
9. Elenco delimitato da virgole di colonne datatable: specificare i nomi di colonna da utilizzare nella nuova tabella. I nomi delle colonne possono essere diversi da quelli nel set di dati di origine, ma deve sempre essere elencata lo stesso numero di colonne qui definite dall'utente per la tabella di output.
10. Numero di righe scritte per operazione di SQL Azure: È possibile configurare il numero di righe in cui vengono scritte in un database SQL in un'unica operazione.

![][writer_properties]

### <a name="step-3"></a>Passaggio 3

1. Eseguire la prova facendo clic su Esegui sotto l'area di prova.
2. Al termine di prova, tutti i moduli avrà un segno di spunta verde per indicare che sono state completate correttamente.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Panoramica di sviluppo SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Creare esercitazione di prova]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduzione a lavorare formazione su Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Computer Azure risorse Studio]: https://studio.azureml.net/Home
[Portale di Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
