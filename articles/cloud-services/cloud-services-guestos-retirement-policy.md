<properties 
   pageTitle="Supporto e ritiro dei criteri guida per il sistema operativo Guest Azure | Microsoft Azure" 
   description="Fornisce informazioni su cosa Microsoft supporterà per quanto riguarda al sistema operativo Guest Azure usati dai servizi Cloud." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Criteri di supporto e ritiro dei sistemi operativi Guest Azure
Le informazioni in questa pagina si riferiscano al sistema operativo Guest Azure ([Sistema operativo Guest](cloud-services-guestos-update-matrix.md)) per il lavoro servizi Cloud e ruoli web (PaaS). Non si applica alle macchine virtuali (IaaS). 

Microsoft ha un pubblicato [criteri di supporto per il sistema operativo Guest](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). La pagina che si sta leggendo viene ora implementazione i criteri.

Il criterio viene 

1. Microsoft supporterà **almeno le famiglie di due più recente del sistema operativo Guest**. Quando viene eliminata una famiglia di clienti hanno 12 mesi dalla data di ritiro dei ufficiale eseguire l'aggiornamento a una famiglia di sistemi operativi Guest supportata più recente.
2. Microsoft supporterà la **almeno due versioni più recenti delle famiglie di sistemi operativi Guest supportate**. 
3. Microsoft supporterà al **minimo le ultime due versioni di Azure SDK**. Quando viene eliminata una versione di SDK clienti avranno 12 mesi dalla data di ritiro dei ufficiale eseguire l'aggiornamento a una versione più recente. 

In alcuni casi più di due famiglie o versioni potrebbero essere supportate. Informazioni sul supporto di sistemi operativi Guest ufficiale compariranno [Azure Guest OS versioni e nella matrice compatibilità SDK](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Quando è ritirata una famiglia di sistemi operativi Guest o una versione 


Una nuova OS Guest **famiglia** è stato introdotto un intervallo di tempo dopo il rilascio di una nuova versione ufficiale del sistema operativo Windows Server. Ogni volta che è stato introdotto una nuova famiglia di sistemi operativi Guest, Microsoft verrà ritirare della famiglia di sistemi operativi Guest meno recente. 

Nuove OS Guest **versioni** vengono introdotti su ogni mese per incorporare gli aggiornamenti più recenti di MSRC. A causa di mensile regolarmente, una versione del sistema operativo Guest è in genere disattivati 60 giorni dopo il rilascio. In questo modo almeno due versioni di sistemi operativi Guest per ogni famiglia disponibile per l'utilizzo. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante un ritiro dei famiglia di sistemi operativi Guest 


Una volta viene annunciato il ritiro, i clienti hanno un periodo di 12 mesi "transizione" prima della famiglia precedenti ufficiale è stata rimossa dal servizio. Il tempo di transizione può essere esteso discrezione di Microsoft. Gli aggiornamenti verranno registrati in [Azure Guest OS versioni e nella matrice compatibilità SDK](cloud-services-guestos-update-matrix.md).

Un processo graduale pensionistico inizierà 6 mesi al periodo di transizione. Durante questo periodo:

1. Microsoft impegna a comunicare ai clienti del ritiro. 
2. La versione più recente di Azure SDK non supporta la famiglia di sistemi operativi Guest ritirata.
3. Nuove distribuzioni e ridistribuzione dei servizi Cloud non potrà essere per la famiglia ritirata

Microsoft continuerà a introdurre nuova versione del sistema operativo Guest che includa gli aggiornamenti più recenti MSRC fino all'ultimo giorno del periodo di transizione, noto come "Data di scadenza". In tale momento qualsiasi servizi Cloud ancora in esecuzione verrà supportata in contratto di servizio Azure. Microsoft ha discrezione per forzare l'aggiornamento, eliminazione o arrestare tali servizi dopo tale data.



### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante il ritiro una versione del sistema operativo Guest 
Se i clienti imposta il sistema operativo Guest per aggiornare automaticamente, non hanno mai preoccuparsi di gestione delle versioni del sistema operativo Guest. Sempre che utilizzeranno l'ultima versione del sistema operativo Guest.

Versioni del sistema operativo guest vengono rilasciate ogni mese. A causa di velocità dei rilasci regolari, ogni versione include una durata fissa.

60 giorni in durata una versione è "*disabilitato*". "Disabilitato" indica che la versione è stata rimossa dal portale di classica Azure. Anche può non essere dal file di configurazione CSCFG. Distribuzioni esistenti rimangono in esecuzione, ma le nuove distribuzioni e gli aggiornamenti di codice e configurazione di distribuzioni esistenti non potrà essere. 

In un secondo momento, il sistema operativo Guest versione "*scade*" e le installazioni ancora in esecuzione la versione sono forza aggiornato e set per aggiornare automaticamente il sistema operativo Guest in futuro. Scadenza avviene in batch in modo che il periodo di tempo tra disattivazione e scadenza può variare. 

Questi periodi possono essere effettuati più discrezione di Microsoft per facilitare le transizioni cliente. Le modifiche vengono comunicate [Azure Guest OS versioni e nella matrice compatibilità SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notifiche durante il ritiro 

* **Pensionistico famiglia** <br>Microsoft utilizzerà i post di blog e Azure notifica portale classica. I clienti che hanno siano ancora usando una famiglia di sistemi operativi Guest ritirata verranno notificati tramite comunicazione diretta (posta elettronica, messaggi portale e telefonata) per gli amministratori di assistenza assegnate. Tutte le modifiche verranno registrate in questa pagina e il feed RSS elencati all'inizio della pagina. 


* **Versione pensionistico** <br>Tutte le modifiche verranno registrate in questa pagina e il feed RSS elencati all'inizio della pagina, tra cui la versione, disabilitato e le date di scadenza. Gli amministratori di servizi riceveranno messaggi di posta elettronica se hanno distribuzioni in esecuzione in una versione del sistema operativo Guest disabilitato o parenti. L'intervallo di questi messaggi di posta elettronica può variare. In genere vengono almeno un mese prima della disattivazione, anche se questo intervallo non è un contratto di servizio ufficiale. 


## <a name="frequently-asked-questions"></a>Domande frequenti

**Come è possibile ridurre l'impatto della migrazione?**

È necessario utilizzare più recente famiglia di sistemi operativi Guest per la progettazione di servizi Cloud. 

1. Iniziare a pianificare la migrazione a una famiglia di più recente all'inizio. 
2. Impostare le distribuzioni di test temporanea per verificare il servizio Cloud in esecuzione su nuova famiglia. 
3. Impostare la versione del sistema operativo Guest su **automatico** (osVersion = * nel file [cscfg](cloud-services-model-and-package.md#cscfg) ) in modo che la migrazione alle nuove versioni di sistemi operativi Guest viene eseguita automaticamente.

**Cosa fare se l'applicazione web richiede più approfondita integrazione con sistema operativo?**

Se l'architettura dell'applicazione web richiede più approfondita dipendenza del sistema operativo, piattaforma di usare le funzionalità supportate, ad esempio [attività di avvio](cloud-services-startup-tasks.md) o altri meccanismi di estensibilità che potrebbero essere disponibile in futuro. In alternativa, è possibile anche utilizzare [macchine virtuali di Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS-infrastruttura come servizio), in cui è responsabile della gestione del sistema operativo sottostante.
 
## <a name="next-steps"></a>Passaggi successivi
Esaminare il più recente [del sistema operativo Guest rilascia](cloud-services-guestos-update-matrix.md).
