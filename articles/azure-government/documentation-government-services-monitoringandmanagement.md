<properties
    pageTitle="Documentazione per la pubblica amministrazione Azure | Microsoft Azure"
    description="In questo modo un confronto delle caratteristiche e le indicazioni sullo sviluppo di applicazioni per la pubblica amministrazione Azure."
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Gestione e il monitoraggio di Azure per la pubblica amministrazione

Questo articolo illustra il monitoraggio e gestione dei servizi varianti e considerazioni per l'ambiente per enti pubblici Azure.

## <a name="automation"></a>Automazione

Automazione è generalmente disponibile in Azure per enti pubblici.

### <a name="variations"></a>Varianti

Le caratteristiche di automazione seguenti non sono attualmente disponibili in Azure per enti pubblici.

+ Creazione di una credenziale principio servizio per l'autenticazione

Per ulteriori informazioni, vedere [la documentazione pubblica di automazione](../automation/automation-intro.md).

## <a name="log-analytics"></a>Registro Analitica

Registro Analitica disponibile in genere in Azure per enti pubblici.

### <a name="variations"></a>Varianti

Le caratteristiche di Analitica Log e le soluzioni seguenti non sono attualmente disponibili in Azure per enti pubblici.

+ Soluzioni che sono in anteprima di Microsoft Azure, tra cui:
  - Soluzione di monitoraggio di rete
  - Soluzione il monitoraggio della dipendenza dell'applicazione
  - Soluzione Office 365
  - Soluzione Analitica l'aggiornamento di Windows 10
  - Soluzione approfondimenti applicazione
  - Soluzione di rete Analitica Azure
  - Soluzione di automazione Analitica Azure
  - Soluzione chiave archivio Analitica
+ Soluzioni e le funzionalità che richiedono aggiornamenti software in locale, tra cui:
  - Integrazione con sistema Center Operations Manager 2016 (sono supportate nelle versioni precedenti di Operations Manager)
  - Gruppi di computer da System Center Configuration Manager
  - Soluzione Hub a superficie
+ Caratteristiche che sono in anteprima di Azure pubblico, tra cui:
  - Esportazione dei dati in Power BI
+ Azure metrica e diagnostica Windows Azure
+ Applicazioni mobili Management Suite operazioni

Gli URL dei Log Analitica sono diversi in Azure per enti pubblici:

| Pubblica Azure | Azure per la pubblica amministrazione | Note |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.us | Portale Analitica log |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [API di raccolta dati](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |


Le seguenti funzionalità Analitica Log presentano per enti pubblici Azure:

+ Agente di Windows deve essere scaricato dal [portale Analitica Log](https://oms.microsoft.us) per la pubblica amministrazione Azure.
+ Per connettere il server di gestione System Center Operations Manager Log Analitica, è necessario scaricare e importare aggiornato management pack.
  1. Scaricare e salvare [aggiornato management pack](http://go.microsoft.com/fwlink/?LinkId=828749).
  2. Decomprimere il file scaricato.
  3. Importare i management pack per Operations Manager. Per informazioni su come importare un management pack da un disco, vedere [come importare un Management Pack di Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) nel sito Web Microsoft TechNet.
  4. Per connettere Operations Manager Log Analitica, seguire i passaggi descritti in [Connettere Operations Manager per Log Analitica](../log-analytics/log-analytics-om-agents.md).


## <a name="frequently-asked-questions"></a>Domande frequenti

+ È possibile migrare dati da Analitica Log in Microsoft Azure per enti pubblici Azure?
  - No. Non è possibile spostare dati o l'area di lavoro da Microsoft Azure a Government Azure.
+ Si può passare tra Microsoft Azure e Azure governo aree di lavoro dal portale di operazioni Management Suite Log Analitica?
  - No. Portali per Microsoft Azure e Azure Government sono distinti e non condividere informazioni.

Per ulteriori informazioni, vedere [Log Analitica documentazione pubblica](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
