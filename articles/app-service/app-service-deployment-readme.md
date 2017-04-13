<properties
    pageTitle="Distribuzione di applicazioni al servizio App Azure"
    description="Informazioni su come distribuire applicazioni per l'utilizzo del servizio di App"
    keywords="App, azure app servizio, distribuzione, distribuzione"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Cenni preliminari sulla distribuzione di Azure App servizio

Servizio App Azure fornisce una funzionalità ricca e integrata impostata per supportare la creazione di flussi di lavoro di distribuzione potente e flessibile. Distribuzione delle App è possibile sfruttare le opzioni che includono integrazione continua o controllo del codice sorgente locale di pubblicazione, WebDeploy e FTP. Il metodo consigliato per la distribuzione di produzione app è scambio di intervallo aperto di distribuzione. Bande orarie distribuzione rappresentano ambienti di prova e integrazione associati con le applicazioni di produzione. Bande orarie distribuzione possono essere configurati e destinate con il traffico web per la convalida e il traffico può essere scambiato su richiesta per la distribuzione di produzione senza ora verso il basso e automatizzato riscaldamento. È possibile automatizzare facilmente i passaggi del flusso di lavoro di distribuzione tramite prodotti di gestione di rilascio, ad esempio Visual Studio rilasciare Management. È utile per coordinazione con altre risorse soluzione (ad esempio archivio dati), ricorrenza e la replica su più unità di distribuzione. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
