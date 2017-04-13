<properties 
    pageTitle="Utilizzo di partner per recapitare Widevine licenze a servizi multimediali di Windows Azure | Microsoft Azure" 
    description="Questo articolo descrive come è possibile utilizzare servizi multimediali di Azure (AMS) per eseguire un flusso di dinamicamente crittografato tramite AMS con PlayReady e Widevine DRMs. La licenza PlayReady proviene dal server licenze PlayReady di servizi di supporto ed Widevine licenza viene recapitato da server licenze castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizzo di partner per recapitare Widevine licenze a servizi multimediali di Windows Azure

##<a name="overview"></a>Panoramica

Servizi multimediali di Microsoft Azure consente di eseguire MPEG-tratto è protetto con Widevine DRM, che vengono crittografati per le specifiche di crittografia comuni (CENC).

A partire da Media Services .NET SDK versione 3.5.2, servizi multimediali consente di configurare il modello di licenza Widevine e ottenere Widevine licenze. È inoltre possibile utilizzare i seguenti partner AMS per offrire licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

È possibile utilizzare [castLabs](http://castlabs.com/company/partners/azure/) per offrire Widevine licenze. Per ulteriori informazioni, vedere [uso castLabs per offrire licenze DRM servizi multimediali di Windows Azure](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

È possibile utilizzare [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) per offrire Widevine licenze. Per ulteriori informazioni, vedere [Uso Axinom per offrire licenze DRM servizi multimediali di Windows Azure](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Crittografia PlayReady e/o Widevine dinamico comuni](media-services-protect-with-drm.md)

[Blog di Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

