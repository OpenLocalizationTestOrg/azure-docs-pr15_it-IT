<properties
   pageTitle="Manager delle risorse servizi supportati | Microsoft Azure"
   description="Descrive il provider di risorse che supportano Manager delle risorse, gli schemi e versioni API disponibili e le aree geografiche che possono contenere le risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provider di gestione delle risorse, aree, versioni API e gli schemi

Gestione risorse di Azure fornisce un nuovo metodo distribuire e gestire i servizi che costituiscono le applicazioni. La maggior parte, ma non tutti i servizi di supporto tecnico Manager delle risorse e alcuni servizi supportano solo parzialmente Manager delle risorse. In questo argomento fornisce un elenco di provider di risorse supportati per gestione di risorse di Azure.

Quando si distribuisce le risorse, è necessario sapere quali aree di tali risorse di supporto e le versioni di API sono disponibili per le risorse. La sezione [aree supportati](#supported-regions) viene illustrato come scoprire quali aree di lavoro per la sottoscrizione e le risorse. Nella sezione [versioni supportate API](#supported-api-versions) viene illustrato come determinare quali versioni di API è possibile utilizzare.

Per vedere quali servizi sono supportati nel portale di Azure e portale classica, vedere [grafico Azure della disponibilità al portale](https://azure.microsoft.com/features/azure-portal/availability/). Per vedere quali servizi spostamento risorse di supporto, vedere [spostare le risorse nuovo gruppo di risorse o l'abbonamento](resource-group-move-resources.md).

Le tabelle seguenti elencano quali Microsoft Servizi supporto distribuzione e gestione tramite Gestione risorse e che non sono. Il collegamento nella colonna **Modelli Guida introduttiva** invia una query all'archivio modelli Guida introduttiva di Azure per il provider di risorse specificato. Guida introduttiva modelli vengono aggiunti e aggiornati di frequente. La presenza di un collegamento per un particolare servizio non necessariamente che la query restituisca i modelli dal repository. Sono inoltre disponibili molti provider di terze parti risorse che supportano Manager delle risorse. Si imparerà visualizzare tutti i provider di risorse nella sezione [tipi e dei gestori di risorse](#resource-providers-and-types) .


## <a name="compute"></a>Calcolare

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------------------------ |-------- | ------ | ------ |
| Batch   | Sì     | [Batch resto](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Contenitore | Sì | [Servizio contenitore resto](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Servizi di ciclo di vita Dynamics | Sì  |      |        |  |
| Set di scala | Sì | [Scala imposta resto](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| Infrastruttura di servizio | Sì  | [Servizio tessuti resto](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Macchine virtuali | Sì | [RESTO MACCHINE VIRTUALI](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Macchine virtuali (classica) | Limitato | - | - | - |
| App remote | No      | -  | - | - |
| Servizi cloud (classica) | Limitata (vedere sopra) | - | - | - |

Macchine virtuali (classico) fa riferimento a risorse distribuite tramite il modello di distribuzione classico, invece che tramite il modello di distribuzione di Manager delle risorse. In generale, queste risorse non supportano operazioni di gestione risorse, ma alcune operazioni che sono state abilitate. Per ulteriori informazioni su questi modelli di distribuzione, vedere [distribuzione classica e gestione di informazioni sulle risorse](resource-manager-deployment-model.md). 

Servizi cloud (classici) possono essere utilizzati con altre risorse classiche; Tuttavia, le risorse classiche non trarre vantaggio da tutte le funzionalità di gestione risorse e non sono un'opzione valida per soluzioni future. Se, tuttavia, provare a modificare l'infrastruttura di applicazione per utilizzare le risorse dagli spazi dei nomi Microsoft.Compute, Microsoft.Storage e Microsoft.Network.


## <a name="networking"></a>Rete

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | -------  | -------- | ------ | ------ |
| Gateway di applicazioni | Sì | [Applicazione Gateway resto](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Sì | [RESTO DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Sì  | [ExpressRoute resto](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Bilanciamento del carico | Sì  | [Servizio di bilanciamento del carico resto](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gestore del traffico | Sì | [POSIZIONARE il traffico Manager](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Reti virtuali | Sì| [Virtuali resto](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gateway VPN | Sì | [Gateway di rete resto](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connessioni](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Spazio di archiviazione

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Spazio di archiviazione | Sì  | [Spazio di archiviazione resto](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Account di archiviazione](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Sì  |         |        |  |

## <a name="databases"></a>Database

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sì  | [DocumentDB resto](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis Cache | Sì |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Database SQL | Sì | [Database SQL di resto](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [Anteprima 01 di 04 2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL Data Warehouse | Sì |   |      |


## <a name="web--mobile"></a>Web e Mobile

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| API App | Sì |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [API App](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gestione dell'API | Sì  | [Gestione dell'API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Contenuto del moderatore | Sì |   |   |   |
| Funzione App | Sì |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| App logica | Sì   | [API REST di servizio del flusso di lavoro](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| App per dispositivi mobili | Sì |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Impegni per dispositivi mobili | Sì  |  [Dispositivi mobili coinvolgimento resto](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Ricerca | Sì  | [Ricerca resto](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Web App | Sì |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Analitica

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | -------  | -------- | ------ | ------ |
| Catalogo dati | Sì  | [Catalogo dati resto](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Dati Factory | Sì | [Dati Factory resto](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Dati Lake Analitica | Sì |   |   [Anteprima 01 di 10 2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Archivio Lake dati. | Sì  | [Archivio dati Lake resto](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [Anteprima 01 di 10 2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Sì | [HDInsights resto](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Apprendimento | Sì | [Apprendimento resto](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [Anteprima 01 di 05 2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Flusso Analitica | Sì  | [Vapore Analitica resto](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Sì | [Power BI incorporato resto](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Business Intelligence

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| Servizi cognitivi | Sì |  | [Anteprima 01 di 02 2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet di elementi

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| Hub di evento | Sì  | [Evento Hub resto](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Sì | [Hub IoT resto](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Hub di notifica | Sì | [Notifica Hub resto](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Elementi multimediali e CDN

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| RETE CDN | Sì | [RESTO CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servizio di supporto | Sì | [Servizi multimediali di resto](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Integrazione ibrido

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| Servizi BizTalk | Sì |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Servizio di recupero | Sì | [Ripristino del sito resto](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Bus di servizio | Sì | [Servizio Bus resto](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identità e la gestione degli accessi 

Azure Active Directory interagisce con Gestione risorse per abilitare il controllo dell'accesso basato sui ruoli per l'abbonamento. Per informazioni sull'utilizzo di controllo dell'accesso basato sui ruoli e Active Directory, vedere [Controllo dell'accesso basato sui ruoli di Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Servizi di sviluppo 

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| Informazioni dettagliate sui applicazione | Sì  | [Approfondimenti resto](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Sì  |          |        |  |
| Esercitazioni DevTest | Sì |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Account di Visual Studio | Sì   |          | [26 di 02 2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Gestione e la sicurezza

| Servizio | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------- | ------ | ------ |
| Automazione | Sì | [Automazione resto](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Archivio di chiave | Sì | [Archivio chiave resto](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Archivio di chiave](resource-manager-template-keyvault.md)<br />[Segreto archivio chiave](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Approfondimenti operativi | Sì |          |        |  |
| Utilità di pianificazione | Sì  | [Utilità di pianificazione resto](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Protezione (preview) | Sì | [Protezione resto](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Manager delle risorse

| Caratteristica | Manager delle risorse abilitato | API REST | Schema | Guida introduttiva modelli |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorizzazione | Sì | [Blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controllo dell'accesso basato sui ruoli](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Blocco di risorsa](resource-manager-template-lock.md)<br />[Assegnazione di ruolo](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Risorse | Sì | [Risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Collegamenti a risorse](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Tipi e dei gestori di risorse

Quando la distribuzione delle risorse, è spesso necessario recuperare le informazioni sui tipi di provider di risorse. È possibile recuperare questa informazione tramite API REST, PowerShell Azure o CLI Azure.

Per lavorare con un provider di risorse, è necessario che sia registrato il provider di risorse con l'account. Per impostazione predefinita, molti provider di risorse vengono automaticamente registrati; Tuttavia, potrebbe essere necessario registrare manualmente alcuni provider di risorse. Gli esempi riportati di seguito viene illustrato come ottenere lo stato di registrazione di un provider di risorse e registrare il provider di risorse, se necessario.

### <a name="portal"></a>Portale

È possibile vedere facilmente un elenco di provider di risorse supportati con la procedura seguente:

1. Selezionare **le autorizzazioni**.

    ![le autorizzazioni](./media/resource-manager-supported-services/my-permissions.png)

2. Selezionare **lo stato di provider di risorse**

    ![stato di provider di risorse](./media/resource-manager-supported-services/resource-provider-status.png)

3. Per l'abbonamento, vedere l'elenco completo dei provider di risorse.

    ![provider di risorse dell'elenco](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API REST

Per ottenere tutte le risorse disponibili provider, inclusi i tipi, posizioni, versioni API e lo stato di registrazione, utilizzare l'operazione di [tutti i provider di risorse dell'elenco](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Se è necessario registrare un provider di risorse, vedere [registrare un abbonamento con un provider di risorse](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

Nell'esempio seguente viene illustrato come ottenere tutti i provider di risorse disponibili.

    Get-AzureRmResourceProvider -ListAvailable
    

Nell'esempio seguente viene illustrato come ottenere i tipi di risorse per un provider di risorse specifico.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
L'output è simile a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Per registrare un provider di risorse, specificare lo spazio dei nomi:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI Azure

Nell'esempio seguente viene illustrato come ottenere tutti i provider di risorse disponibili.

    azure provider list
    
L'output è simile a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

È possibile salvare le informazioni per un provider di risorse specifico in un file con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Per registrare un provider di risorse, specificare lo spazio dei nomi:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Aree supportate

Quando la distribuzione delle risorse, in genere è necessario specificare un'area per le risorse. Gestione risorse è supportato in tutti i paesi, ma le risorse che si distribuisce potrebbero non essere supportate in tutti i paesi. Inoltre, potrebbero esserci limitazioni l'abbonamento che consentono di usare alcune aree che supportano la risorsa. Queste limitazioni possono essere dovute al fiscale problemi di proprio paese o il risultato di un criterio inserito dall'amministratore della sottoscrizione da utilizzare solo determinate aree geografiche. 

Per un elenco completo di tutte le aree supportati per tutti i servizi di Azure, vedere [servizi per area geografica](https://azure.microsoft.com/regions/#services). Tuttavia, questo elenco può includere aree che non supporta l'abbonamento. È possibile determinare le aree geografiche per un determinato tipo che supporta l'abbonamento tramite il portale, API REST, PowerShell oppure CLI Azure.

### <a name="portal"></a>Portale

È possibile visualizzare le aree supportate per un tipo di risorsa tramite la procedura seguente:

1. Selezionare **altri servizi** > **Esplora risorse**.

    ![Esplora risorse](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Aprire il nodo **provider** .

    ![Selezionare provider](./media/resource-manager-supported-services/select-providers.png)

3. Selezionare un provider di risorse e visualizzare le aree geografiche e supportati versioni API.

    ![provider di visualizzazione](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST

Per scoprire quali aree sono disponibili per un determinato tipo di abbonamento, utilizzare l'operazione di [tutti i provider di risorse dell'elenco](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

Nell'esempio seguente viene illustrato come accedere alle aree supportate per i siti web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
L'output è simile a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI Azure

Nell'esempio seguente restituisce tutti i percorsi supportati per ogni tipo di risorsa.

    azure location list

È inoltre possibile filtrare i risultati di posizione con un'utilità JSON come [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Che restituisce:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versioni API supportate

Quando si distribuisce un modello, è necessario specificare una versione dell'API da utilizzare per la creazione di ogni risorsa. La versione dell'API corrisponde a una versione di operazioni API REST rilasciati dal provider di risorse. Come un provider di risorse consente nuove caratteristiche, rilascia una nuova versione dell'API REST. La versione dell'API specificare nel modello scelto influisce le proprietà che è possibile specificare nel modello. In generale, si desidera selezionare l'ultima versione dell'API durante la creazione di modelli. Per i modelli esistenti, è possibile decidere se si desidera continuare a usare una versione precedente di API o aggiorna il modello per la versione più recente di sfruttare le nuove caratteristiche.

### <a name="portal"></a>Portale

Determinare la versione API supportata nello stesso modo che è determinato supportati aree (come illustrate in precedenza).

### <a name="rest-api"></a>API REST

Per individuare le versioni di API sono disponibili per i tipi di risorse, utilizzare l'operazione di [tutti i provider di risorse dell'elenco](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

Nell'esempio seguente viene illustrato come ottenere le versioni di API disponibili per un determinato tipo.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
L'output è simile a:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI Azure

È possibile salvare le informazioni (incluse le versioni di API disponibile) per un provider di risorse in un file con il comando seguente.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

È possibile aprire il file e trovare l'elemento **apiVersions**

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di modelli di Manager delle risorse, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
- Per informazioni sulla distribuzione delle risorse, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](resource-group-template-deploy.md).
