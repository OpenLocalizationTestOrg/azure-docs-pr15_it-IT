<properties 
    pageTitle="Riferimento criteri di gestione delle API Azure" 
    description="Informazioni sui criteri disponibili per configurare la gestione di API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Riferimento criteri di gestione delle API Azure

In questa sezione fornisce un indice per i criteri nel [riferimento di criteri di gestione delle API][]. Per informazioni sull'aggiunta e configurazione dei criteri, vedere [i criteri di gestione delle API][].

Espressioni criterio possono essere utilizzate come valori degli attributi o valori di testo in uno dei criteri di gestione delle API, a meno che il criterio specifica in caso contrario. Alcuni criteri, ad esempio i criteri di [flusso di controllo][] e [impostare variabile][] seguono si basano sulle espressioni di criteri. Per ulteriori informazioni, vedere [Avanzate criteri][] e [le espressioni di criteri][]

## <a name="policy-reference-index"></a>Indice di riferimento dei criteri

-   [Criteri di accesso][]
    -   [Intestazione HTTP controllare][] - vengono applicate esistenza e/o il valore di un'intestazione HTTP.
    -   [Tasso di chiamata limite mediante sottoscrizione][] - utilizzo delle API impedisce picchi limitando il tasso di chiamata, alla scala cronologica per abbonamento.
    -   [Tasso di chiamata limite dalla chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) : uso di API impedisce picchi limitando il tasso di chiamata, alla scala cronologica per chiave.
    -   [Limita chiamante IP][] - filtri (consente/Nega) chiamate da indirizzi IP specifici e/o intervalli di indirizzi.
    -   [Imposta quota di utilizzo mediante sottoscrizione][] - consente di applicare un rinnovabile o ciclo di vita chiamata volume e/o la larghezza di banda quota alla scala cronologica per abbonamento.
    -   [Imposta quota di utilizzo dalla chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - consente di applicare un rinnovabile o ciclo di vita chiamata volume e/o la larghezza di banda quota alla scala cronologica per chiave.
    -   [Convalidare JWT][] - vengono applicate esistenza e la validità di un JWT estratte da un'intestazione HTTP specificata o un parametro di query specificata.
-   [Criteri avanzati][]
    -   [Flusso di controllo][] - applica in modo condizionale istruzioni di criteri in base ai risultati della valutazione delle [espressioni][]booleane.
    -   [Richiesta di inoltro][] - inoltra la richiesta per il servizio di back-end.
    -   [Registro eventi hub][] - invia messaggi nel formato specificato a un messaggio di destinazione definito da un'entità [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Riprovare](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - tentativi di esecuzione delle istruzioni per i criteri racchiuso, se e finché non viene soddisfatta la condizione. Esecuzione verrà ripetuta degli intervalli di tempo specificato per tutte le specificato conteggio tentativi.
    -   [Restituire risposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - esecuzione della pipeline le interruzioni e restituisce la risposta specificata direttamente al chiamante.
    -   [Invia richiesta unidirezionale](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - invia una richiesta all'URL specificato, senza attendere che una risposta.
    -   [Invia richiesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - invia una richiesta all'URL specificato.
    -   [Metodo di richiesta di set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - consente di modificare il metodo per una richiesta HTTP.
    -   [Impostare lo stato](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - viene modificato il codice di stato HTTP sul valore specificato.
    -   [Imposta variabile][] - mantenere un valore in una variabile denominato [contesto][] per un accesso successivo.
    -   [Traccia](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) : aggiunge una stringa nell'output di [Controllo API](../api-management/api-management-howto-api-inspector.md) .
    -   [Attendere](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - attendere racchiusa richiesta di invio, valore Get dalla cache o criteri di flusso di controllo per completare prima di continuare.
-   [Criteri di autenticazione][]
    -   [Autentica con Basic][] - autentica con un servizio di back-end mediante l'autenticazione di base.
    -   [Autenticazione con certificato client][] - autentica con un servizio di back-end tramite i certificati client.
-   [Criteri di memorizzazione nella cache][] 
    -   [Ottenere dalla cache][] - eseguire cache cercare e restituire una risposta valida quando disponibili.
    -   [Archivio alla cache][] - risposta cache in base alla configurazione di controllo della cache specificata.
    -   È possibile [ottenere valore dalla cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - recuperare un elemento nella cache dalla chiave.
    -   [Valore di archivio nella cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - Store un elemento nella cache dalla chiave.
    -   [Rimuovere valore dalla cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - rimuovere un elemento nella cache dalla chiave.
-   [Cross criteri di dominio][] 
    -   [Consenti domini chiamate][] - rendono l'API accessibile da Adobe Flash e Silverlight Microsoft client basati su browser.
    -   [CORS][] - aggiunge tra origine condivisore supporto (CORS) per un'operazione o un'API per consentire chiamate domini da client basati su browser.
    -   [JSONP][] - aggiunge JSON con spaziatura interna (JSONP) supporto per un'operazione o un'API per consentire chiamate domini da client basati su browser JavaScript.
-   [Criteri di trasformazione][] 
    -   [Convertire JSON in formato XML][] - converte richiesta o risposta corpo da JSON in formato XML.
    -   [Convertire XML in JSON][] - converte richiesta o risposta corpo da XML in JSON.
    -   [Ricerca e sostituzione di stringa nel corpo][] - trova una sottostringa richiesta o risposta e sostituito con una sottostringa diversa.
    -   [URL di maschera in contenuto][] - nuovamente scrive (maschere) collegamenti nella risposta corpo in modo che puntino al collegamento equivalente tramite il gateway.
    -   [Impostare il servizio di back-end][] - modifica il servizio di back-end per una richiesta di posta in arrivo.
    -   [Impostare corpo][] - imposta il corpo del messaggio per le richieste in arrivo e in uscita.
    -   [Intestazione HTTP set][] - assegna un valore per una risposta esistente e/o intestazione della richiesta o aggiunge una nuova intestazione di risposta e/o richiesta.
    -   [Impostare parametro stringa query][] - aggiunge valore sostituisce o Elimina richiesta di parametro stringa query.
    -   [Riscrittura URL][] - converte un URL di richiesta da forma pubblico al modulo previsto dal servizio web.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle espressioni di criteri, vedere il video seguente.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Criteri di accesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Selezionare l'intestazione HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Tasso di chiamata limite mediante sottoscrizione]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Limita chiamante IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Quota utilizzo set mediante sottoscrizione]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Convalidare JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Criteri avanzati]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flusso di controllo]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Imposta variabile]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[espressioni]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[scelta rapida]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Richiesta di inoltro]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registro eventi hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Criteri di autenticazione]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Eseguire l'autenticazione di base]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Eseguire l'autenticazione con certificato client]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Criteri di memorizzazione nella cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Ottenere dalla cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Memorizzare nella cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross criteri di dominio]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Consenti chiamate domini]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Criteri di trasformazione]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertire JSON in formato XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convertire un JSON XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Ricerca e sostituzione di stringa nel corpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Maschera URL nel contenuto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Servizio di back-end set]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Corpo set]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Intestazione HTTP set]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Parametro stringa query set]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Riscrittura URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Criteri di gestione delle API]: api-management-howto-policies.md
[Riferimento di criteri di gestione delle API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Espressioni di criteri]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
