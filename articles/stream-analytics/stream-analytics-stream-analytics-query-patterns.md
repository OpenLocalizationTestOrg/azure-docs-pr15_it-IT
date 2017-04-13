<properties
    pageTitle="Esempi di modelli di utilizzo comune in flusso Analitica query | Microsoft Azure"
    description="Modelli di Query Analitica Azure flusso comuni "
    keywords="esempi di query"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Esempi di modelli di utilizzo comune Analitica flusso di query

## <a name="introduction"></a>Introduzione

Query di Azure flusso Analitica sono espressi in un linguaggio di query SQL simili, descritte nella Guida di [Riferimento al linguaggio Query flusso Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Questo articolo illustra le soluzioni ai diversi modelli di query comuni basati su scenari reali.  È un lavoro in corso e continuerà a essere aggiornato con nuovi modelli in modo continuativo.

## <a name="query-example-data-type-conversions"></a>Esempio di query: le conversioni del tipo di dati
**Descrizione**: definire i tipi di proprietà sul flusso di input.
Ad esempio, lo spessore di auto sarà disponibile a breve sul flusso di input come stringhe e deve essere convertito in INT per eseguire sommati automaticamente.

**Input**:

| Rendere | Ora | Peso |
| --- | --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z | "1000" |
| Rappresentato | 2015-01-01T00:00:02.0000000Z | "2000" |

**Output**:

| Rendere | Peso |
| --- | --- |
| Rappresentato | 3000 |

**Soluzione**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Spiegazione**: utilizzare un'istruzione CAST sul campo peso per specificare il tipo (vedere l'elenco dei tipi di dati supportati [qui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Esempio di query: uso mi piace/non vogliono serie corrispondenti
**Descrizione**: controllare che il valore di un campo sull'evento corrisponde a un determinato modello, ad esempio, restituite lastre di licenza che iniziano con una lettera e terminano con 9

**Input**:

| Rendere | LicensePlate | Ora |
| --- | --- | --- |
| Rappresentato | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | 369 ABC | 2015-01-01T00:00:03.0000000Z |

**Output**:

| Rendere | LicensePlate | Ora |
| --- | --- | --- |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | 369 ABC | 2015-01-01T00:00:03.0000000Z |

**Soluzione**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Spiegazione**: utilizzare l'istruzione LIKE per verificare che il valore del campo LicensePlate inizia con una lettera quindi con qualsiasi stringa di zero o più caratteri e termina con 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Esempio di query: specificare una logica per diversi casi valori (istruzioni maiuscole e MINUSCOLE)
**Descrizione**: fornire calcolo diverso per un campo in base ad alcuni criteri.
Ad esempio, fornire una descrizione di stringa per auto quanti passato di apportare stesso con un tipo speciale per 1.

**Input**:

| Rendere | Ora |
| --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output**:

| CarsPassed | Ora |
| --- | --- | --- |
| 1 rappresentato | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Soluzione**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Spiegazione**: clausola di maiuscole/MINUSCOLE consente di specificare un calcolo diverso in base ad alcuni criteri (in questo caso il conteggio di Auto nella finestra di aggregazione).

## <a name="query-example-send-data-to-multiple-outputs"></a>Esempio di query: inviare dati a più output
**Descrizione**: inviare dati a più destinatari di output da un singolo processo.
Ad esempio analizzare i dati per un avviso di soglia e l'archiviazione di tutti gli eventi a archiviazione blob

**Input**:

| Rendere | Ora |
| --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z |
| Rappresentato | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**USCITA1**:

| Rendere | Ora |
| --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z |
| Rappresentato | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**USCITA2**:

| Rendere | Ora | Conta. numeri |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Soluzione**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Spiegazione**: il nella clausola indica flusso Analitica che di output per scrivere i dati dell'istruzione.
La prima query è una pass-through dei dati che vengono ricevuti in un output è denominato ArchiveOutput.
La seconda query non alcune aggregazione semplice e filtro e i risultati vengono inviati a un sistema di avvisi downstream.
*Nota*: È inoltre possibile riutilizzare i risultati della CTE (ossia con istruzioni) in più istruzioni di output: questo è il vantaggio di apertura di un numero minore ai lettori di origine di input.
Per esempio 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Esempio di query: il conteggio dei valori univoci
**Descrizione**: contare il numero di valori di campo univoco che vengono visualizzati nel flusso all'interno di un intervallo di tempo.
Ad esempio, quanti rendere univoci di auto passati stand a pagamento in una seconda finestra 2?

**Input**:

| Rendere | Ora |
| --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z |
| Rappresentato | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output:**

| Conta. numeri | Ora |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Soluzione:**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Spiegazione:** Facciamo un'aggregazione iniziale per ottenere rende univoci con il conteggio sopra la finestra.
È quindi possibile eseguire un'aggregazione di quante rende è got – assegnati univoco tutti i valori in una finestra di ottenere lo stesso timestamp quindi la seconda finestra di aggregazione deve essere minima a non eseguirà l'aggregazione 2 windows il primo passaggio.

## <a name="query-example-determine-if-a-value-has-changed"></a>Esempio di query: determinare se un valore è stato modificato#
**Descrizione**: cercare un valore precedente per determinare se è diversa rispetto al valore corrente, ad esempio Auto precedente in viaggio a pagamento lo stesso effettuare come Auto corrente?

**Input**:

| Rendere | Ora |
| --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Output**:

| Rendere | Ora |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Soluzione**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Spiegazione**: usare ritardo da leggere per l'input flusso di un nuovo evento e ottenere il valore di creazione. Confrontare da prendere sull'evento corrente ed output l'evento se sono diversi.

## <a name="query-example-find-first-event-in-a-window"></a>Esempio di query: Trova primo evento in una finestra
**Descrizione**: trova prima auto in intervalli di 10 minuti?

**Input**:

| LicensePlate | Rendere | Ora |
| --- | --- | --- |
| DXE 5291 | Rappresentato | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Rappresentato | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Rappresentato | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Output**:

| LicensePlate | Rendere | Ora |
| --- | --- | --- |
| DXE 5291 | Rappresentato | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Rappresentato | 2015-07-27T00:12:02.0000000Z |

**Soluzione**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

A questo punto si modifica il problema e trovare un'auto prima di particolare effettuare in intervalli di 10 minuti.

| LicensePlate | Rendere | Ora |
| --- | --- | --- |
| DXE 5291 | Rappresentato | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Rappresentato | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Soluzione**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Esempio di query: trova ultimo evento in una finestra
**Descrizione**: trova ultima auto in intervalli di 10 minuti.

**Input**:

| LicensePlate | Rendere | Ora |
| --- | --- | --- |
| DXE 5291 | Rappresentato | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Rappresentato | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Rappresentato | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Output**:

| LicensePlate | Rendere | Ora |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Soluzione**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Spiegazione**: sono disponibili due passaggi della query, il primo trova timestamp più recente di windows 10 minuti. Il secondo passaggio unisce i risultati della prima query con flusso originale per trovare gli eventi che corrispondono ultimo timestamp in ogni finestra. 

## <a name="query-example-detect-the-absence-of-events"></a>Esempio di query: rilevare l'assenza di eventi
**Descrizione**: verificare che un flusso non ha alcun valore corrispondente a determinati criteri.
Ad esempio 2 Auto consecutive da stesso stato immesso strada a pagamento entro 90 secondi?

**Input**:

| Rendere | LicensePlate | Ora |
| --- | --- | --- |
| Rappresentato | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Rappresentato | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEFINIZIONE 987 | 2015-01-01T00:00:03.0000000Z |
| Rappresentato | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**Output**:

| Rendere | Ora | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Rappresentato | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**Soluzione**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Spiegazione**: usare ritardo da leggere per l'input flusso di un nuovo evento e ottenere il valore di creazione. Confrontare da prendere sull'evento corrente ed output l'evento se sono gli stessi e utilizzare ritardo per recuperare i dati relativi Auto precedente.

## <a name="query-example-detect-duration-between-events"></a>Esempio di query: consente di individuare la durata tra gli eventi
**Descrizione**: trovare la durata di un determinato evento. Ad esempio, dato un clickstream web determinare tempo dedicato ad una caratteristica.

**Input**:  
  
| Utente | Caratteristica | Evento | Ora |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Inizio | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Fine | 2015-01-01T00:00:08.0000000Z |
  
**Output**:  
  
| Utente | Caratteristica | Durata |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Soluzione**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Spiegazione**: ultima funzione utilizzare per recuperare l'ultimo valore di ora al tipo di evento è stato 'Start'. Si noti che utilizza ultima funzione partizione da [utente] per indicare che risultato verrà calcolato per ogni utente univoco.  La query ha un limite massimo di 1 ora per differenza tra gli eventi 'Start' e 'Interrompi' ma configurabile come base alle esigenze (limite DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Esempio di query: consente di individuare la durata di una condizione
**Descrizione**: trovare verificata una condizione per il periodo di tempo in uscita.
Si supponga ad esempio che un bug che hanno generato Auto tutti un peso non corretti (più di 20.000 kg) – si desidera calcolare la durata del bug.

**Input**:

| Rendere | Ora | Peso |
| --- | --- | --- |
| Rappresentato | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Rappresentato | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Rappresentato | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Rappresentato | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Output**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Soluzione**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Spiegazione**: usare ritardo per visualizzare il flusso di input per 24 ore e cercare le istanze in cui StartFault e StopFault sono occupate dal peso < 20000.

## <a name="query-example-fill-missing-values"></a>Esempio di query: inserire valori mancanti
**Descrizione**: per il flusso di eventi che hanno valori mancanti, generare un flusso di eventi a intervalli regolari.
Ad esempio generare evento ogni 5 secondi che verranno segnalati coordinata più recente noto.

**Input**:

| t | valore |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

In **output (primi 10 righe)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Soluzione**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Spiegazione**: questa query genererà eventi ogni secondo 5 e verrà restituito l'ultimo evento che è stato ricevuto prima. [Hopping finestra] Durata (https://msdn.microsoft.com/library/dn835041.aspx "Hopping finestra - Azure flusso Analitica") determina la distanza indietro sarà l'aspetto della query per trovare quello più recente (300 secondi in questo esempio).


## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
