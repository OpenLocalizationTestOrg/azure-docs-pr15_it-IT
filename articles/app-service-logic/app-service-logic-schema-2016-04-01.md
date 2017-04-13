<properties 
    pageTitle="Nuova versione di schema 2016-06-01 | Microsoft Azure" 
    description="Informazioni su come scrivere la definizione di JSON per la versione più recente dell'App logica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nuova versione di schema 2016-06-01

Il nuovo schema e API versione per le applicazioni di logica include numerosi miglioramenti che migliorano le prestazioni e facilità di utilizzo delle App logica. Vi sono 3 importanti differenze:

1. Aggiunta di ambiti, le azioni che contengono un insieme di azioni.
1. Condizioni e cicli sono azioni prima classe
1. Esecuzione ordinamento più dettagliato tramite `runAfter` proprietà (che sostituisce `dependsOn`)

Per informazioni sull'aggiornamento delle applicazioni di logica dallo schema 2015-08-01-anteprima allo schema 2016-06-01 [consulta la sezione aggiornamento riportata di seguito.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. ambiti di

Uno dei cambiamenti principali apportati in questo schema è l'aggiunta di ambiti e la possibilità di nidificare azioni all'interno di altro.  Questa funzionalità è utile quando il raggruppamento di un insieme di azioni o la necessità di annidare azioni all'interno di altra (ad esempio una condizione può contenere un'altra condizione).  Ulteriori informazioni sulla sintassi di ambito possono essere trovato [qui](app-service-logic-loops-and-scopes.md), ma un esempio di ambito semplice sono disponibili sotto:


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. le condizioni e cicli modifiche

Nelle versioni precedenti dello schema, condizioni e cicli sono parametri associati a una singola azione.  Questa limitazione è stata elevata in questo schema e ora condizioni e cicli visualizzati come un tipo di azione.  Altre informazioni sono disponibili [in questo articolo](app-service-logic-loops-and-scopes.md)e un semplice esempio di una condizione di azione è illustrato di seguito:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. proprietà RunAfter

Il nuovo `runAfter` sostituzione di proprietà `dependsOn` per consentire maggiore precisione in fase di ordinamento.  `dependsOn`è stato un sinonimo di "l'azione è stato eseguito e ha avuto esito positivo," tuttavia tutte le volte è necessario eseguire un'azione, se l'azione precedente ha esito positivo, non è riuscita o ignorati.  `runAfter`Consente di tale flessibilità.  È un oggetto che specifica tutti i nomi di azione che verrà eseguita dopo e definisce una matrice di stato accettabili al trigger da.  Per esempio se si desidera eseguire dopo passaggio è stata completata A e B è stato esito positivo o negativo, è possibile creare le operazioni seguenti `runAfter` proprietà:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>L'aggiornamento a schema 2016-06-01

L'aggiornamento al nuovo schema 2016-06-01 richiede solo pochi passaggi.  Informazioni sulle modifiche dello schema sono disponibili [in questo articolo](app-service-logic-schema-2016-04-01.md).  Il processo di aggiornamento include l'esecuzione di script di aggiornamento, salvataggio come nuova app logica e potenzialmente sovrascrivere vecchia app logica se necessario.

1. Aprire l'app logica corrente.
1. Fare clic sul pulsante di **Aggiornamento Schema** sulla barra degli strumenti
   
    ![][1]
   
    Viene restituita la definizione aggiornata.  È possibile copiare e incollare questa in una definizione di risorse se ha bisogno, ma è **consigliabile** utilizzare il pulsante **Salva con nome** per assicurarsi che tutte le connessioni riferimenti sono validi nell'app logica aggiornata.
1. Fare clic sul pulsante **Salva con nome** nella barra degli strumenti della stessa e aggiornamento.
1. Compilare il nome e logica lo stato di app e fare clic su **Crea** per distribuire l'applicazione di logica di aggiornamento.
1. Verificare che l'app aggiornata logica funziona come previsto.

    >[AZURE.NOTE] Se si utilizza un trigger manuale o richiesta, l'URL callback sarà cambiato nella nuova app logica.  Utilizzare il nuovo URL per verificare il funzionamento-to-end ed è possibile duplicare tramite l'app logica esistente per mantenere gli URL precedenti.

1. *Facoltativo* Usare il pulsante **duplicato** nella barra degli strumenti (accanto all'icona di **Aggiornamento Schema** nell'immagine precedente) per sovrascrivere l'app logica precedente con la nuova versione di schema.  Questo è necessario solo se si desidera mantenere lo stesso ID risorsa o richiedere URL trigger dell'app logica.

### <a name="upgrade-tool-notes"></a>Strumento di aggiornamento delle note

#### <a name="condition-mapping"></a>Mapping condizione

Lo strumento renderà meglio per raggruppare azioni ramo true e false in un ambito nella definizione del aggiornata.  In particolare lo schema di progettazione di `@equals(actions('a').status, 'Skipped')` deve apparire come una `else` azione.  Tuttavia se lo strumento rileva modelli non riconosce che potenzialmente creerà condizioni separate per il valore vero e falsa diramazione.  Operazioni possono essere nuovamente mappate registrare l'aggiornamento, se necessario.

#### <a name="foreach-with-condition"></a>ForEach condizione
  
Il modello precedente di un ciclo foreach con una condizione per ogni elemento può essere replicato nello schema di nuovo l'azione del filtro.  Dovrebbe essere eseguita automaticamente durante l'aggiornamento.  La condizione diventa un'operazione di filtro prima del ciclo di foreach (per restituire solo una matrice di elementi che soddisfano la condizione) e tale matrice viene passata in atto foreach.  È possibile visualizzare un esempio di questo [in questo articolo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Tag di risorse

Tag di risorse vengono rimosse durante l'aggiornamento e sarà necessario impostare nuovamente per il flusso di lavoro aggiornata.

## <a name="other-changes"></a>Altre modifiche

### <a name="manual-trigger-renamed-to-request-trigger"></a>Attivazione manuale rinominato in trigger richiesta

Il tipo di `manual` è stata deprecata e rinominato in `request` con il tipo di `http`.  Si tratta più coerenza con il tipo di modello che il trigger viene utilizzato per creare.

### <a name="new-filter-action"></a>Nuova azione 'filtro'

Se si lavora con una matrice di entità consistente ed è necessario filtrare per difetto a un set più piccolo di elementi, è possibile utilizzare il nuovo tipo di 'filtro'.  Accetta una matrice e una condizione e verrà valutare la condizione per ogni elemento e restituire una matrice di elementi che soddisfano la condizione.

### <a name="foreach-and-until-action-restrictions"></a>ForEach e fino a restrizioni di azione

Foreach e fino a ciclo continuo sono limitate a una singola azione.

### <a name="trackedproperties-on-actions"></a>TrackedProperties sulle azioni

Azioni ora possono avere una proprietà aggiuntiva (pari a `runAfter` e `type`) chiamato `trackedProperties`.  Si tratta di un oggetto che specifica determinati input di azione o output da includere nel telemetria Azure diagnostica che viene generato come parte di un flusso di lavoro.  Per esempio:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Utilizzare la definizione del flusso di lavoro app logica](app-service-logic-author-definitions.md)
- [Creare un modello di distribuzione app logica](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
