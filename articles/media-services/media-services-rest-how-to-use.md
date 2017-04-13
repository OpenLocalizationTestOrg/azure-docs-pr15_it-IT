<properties 
    pageTitle="Panoramica di API REST di servizi multimediali | Microsoft Azure" 
    description="Panoramica di API REST di servizi multimediali" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Panoramica di API REST di servizi multimediali 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Servizi multimediali di Microsoft Azure è un servizio che accetta le richieste HTTP basato su OData e potranno rispondere di nuovo in dettagliato JSON o atom + pub. Poiché servizi multimediali sono conformi alle linee guida di progettazione di Azure, è un insieme di intestazioni HTTP richieste utilizzate ogni client quando ci si connette ai servizi multimediali, come un insieme di intestazioni che può essere utilizzato. Le sezioni seguenti descrivono le intestazioni e verbi HTTP è possibile utilizzare quando la creazione di richieste e la ricezione di risposte da servizi di supporto.

##<a name="considerations"></a>Considerazioni 

Considerazioni quando si usa resto.

- Quando si eseguono query entità, non esiste un limite di 1000 entità restituite contemporaneamente perché pubblico resto v2 limita i risultati della query ai risultati di 1000. È necessario utilizzare **Salta** e **richiedere** (.NET) / **superiore** (REST) come descritto in [questo esempio viene .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [in questo esempio API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Quando si utilizzando JSON e specificando per utilizzare la parola chiave **__metadata** nella richiesta (ad esempio, per fa riferimento a un oggetto collegato) è necessario impostare l'intestazione **accetta** in [formato JSON dettagliato](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (vedere nell'esempio seguente). OData non riconosciute la proprietà **__metadata** nella richiesta, a meno che non è impostata su dettagliato.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>Intestazioni di richiesta HTTP standard supportate da servizi multimediali

Per ogni chiamata in servizi multimediali, esiste un insieme di intestazioni necessarie, che è necessario includere nella convocazione della e anche un insieme di intestazioni può essere necessario includere. Nella tabella seguente sono elencate le intestazioni necessarie:


Intestazione|Tipo|Valore
---|---|---
Autorizzazione|Titolare|Titolare è il meccanismo di autorizzazione accettato solo. Il valore deve includere anche il token di accesso fornito da ACS.
x-ms-versione|Decimale|2.11
DataServiceVersion|Decimale|3.0
MaxDataServiceVersion|Decimale|3.0



>[AZURE.NOTE] Poiché servizi multimediali di utilizza OData per esporre il relativo archivio dei metadati bene sottostante tramite API REST, le intestazioni DataServiceVersion e MaxDataServiceVersion dovrebbero essere incluso in qualsiasi richiesta; Tuttavia, se non si trovano, quindi attualmente servizi multimediali di si presuppone che il valore di DataServiceVersion in uso è 3.0.

Di seguito è un insieme di intestazioni opzionali:

Intestazione|Tipo|Valore
---|---|---
Data|Data RFC 1123|Timestamp della richiesta
Accettare|Tipo di contenuto|Il tipo di contenuto richiesto per la risposta, ad esempio le operazioni seguenti:<p> -applicazione/json; odata = dettagliato<p> -applicazione/atom + xml<p> Risposte abbia un tipo di contenuto diversi, ad esempio un'operazione di recupero blob nel punto in cui una risposta corretta conterrà flusso blob come payload.
Codifica accettare|Gzip, decompressione|GZIP e DEFLATE codifica, se applicabile. Nota: Per le risorse di grandi dimensioni, servizi multimediali possono ignorare questa intestazione e restituire i dati non compressa.
Accettare lingua|"en", "es" e così via.|Specifica la lingua preferita per la risposta.
Accettare-set di caratteri|Tipo di set di caratteri come "UTF-8"|Il valore predefinito è UTF-8.
Metodo di HTTP X|Metodo HTTP|Consente ai client o i firewall che non supportano metodi HTTP come inserire o eliminare i metodi tunnel tramite una chiamata GET.
Tipo di contenuto|Tipo di contenuto|Le richieste di tipo di contenuto del corpo della richiesta di inserire o POST.
id di richiesta client|Stringa|Valore definito chiamante che identifica la richiesta specificata. Se specificato, questo valore verrà incluso nel messaggio di risposta come un modo per mappare la richiesta. <p><p>**Importante**<p>Valori devono essere impostati 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>Intestazioni di risposta HTTP standard supportate da servizi multimediali

Di seguito è un insieme di intestazioni che può essere restituito all'utente in base alla risorsa che richiesta e l'azione che si desiderava eseguire.


Intestazione|Tipo|Valore
---|---|---
id richiesta|Stringa|Identificatore univoco per l'operazione corrente, servizio generata.
id di richiesta client|Stringa|Un identificatore specificato dal chiamante nella convocazione originale, se presente.
Data|Data RFC 1123|Data in cui è stata elaborata la richiesta.
Tipo di contenuto|Variabile|Tipo di contenuto del corpo della risposta.
Codifica di contenuto|Variabile|Gzip o decompressione, in base alle esigenze.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbi HTTP standard supportati da servizi multimediali

Di seguito è un elenco completo dei verbi HTTP che può essere utilizzato quando le richieste di esecuzione HTTP:


Verbi|Descrizione
---|---
Ottieni|Restituisce il valore corrente di un oggetto.
Inserisci|Crea un oggetto in base ai dati forniti o invia un comando.
INSERIRE|Sostituisce un oggetto o crea un oggetto (se applicabile).
ELIMINA|Elimina un oggetto.
STAMPA UNIONE|Aggiorna un oggetto esistente con le modifiche alle proprietà denominato.
TESTA|Restituisce i metadati di un oggetto di una risposta GET.

##<a name="limitation"></a>Limitazione

Quando si eseguono query entità, non esiste un limite di 1000 entità restituite contemporaneamente perché pubblico resto v2 limita i risultati della query ai risultati di 1000. È necessario utilizzare **Salta** e **richiedere** (.NET) / **superiore** (REST) come descritto in [questo esempio viene .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [in questo esempio API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Individuare il modello servizi multimediali

Per rendere più facilmente individuabili entità servizi multimediali, l'operazione $metadata può essere utilizzata. È possibile recuperare tutti i tipi di entità valido, le proprietà delle entità, associazioni, funzioni, azioni e così via. Nell'esempio seguente viene illustrato come costruire URI: https://media.windows.net/API/$ metadati.

È consigliabile aggiungere "? version=2.x api" alla fine dell'URI se si desidera visualizzare i metadati in un browser o intestazione x-ms-versione non includere nella convocazione.



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
