<properties
    pageTitle="Memorizzazione nella cache personalizzati in Azure API Management"
    description="Informazioni su come memorizzare nella cache gli elementi chiave di gestione delle API di Azure"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>Memorizzazione nella cache personalizzati in Azure API Management
Supporto per [la memorizzazione nella cache di risposta HTTP](api-management-howto-cache.md) mediante l'URL di risorsa come chiave Azure servizio di gestione delle API. È possibile modificare la chiave tramite le intestazioni di richiesta mediante la `vary-by` proprietà. È utile per la memorizzazione nella cache intere risposte HTTP (o rappresentazioni), ma in alcuni casi può essere utile cache solo una parte di una rappresentazione in formato. I nuovi criteri di [valore di ricerca della cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e [valore di archivio cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) consentono di archiviare e recuperare elementi non autorizzati di dati all'interno di definizione dei criteri. Questa funzionalità consente inoltre di migliorare il criterio di precedenza introdotti [richiesta di invio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) perché è ora possibile memorizzare le risposte da servizi esterni.

## <a name="architecture"></a>Architettura  
Servizio di gestione dell'API utilizza una cache dei dati tenant condivise in modo che, quando si adatta fino a più unità verrà comunque accedere allo stesso dati memorizzati nella cache. Tuttavia, quando si lavora con una distribuzione più aree sono indipendenti cache all'interno di ciascuna delle aree. Per questo motivo, è importante non considerare la cache come un archivio di dati, in cui è l'unica fonte di alcune delle informazioni contenute. Se si ha e versioni successive deciso di sfruttare la distribuzione di più paesi, i clienti con utenti che viaggiano potrebbero perdere l'accesso ai dati memorizzati nella cache.

## <a name="fragment-caching"></a>Frammento di memorizzazione nella cache
Esistono casi in cui le risposte vengano restituite contengono una parte dei dati e su cui vengono costosa determinare ancora rimangono aggiornato per un certo tempo. Ad esempio, valutare la possibilità di un servizio incorporato da una compagnia aerea che fornisce informazioni relative voli, lo stato di volo e così via. Se l'utente è un membro del programma di punti delle compagnie aeree, hanno anche informazioni relative alla loro stato corrente e chilometraggio accumulato. Queste informazioni relative agli utenti possono essere memorizzate in un altro sistema, ma può essere utile includere nelle risposte restituite sulle prenotazioni e lo stato di volo. Questa operazione può essere eseguita mediante un processo denominato frammento memorizzazione nella cache. La rappresentazione principale può essere restituita dal server di origine utilizzando un tipo di token per indicare la posizione da inserire le informazioni relative agli utenti. 

Prendere in considerazione la seguente risposta JSON da un API back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Risorsa secondario e `/userprofile/{userid}` analogo a quello,

     { "username" : "Bob Smith", "Status" : "Gold" }

Per determinare le informazioni utente da includere, è necessario identificare che l'utente finale. Questo meccanismo è implementazione dipendenti. Se ad esempio si utilizza il `Subject` richiedere di un `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Questo archiviate `enduserid` valore in una variabile di contesto per successivi utilizzi. Il passaggio successivo consiste nel determinare se una richiesta precedente già recuperare le informazioni utente e memorizzati nella cache. A tale scopo è utilizzare il `cache-lookup-value` criteri.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Se nella cache che corrisponde al valore chiave, quindi non è presente nessuna voce `userprofile` variabile di contesto verrà creato. Si verifica del completamento della ricerca utilizzando i `choose` controllare i criteri di flusso.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Se il `userprofile` variabile di contesto non esiste, quindi procedere alla necessario effettuare una richiesta HTTP a recuperarlo.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Viene utilizzata la `enduserid` costruire l'URL per la risorsa profilo utente. Una volta è stata creata la risposta, è possibile estrarre il corpo del testo fuori la risposta e archiviarlo in una variabile di contesto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Per evitare Contattaci dover apportare nuovamente la richiesta HTTP quando l'utente stesso effettua un'altra richiesta, che è possibile archiviare il profilo utente nella cache.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Il valore è memorizzata nella cache con la stessa chiave esatta originariamente si è tentato di recuperarlo con. La durata si sceglie di archiviare il valore deve essere in base a come le modifiche alle informazioni e gli utenti come tolleranza sono spesso informazioni a aggiornato. 

È importante tenere presente che il recupero dalla cache è ancora un timeout del processo, richiesta di rete e potenzialmente può comunque aggiungere decine di millisecondi la richiesta. I vantaggi provengono per determinare che le informazioni del profilo utente richiede molto più tempo che causa la necessità di database query o informazioni di aggregazione da più back-end.

Il passaggio finale del processo consiste nell'aggiornare la risposta restituita con le informazioni del profilo utente.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Si è scelto di includere le virgolette come parte del token in modo che anche quando non si verifica Sostituisci, la risposta è stata ancora valido JSON. Si tratta principalmente per rendere il debug più semplice.

Dopo aver combinato insieme tutti questi passaggi, il risultato finale è un criterio simile a quella riportata di seguito.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Questo approccio memorizzazione nella cache viene utilizzato principalmente nei siti web in cui è composta HTML sul lato server in modo che sia possibile il rendering come una singola pagina. Tuttavia, può essere anche utile nelle API nel punto in cui i clienti non è possibile eseguire client lato nella cache HTTP o è opportuno non inserire tale responsabilità sul client.

Questo stesso tipo di memorizzazione nella cache frammento può essere eseguito anche sui server web back-end tramite un Redis memorizzazione nella cache server, tuttavia, utilizzando il servizio di gestione di API per eseguire questa operazione è utile quando i frammenti memorizzati nella cache provenienti da back-end diverso rispetto a quelle primarie.

## <a name="transparent-versioning"></a>Controllo delle versioni trasparente
È spesso i più versioni di implementazione diversa di un'API devono essere supportati in qualsiasi momento. Si tratta probabilmente per supportare ambienti diversi, ad esempio sviluppo, test, produzione e così via, o potrebbe essere per supportare le versioni precedenti di API per dare il tempo per gli utenti di API eseguire la migrazione alle versioni più recenti. 

Un approccio per la gestione di questo anziché richiedere agli sviluppatori di client modificare l'URL da `/v1/customers` a `/v2/customers` è archiviare nei dati di profilo del consumatore quale versione dell'API attualmente desiderano utilizzare e chiamare l'URL di back-end appropriati. Per determinare l'URL corretto back-end da chiamare per un determinato client, è necessario eseguire query su alcuni dati di configurazione. Per la memorizzazione nella cache questi dati di configurazione, è possibile ridurre a icona il problema di prestazioni di eseguire la ricerca.

Il primo passaggio consiste nel determinare l'identificatore utilizzato per configurare la versione desiderata. In questo esempio, si è scelto di associare la versione del codice product key abbonamento. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

È quindi eseguire una ricerca nella cache per verificare se è già stato recuperato la versione del client desiderato.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Quindi è controllare se non c' nella cache.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Se si non si è quindi abbiamo passare e recuperarlo.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Estrarre il corpo del testo risposta dalla risposta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Memorizzare nella cache per utilizzi futuri.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

E infine aggiornare l'URL di back-end per selezionare la versione del servizio desiderata dal client.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

Il criterio completamente è come indicato di seguito.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Agli utenti l'API trasparente controllare la versione di back-end accede a client senza dover aggiornare e ridistribuire client è una soluzione elegante che consente di risolvere numerosi problemi di controllo delle versioni di API.

## <a name="tenant-isolation"></a>Isolamento tenant

Nelle distribuzioni multi-tenant e di grandi dimensioni alcune aziende creare gruppi distinti di tenant nelle distribuzioni distinte dell'hardware di back-end. Consente di ridurre il numero di utenti interessati da un problema di hardware nel back-end. Consente inoltre nuove versioni di software essere distribuito in fasi. Ideale questa architettura back-end deve essere trasparente agli utenti di API. Può essere ottenuto in modo simile al controllo delle versioni trasparente poiché è basato sulla stessa tecnica di modifica l'URL di back-end tramite lo stato di configurazione per ogni chiave dell'API.  

Invece di restituire una versione preferita dell'API per ogni abbonamento, si restituirebbe un identificatore che mette in correlazione un tenant al gruppo hardware assegnate. Tale identificatore può essere utilizzato per costruire l'URL di back-end appropriati.

## <a name="summary"></a>Riepilogo
La possibilità di usare la cache di gestione di Azure API per l'archiviazione di qualsiasi tipo di dati consente di accedere facilmente ai dati di configurazione che possono influenzare la modalità di che elaborazione di una richiesta in entrata. Può inoltre essere utilizzato per memorizzare frammenti di dati che è possono integrare restituite da un back-end API e le risposte.

## <a name="next-steps"></a>Passaggi successivi
Inviare commenti e suggerimenti in thread Disqus di questo argomento se sono presenti altri scenari che questi criteri hanno abilitata per l'utente o se esistono scenari si desidera ottenere, ma si ritenga di non attualmente possibili.
