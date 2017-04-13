<properties
    pageTitle="Documentazione per la pubblica amministrazione Azure | Microsoft Azure"
    description="In questo modo un confronto delle caratteristiche e indicazioni sullo sviluppo di applicazioni per la pubblica amministrazione Azure"
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
    ms.date="10/13/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-storage"></a>Spazio di archiviazione per la pubblica amministrazione Azure

##  <a name="azure-storage"></a>Spazio di archiviazione Azure

Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere [lo spazio di archiviazione di Azure documentazione pubblico](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Varianti

L'URL per gli account di archiviazione per la pubblica amministrazione Azure sono diverse:

Tipo di servizio|Pubblica Azure|Azure per la pubblica amministrazione
---|---|---
Archiviazione BLOB|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Spazio di archiviazione coda|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Archiviazione tabella|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Tutti gli script e codice devono a prendere in considerazione i punti finali appropriati.  Vedere [configurare le stringhe di connessione di archiviazione Azure](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Per ulteriori informazioni sulle API vedere <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Costruttore Account di archiviazione Cloud</a>.

Suffisso endpoint da utilizzare in questi overload è core.usgovcloudapi.net 

### <a name="considerations"></a>Considerazioni

Le informazioni seguenti identificano il limite per la pubblica amministrazione Azure per lo spazio di archiviazione di Azure:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dati immessi, archiviati ed elaborati all'interno di un archivio di Azure prodotto può contenere dati di esportazione. Autenticatori statiche, ad esempio le password e PIN smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private dei certificati usati per gestire i componenti della piattaforma Azure. Altri sicurezza informazioni/informazioni riservate, ad esempio i certificati, chiavi di crittografia, le chiavi master e chiavi per l'archiviazione archiviate in servizi di Azure. | Azure lo spazio di archiviazione basato su metadati non può contenere dati di esportazione. Metadati includono tutti i dati di configurazione immessi durante la creazione e gestione del prodotto lo spazio di archiviazione.  Non richiesta l'immissione di dati Regulated/controllati nei campi seguenti: gruppi di risorse, i nomi di distribuzione, i nomi delle risorse, tag di risorse  

##  <a name="premium-storage"></a>Spazio di archiviazione Premium

Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Varianti

Spazio di archiviazione Premium è generalmente disponibile in USGov Virginia. Include macchine virtuali di serie DS. 

### <a name="considerations"></a>Considerazioni

La stessa lo spazio di archiviazione dati elencate considerazioni per gli account di archiviazione premium. 

##  <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni e aggiornamenti sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
