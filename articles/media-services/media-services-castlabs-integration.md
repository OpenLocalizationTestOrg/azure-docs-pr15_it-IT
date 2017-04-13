<properties 
    pageTitle="Utilizzare castLabs per fornire le licenze Widevine su servizi multimediali di Windows Azure | Microsoft Azure" 
    description="Questo articolo descrive come è possibile utilizzare servizi multimediali di Azure (AMS) per eseguire un flusso di dinamicamente crittografato tramite AMS con PlayReady e Widevine DRMs. La licenza PlayReady proviene dal server licenze PlayReady di servizi di supporto ed Widevine licenza viene recapitato da server licenze castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizzare castLabs per fornire le licenze Widevine su servizi multimediali di Windows Azure

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Panoramica

Questo articolo descrive come è possibile utilizzare servizi multimediali di Azure (AMS) per eseguire un flusso di dinamicamente crittografato tramite AMS con PlayReady e Widevine DRMs. La licenza PlayReady proviene dal server licenze PlayReady di servizi di supporto ed Widevine licenza viene recapitato da server licenze **castLabs** .

Per la riproduzione streaming del contenuto protetto da CENC (PlayReady e/o Widevine), è possibile utilizzare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Per informazioni dettagliate, vedere [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) .

Nel diagramma seguente viene illustrato un alto livello servizi multimediali di Windows Azure e architettura di integrazione castLabs.

![integrazione](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Configurazione del sistema tipico

- Contenuti multimediali vengono archiviati in AMS.
- ID chiave di chiavi sono archiviate in castLabs e AMS.
- castLabs e AMS hanno autenticazione token incorporata. Nelle sezioni seguenti vengono illustrano i token di autenticazione. 
- Quando un client richiede al flusso il video, il contenuto è dinamicamente crittografato con **La crittografia comuni** (CENC) e delle presentazioni in modo dinamico AMS Smooth Streaming e trattino. Offrire anche la crittografia di flusso elementare PlayReady M2TS protocollo flusso HLS.
- Licenze PlayReady ricavata dal server licenze AMS e licenza Widevine ricavata dal server licenze castLabs. 
- Media Player decide automaticamente in base a cui licenza per recuperare la funzionalità di piattaforma client. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Generazione di token di autenticazione per ottenere una licenza

CastLabs e AMS supporta formato token JWT (JSON Web Token) utilizzato per autorizzare una licenza. 

###<a name="jwt-token-in-ams"></a>Token JWT in AMS 

Nella tabella seguente descrive token JWT in AMS. 

Emittente|Stringa emittente dalla scelto servizio Token (sicurezza)
---|---
Gruppo di destinatari|Stringa di gruppo di destinatari dal servizio token di sicurezza comuni
Sulle attestazioni|Una serie di attestazioni
NotBefore|Inizio validità del token
Scadenza|Fine validità del token
SigningCredentials|Il tasto condiviso tra Server licenze PlayReady, castLabs Server delle licenze e servizio token di sicurezza, potrebbe essere simmetrici o asimmetrici, una chiave.

###<a name="jwt-token-in-castlabs"></a>Token JWT in castLabs

Nella tabella seguente descrive token JWT in castLabs. 

Nome|Descrizione
---|---
optData|Stringa JSON contenente informazioni personali. 
CRT|Una stringa JSON contenente le informazioni sulle risorse, i diritti di riproduzione e informazioni di licenza.
IAT|Datetime corrente nel periodo.
JTI|Identificatore univoco su questo token (ogni token utilizzabile solo una volta nel sistema castLabs).

##<a name="sample-solution-set-up"></a>Configurare la soluzione di esempio 

[Soluzione di esempio](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) è costituito da due progetti:

-   App console che può essere utilizzata per impostare restrizioni sul DRM su una risorsa già acquisita per PlayReady e Widevine.
-   Applicazione Web che mani dichiarazione di token, potrebbe essere visualizzato come una versione molto semplificata di un servizio token di sicurezza.


Per usare l'applicazione console:

1.  Modificare l'App per impostare le credenziali AMS, castLabs credenziali, configurazione di servizio token di sicurezza e chiave condivisa.
2.  Caricare una risorsa in AMS.
3.  È possibile ottenere UUID dal bene caricato e modificare 32 riga nel file Program.cs:

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Utilizzare un idargomento per la denominazione bene nel sistema di castLabs (riga 44 nel file Program.cs).

    È necessario impostare idargomento per **castLabs**; deve essere una stringa di caratteri alfanumerica univoca.

5.  Eseguire il programma.


Per utilizzare il Web applicazione (servizio token di sicurezza):

1.  Modificare il config in azienda castlabs configurazione ID, la configurazione di servizio token di sicurezza e la chiave condivisa.
2.  Distribuire ai siti Web Azure.
3.  Passare al sito Web.

##<a name="playing-back-a-video"></a>Riprodurre un video

Per riprodurre un video crittografato con la crittografia comune (PlayReady e/o Widevine), è possibile utilizzare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Quando si esegue l'app console, l'ID di chiave contenuto e l'URL manifesto vengono restituite.

1.  Aprire una nuova scheda e avviare il servizio token di sicurezza: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Passare a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Incollare l'URL di trasmissione.
4.  Fare clic su casella di controllo **Opzioni avanzate** .
5.  **Protezione** dall'elenco a discesa selezionare PlayReady e/o Widevine.
6.  Incollare il token ottenuto dal servizio token di sicurezza nella casella Token di testo. 
    
    Il server licenze castLab non è necessario il "titolare =" prefisso che precede il token. In modo da rimuovere prima di inviare il token.
7.  Aggiornare il lettore multimediale.
8.  Il video deve essere riprodotto.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
