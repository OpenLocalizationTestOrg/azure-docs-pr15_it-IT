<properties
    pageTitle="Operazioni da eseguire in caso di un Azure service disservizi che interessa Azure chiave archivio | Microsoft Azure"
    description="Informazioni sulle operazioni da eseguire in caso di un'interruzione di Azure servizio che influisce su Azure chiave archivio."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure ridondanza e la disponibilità di archivio di chiave

Archivio chiave Azure caratteristiche più livelli di ridondanza per assicurarsi che le chiavi e informazioni riservate rimangono disponibili per l'applicazione anche se non riesce singoli componenti del servizio.

Il contenuto dell'archivio chiave viene replicato all'interno dell'area e a un'area secondaria almeno 150 miglia fuori sede, ma entro geography stesso. In questo modo affidabilità le chiavi e le informazioni riservate.

Se non riescono singoli componenti all'interno del servizio di archivio chiave, componenti alternativi all'interno dell'area passaggio per servire la richiesta per assicurarsi che non sia presente alcuna riduzione delle funzionalità. Non è necessario intraprendere alcuna azione per impostare un trigger seguente. L'operazione viene eseguita automaticamente e sarà trasparente all'utente.

In evento raro che non è disponibile un'intera area di Azure, le richieste apportate dell'archivio di chiave Azure in quell'area sono automaticamente indirizzato (*non riuscita su*) a un'area secondaria. Quando l'area principale è disponibile nuovamente, le richieste vengono indirizzate indietro (*non è possibile tornare*) all'area principale. Nuovo, non è necessario eseguire alcuna operazione perché si verifica questo evento automaticamente.

Esistono alcune considerazioni da tenere presenti:

* Invariato l'area geografica, richiedono alcuni minuti per il servizio venga reindirizzato. Fino a quando il failover, potrebbero non riuscire richieste produttrice durante questo periodo.
* Dopo avere inserito caso di errore, la chiave archivio è in modalità di sola lettura. Sono richieste supportati in questa modalità:
 * Elenco archivi chiavi
 * Ottenere le proprietà degli archivi di chiave
 * Elenco informazioni riservate
 * Ottenere informazioni riservate
 * Tasti di elenco
 * Ottenere le chiavi (proprietà)
 * Crittografare
 * Decrittografare
 * A capo automatico
 * Annullare la disposizione del
 * Verificare
 * Segno
 * Copia di backup
* Dopo aver caso di errore non riuscito indietro, tutti i tipi di richiesta (incluse le richieste di lettura *e* scrittura) sono disponibili.
