<properties 
    pageTitle="Contenuto di Windows SDK App universale" 
    description="Informazioni sul contenuto del SDK App universale di Windows per Azure Mobile coinvolgimento"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Contenuto di Windows SDK App universale

In questo documento vengono elencati e viene descritto il contenuto distribuito da SDK nell'applicazione.

##<a name="the-resources-folder"></a>Il `/Resources` cartella

Questa cartella contiene tutte le risorse che deve essere coinvolgimento Mobile. È anche possibile personalizzare in modo da adattarlo l'app.

- `EngagementConfiguration.xml`File di configurazione del coinvolgimento: Mobile, si tratta nel punto in cui è possibile personalizzare le impostazioni di impegno Mobile (stringa di connessione di dispositivi mobili coinvolgimento; arresto anomalo di report...).

### <a name="html-folder"></a>cartella seleziona

- `EngagementNotification.html`: Il `Notification` web in visualizzazione struttura html per le intestazioni all'interno dell'applicazione.

- `EngagementAnnouncement.html`: Il `Announcement` web in visualizzazione struttura html per le visualizzazioni intermedi all'interno dell'applicazione.

### <a name="images-folder"></a>cartella /Images

- `EngagementIconNotification.png`: L'icona di marchio visualizzata a sinistra di una notifica, sostituire questa occorrenza dall'icona di marchio.

- `EngagementIconOk.png`: Il `Ok` icona le potenzialità pagine di contenuto per il pulsante di azione o convalida.

- `EngagementIconNOK.png`: Il `NOK` icona utilizzata quando il pulsante convalida le potenzialità pagine di contenuto è disattivato.
 
- `EngagementIconClose.png`: Il `Close` icona delle notifiche di portata e contenuto per il pulsante Elimina.

### <a name="overlay-folder"></a>cartella /overlay

- `EngagementPageOverlay.cs`: La pagina di sovrapposizione responsabile per l'aggiunta di impegno raggiunga in app dell'interfaccia utente all'elemento figlio.
  
