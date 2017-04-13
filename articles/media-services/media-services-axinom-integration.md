<properties 
    pageTitle="Utilizzare Axinom per fornire le licenze Widevine su servizi multimediali di Windows Azure | Microsoft Azure" 
    description="Questo articolo descrive come è possibile utilizzare servizi multimediali di Azure (AMS) per eseguire un flusso di dinamicamente crittografato tramite AMS con PlayReady e Widevine DRMs. La licenza PlayReady proviene dal server licenze PlayReady di servizi di supporto ed Widevine licenza viene recapitato da server licenze Axinom." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizzare Axinom per fornire le licenze Widevine su servizi multimediali di Windows Azure  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Panoramica

Servizi di supporto Azure (AMS) ha aggiunto protezione dinamica Google Widevine (per informazioni dettagliate, vedere [blog del Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) ). Inoltre, Azure Media Player (AMP) ha anche aggiunto supporto Widevine (per informazioni dettagliate, vedere [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) ). Verrà visualizzata un'ottenuto principale in streaming del trattino contenuto protetto da CENC con multi-native-DRM (PlayReady e Widevine) nel browser moderni dotato di MSE ed EME.

A partire da Media Services .NET SDK versione 3.5.2, servizi multimediali consente di configurare il modello di licenza Widevine e ottenere Widevine licenze. È inoltre possibile utilizzare i seguenti partner AMS per offrire licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

In questo articolo viene descritto come integrare e testare server licenze Widevine gestito da Axinom. In particolare, coprire:  

- Configurazione di crittografia comuni dinamiche con multi-DRM (PlayReady e Widevine) con URL di acquisizione licenza corrispondenti;
- Generando un token JWT per soddisfare i requisiti di server licenze;
- Sviluppo di app Azure Media Player che gestisce l'acquisizione di licenza con l'autenticazione token JWT;

Il sistema completo e il flusso di contenuto che chiave, chiave ID chiave valore iniziale, token JTW e il relativo domande possono essere meglio descritti nel diagramma seguente.

![TRATTINO e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Protezione dei contenuti

Configurare protezione dinamica e criteri di recapito chiave, vedere il blog di Mingfei: [come configurare imballaggio Widevine con servizi multimediali di Windows Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

È possibile configurare protezione CENC dinamica con multi-DRM per trattino streaming verificano entrambe le operazioni seguenti:

1. Protezione PlayReady per MS Edge e IE11, che potrebbe avere un restrizioni token di autorizzazione. Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza), ad esempio Azure Active Directory.
1. Protezione Widevine per Chrome, può richiedere l'autenticazione token con token rilasciato da un altro servizio token di sicurezza. 

Vedere [Generazione Token JWT](media-services-axinom-integration.md#jwt-token-generation) sezione per motivi per cui non è possibile utilizzare come un servizio token di sicurezza Azure Active Directory per server licenze Widevine del Axinom.

###<a name="considerations"></a>Considerazioni

1. È necessario utilizzare Axinom specificato chiave valore iniziale (8888000000000000000000000000000000000000) e il generato o selezionato ID per generare la chiave del contenuto per la configurazione del servizio di recapito della chiave di chiave. Server licenze Axinom visualizzerà tutte le licenze contenente chiavi contenute in base a valore iniziale stessa chiave, valido per la verifica e produzione.
1. L'URL di acquisizione Widevine licenza per il testing: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS sono consentiti.

##<a name="azure-media-player-preparation"></a>Preparazione del lettore multimediale Azure

V1.4.0 AMP supporta la riproduzione di contenuti AMS dinamicamente fornito con PlayReady e Widevine DRM.
Se il server di licenze Widevine non richiede l'autenticazione token, non è aggiuntive che è necessario eseguire per provare un trattino di contenuto protetto da Widevine. Per un esempio, il team AMP rappresenta un semplice [esempio](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), in cui è possibile visualizzare il funzionamento di bordo e IE11 con PlayReady e Chrome con Widevine.
Il server di licenza Widevine fornito da Axinom richiede l'autenticazione token JWT. Token JWT deve essere inviato con la richiesta di licenza tramite un'intestazione HTTP "X-AxDRM-messaggio". A tale scopo, è necessario aggiungere il seguente codice javascript nella pagina web hosting AMP prima di impostare l'origine:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Il resto del codice AMP è API AMP standard come documento AMP [qui](http://amp.azure.net/libs/amp/latest/docs/).

Si noti che il codice javascript sopra per intestazione di autorizzazione personalizzato impostazione ancora un approccio a breve termine prima ufficiale approccio a lungo termine amp viene rilasciato.

##<a name="jwt-token-generation"></a>Generazione di Token JWT

Axinom Widevine server licenze per il testing richiede l'autenticazione token JWT. Inoltre, uno dei reclami token JWT è di un tipo di oggetto complesso invece di tipo di dati di base.

Purtroppo Azure Active Directory può solo emettere token JWT con tipi di base. Analogamente, API di .NET Framework (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) solo consente di immettere il tipo di oggetto complesso come reclami. Tuttavia, le attestazioni sono ancora serializzate come stringa. Pertanto è possibile usare uno dei due per generare il token JWT per la richiesta di licenza Widevine.


Di John Sheehan [pacchetto Nuget JWT](https://www.nuget.org/packages/JWT) soddisfa le esigenze in modo che verranno utilizzare questo pacchetto Nuget.

Di seguito è il codice per la generazione token JWT con crediti necessari come richiesto dal server licenze Axinom Widevine per il testing:

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Server licenze Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Considerazioni

1.  Anche se è necessario il servizio di recapito licenza PlayReady AMS "titolare =" che precede un token di autenticazione, server licenze Axinom Widevine non usarlo.
2.  Il tasto di comunicazione Axinom viene utilizzato come chiave di firma. Si noti che la chiave è una stringa esadecimale, tuttavia deve essere considerato come una serie di byte non è una stringa durante la codifica. Questa operazione viene eseguita tramite il metodo ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Recupero di ID chiave

Si sarà notato che il codice per la generazione di un JWT ID token, chiave è obbligatorio. Poiché le esigenze di token JWT sia pronto prima di caricamento AMP player, chiave ID deve essere recuperati per generare token JWT.

Naturalmente esistono diversi modi per ottenere contenere della chiave ID. Ad esempio uno potrebbe archiviare ID chiave insieme metadati contenuto in un database. In alternativa è possibile recuperare chiave ID dal file MPD trattino (descrizione della presentazione Media). Il codice riportato di seguito è per quest'ultima.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Riepilogo

Con più recente aggiunta di supporto Widevine la protezione dei contenuti di servizi multimediali Azure e Azure Media Player, siamo in grado di implementare streaming del trattino + Multi-native-DRM (PlayReady + Widevine) con il servizio di entrambi licenza PlayReady in AMS e Widevine server licenze di Axinom per i browser moderni seguenti:

- Chrome
- Bordo di Microsoft in Windows 10
- Internet Explorer 11 in Windows 8.1 e Windows 10
- Firefox (Desktop) e Safari su Mac (non iOS) sono supportati anche tramite Silverlight e lo stesso URL con Azure Media Player

I parametri seguenti sono necessarie nel server licenze di Axinom Widevine utilizzo soluzione minima. Ad eccezione di chiave ID, il resto dei parametri sono forniti da Axinom in base all'impostazione delle server Widevine.


Parametro|Modalità di utilizzo
---|---
ID chiave di comunicazione|Devono essere inclusi come valore della richiesta di rimborso "com_key_id" in token JWT (vedere [la](media-services-axinom-integration.md#jwt-token-generation) sezione).
Chiave di comunicazione|Deve essere utilizzato come chiave di firma di token JWT (vedere [la](media-services-axinom-integration.md#jwt-token-generation) sezione).
Valore iniziale chiave|Deve essere utilizzato per generare la chiave del contenuto con tutto il contenuto assegnato ID chiave (vedere [la](media-services-axinom-integration.md#content-protection) sezione).
URL di acquisizione di licenza Widevine|Deve essere utilizzato per la configurazione dei criteri di recapito bene per trattino streaming (vedere [la](media-services-axinom-integration.md#content-protection) sezione).
ID chiave contenuto|Devono essere inclusi come parte del valore della richiesta di messaggio il diritto di token JWT (vedere [la](media-services-axinom-integration.md#jwt-token-generation) sezione). 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Riconoscimenti 

Vogliamo confermare seguendo altre persone che hanno contribuito per creare il documento: Kristjan Jõgi di Axinom, Mingfei Yan e Amit Rajput.
