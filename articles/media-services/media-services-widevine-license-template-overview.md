<properties 
    pageTitle="Informazioni generali sui modelli di licenza Widevine | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica di un modello di licenza Widevine che consente di configurare Widevine licenze." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Informazioni generali sui modelli di licenza Widevine

##<a name="overview"></a>Panoramica

Servizi multimediali di Azure consente ora di configurare e richiedere Widevine licenze. Quando il lettore utente finale tenta di riprodurre il contenuto di Widevine protetto, viene inviata una richiesta al servizio di recapito licenza per ottenere una licenza. Se il servizio licenze approva la richiesta, problemi alla licenza che viene inviata al client e può essere utilizzata per decrittografare e riprodurre il contenuto specificato.

Richiesta di licenza Widevine viene formattato come messaggio JSON.  

Si noti che è possibile scegliere di creare un messaggio vuoto senza valori soltanto "{}" e verrà creato un modello di licenza con tutti i valori predefiniti.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Messaggio JSON

Nome | Valore | Descrizione
---|---|---
payload |Stringa con codificata base 64 |Richiesta di licenza inviata da un client. 
content_id | Stringa con codificata base 64|Identificatore ricavare KeyId(s) e chiavi del contenuto per ogni content_key_specs.track_type.
provider |stringa |Consente di cercare informazioni su criteri e chiavi del contenuto. Obbligatorio.
nome_criterio | stringa |Nome di un criterio registrato in precedenza. Facoltativo
allowed_track_types | enumerazione  | SD_ONLY o SD_HD. Controlli contenuto chiavi dovrebbero essere incluso in una licenza
content_key_specs | Matrice di JSON strutture, vedere **Contenuto specifiche di chiave** | Un maggiore controllo granulare in quali contenuti tasti di direzione per restituire. Per informazioni dettagliate, vedere contenuto specifiche chiave sotto.  È possibile specificare una sola allowed_track_types e content_key_specs. 
use_policy_overrides_exclusively | valore booleano. true o false | Utilizzare attributi di criteri specificati da policy_overrides e omettere tutti i criteri memorizzati in precedenza.
policy_overrides | JSON struttura, vedere **Criterio sostituisce** seguente | Impostazioni di criteri per questa licenza.  Nel caso in cui la risorsa dispone di un criterio predefinito, verranno usati tali valori specificati. 
session_init | JSON struttura, vedere **Inizializzazione della sessione** | Dati facoltativi passato alla licenza.
parse_only | valore booleano. true o false | Viene analizzata la richiesta di licenza, ma nessuna licenza viene rilasciata. Tuttavia, i valori del modulo la richiesta di licenza vengono restituiti nella risposta.  

##<a name="content-key-specs"></a>Specifiche di chiave del contenuto 

Se esiste un criterio esistente, è necessario specificare uno o più valori nella specifica di chiave di contenuto.  Criteri preesistenti associato a questo contenuto verranno utilizzato per determinare la protezione di output, ad esempio HDCP e CGMS.  Se un criterio esistente non è registrato con il Server licenze Widevine, il provider di contenuti può inserire i valori nella richiesta di licenza.   


Ogni content_key_specs deve essere specificato per tutte le tracce, indipendentemente dal use_policy_overrides_exclusively opzione. 


Nome | Valore | Descrizione
---|---|---
content_key_specs. track_type | stringa | Nome del tipo di traccia. Se content_key_specs viene specificato nella richiesta di licenza, assicurarsi di specificare che tutti tenere traccia dei tipi in modo esplicito. In caso contrario, verrà generato un errore per la riproduzione ultimi 10 secondi. 
content_key_specs  <br/> security_level | UInt32 | Definisce i requisiti di affidabilità client per la riproduzione. <br/> 1 - basate sul software whitebox crypto è necessario. <br/> 2 - è necessario crypto software e un decodificatore offuscato. <br/> 3 - il materiale della chiave di crittografia operazioni da eseguire in un ambiente di backup esecuzione trusted hardware. <br/> 4 - la crittografia e decodifica dei contenuti deve essere eseguite in un ambiente di backup esecuzione trusted hardware.  <br/> 5 - la crittografia, decompressione e tutti gestione degli elementi multimediali (con e senza compressione) devono essere gestiti in un ambiente di backup esecuzione trusted hardware.  
content_key_specs <br/> required_output_protection.HDC | stringa - uno dei: HDCP_NONE, HDCP_V1, HDCP_V2 | Indica se è richiedono HDCP
content_key_specs <br/>chiave | Base 64 <br/>stringa codificata|Contenuto chiave da utilizzare per la traccia. Se specificato, è necessario il track_type o key_id.  Questa opzione consente il provider di contenuti inserire la chiave del contenuto per la traccia anziché consentire server licenze Widevine generare o una chiave di ricerca.
content_key_specs.key_ID| Binario, stringa con codifica base 64 16 byte | Identificatore univoco per la chiave. 


##<a name="policy-overrides"></a>Override di criteri 

Nome | Valore | Descrizione
---|---|---
policy_overrides. can_play | valore booleano. true o false | Indica che la riproduzione del contenuto è consentita. Il valore predefinito è falsa.
policy_overrides. can_persist | valore booleano. true o false |Indica che la licenza può essere mantenuta per lo spazio di archiviazione non volatili per l'utilizzo offline. Il valore predefinito è falsa.
policy_overrides. can_renew | booleano true o false |Indica che il rinnovo della licenza è consentito. Se true, è possibile estendere la durata della licenza mediante heartbeat. Il valore predefinito è falsa. 
policy_overrides. license_duration_seconds | Int64 | Indica l'intervallo di tempo per questa licenza specifica. Il valore 0 indica che non esiste alcun limite per la durata. Il valore predefinito è 0. 
policy_overrides. rental_duration_seconds | Int64 | Indica il periodo di tempo durante la riproduzione sia consentita. Il valore 0 indica che non esiste alcun limite per la durata. Il valore predefinito è 0. 
policy_overrides. playback_duration_seconds | Int64 | Finestra di visualizzazione di tempo dopo la riproduzione inizia all'interno della durata di licenza. Il valore 0 indica che non esiste alcun limite per la durata. Il valore predefinito è 0. 
policy_overrides. renewal_server_url |stringa | Tutte le richieste di heartbeat (con rinnovo) per questa licenza devono essere diretto all'URL specificato. In questo campo viene utilizzato solo se can_renew è vera.
policy_overrides. renewal_delay_seconds |Int64 |Il numero di secondi dopo license_start_time, prima che il rinnovo è il primo tentativo. In questo campo viene utilizzato solo se can_renew è vera. Il valore predefinito è 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Specifica il ritardo in secondi tra le richieste di rinnovo licenza successive, in caso di errore. In questo campo viene utilizzato solo se can_renew è vera. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | Il periodo di tempo, in cui la riproduzione può continuare mentre il rinnovo è tentativo, ma non riuscita a causa di problemi di back-end con il server licenze. Il valore 0 indica che non esiste alcun limite per la durata. In questo campo viene utilizzato solo se can_renew è vera.
policy_overrides. renew_with_usage | booleano true o false |Indica che la licenza essere inviata per il rinnovo quando viene avviato l'uso. In questo campo viene utilizzato solo se can_renew è vera. 

##<a name="session-initialization"></a>Inizializzazione della sessione

Nome | Valore | Descrizione
---|---|---
provider_session_token | Stringa con codificata base 64 |Questo token di sessione passato in alla licenza e saranno disponibili in aggiornamenti successivi.  Il token di sessione non vengono mantenute oltre sessioni. 
provider_client_token | Stringa con codificata base 64 | Token di client per inviare nuovamente la risposta di licenza.  Se la richiesta di licenza contiene un token di client, questo valore viene ignorato. Il token del client da mantenerla sessioni di licenza.
override_provider_client_token | valore booleano. true o false |Se false e la richiesta di licenza contiene un token di client, utilizzare il token dalla richiesta anche se è stato specificato un token client nella struttura.  Se true, utilizzare sempre il token specificato nella struttura.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurare le licenze Widevine utilizzo dei tipi di .NET

Servizi multimediali di fornisce API .NET che consentono di configurare le licenze Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classi come definito nella Media Services .NET SDK

Di seguito sono le definizioni di questi tipi.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come utilizzare le API di .NET per configurare una licenza Widevine semplice.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vedere anche

[Usa la crittografia comune PlayReady e/o Widevine dinamiche](media-services-protect-with-drm.md)
