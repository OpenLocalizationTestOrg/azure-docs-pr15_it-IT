<properties
    pageTitle="Azure coinvolgimento Mobile iOS note sulla versione SDK | Microsoft Azure"
    description="Ultimi aggiornamenti e le procedure per iOS SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Note sulla versione di Azure coinvolgimento Mobile iOS SDK

##<a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notifica fissa non prese in considerazione in dispositivi iOS 10.
-   Impostare come deprecato XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Fisso aggregazione tra registri tecnici e altre.

##<a name="323-06072016"></a>3.2.3 (06/07/2016)

-   Corretto il bug in commenti e suggerimenti recapito non quando viene riportato app viene eseguita in background.
-   Ottimizzato per l'invio dei registri tecnici.

##<a name="322-04072016"></a>3.2.2 (04/07/2016)

-   Bug corretto al momento dell'annullamento richiesta HTTP che in alcuni casi dei clienti potenziali subisce un arresto anomalo.

##<a name="321-12112015"></a>3.2.1 (12/11/2015)

-   Fisso il ritardo quando una nuova istanza di app viene attivata da una notifica con collegamenti complete

##<a name="320-10082015"></a>3.2.0 (08/10/2015)

-   Attivazione Bitcode in SDK affinché funzioni con **Xcode 7**.
-   Corretti relativi alle notifiche nell'app.
-   Una chiamata effettuata le notifiche di app più affidabile in caso di batteria e altri scenari.
-   Rimuovere i registri console aggiuntive generati dalla libreria di terze parti 3 °.

##<a name="310-08262015"></a>3.1.0 (08/26/2015)

-   Correggere iOS 9 compatibilità bug con una raccolta di terze parti. È stata causa anomalo durante l'invio di polling risultati, le informazioni sull'applicazione o dati aggiuntivi.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Coinvolgimento mobile utilizza automatica delle notifiche Push.
-   Eliminare il supporto per iOS 4. Inizio di questa versione la destinazione della distribuzione dell'applicazione in uso deve essere almeno iOS 6.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   L'id del dispositivo Mobile impegno per i dispositivi < iOS 6 è basato su un GUID generato al momento dell'installazione.

##<a name="210-04242015"></a>2.1.0 (24/04/2015)

-   Aggiunta rapida compatibilità.
-   Se si fa clic su una notifica, eseguita l'azione che URL è ora destra dopo l'apertura dell'applicazione.
-   Aggiunta file di intestazione mancanti nel pacchetto SDK.
-   Correzione di un problema quando chi arresto anomalo coinvolgimento Mobile è stato disabilitato.

##<a name="200-02172015"></a>2.0.0 (17/02/2015)

-   Versione iniziale di Azure impegno per dispositivi mobili
-   configurazione di appId/sdkKey viene sostituita da una configurazione della stringa di connessione.
-   Rimuovere API per inviare e ricevere messaggi di azienda non autorizzati da entità azienda non autorizzata.
-   Rimuovere API per inviare e ricevere messaggi tra i dispositivi.
-   Miglioramenti della protezione.
-   Verifica SmartAd rimossi.
