<properties
    pageTitle="Croce origine supporto condivisione (CORS) | Microsoft Azure"
    description="Informazioni su come attivare il supporto di CORS per i servizi di archiviazione di Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Origine croce condivisore (CORS) supporto per i servizi di archiviazione Azure

A partire da versione 2013-08-15, i servizi di archiviazione Azure supportano la condivisione delle risorse tra origine (CORS) per i servizi di Blob, tabella, coda e File. CORS è una caratteristica HTTP che consente a un'applicazione web in esecuzione in un dominio accedere alle risorse in un altro dominio. Browser Web implementano restrizioni di protezione noto come [criteri stessa origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) che impedisce a una pagina web da chiamare le API in un dominio diverso. CORS offre un modo protetto per consentire a un dominio, il dominio di origine, chiamare API in un altro dominio. Vedere le [specifiche CORS](http://www.w3.org/TR/cors/) per informazioni dettagliate sulla CORS.

È possibile impostare regole CORS singolarmente per ciascun servizio di archiviazione chiamando [Impostare le proprietà delle servizio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Impostare proprietà del servizio di coda](https://msdn.microsoft.com/library/hh452232.aspx)e [Impostare le proprietà delle servizio tabella](https://msdn.microsoft.com/library/hh452240.aspx). Dopo aver impostato le regole CORS per il servizio, una richiesta correttamente autenticata effettuata in base al servizio da un altro dominio verrà valutata per determinare se è consentito in base alle regole specificate.

>[AZURE.NOTE] Si noti che CORS non è un meccanismo di autenticazione. Qualsiasi domanda rispetto a una risorsa di archiviazione quando è abilitata CORS deve avere una firma di autenticazione appropriata oppure dovrà essere considerato relative a una risorsa pubblica.

## <a name="understanding-cors-requests"></a>Informazioni sulle CORS richieste

Una richiesta di CORS da un dominio di origine può essere costituito da due richieste separate:

- Una richiesta di verifica preliminare, che CORS restrizioni il servizio di una query. Richiesta di verifica preliminare è necessaria solo se la convocazione è un [metodo semplice](http://www.w3.org/TR/cors/), ovvero GET, testa o POST.

- La richiesta, eseguita la risorsa desiderata.

### <a name="preflight-request"></a>Richiesta di verifica preliminare

Le query di richiesta di verifica preliminare le restrizioni CORS che sono state create per il servizio di archiviazione per il proprietario dell'account. Nel web browser (o altro agente utente) invia una richiesta di opzioni che include le intestazioni di richiesta, dominio metodo e origine. Il servizio di archiviazione viene valutato l'operazione in base a un preconfigurato set di regole CORS che specificano possono specificare quali domini di origine, metodi di richiesta e le intestazioni della richiesta in una richiesta effettiva rispetto a una risorsa di archiviazione.

Se esiste una regola CORS che corrisponde alla richiesta di verifica preliminare CORS è abilitato per il servizio, il servizio risponde con codice di stato 200 (OK) e include le intestazioni di controllo di accesso richieste nella risposta.

Se CORS non è abilitato per il servizio o nessuna regola CORS corrisponde alla richiesta di verifica preliminare, il servizio risponderà con codice di stato 403 (accesso negato).

Se la richiesta di opzioni non contiene intestazioni CORS richieste (le intestazioni di origine e metodo richiesta di controllo accesso), il servizio risponderà con codice di stato 400 (richiesta non valida).

Si noti che una richiesta di verifica preliminare viene valutata in base al servizio (Blob, coda e una tabella) e non per la risorsa richiesta. È necessario che il proprietario dell'account sia abilitato CORS come parte della proprietà del servizio di account per la richiesta completare l'ordine.

### <a name="actual-request"></a>Richiesta effettivo

Quando la risposta viene restituita la richiesta di verifica preliminare viene accettata, il browser invierà la richiesta in una risorsa dello spazio di archiviazione. Il browser impedisce l'effettivo richiedere immediatamente se la richiesta di verifica preliminare viene rifiutata.

La richiesta viene considerata come richiesta normale in base al servizio di archiviazione. La presenza dell'intestazione di origine indica che la richiesta sia una richiesta CORS e il servizio controllerà le regole di CORS corrispondenti. Se viene trovata una corrispondenza, le intestazioni di controllo di accesso sono aggiunto alla risposta e inviate al client. Se non viene trovata una corrispondenza, le intestazioni di controllo di accesso CORS non vengono restituite.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Attivazione CORS per i servizi di archiviazione di Azure

Le regole CORS sono impostate a livello di servizio, pertanto è necessario abilitare o disabilitare CORS per ogni servizio (Blob, coda e una tabella) separatamente. Per impostazione predefinita, CORS è disattivata per ogni servizio. Per abilitare CORS, è necessario impostare le proprietà di servizio appropriato tramite versione 2013-08-15 o versioni successive e aggiungere regole di CORS per le proprietà del servizio. Per informazioni dettagliate su come abilitare o disabilitare CORS di un servizio e su come impostare le regole di CORS, fare riferimento a [Impostare le proprietà delle servizio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Impostare proprietà del servizio di coda](https://msdn.microsoft.com/library/hh452232.aspx)e [Impostare le proprietà delle servizio tabella](https://msdn.microsoft.com/library/hh452240.aspx).

Ecco un esempio di una singola regola CORS specificato tramite un'operazione di impostare le proprietà del servizio:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Ogni elemento incluso nella regola CORS descritto di seguito:

- **AllowedOrigins**: I domini di origine sono consentiti a una richiesta in base al servizio di archiviazione tramite CORS. Dominio di origine è il dominio da cui è stata creata la richiesta. Si noti che l'origine deve essere una corrispondenza esatta con l'origine età utente invia al servizio. È inoltre possibile utilizzare il carattere jolly ' *' per consentire a tutti i domini di origine rendere le richieste tramite CORS. Nell'esempio precedente, i domini [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) inserirvi richieste per il servizio utilizzando CORS.

- **AllowedMethods**: I metodi, verbi richiesta HTTP, che può usare il dominio di origine per una richiesta CORS. Nell'esempio precedente sono consentite solo le richieste di caricamento e lettura.

- **AllowedHeaders**: le intestazioni di richiesta che il dominio di origine può specificare la richiesta di CORS. Nell'esempio precedente, tutte le intestazioni di metadati a partire da x-ms-metadati, destinazione metatag di ms x e x-ms-meta-abc consentite. Si noti che il carattere jolly "*" indica che qualsiasi inizio intestazione con il prefisso specificato è consentita.

- **ExposedHeaders**: le intestazioni di risposta che potrebbero essere inviate in risposta alla richiesta di CORS ed esposti dal browser in autorità richiesta. Nell'esempio precedente, il browser viene richiesto di esporre qualsiasi iniziando di intestazione x-ms-metatag.

- **MaxAgeInSeconds**: richiedere massima quantità di tempo che un browser memorizzazione nella cache le opzioni di verifica preliminare.

I servizi di archiviazione Azure supporta la specifica intestazioni prefissata per entrambi gli **AllowedHeaders** **ExposedHeaders** elementi e. Per consentire una categoria di intestazioni, è possibile specificare un prefisso comune alla categoria. Ad esempio, specificando *x-ms-metatag** come un'intestazione prefissata stabilisce una regola che corrisponda a tutte le intestazioni che iniziano con x-ms-metatag.

Limitazioni per le regole CORS:

- È possibile specificare un massimo di cinque regole CORS per il servizio di archiviazione (Blob, tabella e coda).
- La dimensione massima del tutto CORS impostazioni relative alle regole su richiesta, esclusione dei tag XML, non deve superare 2 KB.
- La lunghezza di un'intestazione consentiti, intestazione esposti o origine consentiti non deve superare 256 caratteri.
- Le intestazioni di consentiti ed esposti possono essere:
  - Intestazioni letterale, in cui viene fornito il nome esatto dell'intestazione, ad esempio **x ms-metatag elaborato**. È possibile specificare un massimo di 64 letterale intestazioni nella richiesta.
  - Prefisso intestazioni, in cui viene fornito un prefisso dell'intestazione, ad esempio **i metadati di ms x***. Specificando un prefisso in questo modo consente o espone un'intestazione che inizia con il prefisso specificato. È possibile specificare un massimo di due intestazioni prefisso nella richiesta.
- Metodi (o verbi HTTP) specificato nell'elemento **AllowedMethods** devono essere conforme ai metodi supportati dal servizio di archiviazione Azure API. Metodi supportati sono Elimina, GET, testa, stampa unione, POST, opzioni e inserire.

## <a name="understanding-cors-rule-evaluation-logic"></a>Logica di valutazione di Understanding CORS regola

Quando un servizio di archiviazione riceve una richiesta di verifica preliminare o effettiva, verrà visualizzata la dicitura la richiesta in base alle regole CORS che aver stabilito per il servizio tramite l'operazione di impostare le proprietà del servizio appropriata. Le regole CORS vengono valutate nell'ordine in cui sono state impostate nel corpo della richiesta dell'operazione di impostare le proprietà del servizio.

Le regole CORS vengono valutate come indicato di seguito:

1. Prima di tutto, il dominio di origine della richiesta viene verificato con i domini elencati per l'elemento **AllowedOrigins** . Se il dominio di origine è incluso nell'elenco o tutti i domini consentiti con il carattere jolly "*", quindi le regole di valutazione procede. Se il dominio di origine non è incluso, la richiesta non riesce.

2. Successivamente, il metodo (o verbo HTTP) della richiesta rispetto a quelli elencati nell'elemento **AllowedMethods** . Se il metodo è incluso nell'elenco, quindi procede valutazione delle regole; in caso contrario, la richiesta.

3. Se la richiesta corrisponde a una regola nel proprio dominio di origine e il relativo metodo tale regola sia selezionata al processo che vengono valutate la richiesta e non altre regole. Prima che è possibile eseguire correttamente la richiesta, tuttavia, le intestazioni specificate nella richiesta vengono confrontate con le intestazioni elencate nell'elemento **AllowedHeaders** . Se le intestazioni inviate non corrispondono a intestazioni consentite, la richiesta.

Poiché le regole vengono elaborate nell'ordine che sono presenti nel corpo della richiesta, è consigliabile specificare innanzitutto le regole più restrittive relative alle origini nell'elenco, in modo che i vincoli vengono valutati prima di tutto. Specificare le regole che vengono meno restrittive –, ad esempio, una regola per consentire a tutte le origini diverse, alla fine dell'elenco.

### <a name="example--cors-rules-evaluation"></a>Esempio: CORS regole di valutazione

Nell'esempio seguente mostra un corpo della richiesta parziale per un'operazione impostare le regole CORS per i servizi di archiviazione. Per informazioni dettagliate sulla creando la richiesta, vedere [Impostare le proprietà delle servizio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Impostare proprietà del servizio di coda](https://msdn.microsoft.com/library/hh452232.aspx)e [Impostare proprietà del servizio di tabella](https://msdn.microsoft.com/library/hh452240.aspx) .

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Successivamente, prendere in considerazione le richieste di CORS seguenti:

Richiesta||| Risposta||
---|---|---|---|---
**Metodo** |**Origine** |**Le intestazioni di richiesta** |**Corrispondenza regola** |**Risultato**
**INSERIRE** | http://www.contoso.com |x-ms-blob-tipo di contenuto | Prima regola |Successo
**Ottieni** | http://www.contoso.com| x-ms-blob-tipo di contenuto | Seconda regola |Successo
**Ottieni** | http://www.contoso.com| x-ms-blob-tipo di contenuto | Seconda regola | Errore

La prima richiesta soddisfa la prima regola: il dominio di origine corrisponda origini consentite, il metodo corrisponde metodi consentiti e l'intestazione corrisponde intestazioni consentite – e pertanto ha avuto esito positivo.

La seconda richiesta non corrisponde la prima regola, perché il metodo non corrisponde a metodi consentiti. Tuttavia, corrisponde la seconda regola, in modo che ha avuto esito positivo.

La terza richiesta soddisfa la seconda regola il dominio di origine e il metodo, in modo che nessun ulteriormente regole vengono valutate. *Intestazione x-ms--richiesta-id client* non consentito tramite la seconda regola, in modo che la richiesta non riesce, indipendentemente dal fatto che avrebbe consentito la semantica della terza regola che venga eseguita correttamente.

>[AZURE.NOTE] Anche se questo esempio mostra una regola meno restrittiva prima di un progetto più restrittiva, in generale è la procedura consigliata per visualizzare un elenco di regole più restrittive prima di tutto.

## <a name="understanding-how-the-vary-header-is-set"></a>Informazioni sulla configurazione intestazione variano

L'intestazione *variano* è un'intestazione HTTP/1.1 standard costituito da un set di campi di intestazione richiesta che l'agente utente o browser informazioni sui criteri selezionati dal server per l'elaborazione della richiesta di notifica. Intestazione *variano* viene utilizzato principalmente per la memorizzazione nella cache proxy, browser e reti CDN che utilizzano per determinare il modo in cui deve essere memorizzata la risposta. Per informazioni dettagliate, vedere le specifiche per l' [intestazione variano](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando il browser o un altro agente utente memorizza la risposta da una richiesta di CORS, il dominio di origine viene memorizzato come origine consentito. Quando un dominio di secondo invia la stessa richiesta per una risorsa di archiviazione mentre è attiva la cache, l'agente utente recupera il dominio di origine memorizzati nella cache. Secondo dominio non corrisponde al dominio memorizzati nella cache, in modo che la richiesta non riesce quando avrebbero esito positivo. In alcuni casi, lo spazio di archiviazione di Azure imposta l'intestazione variano in **origine** per indicare l'agente utente per inviare la richiesta CORS successive al servizio quando il dominio richiedente è diversa dall'origine memorizzati nella cache.

Archiviazione Azure imposta l'intestazione *varia* **origine** per le richieste GET/testa effettive nei seguenti casi:

- Quando l'origine richiesta corrisponde esattamente a origine consentito definito da una regola CORS. Per una corrispondenza esatta, la regola CORS potrebbe non includere un carattere jolly ' * ' caratteri.

- Nessuna regola corrispondente origin richiesta, ma CORS è abilitato per il servizio di archiviazione.

Nel caso in cui una richiesta GET/testa corrisponde a una regola CORS che consente di tutte le origini diverse, la risposta indica che sono consentite tutte le origini diverse e la cache dell'agente utente consentirà le successive richieste di qualsiasi dominio di origine mentre è attiva la cache.

Si noti che per le richieste di metodi diverso da GET/testa, i servizi di archiviazione non verranno impostata intestazione varia dal momento che non vengono memorizzate le risposte a questi metodi per gli agenti utente.

Nella tabella seguente indica lo spazio di archiviazione come Azure risponderà alle richieste GET/testa in base a casi indicati in precedenza:

Richiesta|Impostazione dell'account e il risultato della valutazione delle regole|||Risposta|||
---|---|---|---|---|---|---|---|---
**Intestazione di origine richiesta** | **Regole CORS specificate per questo servizio** | **Esiste una regola corrispondente che consente di tutte le origini (*)** | **Regola di corrispondenza esistente per corrispondenza esatta origine** | **Risposta include intestazione variano impostato su origine** | **Risposta include Access controllo-consentita-origine: "*"** | **Risposta include accesso--esposti-intestazioni dei controlli**
No|No|No|No|No|No|No
No|Sì|No|No|Sì|No|No
No|Sì|Sì|No|No|Sì|Sì
Sì|No|No|No|No|No|No
Sì|Sì|No|Sì|Sì|No|Sì
Sì|Sì|No|No|Sì|No|No
Sì|Sì|Sì|No|No|Sì|Sì


## <a name="billing-for-cors-requests"></a>Per le richieste CORS di fatturazione

Richieste di verifica preliminare completate vengono addebitate se è stata attivata CORS per uno dei servizi di archiviazione per l'account (chiamando [Impostare le proprietà delle servizio Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Impostare le proprietà delle servizio coda](https://msdn.microsoft.com/library/hh452232.aspx)o [Impostare proprietà del servizio di tabella](https://msdn.microsoft.com/library/hh452240.aspx)). Per ridurre a icona in base alle tariffe, provare a impostare l'elemento **MaxAgeInSeconds** nelle regole CORS a un valore di grandi dimensioni in modo che l'agente utente memorizza la richiesta.

Richieste di verifica preliminare non non sarà effettuate.

## <a name="next-steps"></a>Passaggi successivi

[Impostare le proprietà di Blob servizio](https://msdn.microsoft.com/library/hh452235.aspx)

[Impostare le proprietà del servizio di coda](https://msdn.microsoft.com/library/hh452232.aspx)

[Configurare le proprietà del servizio di tabella](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C tra origine condivisore specifica](http://www.w3.org/TR/cors/)
