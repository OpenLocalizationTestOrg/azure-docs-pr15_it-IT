<properties
    pageTitle="Integrazione con Windows SDK universale"
    description="Integrazione di universale di Windows SDK per Azure impegno per dispositivi mobili"                                     
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integrazione con Windows SDK universale di Azure assunzione per dispositivi mobili

Questo documento descrive tutte le integrazione e configurazione opzioni disponibili per Azure Mobile coinvolgimento Windows universale SDK.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è innanzitutto necessario completare le [esercitazioni di 15 minuti](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Caratteristiche avanzate

### <a name="reporting-features"></a>Funzionalità di creazione report
È possibile aggiungere queste caratteristiche:

1. [Creazione di report opzioni avanzate](mobile-engagement-windows-store-advanced-reporting.md)

2. [Opzioni di configurazione avanzate](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notifiche

[Come integrare portata (notifiche) nell'app universale di Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementazione piano tag:

[Come usare il coinvolgimento Mobile avanzate tagging API nell'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Note sulla versione

###<a name="340-04192016"></a>3.4.0 (19/04/2016)

-   Raggiungere miglioramenti sovrapposti.
-   Registri console aggiunto "TestLogLevel" API per abilitare/disabilitare/filtro emessi da SDK.
-   Iniziare a fisse notifiche in attività per la prima attività non è visualizzata scegliere App.

Per le versioni precedenti, vedere le [note sulla versione completa](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procedure di aggiornamento

Se è già stata integrata una versione precedente di impegno nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

Notato diverse versioni di SDK, è necessario eseguire alcune procedure. Vedere completare [Le procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md). Se, ad esempio la migrazione da 0.10.1 a 0.11.0 che è necessario innanzitutto seguire la procedura "da 0.9.0 a 0.10.1" quindi la procedura "da 0.10.1 a 0.11.0".

###<a name="from-330-to-340"></a>Da 3.3.0 a 3.4.0

####<a name="test-logs"></a>Log di test

Log degli console prodotti da SDK può essere attivato/disattivato/filtrati. Per personalizzare, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` a uno dei valori di dalla `EngagementTestLogLevel` enumerazione, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Risorse

Sovrapposizione portata è stata migliorata. Fa parte delle risorse di pacchetto NuGet SDK.

Durante l'aggiornamento alla nuova versione di SDK, è possibile scegliere se si desidera mantenere i file esistenti dalla cartella sovrapposizione delle risorse o meno:

* Se è funziona sovrapposizione precedente o si integra la `WebView` elementi manualmente, quindi è possibile scegliere di mantenere la chiusura di un file, continuerà a funzionare.
* Per aggiornare al nuovo sovrapposti, sostituire l'intero `overlay` cartella dalle risorse con quello nuovo dal pacchetto SDK (app UWP: dopo l'aggiornamento, è possibile ottenere la nuova cartella di sovrapposizione da % profiloutente %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Tramite la sovrapposizione nuova sovrascrive tutte le personalizzazioni apportate nella versione precedente.

### <a name="upgrade-from-older-versions"></a>Eseguire l'aggiornamento da versioni precedenti

Vedere [le procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md)
