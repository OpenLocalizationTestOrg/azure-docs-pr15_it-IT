<properties
    pageTitle="Documentazione per la pubblica amministrazione Azure | Microsoft Azure"
    description="In questo modo un confronto delle caratteristiche e indicazioni sullo sviluppo di applicazioni per la pubblica amministrazione Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="scooxl"/>
#  <a name="azure-government-management-and-security"></a>Sicurezza e la gestione di azure per la pubblica amministrazione

## <a name="automation"></a>Automazione

Automazione è generalmente disponibile in Azure per enti pubblici.

### <a name="variations"></a>Varianti

Le caratteristiche di automazione seguenti non sono attualmente disponibili in Azure per enti pubblici.

+ Creazione di una credenziale principio servizio per l'autenticazione

Per ulteriori informazioni, vedere [la documentazione pubblica di automazione](../automation/automation-intro.md).


##  <a name="key-vault"></a>Archivio di chiave
Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere il <a href="https://azure.microsoft.com/documentation/services/key-vault">documentazione pubblica Azure chiave archivio.</a>
### <a name="data-considerations"></a>Considerazioni sui dati
Le informazioni seguenti consente di identificare il limite per la pubblica amministrazione Azure per Azure chiave archivio:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati vengono crittografati con una chiave di Azure chiave archivio possono contenere dati Regulated/controllati. | Azure chiave archivio metadati non può contenere dati di esportazione. Metadati includono tutti i dati di configurazione immessi durante la creazione e gestione l'archivio di chiave.  Non richiesta l'immissione di dati Regulated/controllati nei campi seguenti: nomi di gruppo di risorse, nomi di archivio di chiave, nome dell'abbonamento |

Chiave archivio è in genere disponibile in Azure per enti pubblici. Come pubblico, non è estensione, pertanto chiave archivio è disponibile tramite PowerShell e CLI solo.
## <a name="log-analytics"></a>Registro Analitica
Registro Analitica disponibile in genere in Azure per enti pubblici. 

### <a name="variations"></a>Varianti

Le caratteristiche di Analitica Log e le soluzioni seguenti non sono attualmente disponibili in Azure per enti pubblici. In questo elenco viene aggiornato quando lo stato delle funzioni / soluzioni cambia.

+ Soluzioni che sono in anteprima di Azure pubblico, tra cui:
  - Soluzione di monitoraggio di rete
  - Il monitoraggio della dipendenza di applicazione
  - Soluzione Office 365
  - Soluzione Analitica l'aggiornamento di Windows 10
  - Informazioni dettagliate sui applicazione
  - Soluzione di rete Analitica Azure
  - Automazione Azure Analitica
  - Archivio chiave Analitica
+ Soluzioni e le funzionalità che richiedono aggiornamenti software in locale, inclusi
  - Integrazione con sistema Center Operations Manager 2016 (sono supportate nelle versioni precedenti di Operations Manager)
  - Gruppi di computer da System Center Configuration Manager
  - Soluzione Hub a superficie
+ Caratteristiche che sono in anteprima di Azure pubblico, incluse
  - Esportazione di dati per ottenere informazioni
+ Azure metrica e diagnostica Windows Azure
+ Applicazioni per dispositivi mobili OMS

Gli URL dei Log Analitica sono diversi in Azure per enti pubblici:

| Pubblica Azure | Azure per la pubblica amministrazione | Note |
|--------------|------------------|-------|
| MMS.microsoft.com | OMS.microsoft.us | Portale Analitica log |
| *workspaceId*. ods.opinsights.azure.com | *workspaceId*. ods.opinsights.azure.us | [API di raccolta dati](../log-analytics/log-analytics-data-collector-api.md) 
| \*. ods.opinsights.azure.com | \*. ods.opinsights.azure.us | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. oms.opinsights.azure.com | \*. oms.opinsights.azure.us | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*. blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Comunicazione dell'agente - [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |


Le seguenti funzionalità Analitica Log hanno un comportamento diverso in Azure per enti pubblici:

+ Agente di Windows deve essere scaricato dal [portale Analitica Log](https://oms.microsoft.us) per la pubblica amministrazione Azure.
+ Per connettere il server di gestione System Center Operations Manager Log Analitica, è necessario scaricare e importare aggiornato Management Pack.
  1. Scaricare e salvare [aggiornato management pack](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Decomprimere il file scaricato
  3. Importare i management pack per Operations Manager. Per informazioni su come importare un management pack da un disco, vedere l'argomento [su come importare un Management Pack di Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) nel sito Web Microsoft TechNet.
  4. Per connettere Operations Manager Log Analitica, seguire i passaggi descritti in [Connettere Operations Manager per Analitica Log](../log-analytics/log-analytics-om-agents.md) 



### <a name="frequently-asked-questions"></a>Domande frequenti

+ È possibile migrare dati da Analitica Log Government Azure a Azure pubblico?
  - No. Non è possibile spostare dati o l'area di lavoro da pubblico per enti pubblici Azure per Azure.
+ È possibile alternare pubblici Azure e Azure Government le aree di lavoro dal portale di OMS Log Analitica?
  - No. Portali per pubblico Azure e Azure Government sono distinti e non condividere informazioni. 

Per ulteriori informazioni, vedere [Log Analitica documentazione pubblica](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
 
