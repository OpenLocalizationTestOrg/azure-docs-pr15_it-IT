<properties
    pageTitle="Controllare il traffico di Azure web app con Azure il traffico Manager"
    description="In questo articolo fornisce informazioni di riepilogo per Azure il traffico Manager in relazione a Azure web apps."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controllare il traffico di Azure web app con Azure il traffico Manager

> [AZURE.NOTE] In questo articolo fornisce informazioni di riepilogo per Microsoft Azure il traffico Manager in relazione ad Azure App servizio Web Apps. Sono disponibili ulteriori informazioni sulla gestione del traffico di Azure stesso, visitare i collegamenti alla fine di questo articolo.

## <a name="introduction"></a>Introduzione
È possibile utilizzare gestore del traffico Azure per controllare la modalità di distribuzione richieste dai client web alle applicazioni web nel servizio App Azure. Quando i punti finali app web vengono aggiunti a un profilo di gestore del traffico Azure, Azure il traffico Manager tiene traccia dello stato delle applicazioni web (in esecuzione, arrestato o eliminati) in modo che è possibile decidere che tali endpoint deve ricevere il traffico.

## <a name="load-balancing-methods"></a>Metodi di bilanciamento del carico
Gestione di traffico Azure utilizza tre metodi di bilanciamento del carico diversi. Descritti di seguito come si riferiscono a Azure web apps.

* **Failover**: se si dispone web app duplicati in aree diverse, è possibile utilizzare questo metodo per configurare un'applicazione web in tutto il traffico client web di servizio e configurare un'altra web app in un'area diversa per gestire il traffico nel caso in cui il primo web app non è più disponibile.

* **Circolari**: se si dispone web app duplicati in aree diverse, è possibile utilizzare questo metodo per distribuire il traffico identico tra le applicazioni web in diverse aree geografiche.

* **Prestazioni**: metodo delle prestazioni di rete distribuisce il traffico in base a più breve tempo di andata e ritorno ai client. Il metodo di prestazioni può essere utilizzato per web apps all'interno dell'area stesso o in aree diverse.

##<a name="web-apps-and-traffic-manager-profiles"></a>Gestore del traffico profili e Web App
Per configurare il controllo del traffico web app, creare un profilo in Azure il traffico Manager, che viene utilizzato uno dei tre metodi di caricamento bilanciamento del carico descritti in precedenza e quindi aggiungere gli endpoint (in questo caso, web App) per il quale si desidera controllare il traffico al profilo. Lo stato di app web (in esecuzione, arrestato o eliminati) regolarmente è comunicato al profilo in modo da Azure il traffico Manager può indirizzare il traffico di conseguenza.

Quando si utilizza gestore del traffico Azure con Azure, tenere presenti i seguenti punti:

* Distribuzioni web app solo all'interno dell'area stessa, Web Apps fornisce già failover e funzionalità circolari indipendentemente dalla modalità di applicazione web.

* Per distribuzioni nella stessa regione che utilizzano Web Apps in combinazione con un altro servizio cloud Azure, è possibile combinare entrambi i tipi di endpoint per abilitare gli scenari di distribuzione ibrida.

* È possibile specificare un endpoint di app web regione solo in un profilo. Quando si seleziona un'app web come un endpoint per una regione, rimanente web App in quell'area diventano disponibili per la selezione per tale profilo.

* I punti finali app web specificati in un profilo di Azure il traffico Manager verranno visualizzate sotto la sezione **Domain Names** nella pagina Configura per l'applicazione web nel profilo, ma non è possibile configurare non esiste.

* Dopo aver aggiunto un'app web a un profilo, l' **URL del sito** nel Dashboard di pagina del portale dell'applicazione web verrà visualizzato l'URL di dominio personalizzato di web app se è stata configurata una. In caso contrario, verrà visualizzato l'URL di profilo di gestore del traffico (ad esempio `contoso.trafficmgr.com`). Sia il nome di dominio diretto del web app e l'URL di gestione di traffico saranno visibile nella pagina di configurazione dell'applicazione web nella sezione **Domain Names** .

* I nomi di dominio personalizzato funzionano come previsto, ma oltre ad aggiungere loro alle App web, è necessario configurare anche la mappa DNS in modo che puntino all'URL del traffico Manager. Per informazioni su come configurare un dominio personalizzato per un'app web Azure, vedere [configurazione di un nome di dominio personalizzato per un sito web Azure](web-sites-custom-domain-name.md).

* È possibile aggiungere solo le applicazioni web che sono in modalità standard per un profilo di Azure il traffico Manager.

## <a name="next-steps"></a>Passaggi successivi

Per una concettuale e tecnica Panoramica di gestione di traffico di Azure, vedere [Panoramica di gestione di traffico](../traffic-manager/traffic-manager-overview.md).

Per ulteriori informazioni sull'utilizzo di gestione di traffico con Web Apps, vedere che il post di blog [Mediante il gestore di traffico Azure con siti Web di Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Azure il traffico Manager ora possibile integrare con i siti Web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
