<properties
   pageTitle="Cenni preliminari sulla sicurezza di Azure macchine virtuali | Microsoft Azure"
   description=" Azure macchine virtuali di fornire la flessibilità della virtualizzazione senza che sia necessario acquistare e gestire l'hardware che viene eseguita la macchina virtuale.  In questo articolo viene fornita una panoramica delle principali funzionalità di sicurezza di Azure che possono essere usate con macchine virtuali di Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Cenni preliminari sulla protezione macchine virtuali di Azure

Macchine virtuali di Azure consente di distribuire un'ampia gamma di soluzioni informatiche, in modo agile. Con il supporto per Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e servizi BizTalk di Windows Azure, è possibile distribuire qualsiasi lingua praticamente qualsiasi sistema operativo e qualsiasi carico di lavoro.

Una macchina virtuale Azure offre flessibilità di virtualizzazione senza che sia necessario acquistare e gestire l'hardware che viene eseguita la macchina virtuale.  È possibile compilare e distribuire le applicazioni con la certezza che i dati sono protetti e sicuro nel Data Center di protezione avanzata.

Con Azure, è possibile creare soluzioni di protezione avanzata, conforme che:

- Proteggere le macchine virtuali da virus e malware
- Crittografare i dati sensibili
- Proteggere il traffico di rete
- Identificare e rilevare rischi
- Requisiti di conformità

L'obiettivo di questo articolo è fornita una panoramica delle principali funzionalità di sicurezza di Azure che possono essere usate con macchine virtuali. Inoltre, sono disponibili collegamenti ad articoli che forniscono i dettagli di ogni caratteristica in modo che si acquisiscono informazioni.  

Le funzionalità di sicurezza macchina virtuale Azure principali da coprire in questo articolo:

- Antimalware
- Modulo di sicurezza hardware
- Crittografia del computer virtuale
- Copia di backup macchina virtuale
- Ripristino del sito Azure
- Rete virtuale
- Gestione dei criteri di sicurezza e la segnalazione
- Conformità

## <a name="antimalware"></a>Antimalware

Con Azure, è possibile utilizzare software antimalware di fornitori di sicurezza, ad esempio Microsoft, Symantec, Trend Micro, McAfee e Kaspersky per proteggere le macchine virtuali di file dannosi, adware e altri rischi. Vedere la sezione approfondimenti seguente per trovare articoli disponibili in partner soluzioni.

Microsoft Antimalware per servizi Cloud Windows Azure e macchine virtuali è una funzionalità di protezione in tempo reale che consente di individuare e rimuovere virus, spyware e altro software dannoso.  Microsoft Antimalware fornisce configurabili avvisi quando noto software dannoso o indesiderato tenta di installare stesso o eseguire sui sistemi Azure.

Microsoft Antimalware è una soluzione single-agent per applicazioni e ambienti tenant, progettati per l'esecuzione in background senza intervento. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro applicazione, con una base sicura-per impostazione predefinita o configurazione personalizzata, inclusi il monitoraggio antimalware avanzata.

Quando si distribuisce e Abilita Antimalware Microsoft, sono disponibili le seguenti caratteristiche principali:

- Protezione in tempo reale - Monitor attività nei servizi Cloud e in macchine virtuali per rilevare e bloccare l'esecuzione di software dannoso.
- Funzionalità di analisi pianificata - periodicamente esegue la scansione target per rilevare software dannoso, inclusi in esecuzione programmi.
- Monitoraggio e aggiornamento di software dannoso, viene inserito automaticamente azione malware rilevato, ad esempio l'eliminazione o la messa in quarantena file dannosi e pulizia le voci del Registro di sistema dannoso.
- Aggiornamenti di firma - automaticamente installazioni le firme di protezione più recenti (definizioni di virus) per garantire la protezione sia aggiornato su una frequenza predeterminata.
- Motore antimalware automaticamente gli aggiornamenti: aggiorna il modulo Microsoft Antimalware.
- Piattaforma antimalware automaticamente gli aggiornamenti: aggiorna la piattaforma Microsoft Antimalware.
- Attiva protezione - dei report per i metadati di telemetria Azure sulle minacce rilevate e risorse sospette per garantire una risposta rapida e attiva l'invio di firma icona in tempo reale tramite Microsoft Attiva protezione sistema (mappe).
- Esempi di creazione di report - fornisce rapporti ed esempi per il servizio Microsoft Antimalware per perfezionare il servizio e abilitare la risoluzione dei problemi.
- Esclusioni – consente agli amministratori di servizi e applicazioni configurare alcuni file, processi e unità escluderli dalle protezione e l'analisi per prestazioni e altri motivi.
- La raccolta degli eventi antimalware - registrate antimalware integrità dei servizi, verificare l'affidabilità le attività le azioni di correzione eseguite nel registro eventi di sistema operativo e consente di raccogliere in considerazione lo spazio di archiviazione di Azure del cliente.

Altre informazioni: per altre informazioni su software antimalware per proteggere le macchine virtuali, vedere:

- [Microsoft Antimalware per i servizi Cloud Azure e macchine virtuali](../security/azure-security-antimalware.md)
- [Distribuire soluzioni Antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Come installare e configurare Trend Micro complete Security come servizio in una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nuove opzioni di Antimalware per la protezione delle macchine virtuali Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Soluzioni di protezione in Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Protezione hardware modulo

Crittografia e autenticazione non migliora sicurezza a meno che non sono protetti i tasti. Per semplificare la gestione e protezione delle informazioni riservate critici e dei tasti di archiviazione in Azure chiave archivio. Archivio chiave consente di archiviare le chiavi nei moduli di sicurezza hardware (HSM) Certificate a FIPS 140-2 livello 2 standard. Le chiavi di crittografia di SQL Server per il backup o [la crittografia dei dati trasparente](https://msdn.microsoft.com/library/bb934049.aspx) possono tutti essere archiviate nella chiave archivio con le chiavi o informazioni riservate dalle applicazioni di posta. Autorizzazioni e accesso a questi elementi protetti vengono gestite tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Ulteriori informazioni:

- [Che cos'è archivio chiave Azure?](../key-vault/key-vault-whatis.md)
- [Guida introduttiva di Azure chiave archivio](../key-vault/key-vault-get-started.md)
- [Azure chiave archivi blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Crittografia del computer virtuale

Crittografia disco Azure è una nuova funzionalità che consente di crittografare il disco di Windows e Linux Azure Virtual Machine. Crittografia disco Azure utilizza la funzionalità di [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard di settore di Windows e la funzionalità di [crittografia di data mining](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e dischi di dati.

La soluzione è integrata con Azure chiave archivio consentono di controllare e gestire le chiavi di crittografia disco e le informazioni riservate nel proprio abbonamento archivio chiave, assicurandosi che tutti i dati nei dischi macchina virtuale vengono crittografati inattivi in archiviazione Azure.

Ulteriori informazioni:

- [Crittografia di Azure disco per Windows e macchine virtuali Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Crittografia disco Azure per Linux e macchine virtuali di Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Crittografare una macchina virtuale](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Copia di backup macchina virtuale

Backup Azure è una soluzione scalable che consente di proteggere i dati dell'applicazione con zero investimento di capitale e costi operativi minimi. Gli errori dell'applicazione possono danneggiare i dati e gli errori di risorse umani possono comportare bug nelle applicazioni. Con il Backup di Azure, sono protetti macchine virtuali che eseguono Windows e Linux.

Ulteriori informazioni:

- [Che cos'è il Backup di Azure?](../backup/backup-introduction-to-azure-backup.md)
- [Percorso formativo Backup Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Servizio di Backup Azure - domande frequenti](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Ripristino del sito Azure

Una parte importante della strategia di gestione delle BCDR dell'organizzazione è scoprire come stare carichi di lavoro aziendali e le App e in esecuzione quando si verificano interruzioni pianificate e impreviste. Il ripristino del sito Azure consente di progettare replica, failover e ripristino di carichi di lavoro e App in modo che siano disponibili da una posizione secondaria se la posizione principale non è raggiungibile.

Ripristino del sito:

- **Semplifica la strategia BCDR** , ovvero il ripristino del sito rende più facile gestire replica, failover e ripristino di più carichi di lavoro di business e App da un'unica posizione. Il ripristino del sito coordina replica e failover ma non intercetta i dati dell'applicazione o le informazioni.
- **Fornisce replica flessibile** , utilizzando il ripristino del sito è possibile replicare carichi di lavoro in esecuzione in macchine virtuali di Hyper-V, macchine virtuali VMware e server fisici Windows/Linux.
- **Supporta failover e ripristino** , ovvero il ripristino del sito fornisce failover test per supportare esercitazioni di ripristino di emergenza senza influenzare ambienti di produzione. Con una perdita di dati minima (a seconda della frequenza di replica) per guasti imprevisti, è possibile eseguire failover pianificato con una perdita di dati zero per le interruzioni previste o failover non previsti. Dopo il failover, è possibile failback ai siti principali. Il ripristino del sito fornisce piani di ripristino che possono includere script e le cartelle di lavoro di automazione Azure in modo che è possibile personalizzare failover e ripristino delle applicazioni a più livelli.
- **Elimina Data Center secondario** , è possibile replicare in un sito secondario in locale o su Azure. Utilizzo di Azure come destinazione di emergenza Elimina il costo e le complessità della gestione di un sito secondario. Replicato dati vengono archiviati in archiviazione Azure.
- **Integrazione con le tecnologie BCDR esistenti** , ovvero il ripristino del sito collabora con altre caratteristiche BCDR dell'applicazione. Ad esempio, è possibile utilizzare il ripristino del sito per la protezione SQL Server back-end di carichi di lavoro aziendali. Sono inclusi supporto nativo per SQL Server AlwaysOn gestire il failover dei gruppi di disponibilità.

Ulteriori informazioni:

- [Che cos'è il ripristino del sito di Azure?](../site-recovery/site-recovery-overview.md)
- [Come funziona il ripristino del sito Azure?](../site-recovery/site-recovery-components.md)
- [Cosa carichi di lavoro sono protetti tramite il ripristino del sito di Azure?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Rete virtuale

Macchine virtuali richiedono connettività di rete. Per supportare tale requisito, Azure richiede macchine virtuali di essere connessi a una rete virtuale Azure. Una rete virtuale Azure è un costrutto logico integrato nella parte superiore dell'infrastruttura di rete di Azure fisica. Ogni virtuali Azure logico è isolata da tutte le altre reti virtuale di Azure. L'isolamento aiuta accertarsi che il traffico di rete di una distribuzione non è accessibile per gli altri utenti di Microsoft Azure.

Ulteriori informazioni:

- [Cenni preliminari sulla protezione di rete Azure](security-network-overview.md)
- [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)
- [Le funzionalità di rete e partnership per scenari aziendali](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestione dei criteri di sicurezza e la segnalazione

Azure Security Center consente di impedire, rilevare e rispondere a rischi e offre che maggiore visibilità e controllare i la protezione delle risorse di Azure. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Centro protezione di Azure consente di ottimizzare e monitorare sicurezza macchina virtuale tramite:

- Offrire come macchina virtuale [suggerimenti per la sicurezza](../security-center/security-center-recommendations.md) applicare gli aggiornamenti di sistema, configurare gli endpoint ACL, abilitare antimalware, abilitare i gruppi di sicurezza di rete e applicare la crittografia del disco.
- Monitorare lo stato del macchine virtuali

Ulteriori informazioni:

- [Introduzione al Centro protezione di Azure](../security-center/security-center-intro.md)
- [Domande frequenti su Centro protezione di Azure](../security-center/security-center-faq.md)
- [Centro protezione di Azure pianificazione e attività operative](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformità

Azure macchine virtuali è certificata per FISMA, FedRAMP, HIPAA, PCI DSS livello 1 e altre applicazioni di conformità chiave. Questa certificazione rende più facile per applicazioni Azure soddisfare i requisiti di conformità e per l'organizzazione risolvere una vasta gamma di requisiti normativi nazionali e internazionali.

Ulteriori informazioni:

- [Centro protezione di Microsoft: conformità](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Attendibile Cloud: Protezione di Microsoft Azure, Privacy e conformità](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
