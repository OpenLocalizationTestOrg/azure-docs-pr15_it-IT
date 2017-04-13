<properties 
    pageTitle="CENC con Multi-DRM e controllo degli accessi: un progetto di riferimento e implementazione su Azure e Azure servizi multimediali di | Microsoft Azure" 
    description="Informazioni sulle modalità di gestione delle licenze Microsoft® smussate Streaming Client portabilità Kit." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC con Multi-DRM e controllo degli accessi: un progetto di riferimento e implementazione su Azure e Azure servizi multimediali

##<a name="key-words"></a>Parole chiave
 
Azure Active Directory, servizi multimediali di Windows Azure, Azure Media Player, crittografia dinamiche, licenza consegna, PlayReady, Widevine, FairPlay, Encryption(CENC) comuni, Multi-DRM, Axinom, trattino, EME, MSE, JSON Web Token (JWT), sulle attestazioni, browser moderni, attivazione chiave, chiave simmetrico, asimmetrici, una chiave, OpenID connettersi, X509 certificato. 

##<a name="in-this-article"></a>In questo articolo

In questo articolo vengono trattati i seguenti argomenti:

- [Introduzione](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Panoramica di questo articolo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Una struttura di riferimento](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Mappatura di progettazione alla tecnologia di implementazione](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementazione](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procedure di implementazione](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Alcuni trucchi di implementazione](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Argomenti aggiuntivi per l'implementazione](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP o HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory attivazione chiave di firma](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Dove si trova il Token di accesso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [Informazioni sul Live Streaming?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [I server licenze all'esterno di servizi multimediali di Windows Azure?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [Cosa fare se si desidera utilizzare un servizio token di sicurezza personalizzati?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Il sistema completato e test](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Accesso utente](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Utilizzando le estensioni multimediali crittografato per PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Utilizzo di EME per Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Utenti non autorizzati](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Esecuzione di servizio Token di protezione personalizzato](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Riepilogo](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introduzione

È noto che si tratti di un'attività complessa per progettare e creare un sottosistema DRM per un OTT o online soluzione di flusso. E è una pratica comune per gli operatori/video provider affidare parte da provider di servizi speciali DRM. L'obiettivo di questo documento è per presentare un progetto di riferimento e l'implementazione di sottosistema DRM-to-end OTT o una soluzione di flusso in linea.

Lettori di destinazione di questo documento sono ingegneri nel sottosistema DRM di OTT trasmissione/multi-screen soluzioni online o qualsiasi lettori interessati nel sottosistema DRM. Il presupposto è che i lettori abbiano familiari con almeno una delle tecnologie DRM sul mercato, ad esempio PlayReady, Widevine, FairPlay o Adobe Access.

Da DRM, è inoltre includere CENC (crittografia comuni) con multi-DRM. Una linea di tendenza principale in streaming online e settore OTT consiste nell'utilizzare CENC con multi-native-DRM piattaforme client vari MAIUSC dalla tendenza precedente dell'utilizzo di un singolo DRM e il relativo client SDK per diverse piattaforme client. Quando si utilizza CENC con più native DRM, PlayReady e Widevine vengono crittografati per la specifica [Della crittografia comuni (CENC ISO/IEC 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Vantaggi della CENC con multi-DRM sono i seguenti:

1. Riduce la crittografia dei costi in quanto l'elaborazione un singolo crittografia viene utilizzata l'assegnazione del piattaforme diverse con il relativo DRMs nativo;
1. Ridurre i costi di gestione delle risorse crittografati necessaria una sola copia dei beni crittografati;
1. Elimina licenze costo poiché il client DRM nativo è in genere gratuito per la piattaforma nativa client DRM.

Microsoft è stato un promotore attivi del trattino e CENC insieme ad alcuni principali del settore. Servizi multimediali di Microsoft Azure fornisce supporto di TRATTEGGIO e CENC. Per gli annunci recenti, vedere il blog di Mingfei: [servizi di recapito annuncio Google Widevine licenza in servizi multimediali di Windows Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)e [Servizi multimediali di Azure aggiunge imballaggio Widevine Google per l'esecuzione di flusso con più DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Panoramica di questo articolo

L'obiettivo di questo articolo include le operazioni seguenti:

1. Fornisce una struttura di riferimento del sottosistema DRM con CENC multi-DRM;
1. Fornisce un'implementazione di riferimento sulla piattaforma servizi multimediali di Microsoft Azure/Azure;
1. Vengono illustrati alcuni argomenti di progettazione e implementazione.

Vedere l'articolo "multi-DRM" illustra quanto segue:

1. Microsoft PlayReady
1. Widevine di Google
1. Apple FairPlay (non ancora supportato da servizi multimediali di Windows Azure)

La tabella seguente riepiloga la piattaforma/nativo nativo app e browser supportati da ogni DRM.

**Piattaforma client**|**Supporto DRM nativo**|**Browser/App**|**Formati di flusso**
----|------|----|----
**Smart TV, operatore box, OTT box**|PlayReady principalmente, e/o Widevine e/o altro|Linux Opera, WebKit, altri|Diversi formati
**Dispositivi Windows 10 (PC Windows, Tablet Windows, Windows Phone, Xbox)**|PlayReady|MS bordo/IE11/EME<br/><br/><br/>UWP|TRATTINO (per HLS, PlayReady non è supportato)<br/><br/>TRATTINO, Smooth Streaming (per HLS, PlayReady non è supportato) 
**Dispositivi Android (telefono, Tablet TV)**|Widevine|Chrome/EME|TRATTINO
**iOS (iPhone, iPad), OS X client e programmi TV Apple**|FairPlay|Safari 8 + / EME|HLS
**Plug-in: Questo punto Adobe**|Accesso a questo punto|Plug-in browser|UNITÀ 10K, HLS

Considerando lo stato corrente di distribuzione per ogni DRM, un servizio in genere preferibile implementare DRMs 2 o 3 per assicurarsi che l'indirizzo di tutti i tipi di endpoint nel modo migliore.

Esiste un compromesso tra la complessità della logica di servizio e la complessità sul lato client per raggiungere un livello dell'esperienza utente nei client diversi.

Per rendere la selezione, prendere in considerazione gli aspetti:

- PlayReady a livello nativo è implementata in tutti i dispositivi Windows, in alcuni dispositivi Android e sono disponibili nei SDK software su praticamente qualsiasi piattaforma
- Widevine a livello nativo è implementata in tutti i dispositivi Android, Chrome e alcuni altri dispositivi
- FairPlay è disponibile solo nel client di Mac OS e iOS o tramite iTunes.

In modo che un tipico multi-DRM dovrebbero essere:

- Opzione 1: PlayReady e Widevine
- Opzione 2: PlayReady, Widevine e FairPlay


## <a name="a-reference-design"></a>Una struttura di riferimento

In questa sezione verranno presentati una struttura di riferimento che è indipendente da tecnologie utilizzate per applicarla.

Un sottosistema DRM può contenere i componenti seguenti:

1. Gestione delle chiavi
1. Salvataggio di un pacchetto DRM
1. Recapito licenza DRM
1. Controllo diritto
1. Autenticazione/autorizzazione
1. Windows Media Player
1. Origine/CDN

Nel diagramma seguente illustra l'interazione di alto livello tra i componenti di un sottosistema DRM.

![Sottosistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Sono disponibili tre base "livelli" nella struttura della:

1. Eseguire il livello di office (in nero) che non vengono esposte esternamente.
1. Livello di "Rete Perimetrale" (blu) contenente tutti i punti finali affiancate pubblici.
1. Pubblica Internet livello (azzurro) contenente CDN e lettori con il traffico attraverso Internet pubblico.
 
Dovrebbe essere presente uno strumento di gestione del contenuto per il controllo di protezione DRM, indipendentemente dal fatto che la crittografia statica o dinamica. Gli input per la crittografia DRM devono includere:

1. Contenuto video record di avvio principale.
1. Contenuto chiave;
1. URL di acquisizione di licenza.

In fase di riproduzione, il flusso di alto livello è:

1. Utente viene autenticato;
1. Token di autorizzazione viene creato per l'utente.
1. Contenuto protetto DRM (manifesto) viene scaricato in player.
1. Windows Media Player invia richiesta di acquisizione di licenza al server licenze insieme alla chiave ID e autorizzazione token.

Prima di passare all'argomento successivo, alcune parole relative alla struttura di gestione delle chiavi.

**ContentKey-capitale**|**Scenario:**
------|---------------------------
1-1|Maiuscole/minuscole più semplice. Fornisce un controllo avanzato. Ma il risultato in genere è il costo di recapito licenza più alto. In una licenza minima è necessaria per ogni risorsa protetta richiesta.
1-a-molti|È possibile utilizzare la stessa chiave contenuto di più risorse. Per tutte le risorse in un gruppo logico, ad esempio un genere o un sottoinsieme di perimetro (o la filmato), ad esempio, è possibile utilizzare una singola chiave contenuta.
Molti-a-1|Per ogni risorsa sono necessari più tasti contenuti. <br/><br/>Ad esempio, se è necessario applicare la protezione CENC dinamica con multi-DRM per MPEG-tratto e la crittografia AES a 128 dinamica per HLS, è necessario due chiavi di contenuto separate, ognuna con il proprio ContentKeyType. (Per la chiave del contenuto utilizzata per la protezione CENC dinamica, ContentKeyType.CommonEncryption deve essere utilizzato per la chiave del contenuto utilizzata per la crittografia AES a 128 dinamica, ContentKeyType.EnvelopeEncryption deve essere utilizzato..)<br/><br/>Un altro esempio, in protezione CENC contenuto trattino in teoria, una chiave del contenuto può essere utilizzato per proteggere flusso video e un altro contenuto chiave per la protezione flusso audio. 
Molti-a - molti|Combinazione di due scenari indicati: un insieme di chiavi vengono utilizzati per ognuno dei beni più nella stessa bene "gruppo".


Un altro fattore importante da considerare è l'utilizzo di licenze permanenti e non permanente.

Perché sono queste considerazioni importanti? 

Devono avere impatto diretto al costo di recapito licenza se si usa cloud pubblico per il recapito della licenza. Sono illustrati due vari casi seguenti per illustrare:



1. Abbonamento mensile: usare licenza permanente e mapping chiave a bene contenuto 1-a-molti. Ad esempio per tutti i filmati bambini, serve una singola chiave contenuta per la crittografia. In questo caso: 

    Totale licenze # richieste per tutti bambini filmati/dispositivo = 1

1. Abbonamento mensile: utilizzare la licenza non permanente e mapping 1-1 tra chiave del contenuto e risorse. In questo caso:

    Totale licenze # richieste per tutti bambini filmati/dispositivo = [filmati # controllate] x [sessioni #]

Come è possibile vedere facilmente, due diversi modelli come risultato nei modelli di richiesta di licenza molto diversa pertanto recapito licenza costo se il servizio di recapito licenza viene fornito da un'area pubblica, ad esempio servizi multimediali di Windows Azure.

## <a name="mapping-design-to-technology-for-implementation"></a>Mappatura di progettazione alla tecnologia di implementazione

Successivamente, vengono mappate il progetto generico alle tecnologie su piattaforma servizi multimediali di Microsoft Azure/Azure, specificando la tecnologia da utilizzare per ogni blocco predefinito.

Nella tabella seguente viene illustrato il mapping:

**Blocchi predefiniti**|**Tecnologia**
------|-------
**Windows Media Player**|[Lettore multimediale Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Provider di identità (IDP)**|Azure Active Directory
**Proteggere servizio Token)**|Azure Active Directory
**Flusso di lavoro protezione DRM**|Protezione dinamica dei servizi Media Azure
**Recapito licenza DRM**|1. azure Media Services recapito licenza (PlayReady, Widevine, FairPlay) o <br/>2. Server licenze di Axinom, <br/>3. Server licenze PlayReady personalizzato
**Origine**|Endpoint trasmissione dei servizi Media Azure
**Gestione delle chiavi**|Non è necessaria per l'implementazione di riferimento
**Gestione dei contenuti**|Un'applicazione console c#

In altre parole, il Provider di identità (IDP) e sicura servizio token di saranno Azure Active Directory. Per Windows Media player, verrà utilizzato [API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Servizi multimediali di Windows Azure e Azure Media Player supporta trattino e CENC con multi-DRM.

Il diagramma seguente illustra la struttura complessiva e flusso con il mapping di tecnologia precedente.

![CENC su AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Per configurare la crittografia CENC dinamiche, lo strumento di gestione dei contenuti utilizzerà input seguenti:

1. Aprire contenuto;
1. Chiave contenuto dalla chiave generazione/gestione;
1. URL di acquisizione di licenza;
1. Elenco di informazioni da Azure Active Directory.

L'output dello strumento di gestione dei contenuti sarà:

1. ContentKeyAuthorizationPolicy che contiene la specifica in come recapito licenza verifica token JWT e specifiche licenze DRM;
1. AssetDeliveryPolicy contenente specifiche in streaming format, protezione DRM e URL di acquisizione di licenza.

In fase di esecuzione è il flusso di seguito:

1. Dopo l'autenticazione utente, viene generato un token JWT;
1. Uno dei reclami contenuti nel token JWT è Richiedi "gruppi" che contiene l'ID di oggetto gruppo di "EntitledUserGroup". La richiesta verrà utilizzata per il passaggio "controllo diritto".
1. Windows Media Player download client manifesto di un CENC contenuto protetto e "" nel modo seguente:
    1. ID, chiave 
    1. il contenuto è protetto, CENC
    1. URL di acquisizione di licenza.

1. Windows Media Player effettua una richiesta di acquisizione di licenza basata su browser/DRM supportati. Nella convocazione di acquisizione di licenza, ID chiave e verrà inviato anche il token JWT. Il servizio di recapito licenza verrà verificata token JWT e sulle attestazioni contenute prima di rilasciare licenze necessarie.

##<a name="implementation"></a>Implementazione

###<a name="implementation-procedures"></a>Procedure di implementazione

L'implementazione conterrà la procedura seguente:

1. Preparare l'ambiente di test applicati: codificare/pacchetto di un video di test di velocità multi frammentato MP4 in servizi multimediali di Windows Azure. Questa risorsa non è protetto tramite DRM. Protezione DRM verrà eseguita da protezione dinamica in un secondo momento.
1. Creare chiave ID e il contenuto chiave (facoltativamente da chiave partenza). Per il nostro obiettivo sistema di gestione delle chiavi non è necessaria in quanto è in corso sulla gestione con un'unica serie di chiave ID e la chiave del contenuto per un paio di risorse del test;
1. Utilizzare AMS API per configurare i servizi di recapito DRM con più licenze per le risorse di test. Se si Usa server licenze personalizzato dall'azienda o fornitori della società invece di servizi di licenza in servizi multimediali di Windows Azure, è possibile ignorare questo passaggio e specificare gli URL di acquisizione di licenza nel passaggio per la configurazione di recapito della licenza. API AMS è necessario specificare che alcune dettagliate configurazioni, ad esempio limitazione ai criteri di autorizzazione, i modelli di risposta di licenze per diversi servizi di licenza DRM e così via. Al momento, il portale di Azure non ancora fornisce l'interfaccia utente necessaria per la configurazione. È possibile trovare informazioni a livello di API e codice in un documento di Julia Kornich di esempio: [utilizzando PlayReady e/o Widevine dinamico crittografia comuni](media-services-protect-with-drm.md). 
1. Utilizzare AMS API per configurare i criteri di recapito risorse per le risorse di test. È possibile trovare informazioni a livello di API e codice in un documento di Julia Kornich di esempio: [utilizzando PlayReady e/o Widevine dinamico crittografia comuni](media-services-protect-with-drm.md).
1. Creare e configurare un tenant di Azure Active Directory in Azure;
1. Creare alcuni account utente e gruppi nel tenant di Azure Active Directory: è necessario creare almeno "EntitledUser" raggruppare e aggiungere un utente al gruppo. Gli utenti in questo gruppo passerà controllo il diritto di acquisizione di licenza e gli utenti non in questo gruppo avrà esito negativo passare controllo di autenticazione non saranno in grado di acquisire qualsiasi licenza. Essere membro del gruppo "EntitledUser" è una domanda "gruppi" richiesti in token JWT rilasciato da Azure Active Directory. Questo requisito Richiedi deve essere specificato nella configurazione multi-DRM licenza recapito servizi-passaggio.
1. Creare un'app MVC ASP.NET che dovranno essere ospitati lettore video. Questa app ASP.NET sarà protetti con l'autenticazione utente in base al tenant di Azure Active Directory. Corretta delle attestazioni includerà i token di accesso ottenuti dopo l'autenticazione utente. API di connettersi OpenID è consigliato per questo passaggio. È necessario installare i pacchetti NuGet seguenti:
    - Pacchetto di installazione Microsoft.Azure.ActiveDirectory.GraphClient
    - Pacchetto di installazione Microsoft.Owin.Security.OpenIdConnect
    - Pacchetto di installazione Microsoft.Owin.Security.Cookies
    - Pacchetto di installazione Microsoft.Owin.Host.SystemWeb
    - Pacchetto di installazione Microsoft.IdentityModel.Clients.ActiveDirectory
1. Creare un lettore con [API di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [API ProtectionInfo del lettore multimediale Azure](http://amp.azure.net/libs/amp/latest/docs/) consente di specificare la tecnologia DRM da utilizzare in piattaforma DRM diversa.
1. Matrice di test:

**DRM**|**Browser**|**Risultato per l'utente ha diritto**|**Risultato per l'utente non ha diritto**
---|---|-----|---------
**PlayReady**|Bordo MS o IE11 in Windows 10|Esito positivo|Esito negativo
**Widevine**|Chrome in Windows 10|Esito positivo|Esito negativo
**FairPlay** |TBD||

George Trifonov del Team di servizi multimediali di Azure è scritto un blog che fornisce istruzioni dettagliate per l'impostazione di Azure Active Directory per un'app di Windows Media player MVC ASP.NET: [MVC OWIN servizi di integrazione Azure multimediali in base a app con Azure Active Directory e limitare la distribuzione di contenuti chiave in base a reclami JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Giorgio scritto un blog [sull'autenticazione token JWT servizi multimediali di Windows Azure e della crittografia dinamico](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Ed ecco il suo [esempio sull'integrazione di Azure Active Directory con recapito chiave servizi multimediali di Windows Azure](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Per informazioni su Azure Active Directory:

- È possibile trovare informazioni per gli sviluppatori nella [Guida per sviluppatori di Azure Active Directory](../active-directory/active-directory-developers-guide.md).
- Sono disponibili informazioni per gli amministratori [Amministrare Your Azure Active Directory](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Alcuni trucchi di implementazione

Esistono alcune "trucchi" nell'implementazione. Probabilmente l'elenco di "trucchi" seguente consente di risoluzione dei problemi nel caso in cui si verificano problemi.

1. **Emittente** URL deve terminare con **"/"**.  

    **Gruppo di destinatari** dovrebbe essere l'ID di Windows Media player applicazione client e che sia necessario aggiungere anche **"/"** alla fine dell'URL emittente.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    Nel [JWT decodificatore](http://jwt.calebb.net/), verrà visualizzato **aud** e **iss** come sotto token JWT:
    
    ![1 ° problema](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Aggiungi autorizzazioni all'applicazione di AAD (nella scheda Configurazione dell'applicazione). Questa operazione è necessaria per ogni applicazione (versioni locale e distribuite).

    ![problema 2a](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Utilizzare destra autorità nell'impostazione di protezione CENC dinamica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    Di seguito non funziona:
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    Il GUID è l'ID di tenant AAD. GUID sono disponibili nel popup endpoint nel portale di Azure.

4. Concedere l'appartenenza richiede privilegi. Verificare che nel file manifesto AAD, è necessario le operazioni seguenti

    "groupMembershipClaims": "Tutti", (il valore predefinito è null)

5. Impostazione corretta TokenType in durante la creazione dei requisiti di restrizione.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Dopo l'aggiunta del supporto di JWT (AAD) oltre a SWT (ACS), il valore predefinito TokenType in è TokenType.JWT. Se si usa SWT/ACS, è necessario impostare a TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Argomenti aggiuntivi per l'implementazione
È quindi verranno disco uss alcuni argomenti aggiuntivi nella progettazione e di implementazione.

###<a name="http-or-https"></a>HTTP o HTTPS?

L'applicazione di Windows Media player Asp.net che è stato creato deve supportare le operazioni seguenti:

1. Autenticazione degli utenti tramite Azure Active Directory che deve essere in HTTPS.
1. Scambio di token JWT tra client e Azure Active Directory che deve essere in HTTPS.
1. Acquisizione di licenza DRM dal client che deve essere in HTTPS se recapito licenza viene fornito da servizi multimediali di Windows Azure. Naturalmente, famiglia di prodotti PlayReady non sarà più HTTPS per il recapito della licenza. Se il server licenze PlayReady non rientra servizi multimediali di Windows Azure, potrebbero essere utilizzate HTTP o HTTPS.

Di conseguenza, l'applicazione di Windows Media player ASP.NET utilizzerà HTTPS è buona norma. Di conseguenza, che il lettore multimediale Azure verrà visualizzato in una pagina in HTTPS. Tuttavia, per lo streaming si preferisce HTTP, pertanto è necessario prendere in considerazione problema contenuto misto.

1. Contenuto misto non consentito browser. Consentendo plug-in come plug-in Silverlight e OSMF per un passaggio uniforme e trattino. Contenuto misto è un problema di sicurezza - ciò è dovuto il rischio della possibilità di inserire JS dannoso che possono causare i dati dei clienti a rischio.  Browser questo bloccano per impostazione predefinita e l'unico modo per risolverli è sul lato server (origine), per consentire a tutti i domini (indipendentemente dal fatto che https o http). Questo è probabile che non una buona idea.
1. Sarà possibile bisogna evitare contenuto misto: utilizzano HTTP o entrambi utilizzare HTTPS. Durante la riproduzione di contenuto misto, è necessario silverlightSS tech cancellazione di un avviso di contenuto misto. tech flashSS gestisce contenuto misto senza preavviso contenuto misto.
1. Se è stato creato l'endpoint streaming prima di agosto 2014, non supporterà HTTPS. In questo caso, creare e utilizzare un nuovo endpoint di flusso per HTTPS.

Per contenuto protetto DRM, l'implementazione di riferimento dell'applicazione e il flusso sarà in HTTTPS. Per aprire contenuto, Windows Media player non richiede l'autenticazione o licenza, in modo da avere libertà usare HTTP o HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory attivazione chiave di firma

Si tratta di un punto importante da prendere in considerazione dell'implementazione. Se non si considerano nell'implementazione, il sistema completati interromperà eventualmente lavoro completamente entro al massimo 6 settimane.

Azure Active Directory utilizza standard di settore per stabilire l'attendibilità tra stesso e le applicazioni che utilizzano Azure Active Directory. In particolare Azure Active Directory utilizza una chiave di firma è costituito da una coppia di chiavi pubblica e privatehttp. Quando Azure Active Directory crea un token di sicurezza che contiene le informazioni relative all'utente, questo token è firmato da Azure Active Directory utilizzando la chiave privata prima che venga inviato all'applicazione. Per verificare che il token è valido ed effettivamente originati da Azure Active Directory, l'applicazione deve convalida firma del token utilizzando la chiave pubblica esposta dall'Azure Active Directory contenuto nel documento di metadati federazione del tenant. Questa chiave pubblica e la chiave di firma da cui deriva – è lo stesso utilizzato per tutti i tenant Azure Active Directory.

Sono disponibili informazioni dettagliate su attivazione di Azure Active Directory chiave nel documento: [Informazioni sulla firma chiave di attivazione di Azure Active Directory](../active-directory/active-directory-signing-key-rollover.md).

Tra la [coppia di chiave pubblica privata](https://login.windows.net/common/discovery/keys/) 

- La chiave privata viene utilizzata da Azure Active Directory per generare un token di JWT;
- Chiave pubblica viene usata da un'applicazione, ad esempio servizi di recapito licenza DRM in AMS per verificare il token JWT;
 
A scopo di sicurezza, Azure Active Directory ruota questo certificato periodicamente (ogni 6 mesi). In caso di eventuali violazioni della protezione attivazione chiave può verificarsi in qualunque momento. Di conseguenza, i servizi di consegna licenza in AMS necessario aggiornare la chiave pubblica utilizzata come Azure Active Directory ruota coppia di chiavi, in caso contrario autenticazione token in AMS non verrà eseguita e non verrà inviata nessuna licenza. 

Questa operazione è necessario impostare TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument durante la configurazione di servizi di recapito licenza DRM.

È possibile il flusso di token JWT sotto:

1.  Azure Active Directory genererà token JWT con la chiave privata corrente per un utente autenticato;
2.  Quando un lettore rileva un CENC con contenuto protetto tramite DRM multipla, prima di tutto consente di trovare token JWT rilasciato da Azure Active Directory.
3.  Windows Media player Invia richiesta di acquisizione di licenza con token JWT ai servizi di recapito licenza in AMS;
4.  I servizi di consegna licenza in AMS utilizzerà la chiave pubblica corrente/valido da Azure Active Directory per verificare il token JWT prima di rilasciare licenze.

Servizi di recapito licenza DRM verranno sempre la verifica per la chiave pubblica corrente/valido da Azure Active Directory. Chiave pubblica presentata da Azure Active Directory sarà la chiave utilizzata per la verifica di un token JWT rilasciato da Azure Active Directory.

Cosa fare se la chiave chiusura avviene AAD genera un token JWT ma prima di JWT token inviato da lettori a servizi di recapito licenza DRM in AMS per la verifica? 

Poiché in qualsiasi momento, è possibile annullare una chiave, è sempre più di una chiave pubblica valida disponibili nel documento di metadati federazione. Azure recapito di licenza di servizi multimediali è possibile utilizzare i tasti specificati nel documento, poiché un tasto può distribuire breve, un altro potrebbe essere la sostituzione e così via.

### <a name="where-is-the-access-token"></a>Dove si trova il Token di accesso?

Se osserva come un'app web effettuerà un'app API in [Identità con OAuth 2.0 Client credenziali Grant](active-directory-authentication-scenarios.md#web-application-to-web-api), il flusso di autenticazione è possibile eseguire sotto:

1.  Un utente ha effettuato l'accesso a Azure Active Directory nell'applicazione web (vedere [Web Browser all'applicazione Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  L'endpoint di autorizzazione di Azure Active Directory reindirizza l'agente utente all'applicazione client con un codice di autorizzazione. L'agente utente restituisce il codice di autorizzazione a URI di reindirizzamento dell'applicazione client.
3.  L'applicazione web è necessario acquisire un token di accesso in modo che possa eseguire l'autenticazione sul Web API e recuperare la risorsa desiderata. Effettua una richiesta all'endpoint token di Azure Active Directory, fornire le credenziali, ID client e applicazione web dell'API URI ID. Verrà visualizzato il codice di autorizzazione per verificare che l'utente ha acconsentito.
4.  Azure Active Directory autentica l'applicazione e viene restituito un token di accesso JWT viene utilizzato per chiamare API web.
5.  Su HTTPS, l'applicazione web utilizza il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Titolare" nell'intestazione di autorizzazione della richiesta API sul Web. API web convalida token JWT e se la convalida ha esito positivo, restituisce la risorsa desiderata.

In questo flusso "identità dell'applicazione" web API trust che l'applicazione web autenticato l'utente. Per questo motivo, questo modello è chiamato un sottosistema attendibile. Il [diagramma in questa pagina](http://msdn.microsoft.com/library/azure/dn645542.aspx/) viene descritto come codice di autorizzazione concedere works flusso.

Acquisizione di licenza con restrizione token, è stiamo seguendo lo stesso modello di sottosistema attendibile. E il servizio di recapito licenza in servizi multimediali di Windows Azure web API risorsa, quella "back-end" un'applicazione web che richiede l'accesso. Dove si trova in modo il token di accesso?

È infatti stiamo ottenere token di accesso da Azure Active Directory. Dopo l'autenticazione dell'utente, viene restituito il codice di autorizzazione. Il codice di autorizzazione viene quindi utilizzato insieme a chiave ID e app client di exchange per token di accesso. E il token di accesso per l'accesso a un'applicazione di "puntatore" puntare o che rappresenta il servizio di recapito licenza Servizi multimediali di Windows Azure.

È necessario registrare e configurare l'app "puntatore" in Azure Active Directory seguendo la procedura seguente:

1.  Nel tenant di Azure Active Directory

    - aggiungere un'applicazione (risorse) con l'URL di accesso: 

    https://[resource_name].azurewebsites.NET/ e 

    - App ID URL: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  Aggiungere una nuova chiave per l'app risorsa;
3.  Aggiornare il file manifesto app in modo che la proprietà groupMembershipClaims è il valore seguente: "groupMembershipClaims": "Tutti"  
4.  Nell'applicazione di Azure Active Directory che puntano alle app web di Windows Media player, nella sezione "autorizzazioni ad altre applicazioni", aggiungere l'app di risorse che è stata aggiunta nel passaggio 1. In "autorizzazioni delegato" selezionare "Accesso [resource_name]" segno di spunta. In questo modo l'autorizzazione di app web per creare i token di accesso per l'accesso alle app delle risorse. Eseguire questa operazione per la versione locale e distribuita di web app se si sviluppa con Visual Studio e Azure web app.
    
Di conseguenza, il token JWT rilasciato da Azure Active Directory sia effettivamente il token di accesso per l'accesso a questa risorsa "puntatore".

### <a name="what-about-live-streaming"></a>Informazioni sul Live Streaming?

In quanto sopra menzionato, occupa la discussione della risorse su richiesta. Informazioni sul live streaming?

Fortuna è che è possibile utilizzare esattamente la stessa struttura e implementazione per la protezione streaming live in servizi multimediali di Windows Azure, gestendo bene associato a un'applicazione come "risorsa VOD".

In particolare, si noti che per eseguire live streaming in servizi multimediali di Windows Azure, è necessario creare un canale, quindi un programma nel canale. Per creare il programma, è necessario creare una risorsa che conterrà live archivio per il programma. Per fornire CENC protezione DRM con più di contenuto in diretta, tutto è necessario gestirlo, è possibile applicare la stessa configurazione/elaborazione per la risorsa come se fosse un "bene VOD" prima di avviare il programma.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>I server licenze all'esterno di servizi multimediali di Windows Azure?

Spesso, i clienti possono hanno investito in licenza server farm nei propri dati centrare o ospitato da provider di servizi DRM. Per la protezione dei contenuti di servizi multimediali Azure consente di utilizzare in modalità ibrida: contenuto ospitato e protetto in modo dinamico in servizi multimediali di Windows Azure, mentre licenze DRM vengono fornite da server esterni servizi multimediali di Windows Azure. In questo caso, sono disponibili le considerazioni seguenti modifiche:

1. Servizio Token di sicura deve di rilasciare il token che sono accettabili e può essere verificato dalla server farm licenza. Ad esempio, il server licenze Widevine forniti da Axinom richiede un token JWT specifico che contiene "diritto messaggio". Di conseguenza, è necessario disporre di un servizio token di sicurezza di rilasciare il token tali JWT. Gli autori che hanno completato tale implementazione ma è possibile trovare i dettagli del documento seguenti [nell'Interfaccia di Azure documentazione](https://azure.microsoft.com/documentation/): [Utilizzando Axinom per offrire licenze Widevine servizi multimediali di Windows Azure](media-services-axinom-integration.md). 
1. Non è necessario configurare il servizio di recapito licenza (ContentKeyAuthorizationPolicy) in servizi multimediali di Windows Azure. È necessario è quello di fornire la licenza URL di acquisizione di immagini (per PlayReady, Widevine e FairPlay) quando si configura AssetDeliveryPolicy nell'impostazione CENC con multi-DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>Cosa fare se si desidera utilizzare un servizio token di sicurezza personalizzati?

È possibile che alcuni motivi che un cliente può scegliere di utilizzare un servizio token di sicurezza (sicura servizio Token) personalizzato per fornire i token JWT. Alcune di esse sono:

1.  Servizio token di sicurezza non supporta il Provider di identità (IDP) utilizzato dal cliente. In questo caso un servizio token di sicurezza personalizzata potrebbe essere un'opzione.
2.  Il cliente potrebbe essere necessario controllo più flessibile o maggiore integrazione servizio token di sicurezza con sottoscrizione del cliente sistema di fatturazione. Ad esempio, un operatore MVPD può offrire più pacchetti sottoscrittore OTT, ad esempio premium base sport, e così via. L'operatore potrebbe essere in modo che corrispondano sulle attestazioni in un token con il pacchetto del sottoscrittore in modo che solo il contenuto nel pacchetto di destra è disponibile. In questo caso, un servizio token di sicurezza personalizzati offre flessibilità necessaria e al comando.

Due deve essere modificato quando si usa un servizio token di sicurezza personalizzato:

1.  Quando si configura il servizio di recapito licenza per una risorsa, è necessario specificare la chiave di protezione utilizzata per la verifica tramite il personalizzato servizio token di sicurezza (ulteriori dettagli riportata di seguito) anziché la chiave corrente da Azure Active Directory.
2.  Quando viene generato un token JTW, anziché la chiave privata di X509 corrente è specificata una chiave di sicurezza certificato di Azure Active Directory.

Esistono due tipi di chiavi di sicurezza:

1.  Chiave simmetrico: stesso usata per generare e verificare un token di JWT;
2.  Chiave asimmetrici: una coppia di chiave pubblica privata in un X509 certificato usato con la chiave privata per la crittografia/generare un token JWT e la chiave pubblica per verificare il token.

####<a name="tech-note"></a>Nota tecnica

Se si utilizza .NET Framework / c# come piattaforma di sviluppo, il X509 certificato usato per chiave di protezione asimmetrici deve avere almeno 2048 lunghezza della chiave. Questo è un requisito della classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. In caso contrario, verrà generata l'eccezione seguente:

IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey per la firma non può essere minore di '2048' bit. 

## <a name="the-completed-system-and-test"></a>Il sistema completato e test

Esamineremo alcuni scenari del sistema-to-end completa in modo che i lettori possono avere un basic "immagini" del comportamento prima di ottenere un account di accesso.

L'applicazione web di Windows Media player e l'account di accesso sono disponibili [qui](https://openidconnectweb.azurewebsites.net/).

Se è necessario "non integrata" scenario: risorse video ospitati in servizi multimediali di Windows Azure che non sono uno protetti o DRM protetta ma senza l'autenticazione token (rilasciare una licenza a chiunque la richiesta di), è possibile testare senza account di accesso (passando alla HTTP se la trasmissione video su HTTP).

Se è necessario-to-end integrato scenario: risorse video è in protezione DRM dinamica in servizi multimediali di Windows Azure, con l'autenticazione token e token JWT generati da Azure Active Directory, è necessario effettuare l'accesso.

### <a name="user-login"></a>Accesso utente

Per testare il sistema DRM integrato-to-end, è necessario disporre di un "account" Creazione o aggiunta. 

Quale account?

Sebbene Azure originariamente consentito l'accesso solo dagli utenti account Microsoft, subito consente l'accesso agli utenti di entrambi i sistemi. Questa operazione avveniva facendo in modo che tutti Azure proprietà trust Azure Active Directory per l'autenticazione, con Azure Active Directory di autenticazione degli utenti dell'organizzazione e la creazione di una relazione federazione nel punto in cui il sistema di identità consumer account Microsoft per autenticare gli utenti consumer e trust di Azure Active Directory. Di conseguenza, Azure Active Directory in grado di eseguire l'autenticazione account di Microsoft "guest", nonché "nativo" account Azure Active Directory.

Poiché Azure Active Directory e trust di dominio Account Microsoft (MSA), è possibile aggiungere tutti gli account da uno dei seguenti domini a personalizzato Azure AD tenant e usare l'account di accesso:

**Nome di dominio**|**Dominio**
-----------|----------
**Dominio tenant personalizzata Azure Active Directory**|somename.onmicrosoft.com
**Dominio aziendale**|Microsoft.com
**Dominio Account Microsoft (MSA)**|hotmail.com, Outlook.com, live.com

È possibile contattare uno degli autori disporre di un account di creazione o aggiunta dell'utente. 

Di seguito sono riportati gli screenshot delle pagine di accesso diversi utilizzati dagli account di dominio diverso.

**Azure personalizzato account di dominio Active Directory tenant**: In questo caso, viene visualizzata la pagina di accesso personalizzato di personalizzato nel dominio di tenant di Azure Active Directory.

![Account di dominio personalizzato Azure Active Directory tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Account di dominio Microsoft con smart card**: In questo caso viene visualizzata la pagina di accesso personalizzata da Microsoft aziendale IT con autenticazione a due fattori.

![Account di dominio personalizzato Azure Active Directory tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Account Microsoft (MSA)**: In questo caso, viene visualizzata la pagina di accesso dell'Account Microsoft per gli utenti.

![Account di dominio personalizzato Azure Active Directory tenant](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Utilizzando le estensioni multimediali crittografato per PlayReady

In un browser moderno crittografati Media estensioni (EME) per il supporto di PlayReady, ad esempio 11 di Internet Explorer in Windows 8.1 e backup e browser Microsoft Edge in Windows 10 PlayReady sarà DRM sottostante per EME.

![Utilizzo di EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

L'area player scuro è che la protezione PlayReady impedisce uno apportino ritaglio della schermata del video protetto. 

La schermata seguente mostra i plug-in Windows Media player supporto MSE/EME.

![Utilizzo di EME per PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME Microsoft Edge e Internet Explorer 11 in Windows 10 consente il richiamo di [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) in dispositivi Windows 10 che la supportano. PlayReady SL3000 Sblocca il flusso di contenuti premium avanzato (4 KB HDR, ecc.) e nuovi modelli di distribuzione di contenuti (finestra iniziale per contenuto avanzato).

Focalizzare l'attenzione su dispositivi Windows: PlayReady è DRM solo in hardware disponibile nei dispositivi Windows (PlayReady SL3000). Servizio di trasmissione possa utilizzare PlayReady tramite EME o tramite un'applicazione UWP e offrono una migliore qualità video tramite PlayReady SL3000 rispetto a un'altra DRM. In genere, 2 KB del flusso del contenuto tramite Chrome o Firefox e il contenuto di 4 KB tramite Microsoft Edge/IE11 o da un'applicazione UWP nello stesso dispositivo (a seconda delle impostazioni del servizio e implementazione).


#### <a name="using-eme-for-widevine"></a>Utilizzo di EME per Widevine

In un browser moderno con supporto EME/Widevine, ad esempio Chrome 41 + Windows 10, Windows 8.1, Mac OS x Yosemite e Chrome su Android 4.4.4, Google Widevine è DRM dietro EME.

![Utilizzo di EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Si noti che Widevine non impedirà uno apportino ritaglio della schermata del video protetto.

![Utilizzo di EME per Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Utenti non autorizzati

Se un utente non è un membro del gruppo di "utenti intitolata", l'utente non sarà possibile passare "controllo diritto" e il servizio di licenza con più DRM rifiuta di rilasciare la licenza richiesta, come illustrato di seguito. Descrizione dettagliata è "acquisire licenza non è riuscita", che è progettato.

![Utenti non autorizzati](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Esecuzione di servizio Token di protezione personalizzato

Per lo scenario di esecuzione personalizzato sicura servizio token di token JWT verrà inviato dal servizio token di sicurezza personalizzati utilizzando simmetrica o asimmetrici, una chiave. 

Nel caso di utilizzo di chiave simmetrico (con Chrome):

![Esecuzione di servizio token di sicurezza personalizzati](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Nel caso di utilizzo asimmetrici, una chiave tramite un X509 certificato (con i browser attuali Microsoft).

![Esecuzione di servizio token di sicurezza personalizzati](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In entrambi i casi precedenti, autenticazione utente resta invariato – in Azure Active Directory. L'unica differenza è che token JWT sono stati emessi dal servizio token di sicurezza personalizzata invece di Azure Active Directory. Naturalmente, quando si configura protezione CENC dinamica, la restrizione del servizio di recapito licenza specifica il tipo di token JWT, simmetrica o asimmetrici, una chiave.

## <a name="summary"></a>Riepilogo

In questo documento è stato illustrato CENC con un controllo più native DRM e accesso tramite autenticazione token: la struttura e la relativa implementazione mediante Azure, servizi multimediali di Windows Azure e Azure Media Player.

- Viene visualizzata una struttura di riferimento che contiene tutti i componenti necessari in un sottosistema DRM/CENC;
- Implementazione di riferimento su Azure, servizi multimediali di Windows Azure e Azure Media Player.
- Alcuni argomenti partecipano la struttura e l'implementazione sono descritti.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Riconoscimenti 

William Zhang, Mingfei Yan, Martino Le franco, Kilroy Hughes, Olga Kornich
