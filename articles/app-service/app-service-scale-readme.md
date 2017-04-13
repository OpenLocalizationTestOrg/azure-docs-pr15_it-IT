<properties
    pageTitle="Servizio App Azure: Ridimensionamento delle applicazioni di servizio di App"
    description="Informazioni su vantaggi e svantaggi di ridimensionamento dell'applicazione del servizio di App."
    keywords="App, azure app servizio, piano di servizio app scalable, scala, Costo assistenza app"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Servizio App Azure: Ridimensionamento delle applicazioni di servizio di App

Le applicazioni di servizio App Azure possono [ottenere scala enorme](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Ridimensionamento di un'applicazione invece un problema complesso che non dispone di una soluzione "taglia unica". Scala correttamente l'applicazione sono sono 3 aree chiave che verranno inserita per il successo di applicazioni:

1. Informazioni sull'architettura dell'applicazione e il relativo weaknesses.
    * È stato l'applicazione? Senza informazioni sullo stato?
    * Quali sono tutti i componenti dell'applicazione?
        * Dove si trovano bottiglia nell'applicazione?
    * Quando si carica viene applicata all'applicazione in uso, quali verranno interrotte prima?
2. Comprendere i requisiti di prestazioni e carico previsti.
    * L'applicazione necessario mille utenti? o un milione?
    * Il traffico venga recapitata da un'unica posizione geografica o a livello globale?
    * Sono disponibili le variazioni stagionali? picchi di traffico?
    * La velocità di risposta dell'app? 1 secondo? 1 millisecondi?
3. Informazioni sulle e correttamente sfruttare la piattaforma hosting l'app.
    * Quali caratteristiche è consigliabile utilizzare per raggiungere gli obiettivi la scala?

In questa sezione utili per comprendere meglio tutti i fattori e assistenza scegliere una strategia che consente di sfruttare le funzionalità di servizio App necessarie per raggiungere gli obiettivi del scalabilità.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
