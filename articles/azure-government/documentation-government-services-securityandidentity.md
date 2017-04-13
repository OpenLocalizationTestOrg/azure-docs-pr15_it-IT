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
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Identità e la sicurezza di azure per la pubblica amministrazione

##  <a name="key-vault"></a>Archivio di chiave
Per informazioni dettagliate su questo servizio e su come utilizzarla, vedere il <a href="https://azure.microsoft.com/documentation/services/key-vault">documentazione pubblica Azure chiave archivio.</a>

### <a name="data-considerations"></a>Considerazioni sui dati
Le informazioni seguenti consente di identificare il limite per la pubblica amministrazione Azure per Azure chiave archivio:

| Dati regolata/controllati consentiti | Dati regolata/controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati vengono crittografati con una chiave di Azure chiave archivio possono contenere dati Regulated/controllati. | Azure chiave archivio metadati non può contenere dati di esportazione. Metadati includono tutti i dati di configurazione immessi durante la creazione e gestione l'archivio di chiave.  Non richiesta l'immissione di dati Regulated/controllati nei campi seguenti: nomi di gruppo di risorse, nomi di archivio di chiave, nome dell'abbonamento |

Chiave archivio è in genere disponibile in Azure per enti pubblici. Come pubblico, non è estensione, pertanto chiave archivio è disponibile tramite PowerShell e CLI solo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
