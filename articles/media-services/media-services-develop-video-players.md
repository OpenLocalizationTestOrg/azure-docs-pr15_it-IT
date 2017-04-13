<properties 
    pageTitle="Sviluppo di applicazioni lettore di video" 
    description="L'argomento fornisce collegamenti a Framework lettore e plug-in cui è possibile utilizzare per sviluppare le proprie applicazioni client in grado di utilizzare multimediali dalla pagina Servizi multimediali." 
    authors="Juliako" 
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


#<a name="develop-video-player-applications"></a>Sviluppo di applicazioni lettore di video

##<a name="overview"></a>Panoramica

Servizi multimediali di Azure sono disponibili gli strumenti necessari per creare le applicazioni di Windows Media player accattivanti e dall'aspetto dinamico client per la maggior parte delle piattaforme, tra cui: iOS dispositivi, dispositivi Android, Windows, Windows Phone, Xbox e Set-top caselle. In questo argomento vengono forniti anche i collegamenti a SDK e Framework lettore che è possibile utilizzare per sviluppare le proprie applicazioni client in grado di utilizzare multimediali da servizi multimediali di Windows Azure.

##<a name="azure-media-player"></a>Lettore multimediale Azure

[Azure Media Player](http://aka.ms/ampinfo) è un lettore di video web progettato per la riproduzione dei contenuti multimediali da servizi multimediali di Microsoft Azure su una vasta gamma di dispositivi e browser. Azure Media Player utilizza standard, ad esempio HTML5, elementi multimediali origine estensioni (MSE) ed estensioni di file multimediali crittografati (EME) per assicurare un'esperienza di trasmissione adattata assicura. Quando questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player utilizza Flash e Silverlight come grazie alla tecnologia di fallback. Indipendentemente dalla tecnologia di riproduzione utilizzata, gli sviluppatori avrà un'interfaccia JavaScript unificata per l'accesso API. In questo modo per il contenuto fornito tramite servizi multimediali di Windows Azure da riprodurre tra un'ampia gamma di dispositivi e browser senza ulteriori operazioni.

Servizi multimediali di Microsoft Azure consente di contenuto da effettuare con trattino Smooth Streaming e HLS streaming formati di riprodurre contenuto. Azure Media Player prende in considerazione i diversi formati e riproduce automaticamente il collegamento migliore in base alle funzionalità di piattaforma/browser. Servizi multimediali di Microsoft Azure consente anche di crittografia dinamiche dei beni con la crittografia PlayReady o AES a 128 bit crittografia busta. Azure Media Player consente la decrittografia di PlayReady e AES a 128 bit contenuto crittografato quando è stato configurato correttamente. 

Per ulteriori informazioni:

- [Lettore multimediale Azure](http://aka.ms/ampinfo)
- [Documentazione di lettore multimediale Azure](http://aka.ms/ampdocs) 
- [Lettore Azure Guida introduttiva del Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Iscriversi per mantenersi aggiornati con le ultime da Azure Media Player](http://aka.ms/ampsignup)
- [Aggiungere nuove funzionalità richieste, idee, commenti e suggerimenti](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Altri strumenti per la creazione di applicazioni di Windows Media Player

È anche possibile usare uno dei seguenti SDK:

- [Arrotondare flusso Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Smussate flusso App di Windows Store](media-services-build-smooth-streaming-apps.md)
- [Piattaforma Microsoft Media: Framework di Windows Media Player](http://playerframework.codeplex.com/) 
- [HTML5 Documentazione Framework lettore](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft Smooth Streaming plug-in per OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Gestione delle licenze Microsoft® smussate flusso Client portabilità Kit](http://aka.ms/sspk) 
- [Sviluppo di applicazioni Video XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>La pubblicità

Servizi multimediali di Azure fornisce supporto per l'inserimento di Active Directory mediante la piattaforma Windows Media: Framework Windows Media Player. Modelli di Windows Media Player con supporto di Active Directory sono disponibili per i dispositivi Windows 8, Silverlight, Windows Phone 8 e iOS. Ogni framework player il codice di esempio che illustra come implementare un'applicazione di riproduzione. Esistono tre tipi diversi di annunci che è possibile inserire i file multimediali:

Lineare: annunci frame completo che sospendere il video principale

Non lineare: annunci sovrapposizione che vengono visualizzati durante la riproduzione di video principale, in genere un logo o un'altra immagine statica posizionati all'interno di Windows Media player

Associato – annunci visualizzati all'esterno di Windows Media player

Annunci possono essere inseriti in qualsiasi momento nella sequenza temporale del video principale. È necessario indicare il lettore i tempi di riproduzione Active Directory e gli annunci pubblicitari da riprodurre. Questa operazione viene eseguita mediante un insieme di file basato su XML standard: modello vastissime (Video Active Directory servizio capacità), digitale Video più Active Directory Playlist (VMAP), il modello di sequenza astratti Media (CANTO) e digitale Video Windows Media Player Active Directory interfaccia definizione (VPAID). File maggior specificano quali annunci da visualizzare. File VMAP specificano quando riprodurre diversi annunci e contengono maggior XML. File CANTO sono un altro modo per gli annunci di sequenza che può contenere maggior XML. File VPAID definiscono un'interfaccia tra il lettore video e Active Directory o server Active Directory. Per ulteriori informazioni, vedere [Inserimento di annunci](https://msdn.microsoft.com/library/dn387398.aspx).

Per informazioni sul supporto di sottotitoli codificati e annunci chiuso in Live streaming video, vedere [supportati sottotitoli e standard di inserimento di Active Directory](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Incorporamento di Video MPEG-tratto adattata flusso in un'applicazione di HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js archivio](https://github.com/Dash-Industry-Forum/dash.js)
 
