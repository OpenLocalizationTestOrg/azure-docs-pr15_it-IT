<properties 
    pageTitle="Panoramica di Silverlight SDK di Windows Phone" 
    description="Panoramica di Windows Phone Silverlight SDK per Azure impegno per dispositivi mobili"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Panoramica SDK Silverlight per Azure impegno per dispositivi mobili

Iniziare da qui per visualizzare i dettagli su come integrare Azure coinvolgimento Mobile in un Windows Phone Silverlight App. Se si desidera sperimentare prima di tutto, assicurarsi di che aver completato le [esercitazioni di 15 minuti](mobile-engagement-windows-phone-get-started.md).

Fare clic per visualizzare [Il contenuto SDK](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procedure di integrazione

1. Iniziare qui: [come integrare impegno Mobile nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Per le notifiche: [come integrare portata (notifiche) nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Contrassegnare l'implementazione di piano: [come usare il coinvolgimento Mobile avanzate tagging API nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Note sulla versione

###<a name="330-04192016"></a>3.3.0 (19/04/2016)
Parte di nuget *MicrosoftAzure.MobileEngagement* creare un pacchetto **v3.4.0**

-   Registri console aggiunto "TestLogLevel" API per abilitare/disabilitare/filtro emessi da SDK.

Per la versione precedente vedere le [note sulla versione completa](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procedure di aggiornamento

Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

È necessario eseguire alcune procedure notato diverse versioni di SDK. Vedere completare [Le procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure.md). Se, ad esempio la migrazione da 0.10.1 a 0.11.0 che è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1" quindi la procedura "da 0.10.1 a 0.11.0".

###<a name="from-200-to-330"></a>Da 2.0.0 a 3.3.0

####<a name="test-logs"></a>Log di test

Log degli console prodotti da SDK può essere attivato/disattivato/filtrati. Per personalizzare questo, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` un valore disponibile la `EngagementTestLogLevel` enumerazione, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Eseguire l'aggiornamento da versioni precedenti

Vedere [le procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure.md)
 
