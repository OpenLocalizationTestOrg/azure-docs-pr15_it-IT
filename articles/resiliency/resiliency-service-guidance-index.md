<properties
   pageTitle="Servizio indicazioni sulla resilienza | Microsoft Azure"
   description="Collegamenti a emergenza e indicazioni sulla resilienza e la disponibilità preventiva per i servizi di Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Guida di Microsoft Azure servizio sulla resilienza
Microsoft Azure è progettato per fornire le risorse che necessarie, quando è necessario. Come parte di una buona progettazione e procedure operative, è necessario conoscere sia sulla progettazione di uso dei servizi Azure a disponibilità elevata, nonché cosa fare se l'applicazione è interessato da un'interruzione del servizio. Per facilitare l'operazione, il documento contiene collegamenti a istruzioni di ripristino di emergenza, nonché indicazioni per la progettazione di vari servizi Azure.

##<a name="disaster-recovery-guidance"></a>Guida di ripristino di emergenza
Le indicazioni di ripristino di emergenza dei collegamenti seguenti possono fornire le informazioni necessarie per sfruttare l'applicazione in linea rapidamente se sono interessati da un'interruzione del servizio Azure. Questi collegamenti sono stati creati per rispondere alla domanda, "sta vengano interessati da un'interruzione del servizio Azure, quali operazioni possono eseguire?"

##<a name="design-guidance"></a>Indicazioni per la progettazione
Progettazione indicazioni sui collegamenti seguenti sono progettazione e architettura che è stato creato per capire meglio il modo migliore per utilizzare ogni servizio Azure in modo da ottimizzare i tempi di attività dell'applicazione. Questi collegamenti sono stati creati per rispondere alla domanda "Come si può verificare se che un bug, errori hardware, interruzione del servizio o altri problemi non influiscono su disponibilità complessiva dell'applicazione?" Se non c'è alcun indicazioni specifiche per il servizio che attualmente cercata, l'articolo [disponibilità per applicazioni basate su Microsoft Azure](./resiliency-high-availability-azure-applications.md) potrebbe richiedere ulteriori informazioni che consentono di nella progettazione. 

##<a name="service-guidance"></a>Informazioni aggiuntive sui servizi
| Servizio  | Guida di ripristino di emergenza | Indicazioni per la progettazione |
|:---------|:--------------------------:|:------------------:|
| [Servizi cloud] (https://azure.microsoft.com/services/cloud-services/ "Servizi Cloud Azure")       | [collegamento] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Guida di ripristino di emergenza servizi Cloud Windows Azure")   | Non disponibile |
| [Archivio di chiave] (https://azure.microsoft.com/services/key-vault/ "Archivio chiave Azure")                      | [collegamento] (../key-vault/key-vault-disaster-recovery-guidance.md "Indicazioni di ripristino di emergenza Azure chiave archivio")        | Non disponibile |
| [Spazio di archiviazione] (https://azure.microsoft.com/services/storage/ "Spazio di archiviazione Azure")                            | [collegamento] (../storage/storage-disaster-recovery-guidance.md "Indicazioni di ripristino di emergenza lo spazio di archiviazione di Azure")          | Non disponibile |
| [Database SQL] (https://azure.microsoft.com/services/sql-database/ "Database SQL Azure")           | [collegamento] (../sql-database/sql-database-disaster-recovery.md  "Guida di ripristino di emergenza Database SQL Azure")    | [collegamento] (../sql-database/sql-database-business-continuity.md "Panoramica di continuità aziendale con Database di SQL Azure") |
| [Macchine virtuali] (https://azure.microsoft.com/services/virtual-machines/ "Macchine virtuali di Azure") | [collegamento] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Guida di ripristino di emergenza macchine virtuali di Azure") | Non disponibile |
| [Rete virtuale] (https://azure.microsoft.com/services/virtual-network/ "Azure virtuali")    | [collegamento] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Guida di ripristino di emergenza virtuali Azure")  | Non disponibile |

##<a name="next-steps"></a>Passaggi successivi
Se si sta cercando indicazioni per un pubblico più ampio incentrato sui sistemi e soluzioni, leggere [il ripristino di emergenza e disponibilità per applicazioni basate su Microsoft Azure](https://aka.ms/drtechguide).
