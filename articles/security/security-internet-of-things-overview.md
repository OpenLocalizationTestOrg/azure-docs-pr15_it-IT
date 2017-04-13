<properties
   pageTitle="Cenni preliminari sulla sicurezza di Internet di elementi | Microsoft Azure"
   description=" Azure internet dei servizi di aspetti (IoT) offrono una vasta gamma di funzionalità. In questo articolo consente di imparare a proteggere le soluzioni IoT in Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>Cenni preliminari sulla sicurezza di Internet di elementi

Azure internet dei servizi di aspetti (IoT) offrono una vasta gamma di funzionalità. Questi servizi di voto enterprise consentono di:

- Raccolta dati da dispositivi
- Analizzare flussi dati di animazione
- Archiviare e grandi quantità di dati della query
- Visualizzare i dati in tempo reale e nella cronologia
- Integrazione con i sistemi di back office

Per offrire queste funzionalità, pacchetti di Azure IoT Suite insieme più servizi Azure con le estensioni personalizzate come soluzioni preconfigurate. Queste soluzioni preconfigurate sono implementazione base serie comuni IoT soluzione che consente di ridurre il tempo che necessario per fornire le soluzioni IoT. Usa IoT software development kit, è possibile personalizzare ed estendere queste soluzioni in base alle proprie esigenze. È anche possibile utilizzare queste soluzioni come esempi o modelli quando si sviluppano soluzioni IoT nuove.

La famiglia di Azure IoT è una potente soluzione adatti alle proprie esigenze IoT. Tuttavia, è importante upmost che le soluzioni IoT sono progettate con sicurezza dall'inizio. A causa del numero elevato di dispositivi IoT, qualsiasi incidente può diventare rapidamente un evento diffuso con conseguenze significative.

Per indicazioni utili proteggere le soluzioni IoT, saranno disponibili le informazioni seguenti.

## <a name="security-architecture"></a>Architettura di sicurezza

Durante la progettazione di un sistema, è importante conoscere i rischi per il sistema e aggiungere difesa appropriato di conseguenza, come il sistema è progettato e progettato. È particolarmente importante progettare il prodotto dall'inizio tenendo presente la sicurezza in quanto understanding come un pirata informatico potrebbe essere possibile danneggiare rende attuate le misure si sa appropriate sono attivi dall'inizio.

Per informazioni sull'architettura di sicurezza IoT leggendo [Internet di architettura di sicurezza elementi](../iot-suite/iot-security-architecture.md).

In questo articolo vengono illustrati i seguenti argomenti:

- [Protezione inizia con un modello di rischio](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Sicurezza in IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [Modellazione l'architettura di riferimento IoT Azure](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>Protezione da zero di

Il IoT presentano diverse difficoltà di sicurezza, privacy e conformità per le aziende in tutto il mondo. Diversamente da quanto succede tecnologia informatica tradizionale in cui questi problemi riguardano software e la modalità di implementazione IoT riguarda cosa accade quando l'informatica e mondo fisico convergenza. Per proteggere le soluzioni IoT, è necessario assicurare il provisioning sicura dei dispositivi, la connettività protetta tra i dispositivi e il cloud e protezione dei dati nel cloud durante l'elaborazione e lo spazio di archiviazione. Uso rispetto a tale funzionalità, tuttavia, sono dispositivi con risorse limitate, distribuzione geografica delle distribuzioni e un numero elevato di dispositivi all'interno di una soluzione.

Viene illustrato come gestire la sicurezza in queste aree leggendo [protezione Internet di elementi da zero di](../iot-suite/securing-iot-ground-up.md).

L'articolo vengono illustrati i seguenti argomenti:

- [Infrastruttura sicura sin](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure infrastruttura IoT sicura per l'azienda](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Procedure consigliate

Protezione di un'infrastruttura IoT richiede una strategia di protezione in profondità accurata. Partire da proteggere i dati nel cloud, in protezione l'integrità dei dati in transito su internet e fornire la possibilità di effettuare il provisioning in modo sicuro di dispositivi, ogni layer che si basa maggiori garanzie di protezione dell'infrastruttura complessiva.

Per informazioni sulla sicurezza di Internet di elementi procedure consigliate leggendo [procedure consigliate per la protezione Internet di elementi](../iot-suite/iot-security-best-practices.md).

L'articolo vengono illustrati i seguenti argomenti:

- [IoT hardware produttore/integratore](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Sviluppo di soluzioni IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Distributore soluzione IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Operatore di soluzione IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
