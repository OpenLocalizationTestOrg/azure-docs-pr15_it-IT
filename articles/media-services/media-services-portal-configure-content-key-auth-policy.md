<properties 
    pageTitle="Configurare i criteri di autorizzazione chiave contenuto tramite il portale di Azure | Microsoft Azure" 
    description="Informazioni su come configurare un criterio di autorizzazioni per una chiave del contenuto." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurare i criteri di autorizzazione chiave contenuto
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Panoramica

Servizi multimediali di Microsoft Azure consente di eseguire flussi MPEG-tratto Smooth Streaming e Streaming Live HTTP (HLS) è protetti con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit) o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS è inoltre possibile recapitare flussi trattino crittografati con Widevine DRM. PlayReady e Widevine vengono crittografati per la specifica della crittografia comuni (ISO/IEC 23001 7 CENC).

Servizi multimediali vengono forniti anche un **Servizio di recapito tasto/licenze** da cui i clienti possono ottenere chiavi AES o licenze PlayReady/Widevine per riprodurre il contenuto crittografato.

In questo argomento viene illustrato come utilizzare il portale di Azure per configurare i criteri di autorizzazione chiave contenuto. La chiave è utilizzabile in un secondo momento per crittografare in modo dinamico il contenuto. Nota che attualmente è possibile crittografare streaming seguenti formati: HLS, MPEG trattino e Smooth Streaming. Non è possibile crittografare unità 10K streaming formato o l'opzione progressivo Scarica.

Quando un lettore richiede un flusso che è stato impostato in modo dinamico crittografato, servizi multimediali di utilizza la chiave configurata per crittografare in modo dinamico il contenuto utilizzando la crittografia AES o DRM. Per decrittografare il flusso, il lettore richiederà la chiave del servizio di recapito chiave. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificata per la chiave.


Se si prevede di più tasti contenuto o per specificare un URL del **Servizio di recapito tasto/licenza** diversa da quella il servizio di recapito chiave servizi multimediali, usare Media Services .NET SDK o API REST.

[Configurare i criteri di autorizzazione chiave contenuto tramite Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurare i criteri di autorizzazione chiave contenuto utilizzando API REST di servizi di supporto](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Alcune considerazioni:

- Per poter usare imballaggio dinamico e dinamico crittografia, è necessario assicurarsi avere almeno una trasmissione unità riservato. Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).
- Le risorse devono contenere un insieme di velocità adattata MP4s o file Smooth Streaming velocità adattata. Per ulteriori informazioni, vedere [codifica una risorsa](media-services-encode-asset.md).
- Il servizio di recapito chiave memorizza ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni di criteri e restrizioni) per 15 minuti.  Se si crea un ContentKeyAuthorizationPolicy e specificare l'utilizzo di una restrizione "Token", testare e quindi aggiornare i criteri di restrizione "Apri", saranno necessarie circa 15 minuti prima che il criterio passa alla versione "Aperta" dei criteri.


##<a name="how-to-configure-the-key-authorization-policy"></a>Procedura: configurare i criteri di autorizzazione principali

Per configurare i criteri di autorizzazione principali, selezionare la pagina di **Protezione del contenuto** .

Servizi multimediali sono supportati più metodi di autenticazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto possono contenere **aprire**, **token**o le restrizioni di autorizzazione **IP** (**IP** possono essere configurati con resto o .NET SDK).

###<a name="open-restriction"></a>Apri restrizione

La restrizione **aprire** indica che il sistema verrà eseguita la chiave a qualsiasi utente che effettua una richiesta di chiave. Questa limitazione può risultare utile a scopo di testing.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restrizione token

Per scegliere il criterio token con restrizioni, fare clic sul pulsante **TOKEN** di seguito.

Criteri **token** con restrizioni devono essere accompagnato da un token rilasciato da un **Servizio Token di protezione** di. Servizi multimediali sono supportati i token nel formato **Semplice Web token** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e nel formato **JSON Web Token** (JWT). Per informazioni, vedere [autenticazione token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Servizi multimediali non fornisce **Servizi Token protetti**. È possibile creare un servizio token di sicurezza personalizzati o utilizzare Microsoft Azure ACS ai token problema. Il servizio token di sicurezza deve essere configurato per creare un token firmato con specificato chiave e problema attestazioni specificato nella configurazione del token restrizione. Se il token è valido e le richieste nel token corrispondono a quelli configurato per la chiave del contenuto, il servizio di recapito chiave servizi multimediali restituirà la chiave di crittografia al client. Per ulteriori informazioni, vedere [Usare Azure ACS ai token problema](http://mingfeiy.com/acs-with-key-services).

Quando la configurazione di **TOKEN** limitata criteri, è necessario impostare i valori **chiave di verifica**, **emittente** e **gruppo di destinatari**. La chiave primaria verifica contiene la chiave firmati con il token, emittente è il servizio token di sicuro che genera il token. Gruppo di destinatari (a volte chiamato ambito) descrive lo scopo del token o la risorsa il token autorizza l'accesso a. Il servizio di recapito chiave servizi multimediali convalida che questi valori nel token corrispondano ai valori nel modello.

###<a name="playready"></a>PlayReady

Per proteggere il contenuto con **PlayReady**, una delle operazioni che è necessario specificare i criteri di autorizzazione è una stringa XML che definisce il modello di licenza PlayReady. Per impostazione predefinita, è il seguente criterio:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

È possibile fare clic sul pulsante **Importa criteri xml** e fornire un diverso file XML conforme allo Schema XML definito [qui](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

