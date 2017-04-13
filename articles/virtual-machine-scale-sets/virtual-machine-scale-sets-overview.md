<properties
    pageTitle="Scala macchina virtuale imposta Panoramica | Microsoft Azure"
    description="Altre informazioni sui set di scala macchina virtuale"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

# <a name="virtual-machine-scale-sets-overview"></a>Panoramica di set di scala macchina virtuale

Set di scala macchina virtuale sono una risorsa Azure calcolare che è possibile utilizzare per distribuire e gestire un insieme di macchine virtuali identici. Con tutte le macchine virtuali configurate nella stessa scala macchine Virtuali set sono progettati per supportare autoscale true-nessun pre-provisioning di macchine virtuali è necessaria e come tali semplifica la creazione di servizi su larga scala l'assegnazione del calcolo grande, dati e nei contenitori carichi di lavoro.

Per le applicazioni che richiedono una per le risorse di calcolo e in scala operazioni siano bilanciate in modo implicito domini guasto e aggiornamento. Per un'introduzione alle scala macchine Virtuali set fare riferimento al [blog Azure annuncio](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Esaminare questi video per ulteriori informazioni sui set di scala macchine Virtuali:

 - [Contrassegnare si esprime Russinovich Azure scala set](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Scala macchina virtuale imposta con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Creazione e gestione di scala macchine Virtuali imposta

È possibile creare un Set di scala macchine Virtuali nel [portale di Azure](https://portal.azure.com) selezionando _Nuovo_ e digitando "scala" nella barra di ricerca. Verrà visualizzato "macchina virtuale scala" impostare nei risultati. Da qui è possibile compilare i campi obbligatori di personalizzare e distribuire un set di scala. 

Scala macchine Virtuali è inoltre possibile definire e distribuito utilizzando modelli JSON e [Le API REST](https://msdn.microsoft.com/library/mt589023.aspx) soltanto set, ad esempio singole macchine virtuali di Azure Manager delle risorse. Di conseguenza, qualsiasi standard Manager delle risorse Azure distribuzione possono essere utilizzati. Per ulteriori informazioni sui modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md).

Una serie di modelli di esempio per i set di scala macchine Virtuali sono disponibili nel repository Guida introduttiva di Azure modelli GitHub [qui.](https://github.com/Azure/azure-quickstart-templates) (cercare modelli con _vmss_ nel titolo)

Nelle pagine di dettaglio per questi modelli verrà visualizzato un pulsante che si collega la caratteristica di distribuzione del portale. Per distribuire il set di scala macchine Virtuali, fare clic sul pulsante e quindi compilare i parametri richiesti nel portale. Se non si è sicuri se una risorsa supporta caratteri maiuscoli o misti maiuscole/minuscole è preferibile usare sempre i valori dei parametri lettere minuscole. È inoltre disponibile un comodo dissezioni video di un modello di set scala di macchine Virtuali:

[Macchine Virtuali scala imposta la dissezione modello](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Ridimensionamento di una scala di macchine Virtuali imposta ritirare e

Per aumentare o ridurre il numero di macchine virtuali in un set di scala macchine Virtuali, modificare la proprietà _capacità_ e ridistribuire il modello. Questa semplicità rende più facile scrivere il proprio livello scala personalizzato se si desidera definire gli eventi scala personalizzata che non sono supportati da Azure autoscale.

Se si ridistribuire un modello per modificare la capacità, è possibile definire un modello molto più piccolo che include solo lo SKU e la capacità aggiornata. Viene illustrato un esempio di questo [qui.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Per eseguire i passaggi che creare un set di scala scala viene impostata automaticamente, vedere [Automaticamente scala macchine in un Set di scala macchina virtuale](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Imposta una scala di macchine Virtuali di monitoraggio

Il [portale di Azure](https://portal.azure.com) elenchi scala set e Mostra le proprietà di base, nonché voce macchine virtuali nel set di. Per ulteriori dettagli è possibile utilizzare [Azure risorse](https://resources.azure.com) per visualizzare i set di scala macchine Virtuali. Set di scala macchine Virtuali sono una risorsa in Microsoft.Compute, in modo da questo sito è possibile vederle, espandere i collegamenti seguenti:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Scala macchine Virtuali configurare scenari

In questa sezione sono elencati alcuni scenari di set di scala macchine Virtuali tipici. Alcuni servizi Azure livelli superiore (ad esempio Batch, tessuti servizio o servizio contenitore Azure) utilizzeranno gli scenari seguenti.

 - **RDP / SSH scala macchine Virtuali impostare istanze** - macchine Virtuali A scala set viene creato all'interno di un VNET e singole macchine virtuali nel set di scala non sono assegnate indirizzi IP. In questo modo positiva non si desidera in genere il sovraccarico di spesa e gestione di allocazione di indirizzi IP separati pubblici a tutte le risorse senza informazioni sullo stato della griglia di calcolo ed è possibile connettersi facilmente a queste macchine virtuali da altre risorse nel VNET inclusi quelli che dispongono di indirizzi IP pubblici ad esempio servizi di bilanciamento del carico o macchine virtuali autonomo.

 - **Connettersi a macchine virtuali usando le regole di NAT** - è possibile creare un indirizzo IP pubblico, assegnare a un servizio di bilanciamento del carico e definire le regole in entrata NAT che mappano una porta sull'indirizzo IP a una porta una macchina virtuale nel set di scala macchine Virtuali. Per esempio:
 
    Origine | Porta di origine | Destinazione | Porta di destinazione
    --- | --- | --- | ---
    IP pubblico | Porta 50000 | vmss\_0 | Porta 22
    IP pubblico | Porta 50001 | vmss\_1 | Porta 22
    IP pubblico | Porta 50002 | vmss\_2 | Porta 22

    Ecco un esempio di creazione di un set di scala macchine Virtuali che utilizza le regole NAT per consentire la connessione SSH a ogni macchina virtuale in una scala set mediante un singolo indirizzo IP pubblico: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Ecco un esempio di eseguire la stessa operazione con RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Connettersi a macchine virtuali utilizzando "jumpbox"** - se si crea una scala di macchine Virtuali impostare e autonomo macchine Virtuali nella stessa VNET, autonoma macchine Virtuali e il gruppo di scala macchine Virtuali che macchine virtuali possono connettersi tra loro utilizzando i IP interno indirizzi come definito da VNET/Subnet. Se si crea un indirizzo IP pubblico e assegnarlo a autonoma macchine Virtuali è possibile RDP o SSH autonoma macchine Virtuali e quindi connettersi dal computer per le istanze di set di scala macchine Virtuali. È possibile a questo punto che un semplice insieme scala macchine Virtuali è progettato per più sicuro del prodotto autonomo semplice macchine Virtuali con un indirizzo IP pubblico nella configurazione predefinita.

    [Per un esempio di questo approccio, questo modello consente di creare un semplice cluster Mesos composta da un'indipendente VM schema che gestisce un cluster di macchine Virtuali scala set in base a delle macchine virtuali.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Il bilanciamento del carico scala macchine Virtuali imposta istanze** - se si desidera distribuire lavoro in un cluster di elaborazione delle macchine virtuali mediante un approccio "circolari", è possibile configurare un sistema di bilanciamento del carico Azure con regole di bilanciamento del carico di conseguenza. È possibile definire le ricerche per verificare l'applicazione è in esecuzione eseguendo il ping porte con un percorso di protocollo, intervallo e richiesta specificato. Il [Gateway di applicazioni](https://azure.microsoft.com/services/application-gateway/) Azure supporta anche set scala, insieme a scenari di bilanciamento del carico più complesse.

    [Ecco un esempio che crea un set di scala macchine Virtuali di macchine virtuali in esecuzione server web IIS e utilizza un bilanciamento del carico per distribuire il carico che riceve ogni macchina virtuale. Utilizza anche il protocollo HTTP per effettuare il ping un URL specifico in ogni macchina virtuale.](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (esaminare il tipo di risorsa Microsoft.Network/loadBalancers e networkProfile and extensionProfile nel virtualMachineScaleSet)

 - **Imposta una scala di macchine Virtuali come un cluster di elaborazione di un manager cluster PaaS distribuzione** - scala macchine Virtuali set talvolta vengono descritti come un ruolo di lavoro nuova generazione. Si tratta di una descrizione valida ma viene eseguita anche il rischio di confusione scala caratteristiche di set di funzionalità PaaS v1 lavoro ruolo. In senso scala macchine Virtuali set di forniscono un vero e proprio "ruolo di lavoro" o una risorsa lavoro, che offrono una risorsa di elaborazione GRG piattaforma/runtime indipendente, personalizzabili e si integra Azure Manager delle risorse IaaS.

    Un ruolo di lavoro v1 PaaS, mentre limitata in termini di supporto di piattaforma/runtime (solo immagini di piattaforma Windows) include anche servizi, ad esempio scambia VIP, le impostazioni di aggiornamento configurabili, runtime/app specifica le impostazioni di distribuzione che non sono _ancora_ disponibili in macchine Virtuali scalare set o verranno recapitati da altri servizi PaaS livelli superiore come servizio tessuti. Con questo valore in importante ricordare che è possibile esaminare macchine Virtuali scala set come un'infrastruttura che supporta PaaS. Ad esempio Soluzioni PaaS come servizio tessuti o Manager cluster come Mesos possibile creare nella parte superiore di macchine Virtuali scalare imposta come livello di elaborazione scalable.

    [Per un esempio di questo approccio, questo modello consente di creare un semplice cluster Mesos composta da un'indipendente VM schema che gestisce un cluster di macchine Virtuali scala set in base a delle macchine virtuali.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) Le versioni future del [Servizio di Azure contenitore](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) verranno distribuito altre versioni di complesso/avanzata di questo scenario basato su set di scala macchine Virtuali.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Macchine Virtuali scala impostare proporzioni e le prestazioni indicazioni

- Non creare più di 500 macchine virtuali in scala macchine Virtuali multipli alla volta.
- Pianificare non più di 20 macchine virtuali per conto di spazio di archiviazione (a meno che non si imposta la proprietà _overprovision_ su "falso", nel qual caso è possibile accedere fino a 40).
- Allontanare le prime lettere dei nomi degli account di archiviazione al massimo.  I modelli VMSS di esempio nella [Guida introduttiva di Azure modelli](https://github.com/Azure/azure-quickstart-templates/) forniscono esempi di come eseguire questa operazione.
- In macchine virtuali personalizzate, pianificare non più di 40 macchine virtuali per ogni serie scala macchine Virtuali e in un account di archiviazione singola.  Sarà necessario immagine pre-copiato account di archiviazione prima di iniziare la scala set di distribuzione. Vedere le domande frequenti per altre informazioni.
- Pianificare non più di 4096 macchine virtuali per VNET.
- Il numero di macchine virtuali è possibile creare è limitato dalla quota di base nell'area in cui si desidera distribuire. Potrebbe essere necessario contattare il supporto tecnico per aumentare il limite di quota calcolo aumentato anche se si dispone di un limite elevato di core per l'utilizzo con servizi cloud o IaaS v1 oggi. Per eseguire la quota di una query è possibile eseguire il seguente comando CLI Azure: `azure vm list-usage`e il seguente comando di PowerShell: `Get-AzureRmVMUsage` (se tramite una versione di PowerShell sottostanti 1.0 utilizzano `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Scala macchine Virtuali imposta spesso domande frequenti

**Q.** Quanti macchine virtuali possono avere in un set di scala macchine Virtuali?

**RISPOSTE.** 100 se si utilizzano le immagini di piattaforma che possono essere distribuite tra più account di archiviazione. Se si usa immagini personalizzate, fino a 40 (se la proprietà _overprovision_ è impostata su 20 "false" per impostazione predefinita), poiché immagini personalizzate sono attualmente limitate a un account di archiviazione singola.

**Domande** quali altri limiti delle risorse esistono per macchine Virtuali scala set?

**RISPOSTE.** Sono limitate alla creazione di macchine virtuali non più di 500 in più set di scala per ogni area durante un periodo di 10 minuti. Esistente [servizio di abbonamento Azure limiti /](../azure-subscription-service-limits.md) applicare.

**Q.** Sono dischi di dati supportati in macchine Virtuali scala insiemi?

**RISPOSTE.** Non nella versione iniziale. Le opzioni per l'archiviazione dei dati sono:

- File Azure (unità di piccole e medie imprese condiviso)

- Unità di sistema operativo

- Unità TEMP (locale, non è supportata da archiviazione Azure)

- Servizio di dati di Azure (ad esempio tabelle Azure, BLOB Azure)

- Servizio di dati esterno (ad esempio remote DB)

**Q.** Quali aree di Azure supportano macchine Virtuali scala set?

**RISPOSTE.** Un'area che supporta Gestione risorse di Azure supporta macchine Virtuali scala set.

**Q.** Come si crea una scala di macchine Virtuali set mediante un'immagine personalizzata?

**RISPOSTE.** Lasciare vuota la vhdContainers proprietà, ad esempio:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**Q.** Se riduce la scala di macchine Virtuali imposta capacità da 20 a 15, che verranno rimosse macchine virtuali?

**RISPOSTE.** Macchine virtuali vengono rimossi dalla scala impostare in modo uniforme tra domini di aggiornamento e guasto per ottimizzare la disponibilità. Macchine virtuali con l'id più alto vengono rimossi prima di tutto.

**Q.** Per quanto riguarda, se aumentare la capacità da 15 a 18?

**RISPOSTE.** Se si aumenta la capacità a 18, verranno create 3 macchine virtuali di nuove. Ogni volta che viene incrementato l'ID dell'istanza di macchine Virtuali rispetto al valore più alto precedente (20, 21, 22). Macchine virtuali sono bilanciate tra FDs e UDs.

**Q.** Quando si usano più estensioni in un set di scala macchine Virtuali, è possibile implementare una sequenza di esecuzione?

**RISPOSTE.** Non direttamente, ma per estensione customScript Impossibile attendere uno script di un'altra estensione completare ([ad esempio controllando nel log di estensione](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Informazioni aggiuntive sulla sequenza di estensione sono disponibili in questo post di blog: [La sequenza di estensione in Azure macchine Virtuali scala set](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**Q.** Set di scala macchine Virtuali funzionano con set di disponibilità Azure?

**RISPOSTE.** Sì. Un set di scala macchine Virtuali è una disponibilità implicita set con 5 FDs e 5 UDs. Non è necessario configurare alcuna in virtualMachineProfile. In futuro versioni, macchine Virtuali scala set sono probabilmente estesi a più tenant ma per ora imposta una scala è un insieme di disponibilità singola.
