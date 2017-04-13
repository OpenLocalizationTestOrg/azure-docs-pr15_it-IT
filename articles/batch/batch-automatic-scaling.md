<properties
    pageTitle="Automaticamente scala nodi in un pool di Azure Batch | Microsoft Azure"
    description="Abilitare il ridimensionamento automatico in un pool di cloud per regolare automaticamente il numero dei nodi di calcolo nel pool."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Adattamento automatico nodi di calcolo di un pool di Azure Batch

Per il ridimensionamento automatico, il servizio di Azure Batch dinamicamente aggiungere o rimuovere nodi di calcolo in un pool in base ai parametri definite dall'utente. È possibile risparmiare tempo e denaro regolando automaticamente la quantità di energia calcolo utilizzata dall'applicazione: aggiungere nodi come aumentare le richieste di attività del processo e rimuoverli quando riducono.

Attivare il ridimensionamento automatico in un pool di nodi di calcolo per associare una *formula autoscale* definite dall'utente, ad esempio con [PoolOperations.EnableAutoScale] [ net_enableautoscale] metodo nella libreria di [.NET Batch](batch-dotnet-get-started.md) . Il servizio Batch utilizza quindi questa formula per determinare il numero dei nodi di calcolo necessari per eseguire il carico di lavoro. Batch risponde a metrica servizio esempi di dati che sono stati raccolti periodicamente e modifica il numero dei nodi di calcolo nel pool di intervalli configurabili in base a formula.

È possibile attivare il ridimensionamento automatico quando si crea un pool o in un pool esistente. È inoltre possibile modificare una formula esistente in un pool "autoscale" attivata. Batch consente di valutare le formule prima l'assegnazione di pool, come controllare lo stato di esecuzione di ridimensionamento automatici.

## <a name="automatic-scaling-formulas"></a>Formule di ridimensionamento automatiche

Una formula di ridimensionamento automatica è un valore stringa definite dall'utente che contiene una o più istruzioni e è stato assegnato del pool [autoScaleFormula] [ rest_autoscaleformula] elemento (Batch resto) o [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] proprietà (Batch .NET). Quando assegnata a un pool, il servizio Batch utilizza la formula per determinare il numero dei nodi di calcolo nel pool di destinazione per il successivo intervallo elaborazione (ulteriori informazioni sugli intervalli in un secondo momento). La formula stringa non può superare i 8 KB di dimensioni, può includere fino a 100 istruzioni che sono separate da punti e virgola e possono includere le interruzioni di riga e i commenti.

È possibile pensare delle formule di ridimensionamento automatiche con un Batch autoscale "lingua". Istruzioni formule sono espressioni formato libero che possono includere le variabili di servizio definito (variabili definite dal servizio Batch) e variabili definite dall'utente (variabili definite dall'utente). Eseguono varie operazioni su questi valori utilizzando le funzioni, operatori e tipi incorporati. Ad esempio un'istruzione potrebbe nel modo seguente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formule contengono generalmente più istruzioni che eseguono operazioni sui valori ottenuti nelle istruzioni precedenti. Ad esempio, prima di tutto è ottenere un valore per `variable1`, quindi passare a una funzione per popolare `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Con queste istruzioni nella formula, l'obiettivo è per arrivare a un numero dei nodi di calcolo che il pool di ridimensionamento per il numero di **destinazione** dei **nodi dedicati**. Questo numero può essere superiore, inferiore o uguale al numero corrente dei nodi nel pool. Batch Valuta formula autoscale del pool in un intervallo specifico ([intervalli di ridimensionamento automatici](#automatic-scaling-interval) sono descritti di seguito). Verrà quindi regolare il numero di destinazione dei nodi nel pool di un numero che specifica la formula autoscale in fase di valutazione.

Per fare un rapido esempio questa formula due righe autoscale specifica che il numero dei nodi deve essere corretti in base al numero di attività attive, fino a un massimo di 10 nodi di calcolo:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Sezioni successive di questo articolo illustrano le varie entità che consentono di definire le formule autoscale, incluse variabili, operatori, operazioni e funzioni. Sono disponibili informazioni su come ottenere diverse metriche di elaborazione risorsa e attività all'interno di Batch. È possibile utilizzare queste metriche in maniera intelligente regolare conteggio dei nodi del pool in base allo stato delle attività e l'utilizzo delle risorse. Sarà quindi informazioni su come creare una formula e abilitare il ridimensionamento automatico in un pool utilizzando il resto Batch e l'API di .NET. È necessario completare il processo con alcune formule di esempio.

> [AZURE.IMPORTANT] Ogni account Azure Batch è limitato a un numero massimo di core (e pertanto nodi di calcolo) che può essere usato per l'elaborazione. Il servizio Batch creerà nodi solo fino al limite di base. Di conseguenza, potrebbero non raggiungere il numero di destinazione dei nodi di calcolo specificato da una formula. Per informazioni sulla visualizzazione e aumentare le quote di account, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md) .

## <a name="variables"></a>Variabili

È possibile utilizzare variabili di **servizio definito** e **definite dall'utente** nelle formule autoscale. Il servizio definito variabili sono progettate per il servizio di Batch - alcune sono in lettura / scrittura e alcune sono di sola lettura. Le variabili definite dall'utente vengono che *è* possibile definire. Nella formula di esempio due righe riportati sopra, `$TargetDedicated` è definito servizio variabili, mentre si `$averageActiveTaskCount` è una variabile definite dall'utente.

Tabelle riportate di seguito mostrano le variabili di sola lettura e sola lettura sono definite dal servizio Batch.

È possibile **ottenere** e **impostare** i valori delle variabili servizio definito per gestire il numero dei nodi di calcolo in un pool:

| Lettura e scrittura servizio definito variabili | Descrizione |
| --- | --- |
| $TargetDedicated | Il numero di **destinazione** di **dedicato nodi di calcolo** per il pool. Questo è il numero dei nodi di calcolo che il pool di ridimensionamento per. Dal momento che è possibile che un pool di raggiungere il numero di destinazione dei nodi non è un numero di "destinazione". Questo può verificarsi se il numero di destinazione dei nodi viene modificato nuovamente da una valutazione autoscale successive prima il pool ha raggiunto la destinazione iniziale. Può inoltre verificarsi se si raggiunge una quota di nodo o core account Batch prima che venga raggiunto il numero di destinazione dei nodi. |
| $NodeDeallocationOption | L'azione che si verifica quando i nodi di calcolo vengono rimossi da un pool. Valori possibili sono:<ul><li>**requeue**- termina immediatamente le attività e li inserisce nella coda processo in modo che essi vengono riprogrammate.<li>**terminare**- verrà interrotto immediatamente le attività e li rimuove dalla coda processi.<li>**taskcompletion**- attesa per attualmente in esecuzione attività per terminare e quindi rimuove il nodo del pool.<li>**retaineddata**- attesa per tutti i dati di attività mantenuti locali sul nodo per pulire prima di rimuovere il nodo del pool.</ul> |

È possibile **ottenere** il valore di queste variabili servizio definito per apportare modifiche che si basano sulle metriche dal servizio di Batch:

| Variabili di servizio definito di sola lettura | Descrizione |
| --- | --- |
| $CPUPercent | Percentuale media di utilizzo della CPU. |
| $WallClockSeconds | Il numero di secondi utilizzati. |
| $MemoryBytes | Numero medio di megabyte utilizzato. |
| $DiskBytes | Numero medio di gigabyte utilizzato del disco locale. |
| $DiskReadBytes | Il numero di byte letti. |
| $DiskWriteBytes | Il numero di byte scritti. |
| $DiskReadOps | Eseguita il conteggio di operazioni di lettura del disco. |
| $DiskWriteOps | Eseguita il conteggio delle operazioni di scrittura su disco. |
| $NetworkInBytes | Il numero di byte in ingresso. |
| $NetworkOutBytes | Il numero di byte in uscita. |
| $SampleNodeCount | Conteggio dei nodi di calcolo. |
| $ActiveTasks | Il numero di attività in uno stato attivo. |
| $RunningTasks | Numero di attività in esecuzione. |
| $PendingTasks | Somma delle $ActiveTasks e $RunningTasks. |
| $SucceededTasks | Il numero di attività che è stata completata. |
| $FailedTasks | Il numero di attività che non è riuscita. |
| $CurrentDedicated | Il numero corrente di dedicato nodi di calcolo. |

> [AZURE.TIP] Le variabili di sola lettura, servizio definito visualizzato qui sopra sono *oggetti* che forniscono diversi metodi per accedere ai dati associati a ogni. Per ulteriori informazioni, vedere [ottenere i dati di esempio](#getsampledata) che seguono.

## <a name="types"></a>Tipi di

Questi **tipi** sono supportati in una formula.

- doppia
- doubleVec
- doubleVecList
- stringa
- timestamp timestamp è una struttura composta che contiene i membri seguenti:

    - anno
    - mese (1-12)
    - giorno (1-31)
    - giorno della settimana (nel formato di numero, ad esempio 1 per lunedì)
    - ora (nel formato di numero 24 ore, ad esempio 13 significa 1 PM)
    - minuto (00-59)
    - secondo (00-59)
- timeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Operazioni

Queste **operazioni** sono consentiti per i tipi elencati sopra.

| Operazione                             | Operatori supportati   | Tipo di risultato   |
| ------------------------------------- | --------------------- | ------------- |
| doppia *operatore* doppia              | +, -, *, /            | doppia            |
| doppia *operatore* timeinterval        | *                     | timeInterval      |
| doubleVec *operatore* doppia           | +, -, *, /            | doubleVec         |
| doubleVec *operatore* doubleVec        | +, -, *, /            | doubleVec         |
| timeInterval *operatore* doppia        | *, /                  | timeInterval      |
| timeInterval *operatore* timeinterval  | +, -                  | timeInterval      |
| timeInterval *operatore* timestamp     | +                     | timestamp         |
| timestamp *operatore* timeinterval     | +                     | timestamp         |
| timestamp *operatore* timestamp        | -                     | timeInterval      |
| *operatore*doppia                      | -, !                  | doppia            |
| *operatore*timeinterval                | -                     | timeInterval      |
| doppia *operatore* doppia              | < < =, = =, > =, >,! =  | doppia            |
| stringa di *operatore*              | < < =, = =, > =, >,! =  | doppia            |
| timestamp *operatore* timestamp        | < < =, = =, > =, >,! =  | doppia            |
| timeInterval *operatore* timeinterval  | < < =, = =, > =, >,! =  | doppia            |
| doppia *operatore* doppia              | & &, & #124; & #124;      | doppia            |

Quando si verifica un valore double con un operatore ternario (`double ? statement1 : statement2`), diverso da zero è **vera**e zero è **falsa**.

## <a name="functions"></a>Funzioni

Queste **funzioni** predefinite sono disponibili per l'uso per la definizione di una formula di ridimensionamento automatica.

| Funzione                          | Tipo restituito   | Descrizione
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | doppia        | Restituisce il valore medio di tutti i valori di doubleVecList.
| Len(doubleVecList)                | doppia        | Restituisce la lunghezza del vettore creata dalla doubleVecList.
| LG(Double)                        | doppia        | Restituisce il log di base 2 del doppio.
| LG(doubleVecList)                 | doubleVec     | Restituisce il registro componentwise base 2 del doubleVecList. Un vec(double) devono essere passati in modo esplicito per il parametro. In caso contrario, viene utilizzata la versione lg(double) doppie.
| ln(Double)                        | doppia        | Restituisce il logaritmo naturale del doppio.
| ln(doubleVecList)                 | doubleVec     | Restituisce il registro componentwise base 2 del doubleVecList. Un vec(double) devono essere passati in modo esplicito per il parametro. In caso contrario, viene utilizzata la versione lg(double) doppie.
| log(Double)                       | doppia        | Restituisce il log di base 10 del doppio.
| log(doubleVecList)                | doubleVec     | Restituisce il registro componentwise base 10 del doubleVecList. Un vec(double) devono essere passati in modo esplicito per il parametro double singolo. In caso contrario, viene utilizzata la versione log(double) doppie.
| max(doubleVecList)                | doppia        | Restituisce il valore massimo nel doubleVecList.
| Min(doubleVecList)                | doppia        | Restituisce il valore minimo nel doubleVecList.
| Norm(doubleVecList)               | doppia        | Restituisce la normale due di vettore creata dalla doubleVecList.
| percentile (v doubleVec, p doppia) | doppia        | Restituisce l'elemento percentile di vettore v.
| casuale)                            | doppia        | Restituisce un valore casuale compreso tra 0,0 e 1.0.
| Range(doubleVecList)              | doppia        | Restituisce la differenza tra i valori minimo e massimo di doubleVecList.
| std(doubleVecList)                | doppia        | Restituisce la deviazione standard del campione dei valori nel doubleVecList.
| Stop)                            |               | Interrompe la valutazione dell'espressione il ridimensionamento automatico.
| SUM(doubleVecList)                | doppia        | Restituisce la somma di tutti i componenti del doubleVecList.
| ora (dateTime di stringa = "")          | timestamp     | Restituisce il timestamp dell'ora corrente, se non vengono passati parametri o l'indicatore di data e ora della stringa dateTime se viene passato. Formati supportati dateTime sono W3C DTF e RFC 1123.
| Val (v doubleVec, i doppio)        | doppia        | Restituisce il valore dell'elemento in posizione i in vettore v, con un indice iniziale pari a zero.

Alcune funzioni che sono descritti nella tabella precedente può accettare un elenco come argomento. L'elenco separati da virgola è qualsiasi combinazione di *doppie* e *doubleVec*. Per esempio:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Il valore di *doubleVecList* viene convertito in una singola *doubleVec* prima di valutazione. Ad esempio, se `v = [1,2,3]`, quindi chiamando `avg(v)` è equivalente alla chiamata `avg(1,2,3)`. Chiamare `avg(v, 7)` è equivalente alla chiamata `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Ottenere dati di esempio

Formule AutoScale agiscono su metriche (esempi) i dati forniti dal servizio Batch. Una formula si ingrandisce o riduce le dimensioni del pool in base ai valori ottiene dal servizio. Le variabili servizio definito descritte in precedenza sono oggetti che forniscono diversi metodi per accedere ai dati associato a tale oggetto. Ad esempio, l'espressione seguente viene visualizzata una richiesta per ottenere gli ultimi 5 minuti di utilizzo della CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metodo | Descrizione |
| --- | --- |
| GetSample() | Il `GetSample()` metodo restituisce un vettore di esempi di dati.<br/><br/>Un campione è 30 secondi patrimonio di dati di metrica. Esempi in altre parole, si ottengono ogni 30 secondi. Ma, come indicato di seguito, si verifica un ritardo tra raccolti un campione e quando è disponibile per una formula. Di conseguenza, non tutti gli esempi per un periodo di tempo specifico potrebbero essere disponibili per la valutazione da una formula.<ul><li>`doubleVec GetSample(double count)`<br/>Specifica il numero di campioni di ottenere gli esempi più recenti in cui sono stati raccolti.<br/><br/>`GetSample(1)`Restituisce l'ultimo esempio disponibile. Per metriche come `$CPUPercent`, tuttavia, questo non deve essere utilizzato poiché non è possibile sapere *quando* la raccolta del campione. Potrebbe essere più recente, oppure a causa di problemi del sistema, potrebbe essere molto meno recente. È consigliabile in tal caso per utilizzare un intervallo di tempo, come illustrato di seguito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Specifica un intervallo di tempo per la raccolta di dati di esempio. Facoltativamente, specifica inoltre la percentuale di campioni che devono essere disponibili in intervallo di tempo richiesta.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`restituirebbe 20 campioni se sono presenti nella cronologia CPUPercent tutti gli esempi per gli ultimi dieci minuti. Se l'ultimo minuto della cronologia non è disponibile, tuttavia, verrà restituiti solo 18 esempi. In questo caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`non sono disponibili solo il 90% degli esempi.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`potrebbe avere esito positivo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Specifica un intervallo di tempo per la raccolta di dati con un'ora di inizio e ora fine.<br/><br/>Come detto in precedenza, si verifica un ritardo tra raccolti un campione e quando è disponibile per una formula. Questa operazione è necessario considerare quando si utilizza il `GetSample` metodo. Vedere `GetSamplePercent` sotto.|
| GetSamplePeriod() | Restituisce il periodo di esempi in cui sono state effettuate in un set di dati di esempio nella cronologia. |
| Count) | Restituisce il numero totale di campioni nella cronologia metrica. |
| HistoryBeginTime() | Restituisce il timestamp di esempio disponibile data meno recente per la metrica. |
| GetSamplePercent() |Restituisce la percentuale di esempi in cui sono disponibili per un intervallo di tempo specificato. Per esempio:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Poiché il `GetSample` metodo non riesce se la percentuale di campioni restituito è minore del `samplePercent` specificato, è possibile utilizzare il `GetSamplePercent` metodo per verificare prima di tutto. Quindi è possibile eseguire un'operazione diversa se insufficienti esempi sono presenti, senza interrompere la valutazione di ridimensionamento automatica.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Esempi, alla percentuale di esempio e il metodo *GetSample()*

L'operazione di base di una formula autoscale consiste nel si ottengono dati metrici attività e delle risorse e quindi modificare le dimensioni del pool in base a tali dati. Di conseguenza, è importante avere capire come formule autoscale interagiscono con dati di metrica o "esempi".

**Esempi**

Il servizio Batch periodicamente accetta *esempi* di attività e delle risorse metriche e renderli disponibili per le formule autoscale. In questi esempi vengono registrati ogni 30 secondi dal servizio Batch. È in genere alcuni latenza che causa un ritardo tra quando sono stati registrati questi esempi e quando vengono resi disponibili per (e possono essere letti da) le formule autoscale. Inoltre, a causa di vari fattori, ad esempio problemi di rete o altri infrastruttura, esempi potrebbero non essere stati registrati per un determinato intervallo. Il risultato "mancanti" esempi.

**Percentuale di esempio**

Quando `samplePercent` passato per il `GetSample()` metodo o la `GetSamplePercent()` metodo, "%" si riferisce a un confronto tra il numero totale di tipo *possibili* di esempi in cui sono registrate dal servizio Batch e il numero di campioni effettivamente *disponibili* per la formula autoscale.

Ad esempio possibile esaminare un intervallo di tempo di 10 minuti. Poiché esempi sono registrati ogni 30 secondi, all'interno di un intervallo di tempo di 10 minuti, il numero totale massimo di esempi registrati dal Batch sarà 20 campioni (2 al minuto). Tuttavia, a causa della latenza inerente di meccanismo di creazione di report o un altro problema all'interno dell'infrastruttura di Azure, è possibile solo 15 esempi in cui sono disponibili per la formula autoscale per la lettura. Ciò significa che, per quel periodo di 10 minuti, solo **il 75%** del numero totale di campioni registrato sono realmente disponibili per la formula.

**Intervalli di GetSample() e di esempio**

Le formule autoscale sta per essere ingrandimento o riduzione i pool: aggiunta di nodi o rimozione di nodi. Poiché i nodi costo denaro, si desidera assicurarsi che le formule utilizzano un metodo intelligente di analisi basato su dati sufficienti. Di conseguenza, è consigliabile utilizzare un'analisi di tipo più popolari nelle formule. Questo tipo verrà ingrandimento e riduzione del pool in base a un *intervallo* di campioni raccolti.

Per eseguire questa operazione, utilizzare `GetSample(interval look-back start, interval look-back end)` per restituire un **vettore** di esempi:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando la riga precedente viene valutata tramite Batch, verrà restituito un intervallo di campioni come vettore dei valori. Per esempio:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Dopo aver raccolto vettore di esempi, è quindi possibile utilizzare funzioni come `min()`, `max()`, e `avg()` ricavare significativo per i valori nell'intervallo raccolti.

Per rafforzare la sicurezza, è possibile forzare una valutazione delle formula di *non riuscire* se minore di una determinata percentuale di esempio è disponibile per un periodo di tempo specifico. Quando si forza una valutazione formula l'esito negativo, indicando Batch cessa ulteriormente la valutazione della formula se non è disponibile, ossia la percentuale specificata di campioni e viene apportata alcuna modifica alla dimensione del pool. Per specificare una percentuale desiderata di campioni di valutazione a buon fine, specificare come il terzo parametro `GetSample()`. In questo caso, viene specificato un requisito del 75% di esempi:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

È anche importante, a causa il ritardo descritto in precedenza in disponibilità di esempio, per sempre specificare un intervallo di tempo con un'ora di inizio aspetto indietro antecedenti a un minuto. In questo modo richiede solo pochi minuti per esempi a propagarsi nel sistema, pertanto esempi nell'intervallo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` spesso non saranno disponibili. Nuovo, è possibile utilizzare il parametro di percentuale di `GetSample()` per imporre un requisito di percentuale particolare campione.

> [AZURE.IMPORTANT] È **consigliabile** che è *solo *per evitare il rischio ** di `GetSample(1)` nelle formule autoscale * *. In questo modo `GetSample(1)` essenzialmente indica che per il servizio Batch "forniscono dell'ultimo campione hai, indipendentemente da come tempo fa ottenuto." Poiché si tratta di un unico campione e può essere un esempio precedente, potrebbe non essere rappresentante dell'immagine più grande di recenti attività o stato di risorse. Se si usa `GetSample(1)`, assicurarsi che fa parte di un'istruzione più grande e non il punto di solo i dati che la formula si basa su.

## <a name="metrics"></a>Metrica

È possibile utilizzare le metriche di **risorsa** e **attività** per la definizione di una formula. Modificare il numero di destinazione dei nodi dedicati nel pool in base ai dati metriche da richiedere e valuta. Vedere la sezione [variabili](#variables) sopra per ulteriori informazioni su ogni unità di misura metriche.

<table>
  <tr>
    <th>Unità di misura metriche</th>
    <th>Descrizione</th>
  </tr>
  <tr>
    <td><b>Risorsa</b></td>
    <td><p><b>Metriche di risorse</b> basati sull'utilizzo della CPU, della larghezza di banda e della memoria di nodi di calcolo, come il numero dei nodi.</p>
        <p> Queste variabili servizio definito sono utili per apportare modifiche in base a conteggio dei nodi:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Queste variabili servizio definito sono utili per regolazioni basate sull'utilizzo delle risorse nodo:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Attività</b></td>
    <td><p><b>Attività metriche</b> in base allo stato di avanzamento delle attività, ad esempio attivo, in sospeso e completate. Le variabili di servizio definito seguenti sono utili per effettuare regolazioni di dimensione del pool in base a metriche di attività:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Scrivere una formula in forma autoscale

Creare una formula in forma autoscale formando istruzioni che utilizzano i componenti riportati sopra, quindi combinare le istruzioni in una formula completata. In questa sezione, è necessario creare una formula di esempio autoscale che possono essere eseguite alcune decisioni scala reale.

Prima di tutto, è importante definire i requisiti per la nuova formula autoscale. La formula deve:

1. **Aumentare** il numero di destinazione di nodi di calcolo di un pool se l'utilizzo della CPU sia sufficientemente alto.
2. **Ridurre** il numero di destinazione di nodi di calcolo di un pool quando l'utilizzo della CPU è basso.
3. Sempre limitare il numero **massimo** di nodi 400.

Per *aumentare* il numero dei nodi durante l'utilizzo della CPU elevato, viene definita l'istruzione che popoli una variabile di tipo definito dall'utente (`$totalNodes`) con un valore per il 110 percento del numero di destinazione corrente di nodi, ma solo se l'utilizzo della CPU medio minimo negli ultimi 10 minuti è superiore al 70%. In caso contrario, viene utilizzato il valore dedicato corrente.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Per *ridurre* il numero dei nodi durante l'utilizzo della CPU basso, l'istruzione successiva la formula imposta lo stesso `$totalNodes` variabile al 90% del numero di destinazione corrente dei nodi se l'utilizzo della CPU medio negli ultimi 60 minuti era in 20%. In caso contrario, usare il valore corrente della `$totalNodes` che è stata riempita nell'istruzione precedente.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

A questo punto limitare il numero di destinazione dedicata dei nodi di calcolo a un **massimo** di 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Ecco la formula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Creare un pool autoscale abilitata

Per creare un nuovo pool con il ridimensionamento automatico attivato, è possibile utilizzare una delle tecniche seguenti:

**Batch .NET**

1. Creare il pool con [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Impostare la proprietà [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) `true`.
1. Impostare la proprietà [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) con la formula autoscale.
1. (Facoltativo) Impostare la proprietà [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (valore predefinito è 15 minuti).
1. Eseguire il commit pool con [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) o [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Batch API REST**

* [Aggiungere un pool a un account](https://msdn.microsoft.com/library/azure/dn820174.aspx): specificare il `enableAutoScale` e `autoScaleFormula` gli elementi nella convocazione della API REST per configurare il ridimensionamento automatico per un pool di quando si crea.

Frammento di codice seguente crea un pool di attivazione autoscale utilizzando [.NET Batch] [ net_api] raccolta. Formula autoscale del pool imposta il numero di destinazione dei nodi a 5 lunedì e 1 in ogni giorno della settimana. L' [intervallo di ridimensionamento automatico](#automatic-scaling-interval) è impostato su 30 minuti. In questo e altri c# frammenti di in questo articolo, "myBatchClient" è un'istanza di inizializzato correttamente di [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Oltre alle API REST Batch e .NET SDK, è possibile utilizzare qualsiasi altri [SDK Batch](batch-technical-overview.md#batch-development-apis), [i cmdlet di PowerShell Batch](batch-powershell-cmdlets-get-started.md)e [CLI Batch](batch-cli-get-started.md) per l'uso con il ridimensionamento automatico.

> [AZURE.IMPORTANT] Quando si crea un pool autoscale abilitato, è necessario **non** specificare la `targetDedicated` parametro. Inoltre, se si desidera manualmente ridimensionare un pool di attivazione autoscale (ad esempio con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), quindi è necessario prima **disattivare** scala nel pool di automatica, quindi ridimensionarla.

### <a name="automatic-scaling-interval"></a>Intervallo di ridimensionamento automatico

Per impostazione predefinita, il servizio Batch regola dimensioni del pool secondo la formula autoscale ogni **15 minuti**. Questo intervallo si, tuttavia, utilizzando le seguenti proprietà pool:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (API REST)

L'intervallo minimo è di cinque minuti, e il valore massimo è 168 ore. Se si specifica un intervallo esterno all'intervallo, il servizio Batch verrà restituito un errore richiesta non valida (400).

> [AZURE.NOTE] Il ridimensionamento automatico non è attualmente destinato a rispondere alle modifiche in meno di un minuto, ma viene usato per modificare le dimensioni del pool di gradualmente quando si esegue un carico di lavoro.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Abilitare il ridimensionamento automatico in un pool esistente

Se è già creato un pool con un numero prestabilito di nodi di calcolo con il parametro *targetDedicated* , è comunque possibile attivare il ridimensionamento automatico del pool. Ogni SDK Batch fornisce un'operazione "Abilita autoscale", ad esempio:

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (Batch .NET)
*  [Abilitare il ridimensionamento automatico in un pool di] [ rest_enableautoscale] (API REST)

Quando si abilita il ridimensionamento automatico in un pool esistente, si applica le operazioni seguenti:

* Se il ridimensionamento automatico è **disattivato** nel pool di quando si invia la richiesta di "Abilita autoscale", è *necessario* specificare una formula autoscale valido quando si invia la richiesta. *È possibile* specificare un intervallo di valutazione autoscale. Se non si specifica un intervallo, viene utilizzato il valore predefinito di 15 minuti.

* Se autoscale è **abilitata** nel pool di, è possibile specificare una formula in forma autoscale, un intervallo di valutazione o entrambe. Non è possibile omettere entrambe le proprietà.

  * Se si specifica un nuovo intervallo di valutazione autoscale, la programmazione di valutazione esistente viene interrotta e viene avviata una nuova programmazione. Ora di inizio della nuova programmazione è il tempo in cui è stata inviata la richiesta "abilitare autoscale".

  * Se si omette la formula autoscale o intervallo di valutazione, il servizio Batch continuerà a utilizzare il valore corrente di tale impostazione.

> [AZURE.NOTE] Se è stato specificato un valore per il parametro *targetDedicated* durante la creazione del pool, viene ignorato durante la valutazione della formula di ridimensionamento automatica.

Il frammento di codice c# utilizza [.NET Batch] [ net_api] raccolta per consentire il ridimensionamento automatico in un pool esistente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aggiornare una formula in forma autoscale

Si usa la stessa richiesta "abilitare autoscale" per *aggiornare* la formula in un pool di attivazione autoscale esistente (ad esempio con [EnableAutoScale] [ net_enableautoscale] in Batch .NET). Non esiste alcun operazione "aggiornare autoscale" speciale. Ad esempio, se il ridimensionamento automatico è già attivato in "myexistingpool" quando viene eseguito il codice seguente, la formula autoscale verrà sostituito con il contenuto di `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aggiornare l'intervallo di autoscale

Come con l'aggiornamento di una formula in forma autoscale, utilizzare la stessa [EnableAutoScale] [ net_enableautoscale] metodo per modificare l'intervallo di valutazione autoscale di un pool di attivazione autoscale esistente. Ad esempio, per impostare l'intervallo di valutazione autoscale su 60 minuti per un pool che è già abilitato autoscale:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Valutare una formula in forma autoscale

È possibile valutare una formula prima di applicarla a un pool. In questo modo, è possibile eseguire "test run" della formula per vedere come le relative istruzioni vengono valutate prima di inserire la formula in produzione.

Per valutare una formula in forma autoscale, è necessario prima **abilitare il ridimensionamento automatico** del pool con una **formula valida**. Se si desidera verificare una formula su un pool che non è ancora disponibile il ridimensionamento automatico attivato, è possibile utilizzare la formula di una riga `$TargetDedicated = 0` quando si attiva prima di tutto il ridimensionamento automatico. Quindi, utilizzare una delle operazioni seguenti per valutare la formula da testare:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Questi metodi Batch .NET richiedono l'ID di un pool esistente e una stringa contenente la formula autoscale da valutare. Risultati della valutazione sono contenuti dell'istanza [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) restituito.

* [Valutare una formula di ridimensionamento automatica](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    In questa richiesta API REST, specificare l'ID di pool nell'URI e la formula autoscale nell'elemento *autoScaleFormula* del corpo della richiesta. La risposta dell'operazione contiene le informazioni di errore che potrebbero essere correlate alla formula.

In questo [Batch.NET] [ net_api] frammento di codice, è valutare una formula prima di applicare ai [CloudPool][net_cloudpool]. Se il pool non è disponibile il ridimensionamento automatico attivato, si attivarlo prima di tutto.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Valutato della formula in questo frammento di codice verrà generato un output simile al seguente:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Ottenere informazioni su esecuzioni autoscale

Per garantire che la formula esegue come previsto, è consigliabile che controllare periodicamente i risultati del ridimensionamento automatico "esecuzioni" Batch esegue il pool. In questo modo, (o aggiornare) un riferimento al pool di ed esaminare le proprietà della relativa autoscale ultima esecuzione.

In Batch .NET, la proprietà [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) è diverse proprietà che fornisce informazioni sull'ultima scala automatica mostrarli eseguite nel pool di servizio Batch.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

All'API REST la richiesta di [ottenere informazioni su un pool di](https://msdn.microsoft.com/library/dn820165.aspx) restituisce informazioni sui pool, che include l'informazioni sull'esecuzione di [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun)più recente di adattamento automatico.

Frammento di codice c# riportato utilizza la libreria .NET Batch per stampare le informazioni relative ridimensionamento ultima eseguire nel pool "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Esempio di output del frammento precedente:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Esempio autoscale formule

Esaminiamo alcune formule che mostrano diversi modi per modificare la quantità di risorse in un pool di elaborazione.

### <a name="example-1-time-based-adjustment"></a>Esempio 1: Regolazione di basate sul tempo

Si supponga regolare la dimensione del pool in base a giorno della settimana e l'ora del giorno, per aumentare o ridurre il numero dei nodi nel pool di conseguenza.

Questa formula ottenuto innanzitutto l'ora corrente. Se si tratta di un giorno della settimana (1-5) e all'interno di orari di lavoro (8 AM PM a 6), la dimensione del pool di destinazione è impostata su 20 nodi. In caso contrario, è impostata su 10 nodi.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Esempio 2: Basate sulle attività regolazione

In questo esempio, la dimensione del pool viene regolata in base al numero di attività nella coda. Si noti che i commenti e le interruzioni di riga sono accettabili nelle stringhe di formule.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Esempio 3: Contare le attività in parallelo

Si tratta di un altro esempio che consente di regolare la dimensione del pool in base al numero di attività. Questa formula anche prende in considerazione [MaxTasksPerComputeNode] [ net_maxtasks] valore impostato per il pool. Questa operazione è particolarmente utile nelle situazioni in [esecuzione in parallelo delle attività](batch-parallel-node-tasks.md) è stato attivato il pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Esempio 4: Impostazione di una dimensione pool iniziale

In questo esempio viene illustrato un frammento di codice c# con una formula autoscale che imposta le dimensioni del pool a un determinato numero dei nodi per un periodo di tempo iniziale. Quindi vengono adattate le dimensioni del pool in base al numero di esecuzione e attiva attività trascorso il periodo di tempo iniziale.

La formula frammento di codice seguenti:

- Imposta la dimensione del pool iniziale a quattro nodi.
- Non modificare le dimensioni del pool all'interno di 10 minuti del ciclo di vita del pool.
- Dopo 10 minuti, viene valutato il valore massimo del numero di attività in esecuzione e attiva entro 60 minuti passate.
  - Se entrambi i valori sono 0 (a indicare che nessuna attività è in esecuzione o attiva negli ultimi 60 minuti), la dimensione del pool è impostata su 0.
  - Se il valore è maggiore di zero, viene apportata alcuna modifica.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Passaggi successivi

* [Ingrandire Azure Batch calcolare l'utilizzo delle risorse alle attività simultanee nodo](batch-parallel-node-tasks.md) contiene informazioni dettagliate su come è possibile eseguire più attività contemporaneamente sui nodi di calcolo nel pool. Oltre il ridimensionamento automatico, questa caratteristica può aiutare a ridurre la durata di processo per alcuni carichi di lavoro, riducendo i costi.

* Per un altro richiamo l'efficienza, assicurarsi che l'applicazione Batch query nel servizio Batch in modo ottimale. In una [Query in modo efficiente il servizio di Azure Batch](batch-efficient-list-queries.md), sarà come limitare la quantità di dati che attraversa rete quando si esegue una query lo stato di migliaia di nodi di calcolo o attività.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
