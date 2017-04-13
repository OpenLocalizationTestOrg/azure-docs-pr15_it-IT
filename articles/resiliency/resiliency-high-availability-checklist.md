<properties
   pageTitle="Elenco di controllo disponibilità | Microsoft Azure"
   description="Un rapido elenco di controllo delle impostazioni e le azioni che è possibile eseguire per verificare che siano migliorare la disponibilità di applicazioni con Azure."
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

#<a name="high-availability-checklist"></a>Elenco di controllo di disponibilità
Uno dei principali vantaggi dell'utilizzo di Azure è la possibilità di aumentare la disponibilità (e scalabilità) delle applicazioni con l'aiuto del cloud. Per assicurarsi che si effettua la maggior parte di queste opzioni, l'elenco di controllo riportata di seguito è utilizzata per consentono con alcune nozioni di base sull'infrastruttura a chiave per garantire che le applicazioni siano flessibile. 

>[AZURE.NOTE] La maggior parte dei suggerimenti seguenti sono operazioni che possono essere implementati in qualsiasi momento nell'applicazione e pertanto sono ideali per "veloce correzioni". La soluzione migliore a lungo termine spesso comporta la progettazione di un'applicazione creata per il cloud.  Per un elenco di controllo su queste (ulteriori progettazione orientato agli aree, leggere il nostro [elenco di controllo di disponibilità](../best-practices-availability-checklist.md).

###<a name="are-you-using-traffic-manager-in-front-of-your-resources"></a>Si sta utilizzando il traffico Manager che precede le risorse
Utilizzando il traffico Manager consente si indirizzare il traffico internet in più aree Azure o posizioni di Azure e locale. È possibile ripetere l'operazione per diversi motivi, incluse la latenza e disponibilità. Per ulteriori informazioni su come utilizzare Gestione il traffico per aumentare la flessibilità e distribuire il traffico per più paesi, leggere [Macchine virtuali in esecuzione in più Data Center su Azure disponibilità elevata](../guidance/guidance-compute-multiple-datacenters.md).

__Cosa succede se non si usa il traffico Manager?__ Se non si usa il traffico Manager che precede l'applicazione, sono limitate a una singola regione per le risorse. Limiti di dimensioni dell'azienda, aumenta la latenza per gli utenti che non siano troppo vicini l'area selezionata e riduce la protezione in caso di interruzione del servizio a livello di area geografica.

###<a name="have-you-avoided-using-a-single-virtual-machine-for-any-role"></a>Ha evitato utilizzando una singola macchina virtuale per qualsiasi ruolo?
Buona progettazione consente di evitare qualsiasi singolo punto di errore. È importante in tutta la progettazione di servizio (locale o nel cloud), ma è particolarmente utile nel cloud, di aumentare scalabilità e adattabilità attraverso scalabilità (aggiungendo macchine virtuali) anziché scalabilità (con una macchina virtuale più potente). Per ulteriori informazioni sulla progettazione applicazione scalable, leggere [disponibilità per applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).

__Cosa succede se si dispone di una singola macchina virtuale per un ruolo?__ Un unico computer è un singolo punto di errore e non è disponibile per il [Contratto di servizio Azure macchina virtuale](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). Nei casi migliori, l'applicazione verrà eseguito funziona correttamente ma questa non è una struttura flessibile, non è coperto dal contratto di servizio macchina virtuale Azure e i singoli punti aumenta errore la possibilità di inattività se si verifica un errore.

###<a name="are-you-using-a-load-balancer-in-front-of-your-applications-internet-facing-vms"></a>Si sta utilizzando un bilanciamento del carico davanti macchine virtuali esposto a internet dell'applicazione?
Servizi di bilanciamento del carico consente di distribuire il traffico in arrivo per l'applicazione su un numero non autorizzato di computer. È possibile aggiungere e rimuovere macchine dal bilanciamento del carico in qualsiasi momento, funziona bene con macchine virtuali (e con il ridimensionamento automatico macchina virtuale scala set) che consente di gestire facilmente un aumento del traffico o macchine Virtuali errori. Se si vuole saperne di più sui servizi di bilanciamento del carico, leggere il [servizio di bilanciamento del carico Azure Panoramica](../load-balancer/load-balancer-overview.md) e [l'esecuzione di più macchine virtuali in Azure per disponibilità e scalabilità](../guidance/guidance-compute-multi-vm.md).

__Cosa succede se non si usa un bilanciamento del carico che precede il macchine virtuali esposto a internet?__ Senza un bilanciamento del carico non sarà possibile ridimensionare (aggiungere più macchine virtuali) e l'unica opzione disponibile sarà di scalare (aumentare le dimensioni del computer virtuale basata sul web). Affrontare anche un singolo punto di errore con la macchina virtuale. Sarà anche necessario scrivere codice DNS per osservare se si hanno più di un computer esposto a internet e riassociare la voce DNS al nuovo computer che iniziare a scrivere relativa posizione.

###<a name="are-you-using-availability-sets-for-your-stateless-application-and-web-servers"></a>Sono si usando la disponibilità imposta per il server web e applicazione senza informazioni sullo stato?
Applicazione pratica di computer stesso livello di applicazione di un set di disponibilità rende nelle macchine virtuali idoneo per il contratto di servizio di macchine Virtuali di Azure. Parte di una disponibilità impostare anche garantisce che i computer vengono inseriti in domini aggiornamento diverso (ad esempio host diversi computer sono corretti orari) e fault domini (ad esempio passare computer host che condividono un alimentazione comuni e rete). Senza essere in un set di disponibilità, nelle macchine virtuali potrebbero trovarsi nello stesso computer host e pertanto potrebbe essere un singolo punto di errore che non è visibile all'utente. Per ulteriori informazioni su come aumentare la disponibilità delle proprie macchine virtuali utilizzando set di disponibilità, leggere [gestire la disponibilità di macchine virtuali](../virtual-machines/virtual-machines-windows-manage-availability.md).

__Cosa succede se non si usa una disponibilità set con le applicazioni senza informazioni sullo stato e un server web?__ Non si usa una disponibilità imposta indica che non è possibile sfruttare il contratto di servizio di macchine Virtuali di Azure. Inoltre, significa che i computer di tale livello dell'applicazione Impossibile tutti fuori rete se esiste un aggiornamento sul computer host (computer che ospita macchine virtuali in uso) o un errore hardware comuni.

###<a name="are-you-using-virtual-machine-scale-sets-vmss-for-your-stateless-application-or-web-servers"></a>Si sta utilizzando macchina virtuale scala set (VMSS) per il server web o applicazione senza informazioni sullo stato
Una buona progettazione scalable e flessibile utilizza VMSS per assicurarsi che è possibile ingrandimento/riduzione al numero di computer in un livello dell'applicazione (ad esempio il livello di web). VMSS consente di definire come il livello di applicazione Scale (aggiunta o rimozione di server in base a criteri che scelto). Se si desidera trovare ulteriori informazioni su come utilizzare set scala di Azure macchina virtuale per aumentare la flessibilità per il traffico picchi, panoramica [macchina virtuale scala set](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

__Cosa succede se non ci un Set di scala macchina virtuale con l'applicazione senza informazioni sullo stato del server web?__ Senza un VMSS è limitare le possibilità di scalare senza limiti e come ottimizzare l'utilizzo delle risorse. Una struttura priva di VMSS ha un limite di ridimensionamento superiore che dovranno essere gestite con codice aggiuntivo (o manualmente). Questa mancanza di un VMSS significa anche che l'applicazione può aggiungere e rimuovere computer (indipendentemente dal scala) che consentono di gestire grandi picchi di traffico non facilmente (ad esempio come durante una promozione o se il sito/app/prodotto va sono).

###<a name="are-you-using-premium-storage-and-separate-storage-accounts-for-each-of-your-virtual-machines"></a>Si sta utilizzando lo spazio di archiviazione premium e gli account di archiviazione distinta per ogni macchine virtuali?
È consigliabile utilizzare lo spazio di archiviazione premium per le macchine virtuali di produzione. Inoltre, verificare che si utilizza un account di archiviazione separata per ogni macchina virtuale (questo è valido per le distribuzioni di piccole dimensioni. Per grandi possono essere riutilizzati gli account di archiviazione per più computer ma ci si trova bilanciamento che deve essere eseguita per assicurarsi che siano bilanciate domini aggiornamento e livelli dell'applicazione). Se si desidera scoprire ulteriori informazioni sull'archiviazione Azure prestazioni e scalabilità, leggere [le prestazioni di archiviazione di Microsoft Azure e scalabilità elenco di controllo](../storage/storage-performance-checklist.md).

__Cosa succede se non si usa gli account di archiviazione distinta per ogni macchina virtuale?__ Un account di archiviazione, ad esempio molte altre risorse è un singolo punto di errore. Sebbene esistano numerosi protezioni e funzionalità più flessibili di spazio di archiviazione di Azure, un singolo punto di errore non è mai una buona progettazione. Se, ad esempio, diritti di accesso danneggiati a tale account, viene raggiunto un limite di archiviazione o un [IOPS limitare](../azure-subscription-service-limits.md#virtual-machine-disk-limits) è raggiunta, tutte le macchine virtuali utilizzando l'account di archiviazione sono interessate. Inoltre, se è presente un'interruzione del servizio che influisce su un indicatore di spazio di archiviazione che include l'account di archiviazione specifico è possibile che più macchine virtuali interessate.

###<a name="are-you-using-a-load-balancer-or-a-queue-between-each-tier-of-your-application"></a>Si sta utilizzando un bilanciamento del carico o a una coda tra ogni livello dell'applicazione?
Uso di servizi di bilanciamento del carico o code tra ogni livello dell'applicazione consente di ridimensionare facilmente ogni livello dell'applicazione facilmente e in modo indipendente. È consigliabile scegliere tra queste tecnologie in base alla latenza complessità, e deve distribuzione (ad esempio indica quanto si distribuisce l'app). In generale, code comandi sono particolarmente utili per hanno una latenza maggiore e aggiungere complessità ma è in corso più flessibili e consentire all'utente distribuire l'applicazione su aree di dimensioni maggiori vantaggi (ad esempio tra le aree). Se si desidera trovare ulteriori informazioni su come utilizzare i servizi di bilanciamento del carico interno o code, leggere [bilanciamento del carico interno Panoramica](../load-balancer/load-balancer-internal-overview.md) e [code Azure e servizio Bus code - confrontati e rispetto](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

__Cosa succede se non si usa un servizio di bilanciamento del carico o una coda tra ogni livello dell'applicazione?__ Senza un servizio di bilanciamento del carico, o di coda tra ogni livello dell'applicazione in uso è difficile ridimensionare l'applicazione verso l'alto o verso il basso e distribuire il carico in più computer. Non eseguire questo può comportare sopra o sotto il provisioning delle risorse e il rischio di inattività o esperienza utente di scarsa qualità, nel caso di modifiche impreviste nelle errori il traffico o del sistema.
 
###<a name="are-your-sql-databases-using-active-geo-replication"></a>I database di SQL utilizza la replica geografico attiva? 
La replica di geografico Active consente di configurare fino a 4 database secondari leggibili in aree geografiche stesso o diverse. Database secondari sono disponibili in caso di un'interruzione del servizio o l'impossibilità di connettersi al database primario. Se si desiderano ulteriori informazioni su replica di geografico active Database SQL, consultare l'articolo [Panoramica: SQL Database attiva geografico replica](../sql-database/sql-database-geo-replication-overview.md).
 
 __Cosa succede se non si usa la replica di geografico active con i database SQL?__ Senza active geografico replica, se il database primario mai in linea (manutenzione pianificata, interruzione del servizio, un errore hardware, ecc.) il database dell'applicazione sarà offline fino a quando non è possibile importare in linea il database primario integro. 
 
###<a name="are-you-using-a-cache-azure-redis-cache-in-front-of-your-databases"></a>Si sta utilizzando una cache (Azure Redis Cache) che precede i database?
Se l'applicazione ha un carico elevato database in cui la maggior parte delle chiamate di database sono lettura, è possibile aumentare la velocità dell'applicazione e ridurre il carico sul database mediante l'implementazione di un livello di memorizzazione nella cache che precede il database di scaricare queste operazioni di lettura. È possibile aumentare la velocità dell'applicazione e ridurre il carico di database (aumentando la scala che è possibile gestire) inserendo un livello di memorizzazione nella cache che precede il database. Per ulteriori informazioni su come la cache di Azure Redis, leggere [indicazioni per la memorizzazione nella cache](../best-practices-caching.md).
 
 __Cosa succede se non si usa una cache che precede il database?__ Se il computer di database è sufficientemente potente per gestire il carico di traffico inserire in cui è quindi l'applicazione risponda come di consueto, anche se ciò significa che carico inferiore effettuerà il pagamento per un computer di database in cui è più costoso necessarie. Se il computer di database non è estesa sufficiente per gestire il carico, quindi si inizierà a esperienza utente di scarsa qualità esperienza con l'applicazione (latenza, timeout ed eventualmente i tempi di inattività).
 
###<a name="have-you-contacted-microsoft-azure-support-if-you-are-expecting-a-high-scale-event"></a>Se ci si aspetta un evento di alta, si dispone di contatto del supporto tecnico Microsoft Azure?
Supporto Azure consente di aumentare i limiti di servizio per gestire agli eventi pianificati traffico elevato (ad esempio lancio di nuovi prodotti o festività speciali). Supporto Azure anche possibile consentono di connettersi con esperti che consentono di verificare la progettazione con il team di account e consentono di trovare la soluzione migliore per soddisfare le esigenze di evento alta. Per ulteriori informazioni su come contattare il supporto di Azure, leggere le [Domande frequenti di supporto di Azure](https://azure.microsoft.com/support/faq/).

__Cosa succede se si non contattare il supporto di Azure per un evento vasta scala?__ Se non si comunicare o pianificare, un evento di traffico elevato, si rischia di raggiungere alcuni [limiti di servizi di Azure](../azure-subscription-service-limits.md) e creando così un'esperienza utente di scarsa qualità (o peggiore, i tempi di inattività) durante l'evento. Architettura recensioni e comunicazione prima picchi di corrente consente di ridurre i rischi.

###<a name="are-you-using-a-content-delivery-network-azure-cdn-in-front-of-your-web-facing-storage-blobs-and-static-assets"></a>Si sta utilizzando una rete contenuti (CDN Azure) che precede il BLOB di spazio di archiviazione basata sul web e risorse statici?
Tramite una rete CDN è possibile adottare carico di lavoro del server per la memorizzazione nella cache del contenuto nelle posizioni CDN POP/bordo che si trovano in tutto il mondo. È possibile eseguire questa operazione per ridurre la latenza, scalabilità, ridurre il carico del server e come parte di una strategia di protezione da attacchi di service(DOS) tipo. Se si desidera trovare ulteriori informazioni su come usare Azure CDN per aumentare la flessibilità e ridurre la latenza cliente, panoramica [Di Azure contenuto recapito rete (CDN)](../cdn/cdn-overview.md).

__Cosa succede se non si usa una rete CDN?__ Se non si utilizza una rete CDN tutto il traffico di assistenza clienti per direttamente alle risorse. Questo errore indica che si vedrà carichi superiori nei server che si riducono gli scalabilità. Inoltre, i clienti potrebbero verificarsi latenza superiore reti CDN offre posizioni in tutto il mondo che probabilmente più vicino ai clienti.

##<a name="next-steps"></a>Passaggi successivi:
Se si vuole leggere altre informazioni su come progettare applicazioni per la disponibilità elevata, leggere [disponibilità per applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).