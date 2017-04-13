<properties 
    pageTitle="Configurazione di un Firewall di applicazione Web (WAF) per l'ambiente del servizio di App" 
    description="Informazioni su come configurare un firewall di applicazione web che precede l'ambiente App." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurazione di un Firewall di applicazione Web (WAF) per l'ambiente del servizio di App

## <a name="overview"></a>Panoramica ##
Web firewall applicazione come [WAF Barracuda per Azure](https://www.barracuda.com/programs/azure) che è disponibile in consente [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) sicura che delle applicazioni web controllando in ingresso il traffico web per bloccare l'inserimento SQL rappresenta, script tra siti, i caricamenti malware & applicazione DDoS e altri attacchi. Inoltre esamina le risposte dai server web back-end per prevenzione della perdita dei dati (DLP). In combinazione con l'isolamento e proporzioni dei caratteri aggiuntivi fornita da App servizio ambienti, in questo modo un ambiente ideale per aziende web critiche applicazioni che richiedono il richieste dannose e il traffico di volumi elevati.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Programma di installazione ##
Per questo documento che verrà configurato l'ambiente di servizio App dietro carico più bilanciate istanze di Barracuda WAF in modo che solo il traffico dal WAF contattarle l'ambiente di servizio App e non sarà possibile accedere dalla rete Perimetrale. È inoltre possibile Azure il traffico Manager che precede il nostro istanze Barracuda WAF per il bilanciamento del carico tra Azure data center e delle aree geografiche. Un diagramma di alto livello della configurazione sarà simile a quello mostrato sotto.

![Architettura][Architecture] 

> Nota: Con l'introduzione di [ILB supporto per l'ambiente servizio App](app-service-environment-with-internal-load-balancer.md), è possibile configurare ASE per essere accessibili dalla rete Perimetrale e solo disponibili per la rete privata. 

## <a name="configuring-your-app-service-environment"></a>Configurazione dell'ambiente servizio App ##
Per configurare un ambiente App fare riferimento alla [documentazione](app-service-web-how-to-create-an-app-service-environment.md) sull'argomento. Dopo avere inserito un ambiente App create, è possibile creare [Web Apps](app-service-web-overview.md), [App API](../app-service-api/app-service-api-apps-why-best-platform.md) e [App Mobile](../app-service-mobile/app-service-mobile-value-prop.md) in questo ambiente verrà tutti protetti dietro WAF vengono configurate nella sezione successiva.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurare il servizio Cloud WAF Barracuda ##
Barracuda ha un [articolo informazioni dettagliata](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sulla distribuzione relativa WAF in una macchina virtuale in Azure. Ma poiché si vuole ridondanza e non introdurre un singolo punto di errore, che si desidera distribuirle almeno 2 WAF istanza nel servizio Cloud stesso quando seguendo queste istruzioni.

### <a name="adding-endpoints-to-cloud-service"></a>Aggiungere i punti finali al servizio Cloud ###
Dopo avere inserito 2 o più istanze di macchine Virtuali WAF del servizio Cloud è possibile usare il [portale di Azure](https://portal.azure.com/) per aggiungere i punti finali HTTP e HTTPS che vengono utilizzati dall'applicazione in uso, come mostrato nell'immagine seguente.

![Configurare Endpoint][ConfigureEndpoint]

Se le applicazioni usano altri endpoint, assicurarsi di aggiungerlo all'elenco anche. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurazione Barracuda WAF tramite il portale di gestione ###
Barracuda WAF utilizza 8000 porte TCP per la configurazione tramite il portale di gestione. Poiché si dispone di più istanze di macchine virtuali WAF sarà necessario ripetere questi passaggi per ogni istanza macchine Virtuali. 


> Nota: Dopo aver con configurazione WAF, rimuovere l'endpoint TCP/8000 da tutte le macchine virtuali WAF per proteggere il WAF.

Aggiungere l'endpoint di gestione, come illustrato nell'immagine riportata di seguito per configurare il WAF Barracuda.

![Aggiungere Gestione Endpoint][AddManagementEndpoint]
 
Utilizzare un browser per esplorare l'endpoint di gestione del servizio Cloud. Se il servizio Cloud è denominato test.cloudapp.net, è necessario accedere endpoint passando alla http://test.cloudapp.net:8000. Verrà visualizzata una pagina di accesso come di sotto di accedere con le credenziali specificate in fase di installazione macchine Virtuali WAF.

![Pagina di accesso di gestione][ManagementLoginPage]

Una volta l'accesso verrà visualizzato un dashboard di nell'immagine seguente quello presenterà le statistiche di base informazioni sulla protezione WAF.

![Dashboard di gestione][ManagementDashboard]

Facendo clic sulla scheda servizi consentono di configurare il WAF per protegge i servizi. Per maggiori informazioni sulla configurazione del WAF Barracuda è possibile consultare [la documentazione](https://techlib.barracuda.com/waf/getstarted1). Nell'esempio seguente un'App Web di Azure che servono il traffico HTTP e HTTPS è stata configurata.

![Aggiunta di gestione dei servizi][ManagementAddServices]

> Nota: A seconda della configurazione delle applicazioni e quali caratteristiche in uso nell'ambiente servizio App, sarà necessario inoltrare il traffico per TCP porte 80 e 443, ad esempio, se è stato impostato IP SSL per un'App Web. Per un elenco delle porte di rete utilizzate in ambienti servizio App, fare riferimento alla sezione porte di rete di [controllare il traffico in ingresso documentazione](app-service-app-service-environment-control-inbound-traffic.md) .

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurazione di Microsoft Azure il traffico Manager (facoltativo) ##
Se l'applicazione è disponibile in più aree, quindi che si desidera caricare saldo loro dietro al [Gestore del traffico Azure](../traffic-manager/traffic-manager-overview.md). A tale scopo è possibile aggiungere un endpoint del [portale classica Azure](https://manage.azure.com) usando il nome del servizio Cloud per il WAF nel profilo di traffico Manager, come illustrato nell'immagine seguente. 

![Gestore del traffico Endpoint][TrafficManagerEndpoint]

Se l'applicazione richiede l'autenticazione, verificare di disporre di alcune delle risorse che non richiede alcuna autenticazione per il traffico Manager effettuare il ping la disponibilità dell'applicazione. È possibile configurare l'URL nella sezione Configura nel [portale classica Azure](https://manage.azure.com) , come illustrato di seguito.

![Configurare il traffico Manager][ConfigureTrafficManager]

Per inoltrare ping gestore del traffico dal WAF all'applicazione, è necessario configurazione sito Web traduzioni nel WAF Barracuda per inoltrare il traffico all'applicazione, come illustrato nell'esempio riportato di seguito.

![Sito Web traduzioni][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Protezione del traffico all'ambiente di servizio App con i gruppi di sicurezza di rete (NSG)##
Seguire la [documentazione per il traffico in ingresso di controllo](app-service-app-service-environment-control-inbound-traffic.md) per informazioni dettagliate sulla limitazione il traffico all'ambiente di servizio di App dalla WAF solo utilizzando l'indirizzo VIP del servizio Cloud. Ecco un comando di Powershell di esempio per eseguire questa operazione per la porta TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Sostituire il SourceAddressPrefix con l'indirizzo IP virtuale (VIP) del servizio Cloud di WAF.

> Nota: L'indirizzo VIP del servizio Cloud verrà modificate quando si elimina e ricrea il servizio Cloud. Assicurarsi di aggiornare l'indirizzo IP del gruppo di risorse di rete dopo l'installazione. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
