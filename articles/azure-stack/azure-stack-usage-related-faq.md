<properties
    pageTitle="Domande frequenti sui utilizzo | Microsoft Azure"
    description="Elenco di contatori dello Stack di Azure, il confronto per l'uso di Azure API, tempo di utilizzo e segnalati ora, i codici di errore."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="azure-stack-usage-api-faqs"></a>Domande frequenti sull'utilizzo dell'API Stack Azure
In questo articolo fornisce le risposte ad alcune domande frequenti sull'utilizzo API Stack Azure.

## <a name="what-meter-ids-can-i-see"></a>Quali contatore ID è visualizzare?

Attualmente, l'uso viene segnalato per la rete, l'archiviazione e il provider di risorse di calcolo.

| **Provider di risorse** | **ID contatore** |**Nome contatore** | **Unità** | **Informazioni aggiuntive** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Rete** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Utilizzo di indirizzi IP pubblico | Indirizzo IP |                    
| **Spazio di archiviazione**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*ore | Capacità complessiva consumata da tabelle |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*ore | Capacità complessiva consumata da BLOB di pagine |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*ore  | Capacità complessiva consumata da coda |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*ore  | Capacità complessiva consumata da BLOB di blocco |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Richiedere il numero di 10.000   | Tabella le richieste del servizio (10.000) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Dati di ingresso in GB | In ingresso di dati del servizio di tabella in GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress in GB | Uscita di dati di tabella servizio in GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Contare le richieste di 10.000 | BLOB richieste di assistenza (in 10.000) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Dati di ingresso in GB          | BLOB servizio dati ingresso in GB 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress in GB              | BLOB servizio dati esterni in GB 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Contare le richieste di 10.000   | Accodare le richieste di servizio (in 10.000) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Dati di ingresso in GB         | In ingresso di dati del servizio di coda in GB 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress in GB  | Uscita di dati di servizio di coda in GB 
| **Calcolare** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | Macchine Virtuali dimensioni ore | Dimensioni del computer virtuale |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Confronto di API l'uso dello Stack di Azure e [Azure utilizzo API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (attualmente in pubblica versione di anteprima)

-   L'API di utilizzo Tenant è coerenza con l'API di Azure, con un'eccezione: il contrassegno *showDetails* attualmente non è supportato in pila Azure.

-   API di utilizzo del Provider si applica solo a Stack di Azure.

-   Al momento dell' [API RateCard](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) che è disponibile in Azure non è disponibile in pila Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Che cos'è la differenza tra l'ora di utilizzo e segnalati?

Report sull'uso dei dati sono due valori di ora principale:

-   **Tempo riscontrato**. Il tempo quando l'evento utilizzo immessi nel sistema di utilizzo

-   **Tempo di utilizzo**. Il tempo quando di utilizzo della risorsa di Stack di Azure

Se viene visualizzata una differenza tra valori per l'uso ora e segnalato per un evento specifiche sull'utilizzo. Il ritardo può essere purché più ore in un ambiente.

Attualmente, è possibile eseguire la query *solo dall'ora segnalati*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Che cosa significano i codici di errore l'uso dell'API?

| **Codice di stato HTTP** | **Codice di errore** | **Descrizione** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Richiesta 400/non valida        | *NoApiVersion*     | Il parametro di query *versione api* è manca.
| Richiesta 400/non valida        | *InvalidProperty*  | Una proprietà è vuoto o non è valido. Il messaggio nel codice di errore nel corpo della risposta identifica la proprietà manca.
| Richiesta 400/non valida        | *RequestEndTimeIsInFuture*  | Il valore di *ReportedEndTime* è in futuro. In futuro i valori non sono consentiti per questo argomento.
| Richiesta 400/non valida        | *SubscriberIdIsNotDirectTenant*    | Una chiamata API del provider utilizzato un ID abbonamento che non è un tenant valido del chiamante.
| Richiesta 400/non valida        | *SubscriptionIdMissingInRequest*   | ID abbonamento del chiamante risulta mancante.
| Richiesta 400/non valida        | *InvalidAggregationGranularity*   | È stata richiesta una granularità di aggregazione non valido. Valori validi sono giornaliera e oraria.
| 503                    | *ServiceUnavailable*   | Si è verificato un errore perché il servizio è occupato o la chiamata viene limitata. |

## <a name="next-steps"></a>Passaggi successivi
[Fatturazione clienti e rifiuto in pila di Azure](azure-stack-billing-and-chargeback.md)

[Utilizzo delle risorse provider API](azure-stack-provider-resource-api.md)

[Utilizzo delle risorse API del tenant](azure-stack-tenant-resource-usage-api.md)
