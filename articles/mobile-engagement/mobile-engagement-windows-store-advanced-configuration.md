<properties
    pageTitle="Configurazione di assunzione App universale di Windows SDK avanzata"
    description="Opzioni avanzate di configurazione per Azure Mobile coinvolgimento delle App universale di Windows"                    
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configurazione di assunzione App universale di Windows SDK avanzata

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Questa procedura viene descritto come configurare le opzioni di configurazione del App Azure Mobile coinvolgimento Android.

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configurazione avanzata

### <a name="disable-automatic-crash-reporting"></a>Disattivare la segnalazione di arresto anomalo automatico

È possibile disattivare l'arresto anomalo automatica delle funzionalità di impegno report. Quindi, quando si verifica un'eccezione non gestita, impegno non ha alcun effetto.

> [AZURE.WARNING] Se si disattiva questa caratteristica, quindi quando si verifica un arresto anomalo non gestito nell'app, impegno non inviare che l'arresto anomalo **e** non viene chiusa la sessione e processi.

Per disattivare la segnalazione di arresto anomalo automatico, personalizzare la configurazione in base il modo in cui che è stato dichiarato:

#### <a name="from-engagementconfigurationxml-file"></a>Da `EngagementConfiguration.xml` file

Impostare un arresto anomalo report `false` tra `<reportCrash>` e `</reportCrash>` tag.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Da `EngagementConfiguration` oggetto in fase di esecuzione

Impostare un arresto anomalo report su false usando l'oggetto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Disattivare la segnalazione in tempo reale

Per impostazione predefinita, i report del servizio coinvolgimento registra in tempo reale. Se l'applicazione dei registri frequentemente, è consigliabile per i registri di buffer e per segnalare tutte contemporaneamente in base al regolare intervallo di tempo. Questo comando si chiama "modalità burst".

A tale scopo, chiamare il metodo:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argomento è un valore in **millisecondi**. Ogni volta che si desidera riattivare la registrazione in tempo reale, il metodo senza parametri o con il valore 0.

Modalità burst leggermente aumenta l'autonomia ma ha un impatto sul Monitor coinvolgimento: durata sessioni e processi tutti vengono arrotondati per la soglia burst (in questo modo, sessioni e processi inferiore soglia burst potrebbe non essere visibile). È consigliabile usare una soglia burst non è più di 30000 (30s). Registri salvati sono limitati a 300 elementi. Se l'invio è troppo lungo, è possibile perdere alcuni log.

> [AZURE.WARNING] Soglia burst non può essere configurata per un periodo minore di un secondo. In tal caso, il SDK Mostra una traccia con l'errore e reimposta automaticamente il valore predefinito zero secondi. In questo modo viene attivata SDK per segnalare i log in tempo reale.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
