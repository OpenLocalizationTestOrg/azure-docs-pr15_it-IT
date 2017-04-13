<properties
   pageTitle="Guida introduttiva di Azure Active Directory Graph API | Microsoft Aure"
   description="API di grafico Azure Active Directory fornisce l'accesso di Azure Active Directory tramite i punti finali API REST OData. Applicazioni possono utilizzare l'API di grafico per eseguire creare, leggere, aggiornare ed eliminazione (CRUD) nella directory dati e oggetti."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Guida introduttiva di Azure Active Directory Graph API

L'API di grafico di Azure Active Directory (AD) fornisce l'accesso a Azure Active Directory tramite i punti finali API REST OData. Applicazioni possono utilizzare l'API di grafico per eseguire creare, leggere, aggiornare ed eliminazione (CRUD) nella directory dati e oggetti. Ad esempio, è possibile utilizzare l'API di grafico per creare un nuovo utente, visualizzare o aggiornare le proprietà dell'utente, modificare la password dell'utente, controllare l'appartenenza ai gruppi per l'accesso basato sui ruoli, disabilitare o eliminare l'utente. Per ulteriori informazioni sulle caratteristiche di API di grafico e scenari di applicazioni, vedere [API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [i prerequisiti API di Azure Active Directory grafico](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Azure Active Directory grafico API funzionalità è disponibile anche tramite [Microsoft Graph](https://graph.microsoft.io/), una API unificata che include API da altri servizi Microsoft, ad esempio Outlook, OneDrive, OneNote, pianificazione e Office Graph, accessibile tramite un unico endpoint e con un token di accesso singolo.

## <a name="how-to-construct-a-graph-api-url"></a>Come creare un URL di API di grafico

API di grafico, per accedere ai dati di directory e oggetti (in altre parole, risorse o entità) per la quale si desidera eseguire le operazioni CRUD, è possibile utilizzare gli URL in base al protocollo ODS (OData). URL utilizzato nell'API di grafico sono costituiti da quattro parti principali: servizio radice, identificatore tenant, percorso della risorsa e opzioni di stringa di query: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Prendere in considerazione l'URL seguente: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Radice del servizio**: nell'API di grafico di Azure Active Directory, radice del servizio è sempre https://graph.windows.net.
- **Identificatore tenant**: può trattarsi di un nome di dominio verificato (registrato), nell'esempio precedente, contoso.com. Può essere anche un ID oggetto tenant o "myorganiztion" o "me" alias. Per ulteriori informazioni, vedere [indirizzamento entità e operazioni dell'API grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Percorso della risorsa**: in questa sezione di un URL identifica la risorsa per interagire con (utenti, gruppi, un utente specifico o un gruppo specifico e così via) Nell'esempio precedente, si tratta di gruppi di livello superiore"" all'indirizzo set di risorse. È anche possibile risolvere un'entità specifica, ad esempio "utenti / {objectId}" o "utenti/userPrincipalName".
- **Parametri della query**:? Separa nella sezione percorso risorsa dalla sezione dei parametri query. Il parametro di query "versione api" è necessario per tutte le richieste di API di grafico. L'API Graph supporta anche le seguenti opzioni di query OData: **$filter**, **$orderby**, **$expand**, **$top**e **$format**. Le opzioni di query seguenti non sono attualmente supportate: **$count**, **$inlinecount**e **$skip**. Per ulteriori informazioni, vedere [query supportate, filtri e opzioni di spostamento in API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Grafico API versioni

Specificare la versione per una richiesta di API grafico nel parametro di query "versione api". Per versione 1.5 e versioni successive, si utilizza un valore numerico versione. versione API = 1.6. Per le versioni precedenti, si utilizza una stringa di data che aderisce nel formato gg / MM-aaaa; versione di api, ad esempio, = 2013-11-08. Per le funzionalità di anteprima, utilizzare la stringa "beta"; versione di api, ad esempio, = beta. Per ulteriori informazioni sulle differenze tra versioni API di grafico, vedere [il controllo delle versioni di Azure Active Directory grafico API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Grafico API dei metadati

Per restituire il file di metadati API di grafico, aggiungere il segmento "$metadata" dopo l'identificatore tenant nell'URL di esempio, l'URL seguente restituisce i metadati per la società demo utilizzata da Esplora aree di Graph: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. È possibile immettere l'URL nella barra degli indirizzi del browser per visualizzare i metadati. Il documento di metadati CSDL restituito descrive l'entità e tipi complessi, le relative proprietà e le funzioni e azioni esposte dalla versione dell'API grafico richiesto. Se si omette il parametro versione api restituirà metadati per la versione più recente.

## <a name="common-queries"></a>Query comuni

[Query comuni di Azure Active Directory grafico API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) Elenca comuni che possono essere usate con il grafico Azure Active Directory, incluse le query che possono essere utilizzate per accedere alle risorse di primo livello nella directory e query per eseguire operazioni nella directory.

Ad esempio `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` restituisce informazioni per contoso.com directory società.

O `https://graph.windows.net/contoso.com/users?api-version=1.6` Elenca tutti gli oggetti utente in contoso.com directory.

## <a name="using-the-graph-explorer"></a>In Esplora risorse di grafico

È possibile usare Esplora aree di grafico per l'API di Azure Active Directory grafico la query sui dati directory durante la compilazione di un'applicazione.

> [AZURE.IMPORTANT] Esplora aree di grafico non supporta la scrittura o eliminazione dei dati di una directory. È possibile eseguire solo operazioni di lettura alla directory di Azure Active Directory con Esplora aree di grafico.

Di seguito è riportato l'output si vogliono visualizzare se fosse necessario passare a Esplora aree di grafico, selezionare Usa Demo società e immettere `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` per visualizzare tutti gli utenti nella directory demo:

![Esplora api di Azure Active Directory grafico](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Caricare Esplora aree di Graph**: per caricare lo strumento, passare a [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Fare clic su **Usa Demo aziendale** per eseguire Esplora aree di grafico dati da un tenant di esempio. Le credenziali da utilizzare nella società demo non è necessario. In alternativa, è possibile fare clic su **Accedi** e accedere con le credenziali dell'account Azure Active Directory per eseguire Esplora aree di grafico per il tenant. Se si esegue il proprio tenant Explorer Graph, l'utente o amministratore sarà necessario consenso durante l'accesso. Se si ha un abbonamento a Office 365, è necessario automaticamente un tenant di Azure Active Directory. Le credenziali utilizzati per accedere a Office 365 sono, infatti, gli account Azure Active Directory, ed è possibile usare queste credenziali con Esplora risorse grafico.

**Eseguire una query**: per eseguire una query, digitare la query nella casella di testo richiesta e fare clic su **Leggi** o fare clic su **Immetti** codice. I risultati vengono visualizzati nella finestra di risposta. Ad esempio `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` verranno elencati tutti gli oggetti di gruppo nella directory demo.

Tenere presente le seguenti caratteristiche e limitazioni di Esplora aree di grafico:
- Funzionalità di completamento automatico in set di risorse. Per visualizzare questa operazione, fare clic su **Usa Demo società** e quindi fare clic sulla casella di testo richiesta (in cui appare l'URL della società). È possibile selezionare una risorsa impostati dall'elenco a discesa.

- Supporta "me" e "cui" indirizzamento alias. Ad esempio, è possibile utilizzare `https://graph.windows.net/me?api-version=1.6` per restituire l'oggetto utente dell'utente effettuato l'accesso o `https://graph.windows.net/myorganization/users?api-version=1.6` per restituire tutti gli utenti nella directory corrente. Si noti che utilizzando "me" alias viene restituito un errore per la società demo perché nessun utente è stato effettuato l'accesso in effettua la richiesta.

- Una sezione di intestazioni di risposta. Può essere utilizzato per la risoluzione dei problemi che si verificano durante l'esecuzione di query.

- Visualizzatore JSON per la risposta con funzionalità di espansione e compressione.

- Nessun supporto per la visualizzazione di una foto in miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Utilizzo Fiddler per la scrittura alla directory

Ai fini di questa Guida Guida introduttiva, è possibile utilizzare verrà Web Fiddler per mettere l'esecuzione di operazioni 'scrivere' contro si directory di Azure Active Directory. Per ulteriori informazioni e per installare Fiddler, vedere [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Nell'esempio seguente, si utilizzerà Fiddler Web Debugger per creare un nuovo gruppo di sicurezza 'MyTestGroup' nella directory di Azure Active Directory.

**Ottenere un token di accesso**: per accedere a grafico di Azure Active Directory, i client di eseguire l'autenticazione a Azure Active Directory prima di tutto. Per ulteriori informazioni, vedere [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md).

**Scrittura ed eseguire una query**: eseguire la procedura seguente.

1. Aprire Fiddler Web Debugger e passare alla scheda **compositore** .
2. Dato che si desidera creare un nuovo gruppo di sicurezza, selezionare **Post** come il metodo HTTP dal menu a discesa. Per informazioni sulle operazioni e le autorizzazioni per un oggetto di gruppo, vedere [gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) all'interno del [riferimento all'API REST di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Nel campo accanto al **Post**, digitare il seguente come l'URL della richiesta: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] È necessario sostituire mytenantdomain con il nome di dominio della directory di Azure Active Directory.

4. Nella casella sotto a discesa Post, digitare quanto segue:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Sostituisci i &lt;il token di accesso&gt; con il token di accesso per la directory di Azure Active Directory.

5. Nella casella **corpo richiesta** , digitare quanto segue:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Per ulteriori informazioni sulla creazione di gruppi, vedere [Crea gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Per ulteriori informazioni sulle entità di Azure Active Directory e i tipi di che vengono esposte dal grafico e informazioni sulle operazioni che è possibile eseguire su di essi con grafico, vedere [informazioni di riferimento API REST Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con l' [API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Altre informazioni sugli [ambiti di Azure Active Directory grafico API autorizzazione](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
