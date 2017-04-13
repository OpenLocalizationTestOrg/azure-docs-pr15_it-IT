<properties
   pageTitle="Procedure consigliate per gli aggiornamenti Software in Microsoft Azure IaaS | Microsoft Azure"
   description="Articolo fornisce un insieme di procedure consigliate per gli aggiornamenti software in un ambiente di Microsoft Azure IaaS.  È destinato ai professionisti IT e gli analisti della sicurezza che si occupano di modificare un controllo, gestione degli aggiornamenti e risorse di software su base giornaliera, inclusi i responsabili per le attività di sicurezza e conformità della propria organizzazione."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Procedure consigliate per gli aggiornamenti software in Microsoft Azure IaaS

Prima di leggere i qualsiasi tipo di discussione sulle procedure consigliate per un ambiente di Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , è importante sapere quali sono gli scenari che sarà necessario per la gestione degli aggiornamenti software e le responsabilità. La figura seguente deve comprendere questi limiti:

![Modelli cloud e delle responsabilità](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

La colonna all'estrema sinistra mostra sette responsabilità (definita nelle sezioni che seguono) che le organizzazioni necessario prendere in considerazione, ognuno dei quali collaborazione per la sicurezza e privacy di un ambiente informatico.
 
Classificazione di dati e responsabilità e protezione Client e del punto finale sono le responsabilità esclusivamente nel dominio dei clienti e responsabilità fisici, Host e di rete sono nel dominio del provider di servizi cloud nei modelli PaaS e SaaS. 

Le responsabilità rimanenti vengono condivisi tra i clienti e provider di servizi cloud. Alcune responsabilità richiedono il CSP e cliente gestire e amministrare la responsabilità insieme, tra cui il controllo dei propri domini. Ad esempio, valutare la possibilità di identità e accedere a gestione quando si usa Azure servizi Active Directory; la configurazione dei servizi, ad esempio l'autenticazione a più fattori al cliente, ma garantire funzionalità efficace la responsabilità di Microsoft Azure.

> [AZURE.NOTE] Per ulteriori informazioni sulle responsabilità condivisa nel cloud, leggere [Condivisa responsabilità per il Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Questi essere applicati anche in uno scenario ibrido nel punto in cui la propria azienda Usa macchine virtuali di IaaS Azure in grado di comunicare con le risorse locali, come illustrato nella figura seguente.

![Scenario ibrido tipica con Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Valutazione iniziale

Anche se la propria azienda utilizza già un sistema di gestione di aggiornamento e si dispongono già di criteri di aggiornamento software, è importante frequentemente rivedere valutazioni criteri precedenti e li aggiorna in base alle specifiche esigenze. Questo significa che è necessario avere familiarità con lo stato corrente di risorse della società. Per raggiungere questo stato, è necessario conoscere:

-   Computer fisici e virtuali fanno parte dell'azienda.

-   Sistemi operativi e le versioni in esecuzione su ognuno di questi computer fisici e virtuali.

-   Aggiornamenti software in ogni computer (versioni dei service pack e aggiornamenti software e altre modifiche).

-   La funzione ogni computer viene eseguita all'interno dell'organizzazione.

-   Le applicazioni e programmi in esecuzione su ciascun computer.

-   La proprietà e informazioni di contatto per ogni computer.

-   Le risorse presentano nell'ambiente e il valore relativo per determinare quali aree necessità attenzione al massimo e protezione.

-   Problemi noti di protezione e i processi aziendali sono in posizione per l'identificazione dei problemi di sicurezza o le revisioni nel livello di protezione.

-   Contromisure distribuita per proteggere l'ambiente.

È necessario aggiornare periodicamente queste informazioni e dovrebbe essere immediatamente disponibile per utenti che partecipano il processo di gestione di aggiornamenti software.

## <a name="establish-a-baseline"></a>Stabilire una previsione

Una parte importante del processo di gestione di aggiornamento software crea iniziale installazioni standard di versioni del sistema operativo, applicazioni e hardware per i computer fanno parte dell'azienda; si tratta le previsioni. Una previsione è la configurazione di un prodotto o sistema in un punto specifico nel tempo. Un'applicazione o un sistema operativo previsto, ad esempio, consente di ricostruire un computer o servizio a uno stato specifico.

Le previsioni forniscono la base per l'individuazione e risoluzione dei problemi potenziali e semplificano il processo di gestione di aggiornamento software, riducendo il numero di aggiornamenti software che è necessario distribuire fanno parte dell'azienda e aumentando le possibilità di eseguire il monitoraggio delle conformità.

Dopo aver completato il controllo iniziale dell'azienda, è necessario utilizzare le informazioni ricevute dal controllo per definire una baseline operativa per i componenti IT dell'ambiente di produzione. Un numero di linee di base potrebbe essere necessario, a seconda di diversi tipi di componenti hardware e software distribuita nell'ambiente di produzione.

Ad esempio, alcuni server richiede un aggiornamento software per impedire che sporgente quando si immette il processo di arresto durante l'esecuzione di Windows Server 2012. Una previsione per questi server deve includere questo aggiornamento software.

In organizzazioni di grandi dimensioni, è spesso utile dividere i computer fanno parte dell'azienda in categorie beni e mantenere ogni categoria in una previsione standard usando le stesse versioni di software e aggiornamenti. È quindi possibile utilizzare queste categorie bene nella definizione delle priorità di una distribuzione di aggiornamenti software.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>Sottoscrivere i servizi di notifica di aggiornamento software appropriato

Dopo avere eseguito un controllo iniziale del software in uso nell'organizzazione, è necessario determinare il metodo migliore per la ricezione delle notifiche di nuovi aggiornamenti software per ogni prodotto software e versione. A seconda del prodotto software il migliore metodo di notifica potrebbe essere notifiche tramite posta elettronica, siti Web o pubblicazioni informatiche.

Ad esempio Microsoft Security Response Center (MSRC) risponde a tutti i problemi relativi alla protezione sui prodotti Microsoft e fornisce Microsoft Security Bulletin Service, una notifica tramite posta elettronica gratuito di vulnerabilità identificate e aggiornamenti software rilasciati per risolvere queste vulnerabilità. È possibile sottoscrivere questo servizio in http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Considerazioni sull'aggiornamento software

Dopo avere eseguito un controllo iniziale del software in uso nell'organizzazione, è necessario determinare i requisiti necessari per installare il sistema di gestione di aggiornamento software, che dipende dal sistema di gestione di aggiornamento software che si sta utilizzando. Per Windows Server Update Services leggere [Le procedure consigliate con Windows Server Update Services](https://technet.microsoft.com/library/Cc708536), leggere [pianificazione aggiornamenti Software in Gestione configurazione](https://technet.microsoft.com/library/gg712696)per System Center.

Esistono tuttavia alcune considerazioni generali e le procedure consigliate che è possibile applicare indipendentemente dalla soluzione che si sta utilizzando come illustrato nella sezione seguente.

### <a name="setting-up-the-environment"></a>Impostazione dell'ambiente

Valutare la possibilità di consigliate seguenti quando si intende installare il software di aggiornare l'ambiente di gestione:

-   **Crea raccolte di aggiornamento software di produzione in base a criteri stabile**: In generale, uso dei criteri stabile creare raccolte per inventario di aggiornamento del software e distribuzione consente di semplificare tutte le fasi del processo di gestione di aggiornamento software. Criteri stabili possono includere la versione del sistema operativo client installato e livello di service pack, ruolo di sistema o l'organizzazione di destinazione.

-   **Crea raccolte pre-produzione che includono i computer di riferimento**: insieme pre-produzione deve includere representative configurazioni di versioni del sistema operativo, riga del software aziendale e altro software in esecuzione nell'organizzazione.

È anche necessario prendere in considerazione nel punto in cui il server di aggiornamento software sarà trovano, se si trovano nell'infrastruttura di Azure IaaS nel cloud o se sia locale. È importante stabilire ciò avviene perché è necessario valutare la quantità di traffico tra le risorse locali e infrastruttura di Azure. Per ulteriori informazioni su come connettersi infrastruttura locale in Azure, leggere [connettere una rete locale a una rete Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) .

Le opzioni di progettazione che determineranno nel punto in cui sarà possibile individuare il server di aggiornamento varia in base all'infrastruttura corrente e il sistema di aggiornamento software attualmente in uso. Per Windows Server Update Services leggere [Distribuire servizi di aggiornamento di Windows Server nell'organizzazione](https://technet.microsoft.com/library/hh852340.aspx) e per System Center Configuration Manager letto [Guida alla pianificazione per i siti e gerarchie in Gestione configurazione](https://technet.microsoft.com/library/Gg712681.aspx).

### <a name="backup"></a>Copia di backup

Esecuzione di backup regolari sono importanti non solo per il software aggiornamento piattaforma di gestione stesso ma anche per i server che verranno aggiornati. Le organizzazioni che hanno un [processo di modifica](https://technet.microsoft.com/library/cc543216.aspx) in posizione richiederà IT giustificare i motivi per perché è necessario aggiornare il server, i tempi di inattività stimata e impatto possibili. Per assicurarsi di aver configurato un ripristino in posizione nel caso in cui si verifica un errore di un aggiornamento, assicurarsi di eseguire regolarmente il backup del sistema.

Alcune opzioni di backup per Azure IaaS includono:

-   [Protezione di carico di lavoro IaaS Azure con Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Eseguire il backup macchine virtuali di Azure](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Monitoraggio

È perfettamente periodicamente per controllare il numero di mancanti o installati gli aggiornamenti o aggiornamenti con lo stato non completo, per ogni aggiornamento software autorizzato. Analogamente, reporting aggiornamenti software che non sono ancora autorizzati può facilitare decisioni di distribuzione più semplice.

È anche necessario considerare le seguenti operazioni:

-   Eseguire un controllo degli aggiornamenti di sicurezza applicabili e installati per tutti i computer dell'azienda.

-   Autorizzare e distribuire gli aggiornamenti ai computer appropriati.

-   Tenere traccia dell'inventario e aggiornare lo stato di installazione e stato di avanzamento per tutti i computer della società.

Provare a inoltre a caratteri generali che sono stati illustrati in questo articolo, è necessario prendere in considerazione anche ogni prodotto 's migliore, ad esempio: se si dispone di una macchina virtuale in Azure con SQL Server, verificare che si sta seguendo il suggerimento di aggiornamenti software corrispondente a tale prodotto.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare le istruzioni riportate in questo articolo per risultare utili per determinare le opzioni consigliate per gli aggiornamenti software per macchine virtuali all'interno di Azure IaaS. Sono disponibili numerose analogie tra consigliate aggiornamento software in un Data Center tradizionale e Azure IaaS, pertanto è consigliabile valutare i criteri di aggiornamento software correnti per includere macchine virtuali di Azure e le procedure consigliate pertinenti in questo articolo nel processo di aggiornamento software globale.
