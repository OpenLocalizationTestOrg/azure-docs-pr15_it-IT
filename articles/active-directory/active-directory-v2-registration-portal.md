<properties
    pageTitle="Gli argomenti della Guida del portale di registrazione App | Microsoft Azure"
    description="Descrizione delle varie caratteristiche nel portale di registrazione di Microsoft app."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="app-registration-reference"></a>Guida di riferimento registrazione App
In questo documento fornisce contesto e descrizioni delle varie caratteristiche disponibili nel portale di registrazione di Microsoft App [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Le applicazioni
L'elenco include tutte le applicazioni registrati per l'utilizzo con l'endpoint v 2.0 Azure Active Directory.  Queste applicazioni hanno la possibilità di eseguire l'accesso degli utenti con entrambi gli account personali dall'account Microsoft e gli account aziendale o dell'istituto di istruzione da Azure Active Directory.  Per altre informazioni sull'endpoint v 2.0 Azure Active Directory, vedere la [Panoramica v 2.0](active-directory-appmodel-v2-overview.md).  Queste applicazioni possono essere utilizzate anche per l'integrazione con l'endpoint di autenticazione account Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Applicazioni SDK Live
L'elenco include tutte le applicazioni registrati per l'utilizzo esclusivamente con account Microsoft.  Non sono attivati per l'utilizzo con Azure Active Directory qualsiasi.  Si tratta in cui sono disponibili tutte le applicazioni che erano state registrate in precedenza con il portale per sviluppatori MSA in `https://account.live.com/developers/applications`.  Tutte le funzioni che in precedenza eseguite in `https://account.live.com/developers/applications` ora possono essere eseguite in questo nuovo portale `https://apps.dev.microsoft.com`.  Se hai altre domande relative alle applicazioni di account Microsoft, contatta l'assistenza clienti.

## <a name="application-secrets"></a>Informazioni riservate applicazione
Informazioni riservate applicazione sono credenziali che consentono l'applicazione esegua affidabile [autenticazione client](http://tools.ietf.org/html/rfc6749#section-2.3) con Azure Active Directory.  In OAuth & OpenID connessione, informazioni riservate un'applicazione è in genere definito un `client_secret`.  Protocollo v 2.0, tutte le applicazioni che riceve un token di sicurezza in un percorso web utilizzabili (usando un `https` combinazione) deve utilizzare un segreto applicazione per l'identificazione Azure Active Directory al rimborso di tale token di sicurezza.  Inoltre, tutti i client nativi che riceve token in un dispositivo verrà essere non è consentito utilizzando un segreto applicazione per eseguire l'autenticazione client, per impedire l'archiviazione di informazioni riservate in ambienti non sicuri.

Ogni app può contenere informazioni riservate applicazione valida due in qualsiasi punto nel tempo.  Mantenendo due informazioni riservate, è necessario ablilty per eseguire l'attivazione di chiave periodici intero ambiente dell'applicazione.  Dopo la migrazione completa dell'applicazione in un nuovo segreto, è possibile eliminare il vecchio segreto ed effettuare il provisioning di uno nuovo.

Al momento solo due tipi di informazioni riservate applicazione consentiti nel portale di registrazione di app.  Scelta di **Generare nuova Password** genererà e memorizzare un segreto condiviso nell'archivio di dati, è possibile utilizzare nell'applicazione.  Scelta di **Generare nuova coppia di chiave** verrà creata una nuova coppia di chiave pubblica/privata che può essere scaricata e utilizzata per l'autenticazione del client di Azure Active Directory.

## <a name="profile"></a>Profilo
La sezione profilo del portale di registrazione di app può essere utilizzata per personalizzare la pagina per l'applicazione di accesso.  In questo momento è possibile modificare il logo di applicazione della pagina, condizioni per l'URL del servizio e informativa sulla privacy di accesso.  Il logo deve essere un trasparente 48 x 48 o 50 x 50 pixel un'immagine in un file di immagine GIF, PNG o JPEG 15 KB o ridurre le dimensioni.  Provare a modificare i valori e la visualizzazione di accesso risultante nella pagina!

## <a name="live-sdk-support"></a>Supporto per SDK Live
Quando si abilita "Live SDK supporto", eseguire il provisioning informazioni riservate qualsiasi applicazione creata in Azure AD e archivi dati Account Microsoft.  In questo modo l'applicazione per l'integrazione diretta con il servizio di Account Microsoft (login.live.com).  Se si desidera creare un'app usando l'Account Microsoft direttamente (anziché con l'endpoint di Azure Active Directory v 2.0), assicurarsi che sia abilitato Live SDK supporto.

Disattivando il supporto per Live SDK garantisce che il segreto applicazione è scritto solo nei dati di Azure Active Directory archiviare.  I dati di Azure Active Directory archivio incorpora norme aziendale che consentono di soddisfare determinati standard, ad esempio conformità FISMA.  Se si abilita il supporto di Live SDK, l'applicazione non può ottenere conformità con alcuni di questi standard.

Se si intende solo utilizzare il punto finale v 2.0 di Azure Active Directory, è possibile disabilitare il supporto di Live SDK.

