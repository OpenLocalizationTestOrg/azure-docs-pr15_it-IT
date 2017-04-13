<properties 
    pageTitle="Funzioni dei dati fabbrica e le variabili di sistema | Microsoft Azure" 
    description="Fornisce un elenco di funzioni di Azure dati fabbrica e le variabili di sistema" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Dati di Azure Factory - funzioni e variabili di sistema
In questo articolo fornisce informazioni sulle funzioni e variabili supportate da Azure Data Factory.
  
## <a name="data-factory-system-variables"></a>Variabili di sistema di dati Factory

Nome della variabile | Descrizione | Ambito dell'oggetto | Limitare l'ambito JSON e Use case
------------- | ----------- | ------------ | ------------------------
WindowStart | Inizio dell'intervallo di tempo per l'attività corrente eseguire finestra | attività | <ol><li>Specificare le query di selezione dati. Vedere gli articoli di connettore a cui fa riferimento nell'articolo [Le attività di spostamento dei dati](data-factory-data-movement-activities.md) .</li><li>Parametri passati al Hive script (esempio mostrato sopra).</li>
WindowEnd | Fine dell'intervallo di tempo per l'attività corrente eseguire finestra | attività | come sopra
SliceStart | Inizio dell'intervallo di tempo per la sezione di dati realizzata | attività<br/>set di dati | <ol><li>Specificare i percorsi cartella dinamico e i nomi di file mentre si lavora con [Blob Azure](data-factory-azure-blob-connector.md) e [set di dati di File System](data-factory-onprem-file-system-connector.md).</li><li>Specificare le dipendenze di input con le funzioni di factory dei dati nella raccolta di input attività.</li></ol>
SliceEnd | Fine dell'intervallo di tempo per sezione di dati corrente realizzate | attività<br/>set di dati | come sopra. 

> [AZURE.NOTE] Attualmente factory dati richiede che la pianificazione specificata nell'attività esattamente identico pianificazione specificata nella disponibilità del set di dati di output. Di conseguenza, WindowStart, WindowEnd e SliceStart e SliceEnd sempre eseguire il mapping allo stesso periodo di tempo e una sezione di output singola.
 
## <a name="data-factory-functions"></a>Funzioni Factory dei dati 

È possibile utilizzare funzioni nella factory dati unitamente sopra le variabili di sistema menzionata ai fini seguenti:

1.  Specificare le query di selezione di dati (vedere gli articoli di connettore a cui fa riferimento l'articolo di [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .

    La sintassi per richiamare una funzione di factory dati: ** $$ ** per le query di selezione di dati e altre proprietà nelle attività e set di dati.  
2. Specificare le dipendenze di input con le funzioni factory dei dati in attività ingressi raccolta (vedere l'esempio precedente).

    $$ non è necessario per specificare le espressioni dipendenza input.   

Nell'esempio riportato di seguito **sqlReaderQuery** proprietà in un file JSON viene assegnata a un valore restituito dalla funzione **Text.Format** . In questo esempio Usa anche una variabile di sistema denominata **WindowStart**, che rappresenta l'ora di inizio dell'attività eseguire finestra.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Funzioni

Le tabelle seguenti elencano tutte le funzioni di Azure Data Factory:

Categoria | Funzione | Parametri | Descrizione
-------- | -------- | ---------- | ----------- 
Ora | AddHours(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge ore Y dato momento X. <br/><br/>Esempio: 9/5/2013 12:00:00 PM + 2 ore = 9/5/2013 2:00:00 PM
Ora | AddMinutes(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge minuti di Y a X.<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 15 minuti = 9/15/2013 12:15:00 PM
Ora | StartOfHour(X) | X: Datetime | Ottiene l'ora di inizio dell'ora rappresentata dal componente ora di X. <br/><br/>Esempio: StartOfHour di 15/9/2013 è 05:10:23 PM 15/9/2013 05:00:00 PM
Data | AddDays(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge giorni di Y a X.<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 2 giorni = 9/17/2013 12:00:00 PM
Data | AddMonths(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge mesi di Y a X.<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 1 mese = 10/15/2013 12:00:00 PM 
Data | AddQuarters(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge Y * 3 mesi x.<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM
Data | AddWeeks(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge Y * 7 giorni alla X<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 1 settimana = 9/22/2013 12:00:00 PM
Data | AddYears(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge anni di Y a X.<br/><br/>Esempio: 9/15/2013 12:00:00 PM + 1 anno = 9/15/2014 12:00:00 PM
Data | Day(X) | X: DateTime | Ottiene il componente giorno di X.<br/><br/>Esempio: Giorno di 15/9/2013 12:00:00 PM è 9. 
Data | DayOfWeek(X) | X: DateTime | Ottiene il componente giorno della settimana di X.<br/><br/>Esempio: DayOfWeek di 15/9/2013 12:00:00 PM è domenica.
Data | DayOfYear(X) | X: DateTime | Ottiene il giorno dell'anno rappresentata dal componente relativo all'anno di X.<br/><br/>Esempi:<br/>1/12/2015: giorno 335 2015<br/>31/12/2015: giorno 365 2015<br/>12/31/2016: giorno 366 del 2016 (bisestile)
Data | DaysInMonth(X) | X: DateTime | Ottiene i giorni del mese rappresentato da componente relativo al mese del parametro X.<br/><br/>Esempio: GiorniMese di 15/9/2013 sono 30 perché non ci sono 30 giorni del mese di settembre.
Data | EndOfDay(X) | X: DateTime | Ottiene il formato data-ora che rappresenta la fine del giorno (componente relativo al giorno) di X.<br/><br/>Esempio: EndOfDay di 15/9/2013 è 05:10:23 PM 15/9/2013 11:59:59 PM.
Data | EndOfMonth(X) | X: DateTime | Ottiene la fine del mese rappresentato da componente mese del parametro X. <br/><br/>Esempio: EndOfMonth di 15/9/2013 è 05:10:23 PM 30/9/2013 11:59:59 PM (orario di data che rappresenta la fine del mese di settembre)
Data | StartOfDay(X) | X: DateTime | Ottiene l'inizio del giorno rappresentato dal componente relativo al giorno del parametro X.<br/><br/>Esempio: StartOfDay di 15/9/2013 è 05:10:23 PM 15/9/2013 12:00:00 AM.
DateTime | FROM(X) | X: stringa | Analizzare stringa X in un'ora di Data.
DateTime | Ticks(X) | X: DateTime | Ottiene i segni di graduazione proprietà del parametro X. Un segno di spunta è uguale a 100 nanosecondi. Il valore di questa proprietà rappresenta il numero di segni di graduazione trascorsi 12:00:00 del, il 1 ° gennaio 0001. 
Testo | Format(X) | X: variabile di tipo stringa | Formatta il testo.

#### <a name="textformat-example"></a>Esempio di Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Argomento vedere [stringhe di formato ora e Data personalizzata](https://msdn.microsoft.com/library/8kb3ddd4.aspx) che descriva diverse opzioni di formattazione è possibile usare (ad esempio: yy e aaaa). 

> [AZURE.NOTE] Quando si usa una funzione all'interno di un'altra funzione, non occorre usare **$$** prefisso per la funzione interna. Ad esempio: $$Text.Format (' eq PartitionKey \\' my_pkey_filter_value\\' e grandi RowKey \\' {0:yyyy-MM-dd hh}\\', Time.AddHours (SliceStart, -6)). In questo esempio, si noterà che **$$** prefisso non viene usata per la funzione **Time.AddHours** . 
  

