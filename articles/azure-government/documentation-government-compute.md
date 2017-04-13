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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Elaborazione di Azure per la pubblica amministrazione

##  <a name="virtual-machines"></a>Macchine virtuali

Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere [Azure macchine virtuali di dimensioni](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Varianti

Seguenti SKU di macchine Virtuali sono in genere disponibile (GA) in Azure per enti pubblici:

SKU MACCHINE VIRTUALI|Stati Uniti Gov VA|Stati Uniti Gov IA|Note
---|---|---|---
RISPOSTE|GA|GA|Nessuno
Dv1|GA|-|Nessuno
DSv1|GA|-|Nessuno
Dv2|GA|GA|15 sarà presto disponibile
F|GA|GA|Nessuno
G|Pianificato|-|Nessuno

###  <a name="data-considerations"></a>Considerazioni sui dati

Le informazioni seguenti consente di identificare il limite per la pubblica amministrazione Azure per macchine virtuali di Azure:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dati immessi, memorizzati ed elaborati all'interno di una macchina virtuale può contenere dati di esportazione. File binari in esecuzione in macchine virtuali di Azure. Autenticatori statiche, ad esempio le password e PIN smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private dei certificati usati per gestire i componenti della piattaforma Azure. Stringhe di connessione SQL.  Altri sicurezza informazioni/informazioni riservate, ad esempio i certificati, chiavi di crittografia, le chiavi master e chiavi per l'archiviazione archiviate in servizi di Azure.  | Metadati non può contenere dati di esportazione. Metadati includono tutti i dati di configurazione immessi durante la creazione e gestione macchina virtuale Azure.  Non richiesta l'immissione di dati Regulated/controllati nei campi seguenti: i nomi dei ruoli, gruppi di risorse, i nomi di distribuzione, i nomi delle risorse, tag di risorse del Tenant  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
