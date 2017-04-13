<properties
   pageTitle="Eseguire il monitoraggio delle operazioni, eventi e contatori per NSGs | Microsoft Azure"
   description="Informazioni su come abilitare contatori, eventi e operativa registrazione per NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>Registro analitica per i gruppi di sicurezza di rete (NSGs)

È possibile usare diversi tipi di registri di Azure per gestire e risolvere i problemi di NSGs. Alcuni di questi registri sono accessibili tramite il portale e tutti i registri possono essere estratte da un'archiviazione blob Azure e visualizzati in diversi strumenti, ad esempio [Log Analitica](../log-analytics/log-analytics-azure-networking-analytics.md), Excel e ottenere informazioni. È possibile altre informazioni sui diversi tipi dei registri nell'elenco seguente.

- **Dei registri:** È possibile utilizzare [I log di controllo Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (precedentemente noto come registri operativi) per visualizzare tutte le operazioni di invio per le sottoscrizioni Azure e il relativo stato. Log di controllo attivati per impostazione predefinita e possono essere visualizzati nel portale di Azure anteprima.
- **i registri eventi:** È possibile usare il registro per visualizzare le regole NSG vengono applicate a macchine virtuali e ruoli istanza in base all'indirizzo MAC. Lo stato di queste regole è raccolti ogni 60 secondi.
- **Registri contatori:** È possibile usare il registro per visualizzare il numero di volte ogni regola NSG è stato applicato per impedire o consentire il traffico.

>[AZURE.WARNING] Log sono disponibili solo per le risorse distribuite nel modello di distribuzione Manager delle risorse. Non è possibile utilizzare i registri per le risorse nel modello di distribuzione classica. Per comprendere meglio i due modelli, fare riferimento l'articolo [classica distribuzione e gestione di informazioni sulle risorse](../resource-manager-deployment-model.md) .

##<a name="enable-logging"></a>Attivare la registrazione
La registrazione di controllo viene attivata automaticamente in qualsiasi momento per tutte le risorse di Manager delle risorse. È necessario attivare l'evento e contatore registrazione per avviare la raccolta di dati disponibili tramite i registri. Per attivare la registrazione, seguire la procedura seguente.

1.  Effettuare l'accesso al [portale di Azure](https://portal.azure.com). Se si dispone già di un gruppo di sicurezza di rete esistente, [creare un NSG](virtual-networks-create-nsg-arm-ps.md) prima di continuare.

2.  Nel portale di anteprima, fare clic su **Sfoglia** >> **gruppi di sicurezza di rete**.

    ![Portale Preview - gruppi di sicurezza di rete](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Selezionare un gruppo di sicurezza di rete esistente.

    ![Portale Preview - impostazioni gruppo di sicurezza rete](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. In e **l'Impostazioni** , fare clic su **diagnostica**e quindi nel riquadro di **diagnostica** , accanto a **stato**, fare clic **su**
5. In e **l'Impostazioni** , fare clic su **Account di archiviazione**, quindi selezionare un archivio esistente account o crearne uno nuovo.  

>[AZURE.INFORMATION] log di controllo non richiede un account di archiviazione separata. L'uso di spazio di archiviazione per la registrazione di regola ed evento causeranno spese.

6. Nell'elenco a discesa immediatamente di sotto **Dello spazio di archiviazione Account**selezionare se si desidera registrare gli eventi, contatori o entrambe, quindi fare clic su **Salva**.

    ![Portale Preview - registri di diagnostica](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>Log di controllo
Log (in precedenza noto come "registro operativo") viene generato da Azure per impostazione predefinita.  I registri vengono conservati per 90 giorni nell'archivio i registri eventi di Azure. Ulteriori informazioni su questi file di log, leggere l'articolo [consente di visualizzare gli eventi e registri di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="counter-log"></a>Registro contatore
Questo registro viene generato solo se è stata abilitata alla scala cronologica per NSG come descritto sopra. I dati vengono archiviati in archiviazione l'account specificato quando è abilitata la registrazione. Ogni regola applicata alle risorse è stato effettuato l'accesso in formato JSON, come indicato di seguito.

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>Registro eventi
Questo registro viene generato solo se è stata abilitata alla scala cronologica per NSG come descritto sopra. I dati vengono archiviati in archiviazione l'account specificato quando è abilitata la registrazione. È stato effettuato l'accesso i dati seguenti:

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>Visualizzare e analizzare i log di controllo
È possibile visualizzare e analizzare i dati di log di controllo utilizzando uno dei metodi seguenti:

- **Strumenti azure:** Recuperare le informazioni dai log di controllo tramite PowerShell di Azure, Azure interfaccia della riga di comando (CLI), l'API REST di Azure o il portale di Azure anteprima.  Istruzioni dettagliate per ogni metodo sono descritti nell'articolo [operazioni di controllo Gestione risorse](../resource-group-audit.md) .
- **Power BI:** Se si dispone già di un account di [Power BI](https://powerbi.microsoft.com/pricing) , è possibile provare gratuitamente. Con [i log di controllo Azure contenuto pack per Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) si analizzano i dati con i dashboard preconfigurati utilizzate come-è o personalizzare.

## <a name="view-and-analyze-the-counter-and-event-log"></a>Visualizzare e analizzare contatore e registro eventi

Azure [Analitica Log](../log-analytics/log-analytics-azure-networking-analytics.md) può raccogliere il contatore registro eventi file dal proprio account di archiviazione Blob e include funzionalità di ricerca potenti per l'analisi dei registri e visualizzazioni.

È anche possibile connettersi al proprio account di archiviazione e recuperare le voci di log JSON per i registri eventi e contatore. Dopo avere scaricato i file JSON, è possibile convertire CSV e visualizzazione in Excel, ottenere informazioni o un altro strumento di visualizzazione di dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e concetti di base di modificare i valori costanti e variabili in c#, è possibile utilizzare gli [Strumenti di conversione del registro](https://github.com/Azure-Samples/networking-dotnet-log-converter) di Github.

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare i registri eventi con [Registro Analitica](../log-analytics/log-analytics-azure-networking-analytics.md) e contatore
- Post di blog [Visualizza i registri di controllo Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
- [Visualizzare e analizzare i log di controllo Azure in Power BI e altro ancora](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) post di blog.
