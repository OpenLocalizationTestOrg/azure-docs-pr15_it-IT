<properties
    pageTitle="Creare un'app web in un ambiente App"
    description="Informazioni su come creare applicazioni web e app piani di servizio in un ambiente App"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Creare un'app web in un ambiente App

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come creare applicazioni web e piani di servizio App in un [Ambiente servizio App](app-service-app-service-environment-intro.md) (base). 

> [AZURE.NOTE] Se si desidera imparare a creare un'app web ma non è necessario eseguire in un ambiente App, vedere [creare un'app web .NET](web-sites-dotnet-get-started.md) o uno le esercitazioni correlate per altre lingue e Framework.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che sia creato un ambiente App. Se si è già fatto che ancora, vedere [creare un ambiente App](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Creare un'app web

1. Nel [Portale di Azure](https://portal.azure.com/), fare clic su **Nuovo > Web + Mobile > App Web**. 

    ![][1]

2. Selezionare l'abbonamento.  

    Se si hanno più abbonamenti tenere presente che per creare un'app nel proprio ambiente servizio App, è necessario utilizzare lo stesso abbonamento utilizzato per la creazione dell'ambiente. 

3. Selezionare o creare un gruppo di risorse.

    *Gruppi di risorse* consentono di gestire le relative risorse Azure come unità e sono utili durante la definizione delle regole di *controllo dell'accesso basato sui ruoli* (RBAC) per le app. Per ulteriori informazioni, vedere [gestire le risorse Azure][ResourceGroups]. 

4. Selezionare o creare un piano di servizio di App.

    *Piani di servizio App* vengono gestiti i set di applicazioni web.  In genere quando si seleziona prezzi, il prezzo applicato viene applicato al piano di servizio App piuttosto che a singole applicazioni. In un ASE si paga per le istanze di elaborazione allocate per il ASE piuttosto che con la pagina ASP sono elencati.  Per aumentare il numero di istanze di un'app web ridimensionare le istanze del servizio App piano e viene applicata a tutte le applicazioni web in tale piano.  Alcune caratteristiche, ad esempio bande orarie sito o VNET integrazione dovranno restrizioni quantità all'interno del piano.  Per ulteriori informazioni, vedere [Panoramica sul piano di servizio App Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    È possibile identificare i piani di servizio App nel ASE esaminando il percorso in cui è indicato sotto il nome del piano.  

    ![][5]

    Se si desidera utilizzare un piano di servizio App già presente nel proprio ambiente servizio App, selezionarla. Se si desidera creare un nuovo piano di servizio di App, vedere la sezione seguente di questa esercitazione, [creare un piano di servizio App in un ambiente App](#createplan).

5. Immettere il nome per il web app e quindi fare clic su **Crea**. 

    Se il ASE utilizza un VIP esterni l'URL di un'app in un ASE è: [*NomeSito*]. [*nome dell'ambiente servizio App*]. p.azurewebsites.net invece di [*nome sito*]. azurewebsites.net
    
    Se il ASE utilizza un VIP interno quindi l'URL di un'app in ASE: [*NomeSito*]. [*sottodominio specificato durante la creazione di ASE*]   
    Dopo aver selezionato la pagina ASP durante la creazione di ASE verrà visualizzato il sottodominio aggiornare sotto il **nome**

## <a name="createplan"></a>Creare un piano di servizio di App

Quando si crea un piano di servizio App in un ambiente App, le scelte di lavoro sono diverse come non worker condiviso un ASE.  Worker che è necessario utilizzare sono quelli che sono stati assegnati alla ASE dall'amministratore.  Questo significa che per creare un nuovo piano, è necessario disporre dei collaboratori più assegnati al pool di lavoro ASE rispetto al numero totale di istanze di tutti i piani già in tale pool di lavoro.  Se non è sufficiente worker nel pool di lavoro di base per creare il piano, è necessario lavorare con l'amministratore ASE ottenerli aggiunto.

Un'altra differenza con i piani di servizio App ospitato in un ambiente di servizio App è la mancanza di prezzi selezione.  Quando si dispone di un ambiente App il pagamento viene effettuato per le risorse di elaborazione utilizzate dal sistema e non ha aggiunti in base alle tariffe per i piani in tale ambiente.  In genere quando si crea un piano di servizio App si seleziona un piano prezzo che determina le fatture.  Un ambiente App è essenzialmente un luogo privato nel punto in cui è possibile creare contenuto.  Pagando per l'ambiente e non per ospitare il contenuto.

Le istruzioni seguenti illustrano come creare un piano di servizio App durante la creazione di un'app web, come illustrato nella sezione precedente dell'esercitazione.

1. Fare clic su **Crea nuovo** nella selezione piano dell'interfaccia utente e specificare un nome per il piano così come si farebbe normalmente all'esterno di un ASE.

2. Selezionare ASE che si desidera utilizzare il selettore di posizione.

    Dato che un ambiente App è essenzialmente un percorso di distribuzione privata, viene illustrato in posizione. 

    ![][2]

    Dopo la selezione di un ASE nel selettore di posizione, la creazione di piano di servizio App dell'interfaccia utente gli aggiornamenti.  Il percorso verrà visualizzato il nome del sistema ASE e l'area geografica in e il selettore piano prezzi viene sostituito con un selettore di pool di lavoro.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Selezione di un pool di lavoro

In genere in Azure App servizio e all'esterno di un ambiente App sono disponibili 3 dimensioni di elaborazione disponibili con la selezione di un piano di prezzo dedicato.  Analogamente, per un ASE è possibile definire un massimo di 3 pool di collaboratori e specificare le dimensioni di calcolo che viene utilizzata il pool di lavoro.  Cosa che si intende per tenant del ASE è che invece di selezionare un piano prezzo con dimensioni di calcolo per il piano di servizio di App, selezionare quello che viene definito un *pool di lavoro*.  

La selezione del pool di lavoro dell'interfaccia utente indica le dimensioni di calcolo usata il pool di lavoro sotto il nome.  La quantità disponibile si intende calcolare quante istanze sono disponibili per l'utilizzo del pool.  Pool totale potrebbe effettivamente disporre di più istanze di questo numero, ma questo valore si riferisce a quanti non sono in uso.  Se è necessario regolare l'ambiente App per aggiungere altre calcolare risorse vedere [configurazione dell'ambiente servizio App](app-service-web-configure-an-app-service-environment.md).

![][4]

In questo esempio viene visualizzato solo due pool di lavoro. Ciò avviene perché l'amministratore ASE allocato solo host in tali pool di due lavoro.  Il terzo verrebbe visualizzati quando sono presenti macchine virtuali allocate al suo interno.  

## <a name="after-web-app-creation"></a>Dopo la creazione dell'applicazione web

Esistono alcune considerazioni per l'esecuzione di applicazioni web e gestione dei piani di servizio App in una base che è necessario prendere in considerazione.  

Come indicato in precedenza, il proprietario della ASE è responsabile per le dimensioni del sistema e di conseguenza sono inoltre responsabili per verificare che sia presente sufficiente per ospitare i piani di servizio App desiderati. Se sono non presenti dei collaboratori disponibili, non sarà possibile creare il piano di servizio di App.  Questo è true per scalabilità un'app web.  Se si ha bisogno di più istanze è necessario ottenere l'amministratore di ambiente del servizio di App per aggiungere altri colleghi.

Dopo aver creato il web app e piano di servizio App è utile anche per ridimensionarla in.  In un ASE è sempre necessario disporre almeno 2 istanze del piano di servizio di App per fornire la tolleranza per le app.  Ridimensionamento di un piano di servizio App in un ASE corrisponde normale tramite il piano di servizio App dell'interfaccia utente.  Per ulteriori informazioni sul ridimensionamento, [come ridimensionare un'app web in un ambiente App](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
