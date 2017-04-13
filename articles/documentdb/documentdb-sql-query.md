<properties 
    pageTitle="Sintassi SQL e SQL della query per DocumentDB | Microsoft Azure" 
    description="Informazioni sulla sintassi SQL, concetti di database e query SQL per DocumentDB, un database NoSQL. SQL utilizzabile come un linguaggio di query JSON in DocumentDB." 
    keywords="sintassi SQL, query sql, query sql, linguaggio di query json, database e query sql, funzioni di aggregazione"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Query SQL e sintassi SQL di DocumentDB
Microsoft Azure DocumentDB supporta documenti query utilizzando SQL (Structured Query Language) come un linguaggio di query JSON. DocumentDB è davvero privo di schema. Per il proprio impegno al modello di dati JSON direttamente all'interno del motore di database, fornisce indicizzazione automatica dei documenti JSON senza schema esplicito o la creazione di indici secondari. 

Durante la progettazione del linguaggio di query per DocumentDB era due obiettivi:

-   Invece di realizzazione di un nuovo linguaggio di query JSON, abbiamo voluto supportano SQL. SQL corrisponde a uno dei linguaggi di query più più comuni. DocumentDB SQL fornisce un modello di programmazione formale per query avanzate sui documenti JSON.
-   Come un database di documento JSON in grado di eseguire JavaScript direttamente nel motore di database, si vuole utilizzare modello di programmazione JavaScript come base per il linguaggio di query. È possibile eseguire SQL DocumentDB nel sistema di tipo JavaScript, la valutazione dell'espressione e chiamata di funzione. Questo a sua volta fornisce un modello di programmazione naturale per previsioni relazionali, lo spostamento gerarchico nei documenti JSON, self-join, query spaziale e chiamata di funzioni definite dall'utente (funzioni definite dall'utente) interamente scritta in JavaScript, tra le altre caratteristiche. 

Si ritiene che queste funzionalità sono fondamentali per la riduzione attrito tra l'applicazione e il database e fondamentali per la produttività dello sviluppatore.

Si consiglia di iniziare, guardare il video seguente, in cui Aravind Ramachandran Mostra le funzionalità di query del DocumentDB, e visitando il [Test di Query](http://www.documentdb.com/sql/demo), in cui è possibile provare DocumentDB ed eseguire query SQL sul set di dati.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Quindi, tornare a questo articolo, in cui è possibile iniziare un'esercitazione di query SQL che illustra alcuni documenti JSON semplici e comandi SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Introduzione a comandi SQL in DocumentDB
Per visualizzare DocumentDB SQL in ufficio, iniziare con alcuni documenti JSON semplici e scorrere alcune semplici query. Valutare la possibilità di tali documenti JSON sulle due gruppi. Si noti che con DocumentDB, non è necessaria creare in modo esplicito qualsiasi schemi o indici secondari. Basta inserire i documenti JSON a un insieme di DocumentDB e quindi eseguire una query. Di seguito sono un JSON semplice documento per la famiglia Andersen, gli elementi padre, figli (e rispettivi animali), indirizzo e la registrazione di informazioni. Nel documento sono stringhe, numeri, valori booleani, matrici e proprietà annidate. 

**Documento**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Ecco un secondo documento con una differenza sottile- `givenName` e `familyName` utilizzate al posto di `firstName` e `lastName`.

**Documento**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Ora provare alcuni query su questi dati al comprendere alcuni aspetti di DocumentDB SQL. Ad esempio, la query seguente restituirà i documenti in cui il campo id corrisponde al `AndersenFamily`. Poiché si tratta di un `SELECT *`, l'output della query è il documento JSON completo:

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


A questo punto si consideri il caso in cui è necessario riformattare l'output JSON in un'altra forma. Questa query progetti un nuovo oggetto JSON con due campi selezionati, nome e città, quando città l'indirizzo ha lo stesso nome come lo stato. In questo caso, corrisponde a "NY, NY".

**Query**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Risultati**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La query successiva restituisce cognomi di figli riportato nella famiglia il cui id corrisponde `WakefieldFamily` ordinati in base alla città di residenza.

**Query**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Risultati**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Si vuole attirare l'attenzione su alcuni aspetti importanti di linguaggio di query DocumentDB esaminare gli esempi inclusi che abbiamo visto finora:  
 
-   Poiché DocumentDB SQL funziona sui valori JSON, occupa albero forma entità invece di righe e colonne. Di conseguenza, la lingua consente di fare riferimento a nodi della struttura di qualsiasi profondità non autorizzato, ad esempio `Node1.Node2.Node3…..Nodem`, simile a relazionali SQL che fa riferimento a riferimento due parti della `<table>.<column>`.   
-   Il linguaggio structured query language funziona con meno di schema dati. Di conseguenza, il sistema di tipo deve essere associato in modo dinamico. Il valore di expression stesso questione diversi tipi dei vari documenti. Il risultato di una query di un valore JSON valido, ma non necessariamente essere di uno schema fisso.  
-   DocumentDB supporta solo documenti JSON strict. Di conseguenza, che il sistema di tipo e le espressioni sono limitate a gestire solo i tipi di JSON. Fare riferimento alla [specifica JSON](http://www.json.org/) per altri dettagli.  
-   Un insieme di DocumentDB è un contenitore gratuiti schema dei documenti JSON. Le relazioni di entità di dati all'interno di documenti in una raccolta in modo implicito vengono acquisite dal contenimento e non da chiave primaria e relazioni di chiave esterne. Si tratta di un aspetto importante opportuno sottolineare in considerazione i join tra documenti descritti più avanti in questo articolo.

## <a name="documentdb-indexing"></a>L'indicizzazione DocumentDB

Prima di passare alla sintassi SQL DocumentDB, è opportuno considerare la struttura dell'indicizzazione in DocumentDB. 

Lo scopo degli indici dei database è di gestione delle query nei vari moduli e nelle forme con consumo risorsa minimo (ad esempio CPU e ingresso/uscita) fornendo velocità ottimale. Spesso, la scelta dell'indice appropriata per la ricerca di un database richiede molto pianificazione e sperimentazione. Questo approccio pone per i database di minore di schema nel punto in cui i dati non è conforme a uno schema strict ed evolversi rapidamente. 

È progettato DocumentDB indicizzazione sottosistema, pertanto è impostare gli obiettivi seguenti:

-   Indicizzare documenti senza schema: sottosistema indicizzazione non richiede le informazioni sullo schema o formulare ipotesi sullo schema dei documenti. 

-   Supporto per le query avanzate ed efficiente gerarchiche e relazionali: l'indice supporta il linguaggio di query DocumentDB in modo efficiente, incluso il supporto per previsioni gerarchiche e relazionale.

-   Supporto per le query coerente le un volume velocità di scrittura: per scrittura ampiamente i carichi di lavoro di velocità con le query coerenti, l'indice verrà aggiornato in modo incrementale, in modo efficiente e online in un volume velocità di scrittura. Aggiorna indice coerente è fondamentale per soddisfare le query a livello di coerenza in cui l'utente configurato il servizio di documenti.

-   Supporto per più affitto: dato il modello di prenotazione in base a di governance delle risorse tra tenant, superare il preventivo delle risorse di sistema (CPU, memoria e operazioni di input/output al secondo) allocata per replica vengono eseguiti aggiornamenti di indice. 

-   L'efficienza dello spazio di archiviazione: per l'efficacia dei costi, il sovraccarico di spazio di archiviazione su disco dell'indice è limitata e prevedibili. Questo è fondamentale, perché DocumentDB consente agli sviluppatori di individuazione costo in base a compromesso tra indice sovraccarico in relazione alle prestazioni delle query.  

[Esempi di DocumentDB](https://github.com/Azure/azure-documentdb-net) su MSDN per vedere esempi in cui viene illustrato come configurare i criteri di indicizzazione per una raccolta. Vediamo ora come configurare i dettagli di una sintassi SQL DocumentDB.


## <a name="basics-of-a-documentdb-sql-query"></a>Nozioni di base di una query SQL DocumentDB
Tutte le query include una clausola SELECT e FROM facoltativa e clausola WHERE per standard ANSI SQL. In genere, per ogni query viene enumerato l'origine nella clausola FROM. Quindi viene applicato il filtro nella clausola WHERE per recuperare un sottoinsieme dei documenti JSON dell'origine. Infine, la clausola SELECT viene utilizzata per proiettare valori JSON richiesti nell'elenco di selezione.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>Clausola FROM
Il `FROM <from_specification>` clausola è facoltativa, a meno che l'origine viene filtrato o previsto in un secondo momento nella query. Lo scopo di questa clausola è per specificare l'origine dati su cui deve essere utilizzata la query. In genere l'intera raccolta rappresenta l'origine, ma una matrice può specificare se, tuttavia un sottoinsieme dell'insieme. 

Una query come `SELECT * FROM Families` indica che l'intero insieme di famiglie rappresenta l'origine su cui enumerare. Un identificatore speciale radice può essere utilizzato per rappresentare insieme invece di usare il nome della raccolta. Di seguito sono riportate le regole vengono applicate per query:

- La raccolta può essere utilizzato un alias, ad esempio `SELECT f.id FROM Families AS f` o semplicemente `SELECT f.id FROM Families f`. Di seguito `f` è pari a `Families`. `AS`è l'identificatore parola chiave facoltativa all'alias.

-   Non è possibile associare nota che una volta alias, l'origine. Ad esempio `SELECT Families.id FROM Families f` è sintassi non valida, poiché l'identificatore "Famiglie" non può essere risolti più.

-   Tutte le proprietà che è necessario fare riferimento devono essere completo. In assenza di conformità schema restrittive, questo viene applicato per evitare che le associazioni ambigue. Di conseguenza, `SELECT id FROM Families f` è sintassi non valida dopo la proprietà `id` non è associato.
    
### <a name="sub-documents"></a>Documenti secondari
È possibile anche ridurre l'origine a un sottoinsieme più piccolo. Ad esempio, all'enumerazione solo una struttura in tutti i documenti secondari, radice secondari quindi diventare l'origine, come illustrato nell'esempio seguente.

**Query**

    SELECT * 
    FROM Families.children

**Risultati**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Mentre nell'esempio precedente utilizzata una matrice come origine, un oggetto può essere utilizzato come origine, ovvero quello mostrato nell'esempio seguente. Qualsiasi valore JSON valido (non non definito) presenti nel sito di origine verrà considerati da includere nel risultato della query. Se non dispone di alcune famiglie di un `address.state` valore, verranno escluse nel risultato della query.

**Query**

    SELECT * 
    FROM Families.address.state

**Risultati**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Clausola WHERE
La clausola WHERE (**`WHERE <filter_condition>`**) è facoltativo. Consente di specificare le condizioni che i documenti JSON forniti dall'origine devono soddisfare per essere inclusi come parte del risultato. Qualsiasi documento JSON deve restituire le condizioni specificate su "true" da considerare per il risultato. La clausola WHERE viene utilizzata dal livello di indice per determinare il subset più piccolo assoluto dei documenti di origine che possono far parte del risultato. 

La query seguente le richieste di documenti che contengono una proprietà di nome è il cui valore `AndersenFamily`. Qualsiasi altro documento che non dispone di una proprietà di nome, o in cui il valore non corrisponde `AndersenFamily` viene esclusa. 

**Query**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


Nell'esempio precedente è stato una query di uguaglianza semplice. DocumentDB SQL supporta anche un'ampia gamma di espressioni scalare. Più comunemente usati sono espressioni binario e unario. Anche i riferimenti di proprietà dall'oggetto JSON di origine sono espressioni valide. 

Gli operatori binari seguenti sono attualmente supportati e possono essere utilizzati nelle query, come illustrato negli esempi seguenti:  
<table>
<tr>
<td>Aritmetica</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit per bit</td>    
<td>|, &, ^, <<, >>, >>> (spostamento a destra zero riempimento) </td>
</tr>
<tr>
<td>Logico</td>
<td>E, O MENO</td>
</tr>
<tr>
<td>Confronto</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Stringa</td> 
<td>|| (concatena)</td>
</tr>
</table>  

Esaminiamo alcune query operatori binari.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Gli operatori unario +,-, ~ non sono supportati anche e può essere utilizzato all'interno di query, come illustrato nell'esempio seguente:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Oltre a binario unario operatori, riferimenti alle proprietà anche consentiti. Ad esempio `SELECT * FROM Families f WHERE f.isRegistered` restituisce il documento JSON contenente la proprietà `isRegistered` nel punto in cui il valore della proprietà è uguale alla JSON `true` valore. Altri valori (false, null, definito, `<number>`, `<string>`, `<object>`, `<array>`e così via) conduce al documento di origine escluso dal risultato. 

### <a name="equality-and-comparison-operators"></a>Operatori di confronto e uguaglianza
Nella tabella seguente mostra il risultato di confronti di uguaglianza in DocumentDB SQL tra le due tipi JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Operazione</strong>
         </td>
         <td valign="top">
            <strong>Non definito</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Valore booleano</strong>
         </td>
         <td valign="top">
            <strong>Numero</strong>
         </td>
         <td valign="top">
            <strong>Stringa</strong>
         </td>
         <td valign="top">
            <strong>Oggetto</strong>
         </td>
         <td valign="top">
            <strong>In forma di matrice</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Non definito<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valore booleano<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Numero<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Stringa<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Oggetto<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Non definito </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>In forma di matrice<strong>
         </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
Non definito </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
      </tr>
   </tbody>
</table>

Per altri gli operatori di confronto come >, > =,! =, < e < =, le regole seguenti si applicano:   

-   Confronto tra tipi di risultato non definito.
-   Confronto tra due oggetti o due matrici produce non definito.   

Se il risultato dell'espressione scalare nel filtro è non definito, il documento corrispondente non viene incluso nei risultati, poiché non definito in modo logico non essere uguale a "true".

### <a name="between-keyword"></a>TRA le parole chiave
È anche possibile utilizzare la parola chiave BETWEEN per esprimere le query su intervalli di valori come in SQL ANSI. TRA può essere utilizzata con stringhe o numeri.

Ad esempio, la query restituisce tutti i documenti della famiglia in cui i voti del primo elemento figlio sono compresa tra 1-5 (inclusi). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Diversamente da quanto succede in SQL ANSI, è possibile utilizzare anche la clausola BETWEEN nella clausola FROM come nell'esempio seguente.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Per velocizzare i tempi di esecuzione di query, è necessario creare una regola di indicizzazione che utilizza un tipo di indice intervallo rispetto a eventuali proprietà/percorsi numerici filtrati all'interno della clausola BETWEEN. 

La differenza tra BETWEEN DocumentDB e SQL ANSI principale è che è possibile esprimere le query di intervallo rispetto alle proprietà dei tipi misti: ad esempio, potrebbe essere "voto" è un numero (5) in alcune stringhe in altri ("grade4") e documenti. In questi casi, ad esempio in JavaScript, un confronto tra due risultati diversi tipi di "non definito" e il documento verrà ignorato.

### <a name="logical-and-or-and-not-operators"></a>Logici (AND, OR e non) operatori
Gli operatori logici operano su valori Boolean. Le tabelle verità logiche per questi operatori vengono visualizzate nelle tabelle seguenti.

OR|Vero|FALSO|Non definito
---|---|---|---
Vero|Vero|Vero|Vero
FALSO|Vero|FALSO|Non definito
Non definito|Vero|Non definito|Non definito

E|Vero|FALSO|Non definito
---|---|---|---
Vero|Vero|FALSO|Non definito
FALSO|FALSO|FALSO|FALSO
Non definito|Non definito|FALSO|Non definito

NON|  |
---|---
Vero|FALSO
FALSO|Vero
Non definito|Non definito

### <a name="in-keyword"></a>Parola chiave
La parola chiave IN può essere utilizzata per verificare se un valore specificato corrisponde a qualsiasi valore in un elenco. Ad esempio, la query restituisce tutti i documenti della famiglia nel punto in cui l'id corrisponde a uno dei "WakefieldFamily" o "AndersenFamily". 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

In questo esempio restituisce tutti i documenti in cui lo stato è uno dei valori specificati.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operatori di assegnazione (?) e ternario (?)
Gli operatori ternario e unione possono essere utilizzati per creare espressioni condizionali, simile a diffusi linguaggi come c# e JavaScript. 

L'operatore ternario (?) può essere molto utile quando si creano nuove proprietà JSON in tempo reale. Ad esempio, a questo punto è possibile scrivere query per classificare i livelli di classe in una forma leggibile come principianti/intermedia/avanzate come illustrato di seguito.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

È inoltre possibile annidare le chiamate all'operatore like nella query riportata di seguito.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Come con altri operatori di query, se le proprietà di cui viene fatto riferimento nell'espressione condizionale mancano in qualsiasi documento o se i tipi di confrontati sono diversi, quindi i documenti verranno esclusi nei risultati della query.

L'operatore di unione (?) può essere utilizzato per in modo efficiente verificare la presenza di una proprietà (noto anche è definito) in un documento. Questo è utile quando l'esecuzione di query sui semistrutturati o tipi di dati. Ad esempio, la query restituisce "Cognome" se presente, o "Cognome" Se non è presente.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Accesso alla proprietà tra virgolette
È inoltre possibile accedere mediante l'operatore di proprietà tra virgolette `[]`. Ad esempio `SELECT c.grade` e `SELECT c["grade"]` sono equivalenti. La sintassi seguente è utile quando è necessario interrompere l'esecuzione di una proprietà che contiene spazi, caratteri speciali, o accade per condividere lo stesso nome di una parola chiave SQL o una parola riservata.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Clausola SELECT
La clausola SELECT (**`SELECT <select_list>`**) è obbligatorio e include i valori recuperati dalla query, come in SQL ANSI. Il subset filtrato nella parte superiore di documenti di origine vengono passati in fase di proiezione, in cui vengono recuperati i valori specificati in JSON e viene creato un nuovo oggetto JSON, per ogni input passato su di esso. 

Nell'esempio seguente mostra una tipica query di selezione. 

**Query**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Proprietà annidate
Nell'esempio seguente è stiamo proiezione due proprietà nidificate `f.address.state` e `f.address.city`.

**Query**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Proiezione supporta anche le espressioni di JSON, come illustrato nell'esempio seguente.

**Query**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Esaminiamo il ruolo di `$1` qui. Il `SELECT` clausola informazioni necessarie per creare un oggetto JSON e poiché non viene fornito nessun tasto, viene utilizzato i nomi di variabile argomento implicito a partire da `$1`. Ad esempio, la query restituisce due variabili argomento implicito, etichettate `$1` e `$2`.

**Query**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Alias
A questo punto è possibile estendere dell'esempio precedente utilizzando alias esplicito dei valori. COME è la parola chiave utilizzata per l'alias. Si noti che sia obbligatorio illustrata durante la proiezione al secondo valore come `NameInfo`. 

In caso di una query sono presenti due proprietà con lo stesso nome, alias devono essere utilizzato per rinominare una o entrambe le proprietà in modo che siano identificati nel risultato previsto.

**Query**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Espressioni scalari
Oltre ai riferimenti alle proprietà, la clausola SELECT supporta anche espressioni scalari come costanti, espressioni aritmetiche, le espressioni logiche e così via. Ecco, ad esempio, una query semplice "Hello World".

**Query**

    SELECT "Hello World"

**Risultati**

    [{
      "$1": "Hello World"
    }]


Ecco un esempio più complesso che utilizza un'espressione scalare.

**Query**

    SELECT ((2 + 11 % 7)-2)/3   

**Risultati**

    [{
      "$1": 1.33333
    }]


Nell'esempio seguente, il risultato dell'espressione scalare è un valore Boolean.

**Query**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Risultati**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Creazione di oggetti e in forma di matrice
Un'altra funzione chiave di DocumentDB SQL è la creazione di matrice/oggetti. Nell'esempio precedente, si noti che viene creato un nuovo oggetto JSON. Analogamente, uno inoltre possibile creare matrici come illustrato negli esempi seguenti.

**Query**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Risultati**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>Parola chiave VALUE
La parola chiave **VALUE** offre un modo per restituire il valore JSON. Ad esempio, la query riportata di seguito restituisce il valore scalare `"Hello World"` anziché `{$1: "Hello World"}`.

**Query**

    SELECT VALUE "Hello World"

**Risultati**

    [
      "Hello World"
    ]


La query seguente restituisce il valore JSON senza il `"address"` etichetta nei risultati.

**Query**

    SELECT VALUE f.address
    FROM Families f 

**Risultati**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

Nell'esempio seguente si estende per mostrare come restituire JSON i valori di base (livello foglia della struttura di JSON). 

**Query**

    SELECT VALUE f.address.state
    FROM Families f 

**Risultati**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Operatore di
È supportato l'operatore speciale (*) per il documento come usare project-è. Se utilizzata, deve essere l'unico campo previsto. Durante una query come `SELECT * FROM Families f` è valido, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` non sono validi.

**Query**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Risultati**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Operatore TOP
La parola chiave TOP può essere utilizzata per limitare il numero di valori da una query. Quando inizio viene utilizzato in combinazione con la clausola ORDER BY, set di risultati è limitato al primo numero N di valori ordinati; in caso contrario, restituisce il primo numero N dei risultati in un ordine non definito. Come ottimale, in un'istruzione SELECT, utilizzare sempre una clausola ORDER BY con la clausola TOP. Questo è l'unico modo per prevedibili indicare le righe interessate dalla parte superiore. 


**Query**

    SELECT TOP 1 * 
    FROM Families f 

**Risultati**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

INIZIO da utilizzare con un valore costante (come illustrato sopra) o con un valore variabile utilizzando le query con parametri. Per ulteriori informazioni, vedere query con parametri riportata di seguito.

## <a name="order-by-clause"></a>Clausola ORDER BY
Ad esempio in SQL ANSI, è possibile includere una clausola Order By facoltativa durante la ricerca. La clausola può includere un argomento facoltativo ASC/DESC per specificare l'ordine in cui devono essere recuperati i risultati. Per informazioni più dettagliate in Ordina per, fare riferimento alla [DocumentDB ordine per questa procedura dettagliata](documentdb-orderby.md).

Ecco, ad esempio, una query che recupera famiglie nell'ordine del nome della città residenti.

**Query**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Risultati**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

Di seguito, una query che recupera famiglie in ordine di data di creazione, viene memorizzata come un numero che rappresenta il periodo di tempo, ad esempio, il tempo trascorso dal 1 gennaio 1970 in secondi.

**Query**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Risultati**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Concetti di database avanzate e query SQL
### <a name="iteration"></a>Iterazione
Tramite la parola chiave **IN** DocumentDB SQL per fornire il supporto per scorrere le matrici JSON è stato aggiunto un nuovo costrutto. L'origine da fornisce supporto per iterazione. Iniziamo con l'esempio seguente:

**Query**

    SELECT * 
    FROM Families.children

**Risultati**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

A questo punto diamo un'occhiata a un'altra query che esegue l'iterazione figli nella raccolta. Si noti la differenza nella matrice di output. In questo esempio divisa `children` e semplifica i risultati in un'unica matrice.  

**Query**

    SELECT * 
    FROM c IN Families.children

**Risultati**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Questa operazione può essere ulteriormente utilizzata per filtrare in base a ogni singola voce della matrice come illustrato nell'esempio seguente.

**Query**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Risultati**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Linee di join
In un database relazionale, è molto importante la necessità di join tra tabelle. È logico corollario alla progettazione di schemi normalizzati. Al contrario, DocumentDB riguarda con il modello di dati denormalizzati dei documenti senza schema. Si tratta equivalente logico di un "self-join".

La sintassi che supporta la lingua è JOIN JOIN < from_source2 > < from_source1 >... JOIN < from_sourceN >. In generale, viene restituito un set di **N**- Tuple (tupla con valori **N** ). Ogni tupla è valori prodotti scorrendo tutti gli alias insieme i propri set rispettivi. In altre parole, si tratta di un prodotto incrociato completo dei gruppi di partecipazione del join.

Negli esempi seguenti viene illustrato il funzionamento della clausola JOIN. Nell'esempio seguente, il risultato è vuoto dopo il prodotto incrociato di ogni documento di origine e un set vuoto è vuoto.

**Query**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Risultati**  

    [{
    }]


Nell'esempio seguente il join è compresa tra la radice del documento, la `children` radice secondari. È un prodotto incrociato tra due oggetti JSON. Il fatto che figli è una matrice non è efficace il JOIN poiché è in corso sulla gestione con una singola radice è in forma di matrice figli. Quindi il risultato contiene solo due risultati, poiché il prodotto incrociato di ogni documento con la matrice restituisce esattamente a un solo documento.

**Query**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Risultati**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


Nell'esempio seguente mostra un join più tradizionale:

**Query**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Risultati**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



Si noti innanzitutto è che il `from_source` di **JOIN** è un iteratore. Pertanto, il flusso in questo caso è come indicato di seguito:  

-   Espandere ogni elemento figlio **c** nella matrice.
-   Applicare un prodotto incrociato con la radice del documento **f** con ogni elemento figlio **c** semplificata nel primo passaggio.
-   Infine, project solo la proprietà nome di oggetto **f** radice. 

Il primo documento (`AndersenFamily`) contiene un solo elemento figlio, in modo che il set di risultati include solo un singolo oggetto corrispondente al documento. Il secondo documento (`WakefieldFamily`) contiene due figli. Pertanto, il prodotto incrociato produce un oggetto distinto per ogni elemento figlio, in modo da risultante in due oggetti, uno per ogni elemento figlio corrispondente al documento. Si noti che i campi radice in entrambi i documenti sono stessi, come previsto in un prodotto incrociato.

L'utilità reale del JOIN consiste nel modulo tuple dal prodotto incrociato in una forma in caso contrario è difficile da project. Inoltre, come verrà illustrato nell'esempio seguente, è possibile filtrare la combinazione di una tupla, che consente di l'utente ha scelto una condizione soddisfatta globale per le tuple.

**Query**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Risultati**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



In questo esempio è un'estensione naturale dell'esempio precedente ed esegue un join doppio. Prodotto incrociato, può essere visualizzato come seguente pseudocodice.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`ha un figlio che dispone di un animale domestico. Pertanto, il prodotto incrociato produce un'unica riga (1*1*1) di questo gruppo. WakefieldFamily ha tuttavia due elementi figlio, ma un solo elemento figlio "Jesse" animali. Jesse ha attraverso 2 animali. Di conseguenza il prodotto incrociato restituisce 1*1*2 = 2 righe da questo gruppo.

Nell'esempio seguente è un ulteriore filtro su `pet`. Sono esclusi tutte le tuple in cui non è "Ombreggiatura" nome animale domestico. Si noti che siamo in grado di creare tuple da matrici, filtro in uno qualsiasi degli elementi della tupla e qualsiasi combinazione degli elementi del progetto. 

**Query**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Risultati**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Integrazione di JavaScript
DocumentDB fornisce un modello di programmazione per l'esecuzione di JavaScript in base a applicazione logica direttamente negli insiemi di file in termini di stored procedure e trigger. In questo modo entrambi:

-   Possibilità di eseguire operazioni CRUD transazione prestazioni e query su documenti in una raccolta per l'integrazione di runtime JavaScript direttamente all'interno del motore di database. 
-   Modellazione naturale di flusso di controllo, variabili definizione dell'ambito e un'assegnazione e l'integrazione di eccezione primitive con le transazioni del database. Per ulteriori informazioni dettagliate sul supporto DocumentDB per l'integrazione di JavaScript, consultare la documentazione di programmabilità JavaScript sul lato server.

###<a name="user-defined-functions-udfs"></a>Funzioni definite dall'utente
Insieme tipi già definiti in questo articolo, DocumentDB SQL fornisce supporto per utente definiti funzioni utente. In particolare, scalare funzioni definite dall'utente sono supportati in cui gli sviluppatori possono passare zero o più argomenti e restituire un risultato solo argomento. Ognuno di questi argomenti vengono controllate per essere valori JSON validi.  

La sintassi SQL DocumentDB viene esteso per supportare la logica dell'applicazione personalizzati utilizzando le funzioni definite dall'utente. Funzioni definite dall'utente possono essere registrati con DocumentDB e farvi riferimento come parte di una query SQL. Infatti, le funzioni definite dall'utente exquisitely sono progettati per essere richiamati dalle query. Pertanto per questa scelta, funzioni definite dall'utente non ha accesso all'oggetto contesto che gli altri tipi di JavaScript (stored procedure e trigger). Poiché query eseguite in sola lettura, possono essere eseguiti su principale o su repliche secondarie. Di conseguenza, funzioni definite dall'utente sono progettati per eseguire analisi sui repliche secondarie a differenza di altri tipi di JavaScript.

Di seguito è illustrato un esempio di come è possibile registrare un'utente nel database DocumentDB, in particolare in una raccolta di documenti.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
Nell'esempio precedente consente di creare un'utente il cui nome è `REGEX_MATCH`. Accetta due valori stringa JSON `input` e `pattern` e controlla se le prime corrispondenze il modello specificato nella seconda funzione JavaScript string.match().


È ora possibile utilizzare questo utente in una query in una proiezione. Funzioni definite dall'utente deve essere qualificato con il prefisso tra maiuscole e minuscole "utente". Quando si chiama da all'interno delle query. 

>[AZURE.NOTE] Prima di 17/3/2015, DocumentDB supportati chiamate utente senza "utente". prefisso, ad esempio selezionare REGEX_MATCH(). Questo modello chiamante deprecato.  

**Query**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Risultati**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

L'utente può anche essere utilizzato all'interno di un filtro come illustrato nell'esempio seguente, anche qualificati con "utente". prefisso:

**Query**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Risultati**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


In pratica, funzioni definite dall'utente sono valide espressioni scalare e possono essere usate in previsioni e filtri. 

Per espandere la potenza di funzioni definite dall'utente, esaminiamo un altro esempio con una logica condizionale:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Di seguito è illustrato un esempio in cui vengono eseguite l'utente.

**Query**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Risultati**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Come mostrare esempi precedenti, funzioni definite dall'utente integrare la potenza di linguaggio JavaScript con SQL DocumentDB per fornire un'interfaccia programmabile RTF per eseguire complessa logica procedura, condizionale con l'aiuto di funzionalità di runtime JavaScript incorporate.

DocumentDB SQL fornisce gli argomenti per le funzioni definite dall'utente per ogni documento nell'origine fase corrente (clausola WHERE o clausola SELECT) di elaborazione l'utente. Il risultato è incorporato nella pipeline di esecuzione complessivo diretta. Se le proprietà di cui per l'utente non sono disponibili nel valore JSON parametri, il parametro viene considerato come non definito e pertanto la chiamata utente viene completamente ignorata. Allo stesso modo se il risultato dell'utente è definito, non è incluso nel risultato. 

In conclusione, funzioni definite dall'utente sono validi strumenti per eseguire la logica aziendale complesso come parte della query.

### <a name="operator-evaluation"></a>Valutazione dell'operatore
DocumentDB, grazie dello stato di un database JSON disegna parallela con gli operatori JavaScript e la semantica di valutazione. Durante il tentativo di DocumentDB mantenere la semantica di JavaScript in termini di supporto JSON, la valutazione di operazione differenza in alcuni casi.

In SQL DocumentDB, diversamente da quanto succede in SQL tradizionale, i tipi di valori spesso non conoscono finché i valori vengono effettivamente recuperati da un database. Per eseguire query in modo efficiente la maggior parte degli operatori hanno requisiti di tipo strict. 

DocumentDB SQL non esegue le conversioni implicite, diversamente da quanto succede JavaScript. Ad esempio, una query come `SELECT * FROM Person p WHERE p.Age = 21` corrisponde a documenti che contengono una proprietà Age il cui valore è 21. Qualsiasi altro documento la cui proprietà Age corrisponde varianti eventualmente infinito stringa "21" o altri come "021", "21,0", "0021", "00021", non verrà trovata e così via. Si tratta invece di JavaScript in cui i valori stringa sono cast implicito in numeri (basato su operatore, ex: = =). Questa opzione è fondamentale per efficiente degli indici corrispondenti in DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>Query SQL con parametri
DocumentDB supporta le query con parametri espressi con i consueti @ notazione. Parametri SQL fornisce affidabile la gestione e il carattere di escape input dell'utente, impedendo esposizione accidentale di dati tramite inserimento SQL. 

Ad esempio, è possibile scrivere una query che accetta come parametri cognome e indirizzo stato e quindi eseguirlo per diversi valori di cognome e indirizzo di stato in base all'input utente.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

La richiesta può quindi essere inviata al DocumentDB come una query con parametri JSON come illustrato di seguito.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

L'argomento verso l'alto può essere impostato utilizzando le query con parametri come illustrato di seguito.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

I valori dei parametri possono essere qualsiasi JSON valido (stringhe, numeri, valori booleani, null, anche le matrici o annidata JSON). Anche poiché DocumentDB è minore di schema, parametri non vengono convalidati in base a qualsiasi tipo.

##<a name="built-in-functions"></a>Funzioni predefinite
DocumentDB supporta anche un numero di funzioni predefinite per operazioni comuni che possono essere usate all'interno di query come funzioni definite dall'utente (funzioni definite dall'utente).

<table>
<tr>
<td>Funzioni matematiche</td> 
<td>ABS, CEILING, EXP, PLANIMETRIE, LOG, LOG10, POWER, ROUND, accesso, RADQ, QUADRATA, TRUNC, ARCCOS, ARCSEN, ARCTAN, ATN2, COS, COT gradi, pi greco, radianti, SEN e marrone chiaro</td>
</tr>
<tr>
<td>Tipo di controllo funzioni</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funzioni stringa</td>   
<td>CONCATENA, contiene, ENDSWITH, INDEX_OF, a sinistra, lunghezza, inferiore, funzioni LTRIM, Sostituisci, REPLICARE, REVERSE, a destra, RTRIM, STARTSWITH, SOTTOSTRINGA e superiore</td>
</tr>
<tr>
<td>Funzioni di matrice</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE</td>
</tr>
<tr>
<td>Funzioni spaziale</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID e ST_ISVALIDDETAILED</td>
</tr>
</table>  

Se si sta utilizzando una funzione definita dall'utente (utente) per il quale è ora disponibile una funzione predefinita, è necessario utilizzare la funzione predefinita corrispondente come sta per essere più rapido per l'esecuzione e in modo più efficiente. 

### <a name="mathematical-functions"></a>Funzioni matematiche
Funzioni matematiche ogni eseguono un calcolo, in genere in base a valori di input che vengono forniti come argomenti e restituiscono un valore numerico. Ecco un indice delle funzioni matematiche predefinite supportate.

<table>
<tr>
<td><strong>Uso</strong></td>
<td><strong>Descrizione</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ASS (num_expr)</a></td> 
<td>Restituisce il valore assoluto (positivo) dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td> 
<td>Restituisce il valore all'intero più piccolo maggiore o uguale a, l'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td> 
<td>Restituisce il numero intero più grande minore o uguale all'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Restituisce l'esponente dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Restituisce il logaritmo naturale di espressione numerica specificata oppure il logaritmo utilizzando la base specificata</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Restituisce il valore di logaritmico base 10 di espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">Arrotonda (num_expr)</a></td> 
<td>Restituisce un valore numerico, arrotondato al numero intero più vicino.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">Tronca (num_expr)</a></td> 
<td>Restituisce un valore numerico, troncato all'intero più vicino.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">RADQ (num_expr)</a></td>   
<td>Restituisce la radice quadrata di espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">QUADRATO (num_expr)</a></td>   
<td>Restituisce la radice quadrata dell'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POTENZA (num_expr; num_expr)</a></td>   
<td>Restituisce il potere dell'espressione numerica specificata per il valore specificato.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SEGNO (num_expr)</a></td>   
<td>Restituisce il valore di accesso (-1, 0, 1) dell'espressione numerica specificata.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ARCCOS (num_expr)</a></td>   
<td>Restituisce l'angolo in radianti, il cui coseno è l'espressione numerica specificata; detto anche arcocoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ARCSEN (num_expr)</a></td>   
<td>Restituisce l'angolo in radianti, il cui seno è l'espressione numerica specificata. Detta anche arcoseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ARCTAN (num_expr)</a></td>   
<td>Restituisce l'angolo in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Restituisce l'angolo in radianti, tra l'asse x positivo e raggio dall'origine al punto (y; x), dove x e y sono i valori delle due espressioni mobile specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Restituisce il coseno dell'angolo specificato, espresso in radianti, l'espressione specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Restituisce la cotangente dell'angolo specificato, espresso in radianti, l'espressione numerica specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRADI (num_expr)</a></td> 
<td>Restituisce l'angolo corrispondente espresso in gradi per un angolo espresso in radianti.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI GRECO)</a></td>   
<td>Restituisce il valore costante di pi greco.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANTI (num_expr)</a></td> 
<td>Restituisce radianti immettendo un'espressione numerica, espresso in gradi.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SEN (num_expr)</a></td> 
<td>Restituisce il seno dell'angolo specificato, espresso in radianti, l'espressione specificata.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Restituisce la tangente dell'espressione di input, nell'espressione specificata.</td>
</tr>

</table> 

Ad esempio, è ora possibile eseguire query simile alla seguente:

**Query**

    SELECT VALUE ABS(-4)

**Risultati**

    [4]

La differenza tra le funzioni di DocumentDB confrontate con quelle di SQL ANSI principale è che è progettati per funzionare correttamente con dati dello schema misti e minore di schema. Ad esempio, se si dispone di un documento in cui la proprietà dimensioni non è presente, o che dispone di un valore non numerico come "sconosciuto", quindi il documento è stato ignorato, invece che restituisce un errore.

### <a name="type-checking-functions"></a>Tipo di controllo funzioni
Le funzioni di controllo errori di tipo consentono di controllare il tipo di un'espressione all'interno delle query SQL. Funzioni di controllo di tipo è utilizzabile per determinare il tipo di proprietà all'interno di documenti in tempo reale quando è variabile o sconosciuto. Ecco un indice delle funzioni di controllo dei tipi di predefiniti supportati.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è una matrice.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è un valore Boolean.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è un numero.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è un oggetto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è una stringa.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se la proprietà è stata assegnata un valore.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (espressione)</a></td>
  <td>Restituisce un valore Boolean che indica se il tipo del valore è una stringa, numero, Boolean o null.</td>
</tr>

</table>

Utilizzare queste funzioni, è possibile eseguire query simile alla seguente:

**Query**

    SELECT VALUE IS_NUMBER(-4)

**Risultati**

    [true]

### <a name="string-functions"></a>Funzioni stringa
Le seguenti funzioni scalari eseguono un'operazione su un valore di input di stringa e restituiscono una stringa, un valore numerico o Boolean. Ecco un indice delle funzioni stringa predefiniti:

Uso|Descrizione
---|---
[LUNGHEZZA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Restituisce il numero di caratteri dell'espressione stringa specificata
[CONCATENA (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Restituisce una stringa che rappresenta il risultato di concatenare due o più valori stringa.
[SOTTOSTRINGA (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Restituisce parte di un'espressione stringa.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Restituisce un valore Boolean che indica se la prima espressione stringa termina con il secondo
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Restituisce un valore Boolean che indica se la prima espressione stringa termina con il secondo
[CONTIENE (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Restituisce un valore Boolean che indica se la prima stringa espressione contiene il secondo.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Restituisce la posizione iniziale della prima occorrenza della seconda stringa espressione all'interno della prima espressione stringa specificata, o -1 se la stringa non viene trovata.
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Restituisce la parte sinistra di una stringa con il numero specificato di caratteri.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Restituisce la parte destra di una stringa con il numero specificato di caratteri.
[Funzioni LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Restituisce un'espressione stringa dopo aver rimosso gli spazi vuoti iniziali.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Restituisce un'espressione stringa dopo aver troncato tutti gli spazi finali.
[INFERIORE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Restituisce un'espressione stringa dopo aver convertito i caratteri maiuscoli a minuscoli.
[SUPERIORE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Restituisce un'espressione stringa dopo la conversione dei dati carattere minuscolo in maiuscolo.
[Sostituisci (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Sostituisce tutte le occorrenze di un valore stringa specificata con un altro valore stringa.
[REPLICARE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Ripete un valore stringa un numero di volte specificato.
[INVERSA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Restituisce l'inversa di un valore stringa.

Utilizzare queste funzioni, è possibile eseguire query simile al seguente. Ad esempio, si può restituire il nome della famiglia in lettere maiuscole come indicato di seguito:

**Query**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Risultati**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

O la concatenazione di stringhe come in questo esempio:

**Query**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Risultati**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funzioni stringa possono essere utilizzate anche nella clausola WHERE per filtrare i risultati, come nell'esempio seguente:

**Query**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Risultati**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funzioni di matrice
Le seguenti funzioni scalari eseguono un'operazione su un valore di input in forma di matrice e ritorno numerico, valore Boolean o in una matrice. Ecco un indice delle funzioni di matrice predefiniti:

Uso|Descrizione
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Restituisce il numero di elementi dell'espressione specificata in forma di matrice.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Restituisce una matrice che rappresenta il risultato di concatenare due o più valori in forma di matrice.
[ARRAY_CONTAINS (arr_expr, espr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Restituisce un valore Boolean che indica se la matrice contiene il valore specificato.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Restituisce parte di un'espressione in forma di matrice.

Funzioni di matrice possono essere utilizzate per modificare matrici all'interno di JSON. Ecco, ad esempio, una query che restituisce tutti i documenti in uno dei genitori "Robin Wakefield". 

**Query**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

Ecco un altro esempio che utilizza ARRAY_LENGTH per ottenere il numero di figli per famiglia.

**Query**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Risultati**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funzioni spaziale

DocumentDB supporta le funzioni predefinite Apri geospaziali Consortium (OGC) seguenti per eseguire la query geospaziali. Per informazioni dettagliate sul geospaziali DocumentDB di supporto, vedere [uso di dati geospaziali in Azure DocumentDB](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Restituisce la distanza tra le due espressioni punto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Restituisce un'espressione booleana che indica se il punto di GeoJSON specificato nel primo argomento è all'interno del poligono GeoJSON nel secondo argomento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Restituisce un valore Boolean che indica se l'espressione specificata di punto o poligono GeoJSON è valido.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Restituisce un valore JSON contenente un valore Boolean valore se l'espressione specificata di punto o poligono GeoJSON è valido e se non valido, inoltre il motivo come valore stringa.</td>
</tr>
</table>

Funzioni spaziale possono essere utilizzate per eseguire querries prossimità rispetto ai dati spaziali. Ecco, ad esempio, una query che restituisce tutti i documenti della famiglia che si trovano entro 30 km della posizione specificata, utilizzando la funzione incorporata ST_DISTANCE. 

**Query**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

Se si include l'indicizzazione spaziale nei criteri di indicizzazione, quindi "query distanza" verranno servite in modo efficiente attraverso l'indice. Per ulteriori informazioni sul processo di indicizzazione spaziale, vedere la sezione seguente. Se non si dispone di un indice spaziale per i percorsi specificati, è comunque possibile eseguire query spaziale specificando `x-ms-documentdb-query-enable-scan` intestazione richiesta con il valore impostato su "true". In .NET ciò è possibile passando l'argomento facoltativo **FeedOptions** alle query con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) impostato su true. 

ST_WITHIN può essere utilizzato per verificare se un punto si trova all'interno di un poligono. Simboli poligono vengono utilizzati per rappresentare i limiti come codici postali, limiti di stato o formazioni naturale. Nuovo se si include l'indicizzazione spaziale nei criteri di indicizzazione, quindi "amore" query verranno servite in modo efficiente attraverso l'indice. 

Argomenti poligono ST_WITHIN possono contenere un solo anello, vale a dire il poligono non deve contenere aree vuote. Controllare i [limiti di DocumentDB](documentdb-limits.md) per il numero massimo di punti consentiti in un poligono per una query di ST_WITHIN.

**Query**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Risultati**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Simile a come non corrispondenti works tipi di query DocumentDB, se il valore del percorso specificato nell'argomento non è corretta o non validi, quindi verrà valutata per **definito** e il documento valutato ignorati dai risultati della query. Se la query non restituisce risultati, eseguire ST_ISVALIDDETAILED per debug perché il tipo di spatail è valido.     

ST_ISVALID e ST_ISVALIDDETAILED possono essere utilizzati per verificare la validità di un oggetto spaziale. Ad esempio, la query seguente controlla la validità di un punto di un messaggio del valore latitudine intervallo (-132.8). ST_ISVALID restituisce solo un valore Boolean e ST_ISVALIDDETAILED restituisce il valore booleano e una stringa contenente il motivo per è considerato non valido.

**Query**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Risultati**

    [{
      "$1": false
    }]

Queste funzioni possono anche essere utilizzate per convalidare poligono. Ad esempio, di seguito è utilizzare ST_ISVALIDDETAILED per convalidare un poligono che non viene chiusa. 

**Query**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Risultati**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Che include le funzioni spaziale e sintassi SQL per DocumentDB. Ora esaminiamo un come LINQ query lavora e l'interazione con la sintassi abbiamo visto finora.

## <a name="linq-to-documentdb-sql"></a>LINQ to SQL DocumentDB
LINQ è un modello di programmazione .NET che esprime calcolo come query su flussi di oggetti. DocumentDB fornisce un client libreria lato per interagire con LINQ facilitando la conversione tra oggetti JSON e .NET e un mapping da un sottoinsieme di LINQ richiede alle query DocumentDB. 

Nell'immagine seguente è illustrata l'architettura di supportare le query LINQ con DocumentDB.  Usa il client DocumentDB, gli sviluppatori possono creare un oggetto **IQueryable** che esegue una query direttamente il provider di query DocumentDB, quindi converte la query LINQ in una query di DocumentDB. La query viene quindi passata al server DocumentDB per recuperare un set di risultati nel formato JSON. I risultati restituiti vengono deserializzati in un flusso di oggetti .NET sul lato client.

![Architettura di supportare LINQ query DocumentDB - sintassi SQL, linguaggio di query JSON, database e query SQL][1]
 


### <a name="net-and-json-mapping"></a>.NET e JSON mapping
Il mapping tra gli oggetti .NET e documenti JSON naturale, ogni campo membro di dati associato a un oggetto JSON, in cui il nome del campo sia associato alla parte "chiave" dell'oggetto e la parte "valore" è ricorsivo mappata la parte del valore dell'oggetto. Tenere presente quanto segue. Oggetto famiglia creato sia associato al documento JSON, come illustrato di seguito. E viceversa, il documento JSON sia associato in un oggetto .NET.

**Classe c#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ alla conversione SQL
Il provider di query DocumentDB esegue il mapping di una programmazione più di una query LINQ in una query SQL DocumentDB. Nella casella Descrizione seguente si presuppone che il lettore ha una conoscenza di base di LINQ.

Prima di tutto, per il sistema di tipo, è supporta tutti JSON base tipi: i tipi numerici, boolean, stringa e null. Sono supportati solo questi tipi JSON. Le espressioni scalare seguenti sono supportate.

-   Valori costanti – include i valori costanti dei tipi di dati di base al momento in cui viene valutata la query.

-   Espressioni indice/proprietà in forma di matrice, le espressioni fare riferimento alla proprietà di un oggetto o un elemento di matrice.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Aritmetiche espressioni - ad esempio espressioni aritmetiche comuni sui valori numerici e booleani. Per l'elenco completo, consultare le specifiche di SQL.

        2 * family.children[0].grade;
        x + y;

-   Espressione di confronto di stringa, ad esempio il confronto di un valore stringa su un valore stringa costante.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Oggetto/matrice espressione di creazione - queste espressioni restituito un oggetto di tipo di valore composto o anonimi o una matrice di tali oggetti. Questi valori possono essere annidati.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Elenco degli operatori LINQ supportati
Di seguito è riportato un elenco di operatori LINQ supportati nel provider LINQ incluso in .NET SDK DocumentDB.

-   **Selezionare**: servizio di traduzione previsioni verso l'istruzione SQL SELECT tra cui la costruzione di oggetti
-   **Dove**: i filtri vengono convertite in SQL WHERE e supporto tecnico di conversione tra & &, | | e! per gli operatori SQL
-   **SelectMany**: consente la rimozione delle matrici per la clausola SQL JOIN. Può essere utilizzato per catena/nidificare espressioni per filtrare elementi in forma di matrice
-   **OrderBy e OrderByDescending**: viene convertita in ORDER BY crescente/decrescente:
-   **CompareTo**: viene convertita in confronti intervallo. Generalmente utilizzata per le stringhe che non sono analoghe in .NET
-   **Scrivere**: converte in primo piano SQL per limitare i risultati di una query
-   **Funzioni matematiche**: supporta traduzione da. ARCSEN Ass, ARCCOS, di rete, ARCTAN, ARROTONDA. eccesso, Cos, Exp, planimetrie, Log, Log10, Pow, Round, segno, Sin, Sqrt, Tan, tronca alle funzioni predefinite SQL equivalenti.
-   **Funzioni stringa**: supporta traduzione da. EndsWith concatena, contiene, di rete, IndexOf, conteggio, ToLower, TrimStart, Sostituisci, Reverse, TrimEnd, StartsWith, sottostringa, ToUpper alle funzioni predefinite SQL equivalenti.
-   **Funzioni di matrice**: supporta traduzione da. Concatena della rete, contiene e conteggio per le funzioni incorporate SQL equivalenti.
-   **Funzioni di estensione geospaziali**: supporta traduzione da metodi stub distanza amore, IsValid e IsValidDetailed alle funzioni predefinite SQL equivalenti.
-   **Funzione di estensione funzione definita dall'utente**: funzione definita dal traduzione supporta dal metodo stub UserDefinedFunctionProvider.Invoke all'utente corrispondente.
-   **Varie**: supporta traduzione degli operatori condizionali e unione. Consentono di tradurre contiene stringa contiene, ARRAY_CONTAINS o IN SQL in base al contesto.

### <a name="sql-query-operators"></a>Operatori di query SQL
Ecco alcuni esempi che illustrano come parte di operatori di query standard vengono convertiti fino a DocumentDB query.

#### <a name="select-operator"></a>Selezionare operatore
La sintassi è `input.Select(x => f(x))`, dove `f` è un'espressione scalare.

**Espressione lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Espressione lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Espressione lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operatore SelectMany
La sintassi è `input.SelectMany(x => f(x))`, dove `f` è un'espressione scalare che restituisce un tipo di raccolta.

**Espressione lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>In operatore
La sintassi è `input.Where(x => f(x))`, dove `f` è un'espressione scalare che restituisce un valore Boolean.

**Espressione lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Espressione lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Query SQL composta
Gli operatori sopra possono essere composti in modo da formare query più efficaci. Poiché DocumentDB supporta raccolte annidate, la composizione può essere concatenata oppure annidata.

#### <a name="concatenation"></a>Concatenazione 

La sintassi è `input(.|.SelectMany())(.Select()|.Where())*`. Una query concatenata possibile iniziare con un `SelectMany` query seguita da più `Select` o `Where` operatori.


**Espressione lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Espressione lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Espressione lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Espressione lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Annidamento

La sintassi è `input.SelectMany(x=>x.Q())` dove Q è un `Select`, `SelectMany`, o `Where` operatore.

In una query nidificata, la query interna viene applicata a ogni elemento della raccolta esterna. Una caratteristica importante è che la query interna può fare riferimento ai campi degli elementi della raccolta esterna come self-join.

**Espressione lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Espressione lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Espressione lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Esecuzione di query SQL
DocumentDB espone risorse tramite un API REST che possono essere chiamati da qualsiasi linguaggio in grado di eseguire le richieste HTTP/HTTPS. Inoltre, DocumentDB offre le librerie di programmazione per diverse lingue diffuse quali .NET Node, JavaScript e Python. Supportano per l'API REST e varie librerie di esecuzione delle query SQL. .NET SDK supporta LINQ query oltre SQL.

Negli esempi seguenti viene illustrato come creare una query e inviare i dati con un account di database DocumentDB.

### <a name="rest-api"></a>API REST
DocumentDB offre un modello di programmazione REST aperto su HTTP. Effettuare il provisioning degli account di database utilizza una sottoscrizione Azure. Il modello di risorse DocumentDB è costituito da un set di risorse in un account di database, ognuno dei quali è indirizzato tramite un URI logico e stabile. Un set di risorse viene definito un feed di questo documento. Un account del database è costituito da un set di database, ognuno contenente più raccolte, ognuno dei quali a sua volta contengono documenti, funzioni definite dall'utente e altri tipi di risorse.

Il modello di interazione di base con queste risorse è tramite verbi HTTP GET, inserire, POST ed Elimina con loro interpretazione standard. Il verbo POST viene utilizzato per la creazione di una nuova risorsa, per l'esecuzione di una stored procedure o per l'esecuzione di una query di DocumentDB. Le query vengono sempre letti solo operazioni senza effetti lato.

Nell'esempio seguente mostrano un POST per una query di DocumentDB effettuata una raccolta che contiene due documenti di esempio che abbiamo esaminato finora. La query ha un filtro semplice sulla proprietà nome JSON. Si noti l'utilizzo della `x-ms-documentdb-isquery` e tipo di contenuto: `application/query+json` intestazioni per indicare che l'operazione è una query.


**Richiesta**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Risultati**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Il secondo esempio mostra una query più complessa che restituisce più risultati da join.

**Richiesta**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Risultati**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Se non si adattano risultati di una query all'interno di una singola pagina dei risultati, quindi API REST restituisce un token di continuazione mediante la `x-ms-continuation-token` intestazione di risposta. I clienti possono impaginazione risultati, inclusa l'intestazione nei risultati successivi. È anche possibile controllare il numero di risultati per pagina tramite il `x-ms-max-item-count` intestazione numero.

Per gestire i criteri di coerenza dei dati per le query, utilizzare la `x-ms-consistency-level` intestazione ad esempio tutte le richieste API REST. Per coerenza sessione, è necessario restituire anche le ultime `x-ms-session-token` intestazione Cookie nella richiesta di query. Si noti che criterio di indicizzazione dell'insieme richiesto inoltre influenzare la coerenza dei risultati della query. Con il valore predefinito indicizzazione le impostazioni dei criteri, per le raccolte l'indice è sempre corrente con il contenuto del documento e i risultati della query corrisponderanno la coerenza scelta per i dati. Se il criterio di indicizzazione è resa più flessibile per Lazy, query possono restituire risultati non aggiornati. Per ulteriori informazioni, fare riferimento ai [Livelli di coerenza DocumentDB] [consistency-levels].

Se il criterio di indicizzazione configurato nella raccolta non supporta la query specificata, il server DocumentDB restituisce 400 "richiesta non valida". Questo viene restituito per le query di intervallo su percorsi configurati per eseguire ricerche di hash (uguaglianza) e per i percorsi in modo esplicito esclusi dall'indicizzazione. Il `x-ms-documentdb-query-enable-scan` intestazione può essere specificata per consentire la query eseguire un'analisi quando non è disponibile un indice.

### <a name="c-net-sdk"></a>SDK C# (.NET)
.NET SDK supporta LINQ e SQL query. Nell'esempio seguente viene illustrato come eseguire la query di filtro semplice introdotta in precedenza in questo documento.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


In questo esempio Confronta due proprietà uguaglianza all'interno di ogni documento e utilizza previsioni anonimi. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Nell'esempio successivo viene join esplicito tramite LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Il client .NET scorre automaticamente tutte le pagine dei risultati della query nei blocchi di foreach, come illustrato sopra. Le opzioni di query introdotte nella sezione API REST sono anche disponibili in .NET SDK utilizzando il `FeedOptions` e `FeedResponse` classi nel metodo CreateDocumentQuery. Il numero di pagine può essere controllato utilizzando il `MaxItemCount` impostazione. 

È possibile controllare in modo esplicito anche lo spostamento mediante la creazione di `IDocumentQueryable` utilizzando il `IQueryable` oggetto, quindi, leggere il` ResponseContinuationToken` valori e inviarli nuovamente come `RequestContinuationToken` in `FeedOptions`. `EnableScanInQuery`impostare per attivare l'analisi quando la query non supportata dai criteri di indicizzazione configurato. Per gli insiemi partizionati, è possibile utilizzare `PartitionKey` per eseguire la query rispetto a una singola partizione (anche se DocumentDB può estrarre automaticamente questo dal testo della query), e `EnableCrossPartitionQuery` per eseguire query che potrebbe essere necessario eseguire in più partizioni. 

[Esempi di .NET DocumentDB](https://github.com/Azure/azure-documentdb-net) per vedere altri esempi contenente le query. 

### <a name="javascript-server-side-api"></a>API JavaScript sul lato server 
DocumentDB fornisce un modello di programmazione per l'esecuzione la logica dell'applicazione di JavaScript in base a direttamente in raccolte utilizzando i trigger e stored procedure. Logica JavaScript registrata un livello di raccolta quindi possibile eseguire operazioni di database sulle operazioni sui documenti dell'insieme specificato. Queste operazioni vengono inclusi in ambiente transazioni ACID.

Nell'esempio seguente viene illustrato come utilizzare il queryDocuments nel server JavaScript API per eseguire query dall'interno di stored procedure e trigger.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Funzioni di aggregazione

Supporto per le funzioni di aggregazione è nel programma works, ma se è necessario funzionalità count o sum nel frattempo, è possibile ottenere lo stesso risultato usando vari metodi.  

In percorso di lettura:

- Per eseguire funzioni di aggregazione, recupero dei dati ed eseguire il conteggio in locale. È consigliabile per usare una proiezione di query economico come `SELECT VALUE 1` invece completo dei documenti come `SELECT * FROM c`. Consente di aumentare il numero di documenti elaborati in ogni pagina dei risultati, evitando ulteriori andata e ritorno al servizio, se necessario.
- È anche possibile utilizzare una stored procedure per ridurre al minimo la latenza di rete ripetute round trip. Per un esempio stored procedure che calcola il numero di una query di filtro specificati, vedere [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). Stored procedure consente agli utenti di combinare logica RTF insieme eseguendo aggregazioni in modo efficiente.

Percorso di scrittura:

- Un altro modello comune consiste nel pre-aggregare i risultati nel percorso "scrittura". Si tratta in particolare accattivante quando il volume di richieste di tipo "leggere" è superiore a quello di richieste di tipo "Redazione". Una volta pre-aggregato, i risultati sono disponibili con un singolo punto richiesta di lettura.  Il modo migliore per pre-aggregare in DocumentDB è per impostare un trigger richiamato con ogni "scrittura" e aggiornare un documento basato su metadati che contiene i risultati più recenti per la query che viene materializzata. Ad esempio, vedere l'esempio [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) , che aggiorna minSize, maxSize e totalSize del documento basato su metadati per la raccolta. L'esempio può essere esteso per aggiornare un contatore, somma e così via.

##<a name="references"></a>Riferimenti
1.  [Introduzione a DocumentDB Azure][introduction]
2.  [Specifica DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Esempi di .NET DocumentDB](https://github.com/Azure/azure-documentdb-net)
4.  [Livelli di coerenza DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  JavaScript specifica [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Tecniche di valutazione di query per database di grandi dimensioni [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Elaborazione di sistemi di Database relazionale in parallelo, IEEE Computer, premere la società, 1994 delle query
11. Lu, Ooi, Tan, elaborazione nei sistemi di Database relazionale in parallelo, IEEE Computer, premere la società, 1994 delle Query.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrea Tomkins: maialino latina: una lingua non in modo esterna per l'elaborazione di dati, 2008 SIGMOD.
13.     G. Graefe. Framework a catena per l'ottimizzazione delle query. Dati IEEE jolly Bull., 18, paragrafo 3: 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
