<properties
    pageTitle="Quali carichi di lavoro è possibile proteggere con il ripristino del sito di Azure?"
    description="Il ripristino del sito Azure protegge i carichi di lavoro e le applicazioni per coordinare la replica, failover e ripristino di macchine virtuali locale e server fisici in Azure o a un sito secondario in locale"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con il ripristino del sito di Azure?


In questo articolo vengono illustrati carichi di lavoro e applicazioni che può essere replicato con il servizio il ripristino del sito di Azure.

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia di ripristino (BCDR) emergenza e continuità aziendale per mantenere carichi di lavoro e dati attendibili e disponibili durante il tempo di inattività pianificato e non pianificato e ripristina in condizioni di lavoro ordinario presto.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR. Usa il ripristino del sito, è possibile distribuire replica compatibili con le applicazioni nel cloud, o a un sito secondario. Se le app sono costituite da finestre o basati su Linux, l'esecuzione in server fisici, VMware o Hyper-V, è possibile utilizzare il ripristino del sito per gestire la replica, eseguire test di ripristino di emergenza, nonché Esegui failover e failback.


Il ripristino del sito si integra con le applicazioni di Microsoft, inclusi SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Microsoft collabora con fornitori principali includono Oracle, SAP, IBM e Red Hat. È possibile personalizzare le soluzioni di replica su base app da app.

## <a name="why-use-site-recovery-for-application-replication"></a>Perché usare il ripristino del sito per la replica di applicazione?

Il ripristino del sito contribuisce a livello di applicazione protezione e ripristino come indicato di seguito:

- App indipendente, fornendo replica per qualsiasi carichi di lavoro in esecuzione in un computer supportato.
- Replica vicino sincronizzata con RPOs arrivare fino a 30 secondi in base alle esigenze di applicazioni aziendali più importanti.
- Snapshot coerenti App per le applicazioni di uno o più livelli.
- Integrazione con SQL Server AlwaysOn e collaborazione con altre tecnologie di replica di livello di applicazione, tra cui la replica di Active Directory, SQL AlwaysOn, Database disponibilità gruppi di Exchange (DAGs) e protezione di dati Oracle.
- Piani di ripristino flessibili, che consentono di recuperare una serie intera applicazione con un solo clic e includono in modo da includere nel piano di script esterni e le azioni manuale.
- Gestione di rete avanzate nel ripristino del sito e Azure per semplificare i requisiti di rete app, compresa la possibilità di riservare indirizzi IP, configurare Bilanciamento del carico e l'integrazione con Manager Azure il traffico per switchovers di rete RTO bassa.
-  Libreria di automazione RTF che fornisce gli script di produzione specifici dell'applicazione che possono essere scaricati e integrati con i piani di ripristino.



## <a name="workload-summary"></a>Riepilogo il carico di lavoro

Il ripristino del sito è possibile replicare qualsiasi app in esecuzione in un computer supportato. Inoltre nostro partner con il team di prodotto di effettuare prove specifiche app aggiuntive.

**Carico di lavoro** | **Replicare macchine virtuali di Hyper-V in un sito secondario** | **Replicare macchine virtuali Hyper-V in Azure** | **Replicare macchine virtuali VMware in un sito secondario** | **Replicare macchine virtuali VMware Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
App Web (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Replicare sito SAP Azure per non cluster | Y (testati da Microsoft) | Y (testati da Microsoft) | Y (verificate da Microsoft) | Y (testati da Microsoft)
Exchange (non DAG) | Y | Prossimamente | Y | Y
Desktop remota/VDI | Y | Y | Y | N/D
Linux (sistema operativo e App) | Y (testati da Microsoft) | Y (verificate da Microsoft) | Y (testati da Microsoft) | Y (testati da Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Prossimamente | Y | Prossimamente
Oracle | Y (testati da Microsoft) | Y (testati da Microsoft) | Y (verificate da Microsoft) | Y (verificate da Microsoft)
File Server Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Replica di Active Directory e DNS

Active Directory e infrastruttura DNS sono essenziali per la maggior parte delle App aziendali. Durante il recupero di emergenza, è necessario proteggere e ripristinare questi componenti dell'infrastruttura, prima di recuperare i carichi di lavoro e App.

È possibile utilizzare il ripristino del sito per creare un piano di ripristino di emergenza automatizzato completo per Active Directory e DNS. Ad esempio, se si vuole avere esito negativo su SharePoint e SAP da un principale a un sito secondario, è possibile impostare un piano di ripristino ha esito negativo su Active Directory prima di tutto, quindi un piano di recupero dati specifici di app aggiuntivo eseguire altre App che si basano su Active Directory.

[Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteggere SQL Server

SQL Server fornisce una base di servizi di dati per i servizi di dati per molte aziende: App in un data center locale.  Il ripristino del sito può essere utilizzato insieme a tecnologie SQL Server HA/DR, per proteggere App aziendali a più livelli che utilizzano SQL Server. Il ripristino del sito sono disponibili:

- Una soluzione di ripristino di emergenza semplice e conveniente per SQL Server. Replica più versioni e le edizioni di SQL Server autonomo server e cluster, in Azure o a un sito secondario.  
- Integrazione con i gruppi di disponibilità AlwaysOn SQL, per gestire failover e failback con i piani di ripristino il ripristino del sito di Azure.
- Piani di ripristino-to-end per tutti i livelli in un'applicazione, inclusi i database di SQL Server.
- Ridimensionamento di SQL Server per punta carica con il ripristino del sito, da "suddivisione" loro nelle dimensioni maggiori di macchina virtuale IaaS in Azure.
- Eseguire facilmente il test di ripristino di SQL Server. È possibile eseguire test failover per analizzare i dati ed eseguire la verifica della conformità, senza influire sull'ambiente di produzione.

[Altre informazioni](site-recovery-sql.md) sulla protezione di SQL server.

##<a name="protect-sharepoint"></a>Proteggere con SharePoint

Il ripristino del sito Azure consente di proteggere SharePoint distribuzioni, come indicato di seguito:

- Elimina la necessità e i costi associati infrastruttura di una farm standby di emergenza. Utilizzare il ripristino del sito per replicare un'intera farm (livelli Web app e database) in Azure o a un sito secondario.
- Semplifica la gestione e la distribuzione delle applicazioni. Aggiornamenti distribuiti al sito principale vengono automaticamente replicati e pertanto sono disponibili dopo failover e ripristino di una farm in un sito secondario. Riduce la complessità di gestione e i costi associati all'aggiornamento di una farm standby.
- Semplifica sviluppo di applicazioni SharePoint e verifica mediante la creazione di un ambiente di produzione copia replica su richiesta di test e debug.
- Semplifica la transizione nel cloud tramite il ripristino del sito per eseguire la migrazione di SharePoint distribuzioni in Azure.

[Altre informazioni](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sulla protezione di SharePoint.


## <a name="protect-dynamics-ax"></a>Proteggere Dynamics AX

Il ripristino del sito Azure consente di proteggere la soluzione Dynamics AX ERP by:

- Coordinazione replica dell'intero Dynamics AX ambiente (livelli Web e AOS, livelli di database, SharePoint) in Azure o a un sito secondario.
- Semplificare la migrazione delle distribuzioni Dynamics AX nel cloud (Azure).
- Semplificazione sviluppo di applicazioni Dynamics AX e verifica mediante la creazione di una copia di produzione su richiesta, di test e debug.

[Altre informazioni](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sulla protezione di controllo corrispondente.

## <a name="protect-rds"></a>Proteggere i servizi desktop remoto

Servizi (desktop remoto) consente a un'infrastruttura VDI (VDI), basata sulla sessione desktop e applicazioni, consentendo agli utenti di lavorare ovunque. Il ripristino del sito di Azure consente di:

- Replicare gestito o desktop virtuali in pool di applicazioni remote e un sito secondario e sessioni a un sito secondario o Azure.
- Ecco cosa è possibile replicare:

**SERVIZI DESKTOP REMOTO** | **Replicare macchine virtuali di Hyper-V in un sito secondario** | **Replicare macchine virtuali Hyper-V in Azure** | **Replicare macchine virtuali VMware in un sito secondario** | **Replicare macchine virtuali VMware Azure** | **Replicare server fisici in un sito secondario** | **Replicare server fisici Azure**
---|---|---|---|---|---|---
**In pool Desktop virtuale (non gestita)** | Sì | No | Sì | No | Sì | No
**In pool Desktop virtuale (gestito e senza UDP)** | Sì | No | Sì | No | Sì | No
**Applicazioni remote e sessioni Desktop (senza UDP)** | Sì | Sì | Sì | Sì | Sì | Sì


[Altre informazioni](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sulla protezione RDS.


## <a name="protect-exchange"></a>Proteggere con Exchange

Il ripristino del sito consente di proteggere Exchange, come indicato di seguito:

- Per le distribuzioni di piccole dimensioni di Exchange, ad esempio un server singolo o autonomo, il ripristino del sito può replicare ed esito negativo su Azure o un sito secondario.
- Per grandi distribuzioni, il ripristino del sito si integra con DAGS di Exchange.
- Exchange DAGs rappresentano la soluzione consigliata per il ripristino di emergenza di Exchange in un'organizzazione.  Piani di ripristino di ripristino di siti possono includere DAGs per la gestione di failover DAG nei vari siti.


[Altre informazioni](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sulla protezione di Exchange.

## <a name="protect-sap"></a>Proteggere SAP

Utilizzare il ripristino del sito per proteggere la distribuzione di SAP, come indicato di seguito:

- Attivare la protezione della distribuzione SAP intera, mediante la replica di livelli di distribuzione diversi in Azure o a un sito secondario.
- Semplificare la migrazione cloud, utilizzando il ripristino del sito per eseguire la migrazione della distribuzione SAP in Azure.
- Semplificare lo sviluppo di SAP e la verifica, creando un analogo produzione copiare richiesta per la verifica e il debug di applicazioni.

[Altre informazioni](http://aka.ms/asr-sap) sulla protezione SAP.

## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per la distribuzione il ripristino del sito](site-recovery-best-practices.md) 
