<properties
   pageTitle="Esecuzione di più macchine virtuali | Fare riferimento a architettura | Microsoft Azure"
   description="Come eseguire più istanze di macchine Virtuali in Azure per scalabilità, adattabilità, gestibilità e sicurezza."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Più macchine virtuali in esecuzione in Azure per prestazioni e scalabilità disponibilità 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Questo articolo illustra un insieme di procedure per l'esecuzione di più istanze di macchine virtuali (macchine Virtuali) per migliorare la sicurezza, disponibilità, gestibilità e scalabilità.   

In questa architettura, il carico di lavoro viene distribuito tra più istanze macchine Virtuali. Esiste un solo indirizzo IP pubblico e il traffico Internet è distribuito in macchine virtuali utilizzando un bilanciamento del carico. Questa architettura è utilizzabile per un'app a livello singolo, ad esempio un cluster senza informazioni sullo stato web app o lo spazio di archiviazione. È anche un blocco predefinito per le applicazioni a più livelli. 

In questo articolo si basa [sull'esecuzione di una singola macchina virtuale in Azure][single vm]. Suggerimenti riportati in questo articolo vengono applicate anche a questa architettura.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Macchine virtuali di Azure richiedono il supporto di rete e archiviazione risorse.

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è il "elaborazione - scheda macchine Virtuali multi." 

![[0]][0]

L'architettura è i seguenti componenti: 

- **Impostazione della disponibilità.** [Imposta la disponibilità] [ availability set] contiene le macchine virtuali ed è necessario per supportare la [disponibilità contratto di servizio per macchine virtuali di Azure][vm-sla].

- **VNet**. Ogni macchina virtuale in Azure viene distribuito in una rete virtuale (VNet) che è suddiviso in **subnet**.

- **Bilanciamento del carico Azure.** [Bilanciamento del carico] distribuisce le richieste in arrivo di Internet per le istanze di macchine Virtuali di un set di disponibilità. Bilanciamento del carico include alcune risorse correlate:

  - **Indirizzo IP pubblico.** Un indirizzo IP pubblico è necessario per il bilanciamento del carico ricevere il traffico Internet.

  - **Configurazione front-end.** Associa l'indirizzo IP pubblico con il servizio di bilanciamento del carico.

  - **Pool di indirizzi di back-end.** Contiene le interfacce di rete (NIC) per le macchine virtuali che riceveranno il traffico in entrata.

- **Caricare le regole di bilanciamento del carico.** Consente di distribuire il traffico di rete tra tutte le macchine virtuali nel pool di indirizzi di back-end. 

- **Regole NAT.** Usato per instradare il traffico verso una macchina virtuale specifico. Ad esempio, per consentire al protocollo desktop remoto (RDP) di macchine virtuali, creare una regola di conversione indirizzo rete separata per ogni macchina virtuale. 

- **Interfacce di rete (NIC)**. Ogni macchina virtuale è una scheda di rete per connettersi alla rete.

- **Spazio di archiviazione.** Gli account di archiviazione posizionare le immagini di macchine Virtuali e altre risorse relative ai file, ad esempio i dati di diagnostica macchine Virtuali acquisiti da Azure.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue i suggerimenti descritti di seguito. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico che non supporta un suggerimento. 

### <a name="availability-set-recommendations"></a>Disponibilità imposta consigli

È necessario creare almeno due macchine virtuali di disponibilità impostata per supportare la [disponibilità contratto di servizio per macchine virtuali di Azure][vm-sla]. Notare che il servizio di bilanciamento del carico Azure richiede anche che macchine virtuali di bilanciamento del carico appartengano allo stesso set di disponibilità.

### <a name="network-recommendations"></a>Suggerimenti per reti

Tutte le macchine virtuali supporto bilanciamento del carico devono essere inserite all'interno della stessa subnet. Non esporre macchine virtuali direttamente a Internet, ma fornisce invece ogni macchina virtuale un indirizzo IP privato. I client si connettono usando l'indirizzo IP pubblico di bilanciamento del carico.

### <a name="load-balancer-recommendations"></a>Carico bilanciamento consigli

Aggiungere macchine virtuali tutti nella disponibilità impostata su pool di indirizzi di back-end di bilanciamento del carico.

Definire le regole di bilanciamento carico per reindirizzare il traffico di rete agli macchine virtuali. Ad esempio, per consentire il traffico HTTP, creare una regola che esegue il mapping porta 80 dalla configurazione front-end alla porta 80 nel pool di indirizzi di back-end. Quando il servizio di bilanciamento del carico riceve una richiesta sulla porta 80 dell'indirizzo IP pubblico, effettua la richiesta alla porta 80 su una delle schede nel pool di indirizzi di back-end.

Definire le regole NAT per instradare il traffico a una macchina virtuale specifico. Per abilitare, ad esempio, RDP alle macchine virtuali creare una regola NAT separata per ogni macchina virtuale. Ogni regola è necessario eseguire il mapping di un numero di porta distinti alla porta 3389, la porta predefinita per RDP. (Ad esempio Usa la porta 50001 per "VM1" porta 50002 per "VM2" e così via.) Assegnare le regole NAT a schede in macchine virtuali. 

### <a name="storage-account-recommendations"></a>Suggerimenti di account di archiviazione

Creare gli account di archiviazione di Azure separata per ogni macchine Virtuali tenere il disco rigido virtuale (dischi rigidi virtuali), per evitare di utilizzare le operazioni di ingresso/uscita al secondo [(IOPS) limiti] [ vm-disk-limits] per gli account di archiviazione. 

Creare un account di archiviazione per i registri diagnostici. Questo account di archiviazione può essere condiviso da tutte le macchine virtuali.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Sono disponibili due opzioni per il ridimensionamento di macchine virtuali di Azure: 

- Utilizzare un bilanciamento del carico per distribuire il traffico di rete in un set di macchine virtuali. Scalabilità, effettuare il provisioning di altre macchine virtuali e renderli disponibili supporto bilanciamento del carico. 

- Utilizzare [set di scala macchina virtuale][vmss]. Impostare una scala contiene un numero specificato di macchine virtuali identiche dietro un bilanciamento del carico. Scala macchine Virtuali imposta il ridimensionamento automatico supporto in base a dati sulle prestazioni. Quando il carico in macchine virtuali aumenta, altre macchine virtuali vengono automaticamente aggiunti al servizio di bilanciamento del carico. 

Nelle sezioni successive di confronto tra queste due opzioni.

### <a name="load-balancer-without-vm-scale-sets"></a>Bilanciamento del carico senza macchine Virtuali scala set

Un servizio di bilanciamento del carico accetta le richieste in arrivo di rete e li distribuisce tra le schede NIC nel pool di indirizzi di back-end. Per ridimensionare orizzontalmente, aggiungere più istanze di macchine Virtuali al set di disponibilità (o rilasciare macchine virtuali scalare verso il basso). 

Si supponga ad esempio, che si esegue un server web. Aggiungere una regola di bilanciamento del carico per porte 80 e/o la porta 443 (per SSL). Quando viene inviata una richiesta HTTP, bilanciamento del carico seleziona un indirizzo IP di back-end utilizzando un [algoritmo di hashing] [ load balancer hashing] che include l'indirizzo IP di origine. In questo modo, le richieste dei client vengono distribuite tra tutte le macchine virtuali. 

> [AZURE.TIP] Quando si aggiunge una nuova macchina virtuale per una disponibilità impostare, assicurarsi di creare una scheda di rete per la macchina virtuale e aggiungere la scheda di rete al pool di indirizzi di back-end nel sistema di bilanciamento del carico. In caso contrario, il traffico Internet non inviato per la nuova macchina virtuale.

Ogni abbonamento Azure dispone limiti predefiniti, tra cui il numero massimo di macchine virtuali per ogni area geografica. È possibile aumentare il limite per l'archiviazione di una richiesta di assistenza. Per ulteriori informazioni, vedere [sottoscrizione Azure e limiti del servizio, quote e i vincoli][subscription-limits].  

### <a name="vm-scale-sets"></a>Set di scala macchine Virtuali 

Set di scala macchine Virtuali consentono di distribuire e gestire un insieme di macchine virtuali identici. Con tutti macchine virtuali configurato allo stesso modo, macchine Virtuali scala set supportare true autoscale, senza pre-il provisioning di macchine virtuali, rendendo più semplice compilare servizi su larga scala destinati calcolo grande, dati e nei contenitori carichi di lavoro. 

Per ulteriori informazioni sui set di scala macchine Virtuali, vedere [Panoramica Imposta scala di macchina virtuale][vmss].

Considerazioni per l'utilizzo di set di scala macchine Virtuali:

- Valutare la possibilità di set di scala se è necessario rapidamente scalabilità macchine virtuali, o per scalare automaticamente. 

- Scala set al momento non supportano dischi di dati. Le opzioni per l'archiviazione dei dati sono spazio di archiviazione file Azure, l'unità di sistema operativo, l'unità Temp o un archivio esterno, ad esempio lo spazio di archiviazione di Azure. 

- Tutte le istanze di macchine Virtuali all'interno di una scala impostare automaticamente appartengono allo stesso set di disponibilità, con 5 guasto e i domini 5 aggiornamento.

- Per impostazione predefinita, set di scala utilizzati "overprovisioning", ovvero il set di scala inizialmente disposizioni più macchine virtuali che si chiede e quindi Elimina le macchine virtuali aggiuntive. Per migliorare la velocità di successo globale durante il provisioning di macchine virtuali. 

- È consigliabile non sia più quindi rispetto a 20 macchine virtuali per lo spazio di archiviazione account con overprovisioning macchine virtuali abilitate o non più di 40 con overprovisioning disattivato.  

- È possibile trovare modelli di Manager delle risorse per la distribuzione scala set nei [Modelli di Guida introduttiva di Azure][vmss-quickstart].

- Esistono due modi principali per configurare macchine virtuali distribuite in un set di scala: creare un'immagine personalizzata oppure utilizzare le estensioni per configurare la macchina virtuale dopo che viene eseguito il provisioning.

    - Un set di scala basato su un'immagine personalizzata è necessario creare dischi rigidi virtuali disco OS tutti all'interno di un account di archiviazione. 

    - Con immagini personalizzate, è necessario mantenere aggiornato l'immagine.

    - Con le estensioni, può impiegare più tempo per una macchina appena provisioning virtuale per la riattivazione.

Per altre considerazioni, vedere [La progettazione di set di scala macchine Virtuali per scala][vmss-design].

> [AZURE.TIP]  Quando si usa qualsiasi soluzione di ridimensionamento automatico, verificarla con carichi di lavoro a livello di produzione in anticipo. 

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

La disponibilità Set rende l'app più flessibile per pianificato e non pianificato eventi di manutenzione.

- _Manutenzione pianificata_ si verifica quando Microsoft aggiorna piattaforma sottostante, causando talvolta macchine virtuali essere riavviato. Azure rende che macchine virtuali di un set di disponibilità non sono tutti si riavvia allo stesso tempo, almeno da mantenere in esecuzione mentre gli altri utenti riavviato.

- _Manutenzione non pianificata_ succede se si verifica un errore hardware. Azure garantisce che vengono effettuato il provisioning di macchine virtuali di un set di disponibilità tra più rack di server. In questo modo per ridurre l'impatto degli errori hardware, interruzioni di rete, le interruzioni di alimentazione e così via.

Per ulteriori informazioni, vedere [gestire la disponibilità di macchine virtuali][availability set]. Il video seguente è installato anche un'ampia panoramica dei set di disponibilità: [Come è configurare un insieme di disponibilità per macchine virtuali di scala][availability set ch9]. 

> [AZURE.WARNING]  Assicurarsi di configurare la disponibilità impostata durante il provisioning macchina virtuale. Al momento non è possibile aggiungere una VM Manager delle risorse a una disponibilità imposta dopo la macchina virtuale viene eseguito il provisioning.

Bilanciamento del carico utilizza [integrità analizza] per controllare la disponibilità delle istanze di macchine Virtuali. Se una verifica non riesce a raggiungere un'istanza all'interno di un periodo di timeout, bilanciamento del carico smette di inviare il traffico a quella macchina virtuale. Tuttavia, bilanciamento del carico continuerà per verificare la presenza e se la macchina virtuale nuovamente disponibile, il servizio di bilanciamento del carico riprende invio del traffico a quella macchina virtuale.

Ecco alcuni suggerimenti su carico bilanciamento integrità ricerche:

- Le ricerche verificare HTTP o TCP. Se l'esecuzione di macchine virtuali server HTTP (IIS, nginx, Node app e così via), crea una verifica HTTP. In caso contrario, creare un sondaggio TCP.

- Per una verifica HTTP, specificare il percorso per l'endpoint HTTP. La ricerca verifica la presenza di una risposta HTTP 200 da questo percorso. Può trattarsi di al percorso radice ("/") o un endpoint di monitoraggio implementata alcune logica personalizzata per controllare lo stato dell'applicazione. L'endpoint deve autorizzare le richieste HTTP anonime.

- La ricerca viene inviata da una [Nota] [ health-probe-ip] indirizzo IP 168.63.129.16. Assicurarsi di non bloccare il traffico in o da questo IP in tutti i criteri del firewall o le regole di rete protezione gruppo (NSG).

- Usare [i registri di verifica dell'integrità] [ health probe log] per visualizzare lo stato di analisi dell'integrità. Attivare la registrazione nel portale di Azure per ogni servizio di bilanciamento del carico. Registri a archiviazione Blob Azure. I log mostrano quanti macchine virtuali back-end non ricevono il traffico di rete a causa delle risposte di verifica non riuscito.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Con più macchine virtuali, diventa importante automatizzare processi, in modo che siano attendibile e ripetibile. È possibile utilizzare [L'automazione Azure] [ azure-automation] per automatizzare distribuzione patch del sistema operativo e altre attività. Automazione Azure è un servizio di automazione che viene eseguito su Azure ed è basato su Windows PowerShell. Script di automazione di esempio sono disponibili nella [Raccolta Runbook] su TechNet.

## <a name="security-considerations"></a>Considerazioni sulla protezione

Reti virtuali sono un limite di isolamento il traffico in Azure. Macchine virtuali in uno che vnet non è possibile comunicare direttamente alle macchine virtuali in un altro VNet. Macchine virtuali all'interno della stessa VNet possono comunicare, a meno che non si crea [gruppi di sicurezza di rete] [ nsg] (NSGs) per limitare il traffico. Per ulteriori informazioni, vedere [servizi cloud Microsoft e la sicurezza della rete][network-security].

Per il traffico Internet in arrivo, le regole di bilanciamento del carico definiscono il tipo di traffico contattarle back-end. Tuttavia, le regole di bilanciamento carico non supportano whitelist IP, pertanto se si desidera proprietà consentite consente di risolvere determinati IP pubblico, aggiungere un NSG alla subnet.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni è disponibile in GitHub. L'architettura di riferimento include una rete virtuale (VNet), gruppo di sicurezza di rete (NSG), bilanciamento del carico e due macchine ().

È possibile distribuire l'architettura di riferimento con Windows o macchine virtuali Linux seguendo le indicazioni seguenti: 

1. Pulsante destro del mouse sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, pertanto selezionare **Usa esistente** e immettere `ra-multi-vm-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Selezionare il **Tipo di sistema operativo** dall'elenco a discesa casella, **windows** o **linux**. 
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Si consiglia di immediatamente modificare entrambi i file di parametro includono un nome utente amministratore hardcoded e la password. Fare clic su macchine Virtuali denominata `ra-multi-vm1` nel portale di Azure. Quindi fare clic su **reimpostare la password** nel e **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per salvare il nuovo nome utente e la password. Ripetere la procedura per la macchina virtuale denominata `ra-multi-vm2`.

Per informazioni su altri modi per distribuire l'architettura di riferimento, vedere il file Leggimi di [macchine virtuali di singola indicazioni] [ github-folder] GitHub cartella. 

## <a name="next-steps"></a>Passaggi successivi

Inserimento diverse macchine virtuali dietro un bilanciamento del carico è un blocco predefinito per la creazione di architetture a più livelli. Per ulteriori informazioni, vedere [Esecuzione macchine virtuali di Windows per un'architettura di livelli in Azure] [ n-tier-windows] e [In esecuzione macchine virtuali Linux per un'architettura di livelli in Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[ricerche integrità]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[bilanciamento del carico]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Raccolta runbook]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Architettura di una soluzione di macchine Virtuali di più su Azure che comprendono una disponibilità set con due macchine virtuali e un bilanciamento del carico"
