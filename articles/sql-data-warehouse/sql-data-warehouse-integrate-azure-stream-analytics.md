<properties
   pageTitle="Utilizzare Analitica flusso Azure con SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di Azure flusso Analitica con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utilizzare Analitica flusso Azure con SQL Data Warehouse

Azure flusso Analitica è un servizio completamente gestito fornendo elaborazione dell'evento complesse bassa latenza elevata disponibilità, scalable sopra il flusso di dati nel cloud. Per informazioni di base, leggere [l'Introduzione alle Azure flusso Analitica][]. Viene illustrato come creare una soluzione-to-end con flusso Analitica, seguendo l'esercitazione [Introduzione all'utilizzo di Azure flusso Analitica][] .

In questo articolo si imparerà a utilizzare il database SQL di Azure Data Warehouse come un sink di output per i processi di vapore Analitica.

## <a name="prerequisites"></a>Prerequisiti

Prima di tutto, eseguire i passaggi seguenti nell'esercitazione [Introduzione all'utilizzo di Azure flusso Analitica][] .  

1. Creare un evento Hub di input
2. Configurare e avviare applicazioni di generatore di evento
3. Effettuare il provisioning di un processo di flusso Analitica
4. Specificare input processo e query

Creare un database SQL di Azure Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Specificare l'output di processo: database Warehouse di dati di SQL Azure

### <a name="step-1"></a>Passaggio 1

Il processo di flusso Analitica fare clic su **OUTPUT** nella parte superiore della pagina e quindi fare clic su **Aggiungi OUTPUT**.

### <a name="step-2"></a>Passaggio 2

Selezionare Database SQL e fare clic su Avanti.

![][add-output]

### <a name="step-3"></a>Passaggio 3
Nella pagina successiva, immettere i valori seguenti:

- *Alias di output*: immettere un nome descrittivo per l'output di processo.
- *Abbonamento*:
    - Se il database di SQL Data Warehouse nello stesso abbonamento il processo di flusso Analitica, selezionare Usa SQL Database dell'abbonamento corrente.
    - Se il database si trova in una sottoscrizione diversa, selezionare Usa SQL Database da un'altra sottoscrizione.
- *Database*: specificare il nome di un database di destinazione.
- *Nome del server*: specificare il nome del server per il database appena specificato. È possibile usare il portale classica Azure per trovare.

![][server-name]

- *Nome utente*: specificare il nome utente di un account dotato di autorizzazioni di scrittura per il database.
- *Password*: immettere la password per l'account utente specificato.
- *Tabella*: specificare il nome della tabella di destinazione nel database.

![][add-database]

### <a name="step-4"></a>Passaggio 4

Fare clic sul pulsante di controllo per aggiungere questo output processo e per verificare che flusso Analitica è possibile connettersi al database.

![][test-connection]

Quando la connessione al database ha avuto esito positivo, verrà visualizzata una notifica nella parte inferiore del portale. È possibile fare clic su Test connessione nella parte inferiore per verificare la connessione al database.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][].

Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduzione al flusso Azure Analitica]: ../stream-analytics/stream-analytics-introduction.md
[Introduzione all'utilizzo di Azure flusso Analitica]: ../stream-analytics/stream-analytics-get-started.md
[Panoramica di sviluppo SQL Data Warehouse]:  ./sql-data-warehouse-overview-develop.md
[Cenni preliminari sull'integrazione di SQL Data Warehouse]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
