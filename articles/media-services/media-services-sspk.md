<properties 
    pageTitle="Gestione delle licenze Microsoft® smussate flusso Client portabilità Kit" 
    description="Informazioni sulle modalità di gestione delle licenze Microsoft® smussate Streaming Client portabilità Kit." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Gestione delle licenze Microsoft® smussate flusso Client portabilità Kit

##<a name="overview"></a>Panoramica

Microsoft smussate Streaming Client portabilità Kit (**SSPK** in breve) è un'implementazione client Smooth Streaming ottimizzate per produttori di dispositivi incorporati, cavo e operatori di telefonia mobile, provider di servizi contenuto, produttori ricevitore, produttori software indipendenti () e fornitori di soluzioni per creare prodotti e servizi per lo streaming adattativo flusso contenuto in formato Smooth Streaming. SSPK è un'implementazione indipendente dal dispositivo e la piattaforma del client Smooth Streaming che è possibile trasferire tramite il titolare di licenza in qualsiasi dispositivo e piattaforma. 

Incluso sotto è un'architettura di alto livello e IIS smussate Streaming portabilità casella Kit è l'implementazione di Client Streaming smussate fornito da Microsoft e include tutta la logica di base per la riproduzione di contenuto Smooth Streaming. Questo è quindi trasferito dal partner per un dispositivo specifico o piattaforma le interfacce appropriate. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Descrizione

SSPK viene concesso in termini che offrono valore eccellente aziendale. Licenza SSPK fornisce il settore con:

- Origine Streaming Kit portabilità smussate c++ 
  - implementa funzionalità Client Streaming smussato
  - Aggiunge formato analisi euristica, il buffer logica e così via.
- API di applicazione di Windows Media Player 
  - interfacce di programmazione per l'interazione con un'applicazione di lettore multimediale
- Interfaccia di piattaforma indipendente Layer (PAL) 
  - interfacce di programmazione per l'interazione con il sistema operativo (thread, sockets)
- Interfaccia di hardware indipendente HAL (Layer) 
  - API per l'interazione con hardware A / decodificatore V (decodifica, il rendering)
- Interfaccia di Digital Rights Management (DRM) 
  - interfacce di programmazione per la gestione DRM tramite DRM Abstraction Layer (DAL)
  - Microsoft PlayReady portabilità Kit incluso separatamente, ma si integra tramite questa interfaccia. Per ulteriori informazioni sulle licenze Microsoft PlayReady Device, fare clic [qui](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Esempi di implementazione 
  - esempio di implementazione PAL per Linux
  - esempio di implementazione HAL per GStreamer

##<a name="licensing-options"></a>Le opzioni di licenza

Microsoft smussate Streaming Client portabilità Kit sia messo a disposizione licenziatari in due contratti di licenza distinti: uno per lo sviluppo di smussate Streaming Client provvisori prodotti e un altro per la distribuzione uniforme Streaming finale prodotti Client agli utenti finali.
 
- Per produttori chipset, integratori o produttori software indipendenti () che richiedono un codice sorgente portabilità kit per sviluppare prodotti provvisori, deve essere eseguita una Microsoft smussate Streaming Client portabilità Kit **Provvisori licenza** .
- Per produttori di dispositivi o indipendenti che richiedono diritti di distribuzione uniforme Streaming Client predette agli utenti finali, deve essere eseguita la Microsoft smussate Streaming Client portabilità Kit **Licenza del prodotto finale** .

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client portabilità provvisori Kit di licenza

In questa licenza Microsoft offre un buon Streaming Client portabilità Kit e i diritti di proprietà intellettuale necessari per sviluppare e distribuire smussate Streaming Client provvisori prodotti ad altri licenziatari dispositivo smussate Streaming Client portabilità Kit che distribuiscono smussate Streaming finale prodotti Client.

####<a name="fee-structure"></a>Tariffario

Una Commissione di licenza monouso 50.000 dollari consente di accedere a smussate Streaming Client portabilità Kit. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Client portabilità licenza del prodotto finale Kit

In questa licenza Microsoft offre tutti i diritti di proprietà intellettuale necessari per ricevere smussate Streaming Client provvisori prodotti provenienti da altri licenziatari smussate Streaming Client portabilità Kit e per distribuire società smussate Streaming Client finale prodotti di marchio agli utenti finali.

####<a name="fee-structure"></a>Tariffario

Smussate Streaming Client prodotto finale è disponibile in un modello di royalty come in:

- 0,10 dollari per ogni spedita implementazione del dispositivo
- I compensi sono limitato 50.000 ogni anno
- Nessun royalty per prime 10.000 implementazioni del dispositivo ogni anno 

##<a name="licensing-procedure-and-sspk-access"></a>Procedura di gestione delle licenze e SSPK di accesso

Inviare posta elettronica [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) per tutte le query di licenze.

Il [portale di distribuzione SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) è accessibile a licenziatari provvisori registrati.

Licenziatari provvisori e SSPK finale possono inviare domande tecniche per [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming Client prodotto provvisorio contratto licenziatari

- Adroit Business Solutions, Inc
- Avanzate digitale per la trasmissione di SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret relazione
- Albis Technologies Ltd.
- Alticast Corporation
- Servizi digitali Amazon, Inc.
- AVC multimediali Software CO, Ltd.
- Cavium, Inc.
- EchoStar acquisto Corporation
- Enseo, Inc.
- Fluendo s.
- CO BroadInfoCom HANDAN Ltd.
- Infomir GMBH
- Inc. USA Irdeto
- VC servizi globali di libertà
- MediaTek Inc.
- Co Ltd MStar
- CO Nintendo Ltd.
- OpenTV, Inc.
- Zafferano digitale limitato
- Co Ltd Sichuan Changhong elettrici
- SoftAtHome
- Sony Corporation
- Tatung Technology Inc.
- CO Technoly elettronici (Huizhou) TCL Ltd.
- Sposta Vestel Elektronik Sanayi Ticaret relazione
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming Client prodotto finale contratto licenziatari

- Avanzate digitale per la trasmissione di SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret relazione
- Albis Technologies Ltd.
- Servizi digitali Amazon, Inc.
- Tecnologia AmTRAN CO, Ltd.
- Arcadyan Technology Corporation
- SAN ELEKTRONİK ATMACA. SPOSTA TİC. A.Ş
- British Sky trasmissione limitato
- CastPal tecnologia Inc Shenzhen
- Compal elettronici, Inc.
- Dongguan digitali AV tecnologia corp, Ltd.
- EchoStar acquisto Corporation
- Enseo, Inc.
- Filmflex filmati limitato
- Fluendo s.
- Gibson innovazioni limitato
- Haier informazioni Applicantion S.R.L
- CO BroadInfoCom HANDAN Ltd.
- Co Ltd Homecast
- Hon Hai precisione settore CO, Ltd.
- Infomir GMBH
- CO Kaonmedia Ltd.
- Corporation KDDI
- CO Nintendo Ltd.
- SA arancione
- Zafferano digitale limitato
- Sagemcom Broadband SA
- CO LTD Shenzhen Coship elettronici
- Co Ltd Shenzhen Jiuzhou elettrici
- Shenzhen Skyworth Digital tecnologia Co Ltd
- Sichuan Changhong elettrico CO, Ltd.
- Skardin industriali corp
- Sky Deutschland Fernsehen GmbH & Co KG
- SmarDTV s.
- SoftAtHome
- Sony Corporation
- Limitata TCL oltremare Marketing (Macao commerciali Offshore)
- Tecnologie di recapito Technicolor, sa
- Tongfang Ltd. globale
- Stile di vita Toshiba prodotti e servizi Corporation
- S.r.o. /Slovakia/ Universal Corporation elementi multimediali
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
