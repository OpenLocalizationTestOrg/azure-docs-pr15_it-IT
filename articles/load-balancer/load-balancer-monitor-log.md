<properties
   pageTitle="Eseguire il monitoraggio delle operazioni, eventi e contatori per bilanciamento del carico | Microsoft Azure"
   description="Informazioni su come abilitare eventi di avviso e ricercare la registrazione di stato di integrità per bilanciamento del carico di Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Analitica del Registro di sistema di bilanciamento del carico Azure (Preview)

È possibile usare diversi tipi di registri di Azure per gestire e risolvere i problemi di servizi di bilanciamento del carico. Alcuni di questi registri accessibili tramite il portale. Tutti i registri possono essere estratte da un'archiviazione blob Azure e visualizzati in diversi strumenti, ad esempio Excel e ottenere informazioni. È possibile altre informazioni sui diversi tipi dei registri nell'elenco seguente.

- **Dei registri:** È possibile utilizzare [I log di controllo Azure](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) (precedentemente noto come registri operativi) per visualizzare tutte le operazioni di invio per le sottoscrizioni Azure e il relativo stato. Log di controllo attivati per impostazione predefinita e possono essere visualizzati nel portale di Azure.
- **Registri eventi di un avviso:** È possibile usare il registro per visualizzare quali avvisi per bilanciamento del carico. Lo stato di bilanciamento del carico raccolte 5 minuti. Questo registro è scritta solo se viene generato un evento avviso bilanciamento del carico.
- **i registri di verifica dell'integrità:** È possibile utilizzare il registro per verificare la presenza di verifica dell'integrità controllare lo stato, il numero di istanze è in linea di carico bilanciamento back-end e la percentuale di macchine virtuali di ricevere il traffico di rete di bilanciamento del carico. Viene registrata in Modifica evento di verifica dello stato.

>[AZURE.IMPORTANT] Accedere analitica attualmente funziona solo con i servizi di bilanciamento del carico è connessa a Internet. Log sono disponibili solo per le risorse distribuite nel modello di distribuzione Manager delle risorse. Non è possibile utilizzare i registri per le risorse nel modello di distribuzione classica. Per ulteriori informazioni sui modelli di distribuzione, vedere [distribuzione classica e gestione di informazioni sulle risorse](../../articles/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Attivare la registrazione

Registrazione di controllo viene abilitata automaticamente per ogni risorsa Manager delle risorse. È necessario abilitare la registrazione di verifica dell'integrità per avviare la raccolta di dati disponibili tramite i registri e l'evento. Utilizzare la procedura seguente per attivare la registrazione.

Effettuare l'accesso al [portale di Azure](http://portal.azure.com). Se si dispone già di un servizio di bilanciamento del carico, [creare un bilanciamento del carico](load-balancer-get-started-internet-arm-ps.md) prima di continuare.

1. Nel portale, fare clic su **Sfoglia**.
2. Selezionare **servizi di bilanciamento del carico**.

    ![portale - bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selezionare un servizio di bilanciamento del carico esistente >> **Tutte le impostazioni**.
4. Sul lato destro della finestra di dialogo sotto il nome del sistema di bilanciamento del carico, scorrere fino alla **monitoraggio**, fare clic su **diagnostica**.

    ![portale - impostazioni di sistema di bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. **Nel riquadro di **diagnostica** , in **stato**selezionare.**
6. Fare clic su **Account di archiviazione**.
7. Nella casella **log**, selezionare un account di archiviazione esistente o crearne uno nuovo. Usare il dispositivo di scorrimento per determinare quante giorni evento dat verranno mantenuti nei registri eventi. 8. Fare clic su **Salva**.

    ![Portale - registri di diagnostica](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] log di controllo non richiede un account di archiviazione separata. L'utilizzo di spazio di archiviazione per l'evento e dell'integrità della registrazione verifica causeranno spese.

## <a name="audit-log"></a>Log di controllo

Per impostazione predefinita, viene generato il log di controllo. I registri vengono conservati per 90 giorni nell'archivio i registri eventi di Azure. Ulteriori informazioni su questi file di log, leggere l'articolo [consente di visualizzare gli eventi e registri di controllo](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Registro eventi avvisi

Questo registro viene generato solo se è stata abilitata in una base di bilanciamento carico. Gli eventi sono registrati in formato JSON e archiviati in account di archiviazione che è specificato quando è abilitata la registrazione. Di seguito è illustrato un esempio di un evento.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

L'output JSON Mostra la proprietà *eventname* che descriva il motivo per la creazione di un avviso di bilanciamento del carico. In questo caso, l'avviso generato è stato a causa di esaurimento di porte TCP causato da un'origine IP NAT limiti (SNAT).

## <a name="health-probe-log"></a>Log di verifica dell'integrità

Questo registro viene generato solo se è stata abilitata in un carico bilanciamento in base al come descritto sopra. I dati vengono archiviati in archiviazione l'account specificato quando è abilitata la registrazione. Viene creato un contenitore denominato 'loadbalancerprobehealthstatus di registri approfondimenti' e ha eseguito l'accesso i dati seguenti:

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

L'output JSON Mostra nel campo proprietà le informazioni di base per lo stato di integrità di verifica. La proprietà *dipDownCount* indica il numero totale di istanze nel back-end che non si ricevono il traffico di rete a causa delle risposte di verifica non riuscito.

## <a name="view-and-analyze-the-audit-log"></a>Visualizzare e analizzare i log di controllo

È possibile visualizzare e analizzare i dati di log di controllo utilizzando uno dei metodi seguenti:

- **Strumenti azure:** Recuperare le informazioni dai log di controllo tramite PowerShell di Azure, Azure interfaccia della riga di comando (CLI), l'API REST di Azure o il portale di Azure anteprima. Istruzioni dettagliate per ogni metodo sono descritti nell'articolo [operazioni di controllo Gestione risorse](../../articles/resource-group-audit.md) .
- **Power BI:** Se si dispone già di un account di [Power BI](https://powerbi.microsoft.com/pricing) , è possibile provare gratuitamente. Usa il [log di controllo Azure contenuto pack per Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), è possibile analizzare i dati con i dashboard preconfigurati oppure è possibile personalizzare le visualizzazioni per soddisfare esigenze specifiche.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualizzare e analizzare la verifica dell'integrità e registro eventi

È necessario connettersi al proprio account di archiviazione e recuperare le voci di log JSON per eventi e l'integrità dei registri di verifica. Dopo avere scaricato i file JSON, è possibile convertire CSV e visualizzazione in Excel, ottenere informazioni o un altro strumento di visualizzazione di dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e concetti di base di modificare i valori costanti e variabili in c#, è possibile utilizzare gli [Strumenti di conversione del registro](https://github.com/Azure-Samples/networking-dotnet-log-converter) di Github.

## <a name="additional-resources"></a>Risorse aggiuntive

- Post di blog [Visualizza i registri di controllo Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Visualizzare e analizzare i log di controllo Azure in Power BI e altro ancora](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) post di blog.

## <a name="next-steps"></a>Passaggi successivi

[Comprendere le ricerche bilanciamento carico](load-balancer-custom-probe-overview.md)
