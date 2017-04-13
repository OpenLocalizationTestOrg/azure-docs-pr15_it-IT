<properties
   pageTitle="Interagire con i report utilizzando l'API JavaScript | Microsoft Azure"
   description="Power BI incorporato, interagire con i report utilizzando l'API JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagire con i report di Power BI tramite l'API JavaScript

L'API JavaScript di Power BI consente di incorporare facilmente i report di Power BI all'interno delle applicazioni. Con l'API, le applicazioni a livello di programmazione possono interagire con elementi diversi report quali pagine e i filtri. Questo interattività diventa report di Power BI parte più integrata di gestione dell'applicazione.

Incorporare un report di Power BI nell'applicazione utilizzando un iframe è ospitato come parte dell'applicazione. Iframe funge da un bordo tra l'applicazione e un report, come illustrato nell'immagine seguente. 

![Power BI incorporato iframe senza API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

Iframe rende molto più semplice il processo di incorporamento, ma senza l'API JavaScript il report e l'applicazione non è possibile interagire tra loro. Questa mancanza di interazione può risultare più consapevoli come il report non viene effettivamente parte dell'applicazione. Il report e l'applicazione sono realmente necessari comunicare e viceversa, come illustrato nella figura seguente.

![Power BI incorporato iframe con API Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

L'API JavaScript di Power BI è possibile scrivere codice che è possibile passare in modo sicuro attraverso il limite di iframe. In questo modo l'applicazione a livello di programmazione esegua un'azione in un report e di attendere gli eventi da azioni apportate dagli utenti all'interno del report.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Operazioni eseguibili con l'API JavaScript di Power BI?
Con l'API JavaScript sia possibile gestire i report, spostarsi tra le pagine in un report, filtrare un report e gestire l'incorporamento di eventi. Il diagramma seguente illustra la struttura dell'API.

![Diagramma di alimentazione API JavaScript di BI](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Gestire i report
API Javascript consente di gestire il comportamento a livello di report e pagina:

- Incorporare una specifica connessione all'origine dati in modo sicuro nell'applicazione - provare [incorporare applicazione demo](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Token di accesso set
- Configurare il report
  - Attivare e disattivare il riquadro di filtro e il riquadro di spostamento nel: tenta di [aggiornare le impostazioni demo applicazione](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Impostare le opzioni predefinite per le pagine e filtri, provare a [impostare demo le impostazioni predefinite](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Immettere e uscire dalla modalità a schermo intero

[Ulteriori informazioni sull'incorporamento di un report](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Spostarsi tra le pagine in un report
Enbales API JavaScript per scoprire tutte le pagine in un report e di impostare la pagina corrente. Provare l' [applicazione demo per l'esplorazione](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Ulteriori informazioni sulla struttura di spostamento pagine](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrare un report
L'API JavaScript fornisce base e avanzati filtraggio per i report incorporati e pagine del report. Provare il [filtro applicazione dimostrativa](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)e rivedere codice introduttivo qui.  


#### <a name="basic-filters"></a>Filtri di base
Un filtro di base inserito a livello di colonna o una gerarchia e contiene un elenco di valori per includere o escludere.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtri avanzati
Filtri avanzati utilizzano l'operatore logico e o o e accettare una o due condizioni, ognuna con i propri operatore e valore. Sono supportate condizioni:

- Nessuno
- LessThan
- LessThanOrEqual
- Maggiore
- GreaterThanOrEqual
- Contiene
- DoesNotContain
- StartsWith
- DoesNotStartWith
- È
- IsNot
- Val.
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Altre informazioni sui filtri](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Gestione degli eventi
Oltre a inviare le informazioni in iframe, l'applicazione inoltre possibile ricevere informazioni sugli eventi seguenti provenienti da iframe:

- Incorpora
  - caricato
  - Errore
- Report
  - pageChanged
  - dataSelected (disponibile a breve)

[Ulteriori informazioni sulla gestione degli eventi](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'API JavaScript di Power BI, visitare i collegamenti seguenti:

- [API Wiki JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Riferimenti al modello a oggetti](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Esempi
  - [Angolare](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demo Live](https://microsoft.github.io/PowerBI-JavaScript/demo/)
