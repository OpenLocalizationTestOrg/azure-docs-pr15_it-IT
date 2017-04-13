<properties
  pageTitle="Famiglia di prodotti IoT Azure domande frequenti su | Microsoft Azure"
  description="Domande frequenti su IoT famiglia di prodotti"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Domande frequenti su IoT famiglia di prodotti

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Che cos'è la differenza tra l'eliminazione di un gruppo di risorse nel portale di Azure e facendo clic su Elimina a una soluzione preconfigurata in azureiotsuite.com?

- Se si elimina la soluzione preconfigurata in [azureiotsuite.com][lnk-azureiotsuite], si elimina tutte le risorse che è sono effettuato il provisioning al momento della creazione soluzione preconfigurata. Se si aggiungono risorse aggiuntive per il gruppo di risorse, questi vengono eliminati anche. 

- Se si elimina il gruppo di risorse nel [portale di Azure][lnk-azure-portal], è eliminare solo le risorse quel gruppo di risorse. sarà anche necessario eliminare l'applicazione di Azure Active Directory associato alla soluzione preconfigurata nel [portale classica Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quante istanze IoT Hub è provisioning in un abbonamento? 

Dieci. È possibile creare un [ticket di supporto di Azure] [ link-azuresupportticket] per generare questo limite, ma per impostazione predefinita, è possibile solo eseguire il provisioning dieci IoT hub per abbonamento, come descritto nella [sottoscrizione Azure limiti][link-azuresublimits]. Di conseguenza, in quanto ogni soluzione preconfigurato esegue il provisioning di un nuovo IoT Hub, è possibile solo effettuare il provisioning fino a dieci soluzioni preconfigurate in una sottoscrizione specificata. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Quante istanze DocumentDB posso provisioning in un abbonamento?

50. È possibile creare un [ticket di supporto di Azure] [ link-azuresupportticket] per generare questo limite, ma per impostazione predefinita, è solo possibile eseguire il provisioning cinquanta istanze DocumentDB per abbonamento. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quanti gratuito API di mappe di Bing è provisioning in una sottoscrizione

Due. È possibile creare solo due interno transazioni livello 1 Bing Maps per i piani Enterprise in un abbonamento a Azure. Viene eseguito il provisioning della soluzione di monitoraggio remota con il piano interno transazioni livello 1 per impostazione predefinita. Di conseguenza, è possibile eseguire solo provisioning fino a due soluzioni monitoraggio remote in un abbonamento senza modifiche.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Dispone di una distribuzione soluzione monitoraggio remote con una mappa statica, come aggiungere una mappa di Bing interattiva? 
1. Ottenere l'API Bing Maps per Enterprise QueryKey dal [portale Azure][lnk-azure-portal]: 
 1. Passare al gruppo di risorse in cui l'API Bing Maps per Enterprise è nel [portale di Azure][lnk-azure-portal].
 2. Fare clic su tutte le impostazioni, quindi km. 
 3. Si noterà che due chiavi: chiave master e QueryKey. Copiare il valore per QueryKey.

     > [AZURE.NOTE] Non è disponibile un API Bing Maps per conto dell'organizzazione? Creare uno nel [portale di Azure] [ lnk-azure-portal] by facendo clic su + Nuovo, la ricerca di API Bing Maps per Enterprise e seguire le istruzioni per creare.

2. Il codice più recente a discesa da [Azure IoT-remota monitoraggio][lnk-remote-monitoring-github].

3. Eseguire locale o nel cloud seguenti istruzioni per la distribuzione di riga di comando nella cartella /docs/ nell'archivio di distribuzione. 

4. Dopo avere eseguito locale o cloud distribuzione, cercare nella cartella radice per il *. User file creati durante la distribuzione. Aprire il file in un editor di testo. 

5. Modificare la riga seguente per includere il valore copiato dal QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare una soluzione preconfigurata se dispone di Microsoft Azure per DreamSpark?
Al momento non è possibile creare una soluzione preconfigurata con un [Microsoft Azure per DreamSpark] [ lnk-dreamspark] account. Tuttavia, è possibile creare un [account di valutazione gratuita per Azure] [ lnk-30daytrial] in solo un paio di minuti che consentiranno si crea una soluzione preconfigurata.

### <a name="how-do-i-delete-an-aad-tenant"></a>Come è possibile eliminare un tenant AAD?

Vedere [procedura dettagliata dell'eliminazione di un Tenant di Azure Active Directory]di post di blog di Eric Golpe[lnk-delete-aad-tennant].

## <a name="next-steps"></a>Passaggi successivi

È anche possibile esplorare alcune delle altre caratteristiche e le soluzioni di famiglia IoT preconfigurato:

- [Panoramica della soluzione di manutenzione stima preconfigurata][lnk-predictive-overview]
- [Protezione IoT sin][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
