<properties
pageTitle="Considerazioni per l'utilizzo di immagini di macchine Virtuali Oracle | Microsoft Azure"
description="Informazioni sulle configurazioni supportate e limitazioni per una macchina virtuale Oracle nel Server di Windows Azure prima di distribuire."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Considerazioni varie immagini macchina virtuale Oracle



In questo articolo illustra considerazioni per Oracle macchine virtuali di in Azure, che sono basate su immagini di software Oracle fornite da Oracle.  

-  Immagini macchina virtuale del Database Oracle
-  Immagini della macchina virtuale WebLogic Server Oracle
-  Immagini della macchina virtuale JDK Oracle

##<a name="oracle-database-virtual-machine-images"></a>Immagini macchina virtuale del Database Oracle
### <a name="clustering-rac-is-not-supported"></a>Cluster (diritti) non è supportata

Azure attualmente non supporta Oracle Real Application cluster (diritti) del Database Oracle. Solo le istanze di autonoma di Database Oracle sono possibili. In questo modo Azure attualmente non supporta condivisione disco virtuale in modalità lettura/scrittura tra più istanze di macchina virtuale. Anche UDP multicast non è supportato.

### <a name="no-static-internal-ip"></a>Nessun IP interno statico

Azure assegna ogni macchina virtuale un indirizzo IP interno. A meno che la macchina virtuale fa parte di una rete virtuale, l'indirizzo IP del computer virtuale dinamico e potrebbero essere modificati dopo il riavvio del computer virtuale. Si possono causare problemi perché il Database Oracle prevede l'indirizzo IP statico. Per evitare il problema, provare ad aggiungere la macchina virtuale a una rete virtuale Azure. Per ulteriori informazioni, vedere [Virtuali](https://azure.microsoft.com/documentation/services/virtual-network/) e [creare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="attached-disk-configuration-options"></a>Opzioni di configurazione disco collegato

È possibile inserire i file di dati sul disco del sistema operativo del computer virtuali o nei dischi collegati, noto anche come dischi di dati. Dischi collegati potrebbero offrire maggiore flessibilità dimensioni e prestazioni rispetto al disco del sistema operativo. Il disco di sistema operativo potrebbe essere preferibile solo per i database in 10 gigabyte (GB).

Il servizio di archiviazione Blob Azure si basano dischi collegati. Ogni disco è in grado di un massimo teorico di circa 500 operazioni di input/output al secondo (IOPS). Le prestazioni dei dischi collegati potrebbero non essere ottimale inizialmente e IOPS miglioramento delle prestazioni notevolmente dopo un periodo "burn-in" di circa 90 60 minuti di funzionamento continuo. Se un disco rimane poi inattivo, prestazioni IOPS potrebbero diminuire fino a un altro punto burn-in di funzionamento continuo. In breve, più attivo al disco, più di frequente è possibile ottenere prestazioni ottimali IOPS approccio.

Sebbene l'approccio più semplice consiste nel connettere la macchina virtuale un singolo disco e spostare file di database del disco, questo approccio è più limitazione in termini di prestazioni. Se, tuttavia, è spesso possibile migliorare le prestazioni di IOPS efficace se si usa più dischi collegati, distribuire i dati del database su di essi e quindi Gestione di archiviazione automatica Oracle (ASM). Per ulteriori informazioni, vedere [Panoramica di archiviazione automatica Oracle](http://www.oracle.com/technetwork/database/index-100339.html) . Sebbene sia possibile usare striping di più dischi al livello del sistema operativo, questo approccio è sconsigliato perché non è noto per migliorare le prestazioni di IOPS.

Valutare la possibilità di due diversi approcci per associare più dischi in base a se si desidera assegnare la priorità le prestazioni di operazioni di lettura e scrittura per il database:

- **ASM Oracle autonomamente** è probabilmente migliorare le prestazioni di operazione di scrittura, ma peggiore IOPS per operazioni di lettura in confronto approccio utilizzando matrici disco. La figura seguente illustra in modo logico questa disposizione.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Valutare il compromesso tra le prestazioni di scrittura e lettura alla scala cronologica del caso. I risultati effettivi possono variare quando si utilizza questo.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Alta disponibilità e considerazioni sul ripristino

Quando si usano Database Oracle in macchine virtuali di Azure, è responsabile per l'implementazione di una soluzione di ripristino di alta disponibilità ed evitare qualsiasi tempo di inattività. Inoltre è responsabile per il backup dei dati e applicazione.

È possibile ottenere alto disponibilità e ripristino di emergenza per Oracle Database Enterprise Edition (senza diritti) su Azure utilizzando [protezione dei dati, Attiva protezione dei dati](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)o [Oracle d'oro avanzata](http://www.oracle.com/technetwork/middleware/goldengate)con due database in due macchine virtuali distinte. Entrambe le macchine virtuali dovrebbe essere lo stesso [servizio cloud](virtual-machines-linux-classic-connect-vms.md) e della stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurarsi che accedono tra loro tramite l'indirizzo IP permanente privato.  Inoltre, è consigliabile posizionare le macchine virtuali nella stesso [disponibilità impostato](virtual-machines-windows-manage-availability.md) per consentire di Azure per inserirli in domini separati guasto ed eseguire l'aggiornamento di domini. Solo le macchine virtuali lo stesso servizio cloud partecipare lo stesso set di disponibilità. Ogni macchina virtuale deve contenere almeno 2 GB di memoria e 5 GB di spazio su disco.

Con protezione di dati Oracle, è possibile ottenere disponibilità con un database primario in una macchina virtuale, un database (standby) secondario in un'altra macchina virtuale e la replica unidirezionale configurare tra di esse. Il risultato è accesso in lettura alla copia del database. Con Oracle GoldenGate, è possibile configurare la replica bidirezionale tra i due database. Per informazioni su come configurare una soluzione di disponibilità per i database tramite questi strumenti, vedere documentazione di [Protezione dei dati attivi](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sul sito Web Oracle. Se è necessario lettura e scrittura accesso per la copia del database, è possibile utilizzare [Protezione dei dati attivi Oracle](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Immagini della macchina virtuale WebLogic Server Oracle

-  **Cluster è supportato solo nella versione Enterprise.** Sono autorizzati a utilizzare il servizio cluster WebLogic solo quando si usa WebLogic Server Enterprise Edition. Non utilizzare cluster con WebLogic Server Standard Edition.

-  **Timeout di connessione:** Se l'applicazione si basa su connessioni a endpoint pubbliche di un altro servizio di Azure cloud (ad esempio, un servizio di livello database), Azure potrebbe chiudere le connessioni aperte dopo quattro minuti di inattività. Caratteristiche e le applicazioni che utilizzano i pool di connessioni può influire perché le connessioni inattive per più di questo limite non potrebbero non è più rimangono valide. Se questo problema riguarda l'applicazione, è possibile attivare "Mantieni attivo" logica sul pool di connessione.

    Se un endpoint è *interno* per la distribuzione di servizio cloud Azure (ad esempio una macchina virtuale database autonomo all'interno del servizio cloud *stesso* come macchine virtuali WebLogic), quindi la connessione è diretta non dipende dal servizio di bilanciamento del carico Azure e pertanto non è soggetto a un timeout della connessione.

-  **Multicast UDP non è supportato.** Azure supporta unicasting UDP, ma non multicasting o la trasmissione. WebLogic Server in grado di si basano sulle funzionalità di Azure UDP unicast. Per migliori risultati che si basa su UDP unicast, è consigliabile che le dimensioni di cluster WebLogic verranno mantenuti statica, o verranno mantenuti con non più di 10 server gestiti incluse nel cluster.

-  **WebLogic Server prevede porte pubblici e privati in modo che corrisponda per l'accesso T3 (ad esempio, quando si utilizza Enterprise JavaBeans).** Valutare la possibilità di uno scenario con più livelli in un'applicazione servizio di livello (EJB) è in esecuzione in un cluster di WebLogic Server costituito da due o più server gestito, in un servizio cloud denominato **SLWLS**. Il livello di client è in un servizio cloud diverso, eseguire un programma Java semplice sta tentando di chiamare EJB nel livello dei servizi. Perché è necessario il livello dei servizi di bilanciamento del carico, è necessario che un endpoint di bilanciamento del carico pubblico creato per le macchine virtuali cluster WebLogic Server. Se la porta privata specificate per l'endpoint è la differenza tra la porta pubblica (ad esempio 7006:7008), si verifica un errore, ad esempio le operazioni seguenti:

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    In questo modo per qualsiasi T3 l'accesso remoto, WebLogic Server prevede la porta di bilanciamento del carico e la porta del server WebLogic gestiti in modo che corrisponda. In questo caso, il client accede alla porta 7006 (porta di bilanciamento carico) e il server gestito è in ascolto 7008 (porta privato). Questa limitazione si applica solo per l'accesso T3, non HTTP.

    Per evitare questo problema, usare uno dei metodi descritti di seguito:

    -  Utilizzare gli stessi numeri di porta pubblici e privati per gli endpoint di bilanciamento del carico dedicati all'accesso T3.

    -  Includere il parametro JVM seguente quando si avvia WebLogic Server:

            -Dweblogic.rjvm.enableprotocolswitch=true

Per informazioni correlate, vedere KB articolo **860340.1** in <http://support.oracle.com>.

-  **Dinamico cluster Bilanciamento del carico e limitazioni.** Si supponga che si desidera utilizzare un cluster dinamico in WebLogic Server ed esposto tramite un endpoint bilanciamento del carico singolo, pubblico Azure. A tale scopo, purché si usa un numero di porta fisso per ognuno dei server gestiti (non in modo dinamico assegnato da un intervallo) e non inizino più server gestiti superiore a quello macchine tiene traccia dell'amministratore (vale a dire non più di un server gestito per ogni macchina virtuale). Se la configurazione genera più WebLogic Server verrà avviato superiore a quello macchine virtuali (ovvero, in cui più istanze di WebLogic Server condividono la stessa macchina virtuale), quindi non è possibile per più monitor delle istanze dei server WebLogic Server per associare a un determinato numero di porta: altri sul computer virtuale avrà esito negativo.

    Mano, se si configura il server di amministrazione per assegnare automaticamente i numeri di porta univoco per il server gestiti, quindi il bilanciamento del carico non è possibile perché Azure non supporta i mapping da una singola porta pubblico a più porte private, come sarebbero necessarie per la configurazione.

-  **Più istanze di Weblogic Server in una macchina virtuale.** In base ai requisiti della distribuzione, è consigliabile l'opzione per l'esecuzione di più istanze di WebLogic Server nello stesso computer virtuali, se la macchina virtuale sia sufficiente. Ad esempio, in una macchina virtuale di medie dimensioni, che contiene due core, è possibile scegliere di eseguire due istanze di WebLogic Server. Si noti tuttavia che è comunque consigliabile evitare di introdurre singoli punti di errore nell'architettura, sarebbe le maiuscole/minuscole se è stata usata una sola macchina virtuale che esegue più istanze di WebLogic Server. Utilizzando almeno due macchine virtuali potrebbero essere un approccio più efficace e ognuna di queste macchine virtuali e successivamente eseguire più istanze di WebLogic Server. Ognuna di queste istanze di WebLogic Server può essere ancora parte dello stesso cluster. Nota, tuttavia, attualmente non è possibile utilizzare Azure per gli endpoint di bilanciamento del carico esposti da tali distribuzioni WebLogic Server all'interno della stessa macchina virtuale perché di bilanciamento del carico Azure richiede i bilanciamento del carico dei server distribuito tra macchine virtuali univoche.

##<a name="oracle-jdk-virtual-machine-images"></a>Immagini della macchina virtuale JDK Oracle

-  **JDK 6 e 7 aggiornamenti più recenti.** Mentre si consiglia di utilizzare la versione più recente pubblica e supportata di linguaggio (attualmente Java 8), Azure rende JDK 6 e 7 immagini disponibili. Si tratta delle applicazioni legacy che non sono ancora pronte per l'aggiornamento a JDK 8. Mentre aggiornamenti apportati alle immagini JDK precedenti potrebbero non essere più disponibile al pubblico, dato la relazione di Microsoft con Oracle, 6 JDK e 7 immagini fornite da Azure devono contenere un aggiornamento più recente di pubblici normalmente offerta da Oracle a solo un gruppo di clienti supportati Oracle. Nuove versioni delle immagini JDK saranno disponibili nel tempo con le versioni aggiornate di JDK 6 e 7.

    JDK disponibili in presente JDK 6 e 7 immagini e il macchine virtuali e le immagini derivate da essi, può essere utilizzato solo all'interno di Azure.

-  **JDK 64 bit.** Le immagini di macchina virtuale Oracle WebLogic Server e le immagini di macchina virtuale JDK Oracle fornite da Azure contengono le versioni a 64 bit di Windows Server e JDK.

##<a name="additional-resources"></a>Risorse aggiuntive
[Immagini di macchina virtuale Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)
