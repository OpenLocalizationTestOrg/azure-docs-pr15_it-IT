<properties
   pageTitle="Risolvere i problemi di servizi Cloud con informazioni dettagliate sui applicazione | Microsoft Azure"
   description="Informazioni su come risolvere i problemi relativi al servizio cloud usando applicazione approfondimenti per elaborare i dati da Azure diagnostica."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Risolvere i problemi di servizi Cloud con informazioni dettagliate sui applicazione

Con estensione diagnostica [2,8 SDK Azure](https://azure.microsoft.com/downloads/) e Azure 1,5 è ora possibile inviare i dati di Azure diagnostica per il servizio Cloud direttamente a informazioni dettagliate sui applicazione. Diversi tipi di raccolte da Azure diagnostica registri applicazioni, i registri eventi di windows, inclusi i log ETW registra e contatori ora possono essere inviati a informazioni dettagliate sui applicazione e visualizzati nel portale di applicazione approfondimenti dell'interfaccia utente. Quando utilizzata insieme a SDK approfondimenti di applicazione è ora possibile ottenere informazioni approfondite in metriche e registri provenienti da un'applicazione, nonché di sistema e infrastruttura di livello i dati provenienti da Azure diagnostica.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurare la diagnostica Azure per inviare dati a informazioni dettagliate sui applicazione

Seguire questi passaggi per il progetto di servizio cloud per inviare i dati di Azure diagnostica a informazioni dettagliate sui applicazione di configurazione.

1) In Esplora soluzioni Visual Studio pulsante destro del mouse su un ruolo e scegliere **proprietà** per aprire la finestra di progettazione di ruolo

![Proprietà ruolo Esplora soluzioni][1]

2) Nella finestra di progettazione di ruolo nella sezione diagnostica selezionare la casella di controllo per **inviare i dati di diagnostica a informazioni dettagliate sui applicazione**

![Progettazione di ruolo inviare i dati di diagnostica a informazioni dettagliate sui applicazione][2]

3) Nella finestra di dialogo che appare selezionare la risorsa approfondimenti applicazione che si desidera inviare i dati di diagnostica Windows Azure. La finestra di dialogo consente di selezionare una risorsa applicazione approfondimenti esistente dall'abbonamento oppure specificare manualmente una chiave di strumentazione per una risorsa approfondimenti applicazione. Se non si dispone di una risorsa applicazione approfondimenti esistente è possibile creare in facendo clic sul collegamento **Crea una nuova risorsa** che verrà aperta una finestra del browser al portale di classica Azure in cui è possibile creare una risorsa di informazioni approfondite dell'applicazione. Per ulteriori informazioni sulla creazione di una risorsa applicazione approfondimenti, vedere [creare una nuova risorsa applicazione approfondimenti](../application-insights/app-insights-create-new-resource.md)

![Selezionare applicazione approfondimenti risorsa][3]

4) Dopo aver aggiunto la risorsa applicazione approfondimenti, la chiave strumentazione per tale risorsa viene memorizzata come un'impostazione di configurazione del servizio con il nome **APPINSIGHTS_INSTRUMENTATIONKEY**. È possibile modificare questa impostazione di configurazione per ogni configurazione del servizio o l'ambiente selezionando una configurazione diversa dall'elenco a discesa Configurazione servizio verso il basso e specificare una nuova chiave strumentazione per la configurazione.

![Selezionare la configurazione del servizio][4]

Impostazione di configurazione **APPINSIGHTS_INSTRUMENTATIONKEY** viene utilizzato da Visual Studio per configurare l'estensione di diagnostica con le relative informazioni di risorsa applicazione approfondimenti durante la pubblicazione. L'impostazione di configurazione è utile per la definizione di chiavi strumentazione diversi per le configurazioni di servizi diversa. Verrà tradurre tale impostazione e inserirla la configurazione di pubblico estensione diagnostica per la pubblicazione. Per semplificare il processo di configurazione l'estensione di diagnostica con PowerShell, il pacchetto di output anche da Visual Studio contiene la configurazione pubblica XML con la strumentazione approfondimenti applicazione appropriata chiave inclusi. I file di configurazione pubblico creati nella cartella estensioni e seguono il modello PaaSDiagnostics. <RoleName>. PubConfig.xml. Le distribuzioni di PowerShell in base a è possono utilizzare questo modello per eseguire il mapping di ogni configurazione a un ruolo.

5) Per consentire di **inviare i dati di diagnostica a informazioni dettagliate sui applicazione** configura automaticamente diagnostica Windows Azure per inviare tutti i contatori e i registri di livello di errore che vengono raccolte dall'agente di diagnostica Windows Azure a informazioni dettagliate sui applicazione. Se si desidera configurare ulteriormente i dati verrà inviate a informazioni dettagliate sui applicazione sarà necessario modificare manualmente il file *diagnostics.wadcfgx* per ogni ruolo. Vedere [Configurare Azure diagnostica per inviare dati a informazioni dettagliate sui applicazione](../azure-diagnostics-configure-applicationinsights.md) per altre informazioni sull'aggiornamento manuale la configurazione.

Accertarsi che l'estensione di diagnostica Windows Azure viene abilitata dopo aver configurato il servizio Cloud per inviare i dati di diagnostica Windows Azure a informazioni dettagliate sui applicazione che è possibile distribuirlo in Azure come di consueto. Vedere [pubblicazione di un servizio Cloud utilizzando Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualizzazione dei dati di diagnostica Windows Azure in applicazione approfondimenti
Azure telemetria di diagnostica verrà visualizzato nella risorsa applicazione approfondimenti configurata per il servizio cloud.

Ecco come diagnostica di Windows Azure vari accede mappa tipi con i concetti approfondimenti applicazione:  

-  Contatori vengono visualizzati come metriche personalizzato nell'applicazione approfondimenti
-  I registri eventi di Windows vengono visualizzati come tracce ed eventi personalizzati in applicazione approfondimenti
-  Log applicazioni, i registri ETW e gli eventuali log infrastruttura di diagnostica vengono visualizzati come tracce di informazioni approfondite dell'applicazione.

Per visualizzare dati di diagnostica Windows Azure in approfondimenti applicazione:

- [Metrica explorer](../application-insights/app-insights-metrics-explorer.md) consente di visualizzare eventuali contatori personalizzati o conteggi dei diversi tipi di eventi del log di eventi di windows.

![Metrica personalizzata in Esplora metriche][5]

- Usare la [ricerca](../application-insights/app-insights-diagnostic-search.md) per cercare tra i vari registri di traccia provenienti dai Azure diagnostica. Per esempio se si dispone di un'eccezione non gestita in un ruolo che ha causato il ruolo di arresto anomalo e Cestino tali informazioni verrebbe visualizzati del canale di *applicazione* del *registro eventi di Windows*. È possibile utilizzare la funzionalità di ricerca per esaminare l'errore registro eventi di Windows e ottenere la traccia dello stack completo per l'eccezione che consente di individuare la causa del problema.

![Tracce di ricerca][6]

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungi SDK approfondimenti di applicazione del servizio cloud](../application-insights/app-insights-cloudservices.md) per inviare dati su richieste, eccezioni, dipendenze e qualsiasi telemetria personalizzato dall'applicazione in uso. Combinati con i test di diagnostica Azure dati è possibile ottenere una visualizzazione completa dell'applicazione e sistema nella stessa risorsa comprendere l'applicazione.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
