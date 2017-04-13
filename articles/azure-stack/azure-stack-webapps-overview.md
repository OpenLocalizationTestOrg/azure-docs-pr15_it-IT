<properties
    pageTitle="Panoramica di App Web di Azure Stack | Microsoft Azure"
    description="Panoramica di Web Apps in pila Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Panoramica di Azure Stack Web App
    
> [AZURE.NOTE] Le informazioni seguenti si applicano solo alle distribuzioni TP1 Stack Azure.

Azure Stack Web App è il primo elemento l'offerta di servizi di App Azure portata in pila Azure. Il programma di installazione di Azure Stack Web Apps creerà un'istanza di ciascuno dei cinque tipi di ruolo ricoperto dai partecipanti necessari e creerà anche un file server. Sebbene sia possibile aggiungere più istanze per tutti i tipi di ruolo, tenere presente che non vi è spazio per le macchine virtuali nella Technical Preview 1. Le funzionalità corrente per Azure Stack Web Apps sono principalmente funzionalità foundation necessari per gestire le app web di sistema e l'host.

![Azure Stack servizio App Web Apps nel Azure impilare portale][1]

## <a name="limitations-of-the-technical-preview"></a>Limitazioni di Technical Preview

Non è disponibile alcun supporto per l'anteprima di Azure Stack App Service Release. Non inserire carichi di lavoro di produzione in questa versione di anteprima. Esiste anche un aggiornamento tra Azure Stack App Service Release di anteprima. Gli scopi principali di queste versioni di anteprima vengono per mostrare state rese disponibili e per ottenere il feedback. 

## <a name="what-is-an-app-service-plan"></a>Che cos'è un piano di servizio App?

Il provider di risorse Azure Stack Web Apps Usa lo stesso codice che utilizza la caratteristica di Azure Web Apps nel servizio App Azure. Di conseguenza, alcuni concetti comuni sono patrimonio che descrive. Nelle applicazioni Web, il contenitore prezzo per le applicazioni web viene definito il piano di servizio di App. Rappresenta il set di macchine virtuali dedicate utilizzato per contenere le app. All'interno di una determinata sottoscrizione, è possibile impostare più piani di servizio di App. Questo vale anche in Azure Stack Web Apps. 

In Azure, esistono worker dedicato e condivise. Un lavoro condiviso supporta l'hosting app web multi-tenant ad alta densità ed esiste solo un gruppo di collaboratori condivisi. Dedicati sono solo utilizzati da un tenant e sono disponibili in tre formati: piccole, medie e grandi dimensioni. Le esigenze dei clienti in locale non possono essere descritte sempre utilizzando tali termini. In Azure Stack Web Apps, gli amministratori di provider di risorse possono definire i livelli che si desidera rendere disponibili in modo che hanno più gruppi di collaboratori condivisi o diversi gruppi di collaboratori dedicati in base alle loro esigenze di hosting web univoco. Utilizza le definizioni di livello di lavoro, può quindi definire le proprie prezzi SKU.

## <a name="portal-features"></a>Funzionalità del portale

Come vale anche con back-end, Azure Stack Web Apps utilizza l'interfaccia utente stesso che utilizza Azure Web Apps. Alcune caratteristiche sono disabilitate e non sono ancora funzionale in pila Azure. Ciò avviene perché le aspettative specifiche Azure o servizi che richiedono il queste caratteristiche non sono ancora disponibili in pila Azure. 

## <a name="next-steps"></a>Passaggi successivi

- [Prima di iniziare a Azure Stack Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Installare il Provider di risorse di applicazioni Web](azure-stack-webapps-deploy.md)

È anche possibile provare altre [piattaforma come un servizio di assistenza (PaaS)](azure-stack-tools-paas-services.md), come il [provider di risorse di SQL Server](azure-stack-sql-rp-deploy-short.md) e un [provider di risorse MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
