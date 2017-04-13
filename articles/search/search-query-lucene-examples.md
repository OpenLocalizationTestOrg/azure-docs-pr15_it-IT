<properties
    pageTitle="Esempi di query Lucene per la ricerca di Azure | Ricerca di Microsoft Azure"
    description="Sintassi della query Lucene per la ricerca sfocata, ricerca, termini incrementare, ricerca di espressioni regolari e caratteri jolly."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Esempi di sintassi Lucene query per la creazione di query di ricerca di Azure

Quando si creano query per la ricerca di Azure, è possibile utilizzare la predefinita [nella sintassi della query semplice](https://msdn.microsoft.com/library/azure/dn798920.aspx) o alternativo [Lucene Parser Query di ricerca di Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). Il Parser di Query Lucene supporta più complessi costrutti di query, ad esempio nell'ambito campo query, ricerca sfocata, ricerca, termini incrementare e ricerca di un'espressione reqular.

In questo articolo, è possibile avanzare esempi che visualizzano la sintassi della query Lucene e risultati affiancati. Esempi di eseguire un indice di ricerca precaricato in [JSFiddle](https://jsfiddle.net/), un editor di codice in linea per test script e HTML. 

Fare clic su URL di esempio di query per aprire JSFiddle in una finestra separata del browser.

> [AZURE.NOTE] Negli esempi seguenti si avvalgono un indice di ricerca composta da processi disponibili in base a un set di dati fornito dall'iniziativa [Città di New York OpenData](https://nycopendata.socrata.com/) . Questi dati non devono essere considerati corrente o completata. L'indice è un servizio sandbox fornito da Microsoft. Non è necessario un abbonamento a Azure o una ricerca di Azure a provare queste query.

## <a name="viewing-the-examples-in-this-article"></a>Visualizzare gli esempi riportati in questo articolo

Tutti gli esempi in questo articolo specificare il Parser Query Lucene tramite il parametro di ricerca**queryType** . Quando si utilizza il Parser Query Lucene dal codice, è necessario specificare **queryType** a ogni richiesta.  I valori validi includono **semplice**|**completo**, con **semplice** come predefinito e **completo** per il Parser Query Lucene. Per informazioni dettagliate su come specificare i parametri della query, vedere [Ricerca documenti (Azure ricerca servizio all'API REST)](https://msdn.microsoft.com/library/azure/dn798927.aspx) .

**Esempio 1** - rapida frammento per aprirlo in una nuova pagina di browser che carica JSFiddle ed esegue la query di query come segue:
- [& queryType = completo ed eseguire una ricerca = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

La query seguente restituisce i documenti dall'indice di processi (caricato in un servizio in modalità sandbox)

Nella nuova finestra del browser, verrà visualizzata la sorgente JavaScript e output HTML affiancati. Lo script fa riferimento a una query, viene fornita da URL di esempio in questo articolo. Ad esempio, nell' **esempio 1**, la query sottostante è il seguente:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Avviso la query utilizza un indice ricerca Azure preconfigurato chiamato nycjobs. Il parametro **searchFields** limita la ricerca solo nel campo titolo business. **QueryType** è impostato su **completo**, che indica la ricerca Azure per utilizzare il Parser di Query Lucene per la query.

### <a name="fielded-query-operation"></a>Operazione già distribuito sul campo query

È possibile modificare gli esempi riportati in questo articolo specificando una costruzione **fieldname:searchterm** per definire un'operazione di query già distribuito sul campo, in cui il campo è una singola parola e il termine di ricerca corrisponde a una singola parola o una frase, se lo si desidera con operatori booleani. Alcuni esempi includono le seguenti:

- business_title:(senior NOT junior)
- stato: ("New York" e "Nuovo Jersey")

Assicurarsi di inserire più stringhe racchiuso tra virgolette se si desidera che entrambe le stringhe deve essere valutata come una singola entità, come in questo caso la ricerca di due città distinte nel campo posizione. Inoltre, assicurarsi che l'operatore viene convertita in maiuscolo come viene visualizzato con non e and.

Il campo specificato in **fieldname:searchterm** deve essere un campo di ricerca. Per informazioni sull'utilizzo di attributi indice in definizioni di campo, vedere [Create Index (Azure ricerca servizio all'API REST)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Ricerca sfocata

Una ricerca sfocata consente di trovare corrispondenze in termini che dispone di una costruzione simile. Per [la documentazione Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)sfocate ricerche sono basate su [Damerau Levenshtein distanza](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Per eseguire una ricerca sfocata, utilizzare la tilde "~" simbolo alla fine di una singola parola con un parametro facoltativo, un valore compreso tra 0 e 2, che specifica la distanza di modifica. Ad esempio, "blu ~" o "blu ~ 1" restituirebbe blu, blu e associa.

**Esempio 2** - rapida frammento di query riportato di seguito per Provatelo adesso. Questa query Cerca per funzioni di business con senior termini nel loro, ma non consentirgli:

- [& queryType = completo ed eseguire una ricerca = business_title:senior non consentirgli](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Ricerca

Ricerche di prossimità vengono utilizzate per trovare termini che sono vicini in un documento. Inserire una tilde "~" simbolo alla fine di una frase seguita dal numero di parole che compongono il limite di prossimità. Ad esempio, "aeroporto albergo" ~ 5 troverà l'albergo termini e aeroporto all'interno di 5 parole in un documento.

**Esempio 3** - scelta frammento di query riportato di seguito. Questa query Cerca per i processi con associate termini (nel punto in cui si è errato):

- [& queryType = completo ed eseguire una ricerca = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Esempio 4** - pulsante destro del mouse sulla query. Cercare i processi con il termine "senior analyst" in cui sono separata da non più di una parola:

- [& queryType = completo ed eseguire una ricerca = business_title: "senior analyst" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Esempio 5** , provare a rimuovere nuovamente le parole tra il termine "senior analyst".

- [& queryType = completo ed eseguire una ricerca = business_title: "senior analyst" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Termine miglioramento

Termini incrementare fa riferimento a un documento superiore se contiene invece un incremento termine relativamente documenti che non contengono il termine di classificazione. È diverso dal punteggio profili in profili punteggio promuovere determinati campi, anziché termini specifici. Nell'esempio seguente consente di illustrare le differenze.

Valutare la possibilità di un profilo punteggio aumenta corrisponde a un determinato campo, ad esempio **perimetro** nell'esempio musicstoreindex. Termini incrementare potrebbero essere utilizzate per promuovere ulteriormente determinato ricerca termini superiori rispetto ad altri. Ad esempio, "estremamente ^ 2 elettronico" amplia nei documenti che contengono i termini di ricerca nel campo **perimetro** superiore a altri campi che supportano le ricerche nell'indice analitico. Inoltre, nei documenti che contengono il termine di ricerca "estremamente" verranno classificati superiore altri termini di ricerca "elettronico" in seguito il valore di incremento termini (2).

Per promuovere un termine, utilizzare il punto di inserimento, "^", simbolo con un fattore di incremento (numero) alla fine del termine si esegue la ricerca. Maggiore il fattore Preamplificazione, più pertinenti il termine sarà relativamente altri termini di ricerca. Per impostazione predefinita, il fattore Preamplificazione è 1. Sebbene il fattore Preamplificazione deve essere positivo, può essere minore di 1 (ad esempio 0,2).

**Esempio 6** - pulsante destro del mouse sulla query. Cercare i processi con il termine "analyst di computer" in cui è visualizzato Nessun risultato con il computer di parole e analyst ancora processi analyst sono nella parte superiore dei risultati.

- [& queryType = completo ed eseguire una ricerca = business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Esempio 7** : provare nuovamente, questa volta incrementare risultati con il computer di termini su analyst termini, se non sono presenti entrambe le parole.

- [& queryType = completo ed eseguire una ricerca = business_title:computer ^ 2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Espressioni regolari

Una ricerca di espressioni regolari trova una corrispondenza in base al contenuto tra barre "/", come documentazione della [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Esempio 8** : pulsante destro del mouse sulla query. Eseguire una ricerca per i processi con il termine Senior o scolastica varie classi.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L'URL per questo esempio non verrà visualizzate correttamente nella pagina. In alternativa, copiare l'URL seguente e incollarlo l'indirizzo URL del browser:    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Ricerca con caratteri jolly

È possibile utilizzare sintassi riconosciuta per multiplo (\*) o singolo ricerche con caratteri jolly caratteri (?). Nota Il parser query Lucene supporta l'uso di questi simboli con un singolo termine e non una frase.

**Esempio 9** - pulsante destro del mouse sulla query. Cercare i processi che contengono il prefisso 'il programma' in cui verrebbe inclusi i titoli di business con i termini di programmazione e programmatore al suo interno.

- [& queryType = full & $select = business_title & ricerca = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Non è possibile utilizzare una * o? simbolo come primo carattere di una ricerca.


## <a name="next-steps"></a>Passaggi successivi

Provare a specificare il Parser Query Lucene nel codice. I collegamenti seguenti illustrano come configurare la query di ricerca per .NET e le API REST. I collegamenti usare la sintassi semplice predefinita in modo che è necessario applicare informazioni acquisite in questo articolo per specificare **queryType**.

- [Indice di ricerca Azure utilizzando .NET SDK della query](search-query-dotnet.md)
- [L'indice di ricerca di Azure tramite l'API REST della query](search-query-rest-api.md)


 