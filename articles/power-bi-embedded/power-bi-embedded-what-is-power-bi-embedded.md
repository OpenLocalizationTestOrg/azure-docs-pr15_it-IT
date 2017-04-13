<properties
   pageTitle="Che cos'è Microsoft Power BI incorporato?"
   description="Power BI incorporata consente di integrare i report di Power BI al web e alle applicazioni per dispositivi mobili in modo che non è necessario creare soluzioni personalizzate per visualizzare i dati per gli utenti"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="what-is-microsoft-power-bi-embedded"></a>Che cos'è Microsoft Power BI incorporato?

Con **Power BI incorporato**, è possibile integrare i report di Power BI direttamente nel web o applicazioni mobili.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI incorporato è un **servizio di Azure** che consente agli sviluppatori di app di superficie esperienze di dati di Power BI all'interno delle applicazioni e fornitori di software indipendenti. Gli sviluppatori è già stata creata applicazioni che avranno i propri utenti e distinti set di caratteristiche. Queste applicazioni possono accadere anche disporre di alcuni elementi di dati incorporato come grafici e rapporti possono ora di alimentazione Microsoft Power BI incorporato. Gli utenti non necessario un account di Power BI per usare l'app. Continuano a eseguire l'accesso a un'applicazione come prima, visualizzare e interagire con Power BI reporting esperienza senza qualsiasi licenza aggiuntiva.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Gestione delle licenze per Microsoft Power BI incorporato

Nel modello di utilizzo di **Microsoft Power BI incorporato** , le licenze di Power BI non è responsabilità dell'utente finale.  Se, tuttavia, **esegue il rendering** acquistato dello sviluppatore dell'app che utilizza gli elementi visivi e addebitate per l'abbonamento a cui appartiene le risorse.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI incorporato modello concettuale

![](media\powerbi-embedded-whats-is\model.png)

Come qualsiasi altro servizio Azure vengono effettuato il provisioning di risorse per Power BI incorporato tramite l' [API ARM Azure](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso, la risorsa che viene effettuato il provisioning è una **Raccolta di area di lavoro di Power BI**.

## <a name="workspace-collection"></a>Insieme di un'area di lavoro

Un **Insieme di area di lavoro** è il contenitore di Azure principale per le risorse che contiene 0 o altre **aree di lavoro**.  Un' **area di lavoro** **raccolta** include tutte le proprietà di Azure standard, nonché le operazioni seguenti:

-   **Tasti di scelta** : tasti utilizzati quando si chiama in modo sicuro le API di Power BI (descritto in una sezione successiva).
-   **Gli utenti** : gli utenti di Azure Active Directory (AAD) che dispone dei diritti di amministratore per gestire la raccolta di area di lavoro di Power BI tramite il portale di Azure o ARM API.
-   **L'area geografica** : durante il provisioning di un **Insieme di area di lavoro**, è possibile selezionare un'area di effettuare il provisioning. Per ulteriori informazioni, vedere [Le aree di Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Area di lavoro

Un' **area di lavoro** è un contenitore di contenuti su Power BI, che possono includere set di dati, report e dashboard. Un' **area di lavoro** è vuota al momento della creazione. Durante l'anteprima, è necessario creare tutto il contenuto tramite Power BI Desktop e viene caricata una delle aree di lavoro utilizzando le [API REST di Power BI](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Utilizzo delle aree di lavoro e alle raccolte di un'area di lavoro
**Area di lavoro raccolte** e **le aree di lavoro** sono contenitori di contenuto che vengono utilizzati e organizzati in qualunque modo migliore rientri la progettazione dell'applicazione che si creano. È possibile che si può disporre il relativo contenuto molti modi diversi. È possibile scegliere di inserire tutto il contenuto all'interno di un'area di lavoro e quindi usare in un secondo momento i token di app per suddividere ulteriormente il contenuto tra i clienti. È anche possibile mettere tutti i clienti nelle aree di lavoro distinti in modo che alcune prevede la separazione tra di esse. In alternativa, è possibile scegliere di organizzare gli utenti per area geografica anziché in base al cliente. Questo design flessibile consente di scegliere il modo migliore per organizzare il contenuto.

## <a name="cached-datasets"></a>Set di dati memorizzati nella cache

Set di dati memorizzati nella cache può essere utilizzato in anteprima.  Tuttavia, non è possibile aggiornare i dati memorizzati nella cache una volta caricata in **Microsoft Power BI incorporato**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticazione e l'autorizzazione con i token di app

**Microsoft Power BI incorporata** rinvia all'applicazione per eseguire l'autenticazione utente necessari e l'autorizzazione. Non è necessario esplicito che gli utenti finali essere clienti di Azure Active Directory (Azure Active Directory).  Se, tuttavia, l'applicazione esprime a **Microsoft Power BI incorporata** autorizzazione per il rendering di un report di Power BI tramite **Token di autenticazione dell'applicazione (App token)**.  Questi **Token App** vengono creati in base alle esigenze quando l'app richiede di visualizzare un report.  Vedere [i token di App](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Token di autenticazione dell'applicazione (App token)** vengono utilizzati per l'autenticazione **Microsoft Power BI incorporato**.  Esistono tre tipi di **Token App**:

1.  Il provisioning di token - utilizzati durante il provisioning di una nuova **area di lavoro** in un' **Area di lavoro Raccolta**
2.  Sviluppo token - utilizzato quando le chiamate direttamente all' **API REST di Power BI**
3.  Incorporare i token - usati per effettuare chiamate per il rendering di un report in iframe incorporata

Questi token vengono utilizzati per le diverse fasi delle interazioni con **Microsoft Power BI incorporato**.  I token sono progettati in modo che è possibile delegare le autorizzazioni dell'App Power BI. Per ulteriori informazioni, vedere [Flusso Token di App](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Vedere anche
- [Scenari comuni di Microsoft Power BI incorporata](power-bi-embedded-scenarios.md)
- [Introduzione a Microsoft Power BI incorporata](power-bi-embedded-get-started.md)
