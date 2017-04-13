<properties
    pageTitle="Confronto di servizio App, macchine virtuali, tessuti di servizio e servizi Cloud Azure | Microsoft Azure"
    description="Informazioni su come scegliere tra servizio App Azure, macchine virtuali, tessuti di servizio e servizi Cloud per l'hosting di applicazioni web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Confronto di servizio App, macchine virtuali, tessuti di servizio e servizi Cloud Azure

## <a name="overview"></a>Panoramica

Azure offre diversi modi per i siti web: [Servizio App Azure][], [macchine virtuali][], [Tessuti di servizio][]e [Servizi Cloud][]. In questo articolo consente di conoscere le opzioni e apportare la soluzione ideale per l'applicazione web.

Azure App servizio è la scelta migliore per la maggior parte delle applicazioni web. Distribuzione e gestione sono integrati la piattaforma, ridimensionare rapidamente siti per la gestione di traffico elevati carichi e gestione di bilanciamento del carico e il traffico di carico incorporati fornire disponibilità. Si spostarsi facilmente siti esistenti al servizio App Azure con uno [strumento di migrazione online](https://www.migratetoazure.net/), utilizzare un'app Apri origine della raccolta di applicazione Web o creare un nuovo sito utilizzando il framework e strumenti di propria scelta. La caratteristica [WebJobs][] è facile aggiungere processo in background elaborazione all'applicazione servizio web app.

Infrastruttura di servizio è un'ottima scelta se si sta creando una nuova app o riscrittura un'app esistente per utilizzare un'architettura di microservice. App che vengono eseguite in un pool condiviso di computer, è possibile avviare small e aumento di scala enorme con centinaia o migliaia di computer in base alle esigenze. Informazioni sullo stato servizi rendono più semplice in modo coerente e affidabile archiviare lo stato di app e servizio tessuti gestisce automaticamente servizio partizione, proporzioni dei caratteri e disponibilità dell'utente.  Servizio tessuti supporta anche WebAPI con interfaccia Web aperto per .NET (OWIN) e ASP.NET di base.  Rispetto al servizio di App, tessuti servizio vengono forniti anche più preciso o accesso diretto ai infrastruttura sottostante. È possibile remoto nel server o configurare le operazioni di configurazione del server. Servizi cloud è simile a infrastruttura di servizio in grado di controllo e facilità di utilizzo, ma è un servizio legacy e servizio tessuti è consigliabile per lo sviluppo di nuovi.

Se si dispone di un'applicazione esistente che richiede modifiche significative per l'esecuzione in App servizio o servizio tessuti, è possibile scegliere macchine virtuali per semplificare la migrazione nel cloud. Tuttavia, correttamente la configurazione, la protezione e gestione macchine virtuali richiede molto più tempo e personale IT rispetto al servizio di Azure App e servizio tessuti. Se si intende macchine virtuali di Azure, assicurarsi di che prendere in considerazione l'impegno di manutenzione necessaria per patch, aggiornare e gestire l'ambiente di macchine Virtuali. Azure macchine virtuali è infrastruttura-come-a-Service (IaaS), mentre il servizio di App e servizio tessuti piattaforma-come-a-Service (Paas). 

## <a name="features"></a>Confronto delle caratteristiche

Nella tabella seguente contiene un confronto le funzionalità del servizio di App, servizi Cloud, macchine virtuali e servizio tessuti per mettere la scelta migliore. Per informazioni aggiornate il contratto di servizio per ogni opzione, vedere [Azure i contratti di servizio](/support/legal/sla/).

Caratteristica|Servizio di App (applicazioni web)|Servizi cloud (ruoli web)|Macchine virtuali|Infrastruttura di servizio|Note
---|---|---|---|---|---
Distribuzione di quasi immediata|X|||X|La distribuzione di un'applicazione o un aggiornamento dell'applicazione in un servizio Cloud, o la creazione di una macchina virtuale richiede alcuni minuti almeno; distribuzione di un'applicazione di un'app web richiede secondi.
Passare a più grande computer senza Ridistribuisci|X|||X|
Istanze di server Web condividono contenuto e la configurazione, ovvero che non è necessario ridistribuire o riconfigurare quando si adatta.|X|||X|
Più ambienti di distribuzione (produzione e gestione temporanea)|X|X||X|Servizio tessuti consente di dispone di più ambienti per le app o per distribuire versioni diverse dell'app side-by-side.
Gestione degli aggiornamenti automatici del sistema operativo|X|X|||Gli aggiornamenti automatici del sistema operativo sono previste per rilasciare un'infrastruttura di servizio futura.
Cambio di piattaforma perfetta (spostarsi facilmente tra 32 bit e 64 bit)|X|X|||
Codice distribuito con fra, FTP|X||X||
Distribuire il codice di distribuzione Web|X||X||Servizi cloud supporta l'utilizzo della distribuzione Web per la distribuzione di aggiornamenti per le istanze dei singoli ruoli. Tuttavia, non è possibile utilizzare per la distribuzione iniziale di un ruolo e se si usa distribuzione Web per un aggiornamento è necessario distribuire separatamente per ogni istanza di un ruolo. Più istanze sono necessarie per idonei per il servizio Cloud per gli ambienti di produzione.
Supporto di WebMatrix|X||X||
Accedere a servizi come servizio Bus, lo spazio di archiviazione, Database SQL|X|X|X|X|
Host web o il livello di servizi web di un'architettura multilivello|X|X|X|X|
Intermedio host di un'architettura multilivello|X|X|X|X|Servizio di App web App con facilità ospitare un livello intermedio API REST e la caratteristica [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) possibile ospitare elaborazione processi in background. È possibile eseguire WebJobs in un sito Web dedicato per ottenere la scalabilità indipendente per il livello. La caratteristica di [API App](../app-service-api/app-service-api-apps-why-best-platform.md) preview fornisce ad altre caratteristiche per l'hosting di servizi REST.
Supporto di MySQL come servizio integrato|X|X|X||Servizi cloud possono integrare MySQL come servizio offerte del ClearDB, ma non come parte del flusso di lavoro portale Azure.
Supporto per ASP.NET classico ASP, Node, PHP, Python|X|X|X|X|Servizio tessuti supporta la creazione di un front-end web utilizzando [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) oppure è possibile distribuire qualsiasi tipo di applicazione (Node, Java, e così via) come [guest eseguibile](../service-fabric/service-fabric-deploy-existing-app.md).
Scalabilità a più istanze senza Ridistribuisci|X|X|X|X|Macchine virtuali di possibile aggiungere fino a più istanze, ma i servizi in esecuzione su di essi devono essere scritte per gestire questo scalabilità. È necessario configurare un bilanciamento del carico per indirizzare le richieste tra il computer e creare un gruppo di affinità per evitare il riavvio simultaneo di tutte le istanze a causa di errori di manutenzione o hardware.
Supporto per SSL|X|X|X|X|Per assistenza App web apps SSL per i nomi di dominio personalizzato solo per la modalità di base e Standard. Per informazioni sull'utilizzo di SSL con web apps, vedere [configurazione di un certificato SSL per un sito Web di Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Integrazione con Visual Studio|X|X|X|X|
Il debug remoto|X|X|X||
Codice distribuito con TFS|X|X|X|X|
Isolamento di rete con [Azure virtuali](/services/virtual-network/)|X|X|X|X|Vedere anche [integrazione virtuali di siti Web Azure](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Supporto per [Azure il traffico Manager](/services/traffic-manager/)|X|X|X|X|
Monitoraggio Endpoint integrata|X|X|X||
Accesso desktop remoto ai server||X|X|X|
Installare qualsiasi file MSI personalizzato||X|X|X|Servizio tessuti consente di ospitare qualsiasi file eseguibile come [guest eseguibile](../service-fabric/service-fabric-deploy-existing-app.md) oppure è possibile installare le app in macchine virtuali.
Possibilità di definire/eseguire attività di avvio||X|X|X|
Possibile ascoltare eventi ETW||X|X|X|

## <a name="scenarios"></a>Suggerimenti e scenari

Ecco alcuni scenari comuni di applicazione con suggerimenti per cui Azure potrebbe essere più appropriato per ogni opzione di hosting web.

- [Ho bisogno di un front-end web con l'esecuzione di applicazioni aziendali integrate con nel risorse locale back-end di database e dell'elaborazione in background.](#onprem)
- [È necessario un modo affidabile per ospitare il sito Web aziendale che scalabilità e raggiungono offerte globale.](#corp)
- [Si dispone di un'applicazione di IIS6 in esecuzione in Windows Server 2003.](#iis6)
- [Sono piccole aziende e sono necessarie conveniente per ospitare il sito, ma la crescita futura in considerazione.](#smallbusiness)
- [Sono web o progettazione grafica e si desidera progettare e creare siti web per i clienti.](#designer)
- [Sta migrazione applicazione multilivello con un front-end web nel cloud.](#multitier)
- [Applicazione dipende dal Windows altamente personalizzato o Linux ambienti e si desidera spostare nel cloud.](#custom)
- [Sito personale utilizza software open source e si vuole ospitare in Azure.](#oss)
- [Ha un'applicazione line-of-business che è necessario connettersi alla rete aziendale.](#lob)
- [Si vuole ospitare un API REST o servizio web per client mobili.](#mobile)


### <a id="onprem"></a>Ho bisogno di un front-end web con l'esecuzione di applicazioni aziendali integrate con nel risorse locale back-end di database e dell'elaborazione in background.

Servizio di App Azure è un'ottima soluzione per applicazioni aziendali complesse. Consente di sviluppare App ridimensionamento automatico in una piattaforma non bilanciate carico, vengono protetti con Active Directory e connettersi alle risorse locale. Facilita la gestione di tali applicazioni facilmente tramite un portale dei massimi e le API e consente di approfondire come clienti vengano utilizzati con gli strumenti di analisi di app. La caratteristica [Webjobs][] consente di eseguire processi in background e attività come parte del livello web, mentre la connettività ibrida e VNET funzionalità rendono più semplice effettuare la connessione al risorse locale. Azure servizio App sono disponibili contratto di servizio tre 9 per le applicazioni web e consente di:

* Esecuzione delle applicazioni in modo affidabile su una piattaforma cloud automatica patch automatico.
* Ridimensionamento automatico in una rete globale di Data Center.
* Eseguire il backup e ripristino di emergenza.
* Essere conforme ISO, SOC2 e PCI.
* Integrazione con Active Directory

### <a id="corp"></a>È necessario un modo affidabile per ospitare il sito Web aziendale che scalabilità e raggiungono offerte globale.

Servizio di App Azure è un'ottima soluzione per l'hosting di siti Web aziendale. Consente di ridimensionare rapidamente in modo semplice e rapido per rispondere alla domanda in una rete globale di Data Center di applicazioni web. Offre portata locale, tolleranza e gestione del traffico intelligente. Tutto su una piattaforma che fornisce gli strumenti di gestione dei massimi, consentendo di approfondire integrità del sito e il traffico di sito in modo semplice e rapido. Azure servizio App sono disponibili contratto di servizio tre 9 per le applicazioni web e consente di:

* Eseguire i siti Web in modo affidabile su una piattaforma cloud automatica patch automatico.
* Ridimensionamento automatico in una rete globale di Data Center.
* Eseguire il backup e ripristino di emergenza.
* Gestire i registri e il traffico gli strumenti integrati.
* Essere conforme ISO, SOC2 e PCI.
* Integrazione con Active Directory

### <a id="iis6"></a>Si dispone di un'applicazione di IIS6 in esecuzione in Windows Server 2003.

Servizio App Azure semplifica evitare i costi dell'infrastruttura di migrazione di applicazioni IIS6 precedenti. Microsoft ha creato [gli strumenti di migrazione facile da usare e indicazioni per la migrazione dettagliati](https://www.movemetowebsites.net/) che consentono di verificare la compatibilità e identificare le modifiche da apportare. Integrazione con Visual Studio, TFS e strumenti CMS comuni semplifica la distribuzione di applicazioni IIS6 direttamente al cloud. Una volta distribuito, il portale di Azur sono disponibili gli strumenti di gestione affidabili che consentono di scalare verso il basso per gestire i costi e per soddisfare richiedere in base alle esigenze. Con lo strumento di migrazione è possibile:

* In modo semplice e rapido eseguire la migrazione l'applicazione web di Windows Server 2003 legacy nel cloud.
* Scegliere di lasciare l'allegato SQL database locale per creare un'applicazione ibrida.
* Spostare automaticamente il database SQL insieme applicazione legacy.

### <a id="smallbusiness"></a>Sono piccole aziende e sono necessarie conveniente per ospitare il sito, ma la crescita futura in considerazione.

Servizio App Azure è un'ottima soluzione per questo scenario, è possibile iniziare a utilizzare gratuitamente e quindi aggiungere più funzionalità quando è necessario. Ogni app web gratuito fornito con un dominio fornito da Azure (*your_company*. azurewebsites.net), e la piattaforma include gli strumenti di distribuzione e gestione integrati, nonché una raccolta di applicazione che rendono più semplice per iniziare. Sono disponibili molti altri servizi e le opzioni di ridimensionamento che consentono al sito di aggiornato in base alle esigenze degli utenti maggiore. Con il servizio di App Azure, è possibile:

- Iniziare con il livello gratuito e quindi scalare in base alle esigenze.
- Usare la raccolta di applicazione per impostare rapidamente le applicazioni web più comuni, ad esempio WordPress.
- Aggiungere caratteristiche e servizi Azure aggiuntivi nell'applicazione in base alle esigenze.
- Proteggere il web app con HTTPS.

### <a id="designer"></a>Si è un web o progettazione grafica e si desidera progettare e creare siti Web per i clienti

Per gli sviluppatori web e ai designer dei siti, servizio App Azure si integra facilmente con una varietà di strumenti e Framework, incluso il supporto di distribuzione per fra e FTP e stretta integrazione con strumenti e servizi, ad esempio Visual Studio e Database SQL. Con il servizio di App, è possibile:

- Utilizzare gli strumenti della riga di comando per le [attività automatizzata][scripting].
- Utilizzo di lingue più comuni, ad esempio [.net][dotnet], [PHP][], [Node][nodejs]e [Python][].
- Selezionare tre diversi livelli di ridimensionamento per scalabilità a capacità molto elevata.
- Integrazione con altri servizi di Azure, ad esempio [Database SQL][sqldatabase], [Bus di servizio] [ servicebus] e [lo spazio di archiviazione][]o offerte dei partner dall' [Archivio di Azure][azurestore], ad esempio MySQL e MongoDB.
- Integrazione con strumenti come Visual Studio, fra, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Sta migrazione applicazione multilivello con un front-end web nel cloud

Se si esegue un'applicazione di più livelli, ad esempio un server web che si connette a un database, servizio App Azure è consigliabile che offre stretta integrazione con Database di SQL Azure. Ed è possibile usare la caratteristica WebJobs per l'esecuzione di processi back-end.

Scegliere servizio tessuti per uno o più i livelli se è più necessario controllare l'ambiente di server, ad esempio la possibilità di remote nel server o configurare le operazioni di configurazione del server.

Scegliere macchine virtuali per uno o più i livelli se si desidera utilizzare la propria immagine computer o eseguire software server o servizi che non è possibile configurare nel servizio tessuti.

### <a id="custom"></a>Applicazione dipende dal Windows altamente personalizzato o Linux ambienti e si desidera spostare nel cloud.

Se l'applicazione richiede complessa installazione o configurazione del software e il sistema operativo, macchine virtuali è la soluzione migliore. Con macchine virtuali, è possibile:

- Usare la raccolta di macchina virtuale per iniziare con un sistema operativo, ad esempio Windows o Linux e quindi personalizzarlo in base ai requisiti dell'applicazione.
- Creare e caricare un'immagine personalizzata di un server locale esistente per l'esecuzione in una macchina virtuale in Azure.

### <a id="oss"></a>Sito personale viene usata software open source e si vuole ospitare in Azure

Se il framework Apri origine è supportato nel servizio di App, le lingue e Framework necessari per l'applicazione vengono configurate automaticamente. Servizio di App consente di:

- Utilizzare più popolari Apri origine lingue, ad esempio [.NET][dotnet], [PHP][], [Node][nodejs]e [Python][].
- Configurare WordPress, Drupal, Umbraco, DNN e molte altre applicazioni web di terze parti.
- Eseguire la migrazione di un'applicazione esistente o crearne uno nuovo dalla raccolta dell'applicazione.

Se il framework Apri origine non è supportato nel servizio di App, è possibile eseguire una delle opzioni di hosting altri web Azure. Con macchine virtuali, installare e configurare il software sull'immagine del computer, che può essere Windows o Linux.

### <a id="lob"></a>Ha un'applicazione line-of-business che è necessario connettersi alla rete aziendale

Se si desidera creare un'applicazione line-of-business, il sito Web potrebbe richiedere accesso diretto ai servizi o i dati della rete aziendale. Questo è possibile sul servizio App, tessuti di servizio e macchine virtuali mediante il [servizio di rete virtuale Azure](/services/virtual-network/). Servizio di App è possibile utilizzare la [funzionalità di integrazione di VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), che consente le applicazioni Azure per l'esecuzione come se fossero nella rete aziendale.

### <a id="mobile"></a>Si vuole ospitare un API REST o un servizio web per client mobili

Servizi web basati su HTTP consentono di supportare una vasta gamma di client, inclusi i client mobili. Framework come API Web ASP.NET integrazione con Visual Studio per rendere più semplice creare e utilizzare i servizi di resto.  Questi servizi vengono esposti da un endpoint web, è possibile utilizzare qualsiasi tecnica su Azure di hosting web per supportare questo scenario. Tuttavia, App servizio è la scelta ideale per l'hosting API REST. Con il servizio di App, è possibile:

- Creare rapidamente [un'app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md) o [app API](../app-service-api/app-service-api-apps-why-best-platform.md) per ospitare il servizio web HTTP in una data center distribuito a livello globale di Azure.
- Eseguire la migrazione di servizi esistenti o crearne di nuovi.
- Ottenere contratto di servizio per la disponibilità con una singola istanza oppure scalabilità su più computer dedicato.
- Utilizzare il sito pubblicato per fornire le API REST a qualsiasi client HTTP, inclusi i client mobili.

> [AZURE.NOTE]
> Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account, passare a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile immediatamente creare un'app starter breve nel servizio App Azure gratuitamente. Nessuna carta di credito obbligatorio, senza impegni.

## <a id="nextsteps"></a>Passaggi successivi

Per ulteriori informazioni sulla web tre opzioni di hosting, vedere [Introduzione a Azure](../fundamentals-introduction-to-azure.md).

Per iniziare a utilizzare le opzioni selezionate per l'applicazione, vedere le risorse seguenti:

* [Servizio App Azure](/documentation/services/app-service/)
* [Servizi Cloud Azure](/documentation/services/cloud-services/)
* [Macchine virtuali di Azure](/documentation/services/virtual-machines/)
* [Infrastruttura di servizio](/documentation/services/service-fabric)

<!-- URL List -->

[Servizio App Azure]: /services/app-service/
[Servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=306052
[Macchine virtuali]: http://go.microsoft.com/fwlink/?LinkID=306053
[Infrastruttura di servizio]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Spazio di archiviazione]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
