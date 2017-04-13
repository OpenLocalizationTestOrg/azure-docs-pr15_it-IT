<properties
   pageTitle="Manager delle risorse e classica distribuzione | Microsoft Azure"
   description="Vengono descritte le differenze tra il modello di distribuzione di Manager delle risorse e la visualizzazione classica (o la gestione dei servizi) al modello di distribuzione."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Gestione risorse di Azure e distribuzione classica: informazioni su modelli di distribuzione e lo stato delle risorse

In questo argomento è approfondire Manager delle risorse di Azure e modelli di distribuzione classico, lo stato delle risorse e i motivi per le risorse sono distribuite con uno o l'altro. I Manager delle risorse e modelli di distribuzione classica rappresentano due diversi modi di distribuzione e gestione di soluzioni Azure. Lavorare con loro tramite due diversi insiemi di API e le risorse distribuite possono contenere differenze importanti. I due modelli non sono completamente compatibili tra loro. Questo argomento illustra le differenze.

Per semplificare la distribuzione e gestione delle risorse, si consiglia di utilizzare Gestione risorse per tutte le nuove risorse. Se possibile, Microsoft consiglia ridistribuire le risorse esistenti tramite Gestione risorse.

Se si ha a Gestione risorse, è necessario verificare prima di tutto la terminologia definita in [Panoramica di gestione di risorse Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Cronologia dei modelli

Azure fornito originariamente solo il modello di distribuzione classica. In questo modello, ogni risorsa esistenti in modo indipendente; si è non verificato in alcun modo per raggruppare le relative risorse. Se, tuttavia, si doveva manualmente, verificare quali risorse costituite della soluzione o dell'applicazione, tenendo presente che per gestirli in un approccio coordinato. Per distribuire una soluzione, era necessario creare ogni risorsa singolarmente tramite il portale classico o creare uno script che distribuito tutte le risorse nell'ordine corretto. Per eliminare una soluzione, era necessario eliminare singolarmente ogni risorsa. Non facilmente, è possibile applicare e aggiornare i criteri di controllo di accesso per le risorse correlate. Infine, non è possibile applicare contrassegni alle risorse a un'etichetta termini che consentono di eseguire il monitoraggio delle risorse e gestire la fatturazione.

Nel 2014, Azure introdotto Manager delle risorse, che aggiunto il concetto di un gruppo di risorse. Un gruppo di risorse è un contenitore per le risorse che condividono un ciclo di vita comune. Il modello di distribuzione di gestione risorse offre vari vantaggi:

- È possibile distribuire, gestire ed eseguire il monitoraggio tutti i servizi per la soluzione a livello di gruppo, anziché la gestione di questi servizi singolarmente.
- Più volte, è possibile distribuire la soluzione durante il ciclo di vita e fiducia che le risorse sono distribuite in uno stato coerente.
- È possibile applicare il controllo dell'accesso a tutte le risorse nel gruppo di risorse e i criteri vengono applicati automaticamente quando si aggiungono nuove risorse al gruppo di risorse.
- È possibile applicare contrassegni alle risorse per organizzare logicamente tutte le risorse in abbonamento.
- È possibile utilizzare JavaScript Object Notation (JSON) per definire l'infrastruttura per la soluzione. Il file JSON è noto come modello Manager delle risorse.
- È possibile definire le dipendenze tra le risorse in modo che siano distribuite nell'ordine corretto.

Quando è stato aggiunto Manager delle risorse, tutte le risorse sono stati aggiunti retroattivamente ai gruppi predefiniti delle risorse. Se si crea una risorsa tramite classico per la distribuzione ora, la risorsa viene automaticamente creata all'interno di un gruppo di risorse predefinito per tale servizio, anche se non è stata specificata tale gruppo di risorse in fase di distribuzione. Tuttavia, solo esistenti all'interno di un gruppo di risorse non significa che la risorsa è stata convertita al modello di Manager delle risorse. Vengono illustrate il modo in cui ogni servizio gestisce i modelli di due distribuzione nella sezione successiva. 

## <a name="understand-support-for-the-models"></a>Comprendere il supporto per i modelli 

Per stabilire il modello di distribuzione da usare per le risorse, esistono tre scenari da tenere presenti:

1. Il servizio supporta Manager delle risorse e fornisce un unico tipo.
2. Il servizio supporta Manager delle risorse, ma sono disponibili due tipi: uno per Manager delle risorse e uno per classica. Questo scenario si applica solo a macchine virtuali, gli account di archiviazione e le reti virtuali.
3. Il servizio non supporta Manager delle risorse.

Per scoprire se un servizio supporta Manager delle risorse, vedere [Gestione risorse supportati provider](resource-manager-supported-services.md).

Se il servizio che si desidera utilizzare non supporta Manager delle risorse, è necessario continuare a usare distribuzione classica.

Se il servizio supporta Manager delle risorse e **non è** una macchina virtuale, l'account di archiviazione o virtuali, è possibile utilizzare Manager delle risorse senza problemi.

Per macchine virtuali, gli account di archiviazione e le reti virtuali, se la risorsa è stata creata tramite classica distribuzione, è necessario continuare a operare su di esso tramite operazioni classiche. Se la macchina virtuale, l'account di archiviazione o virtuali creato mediante la distribuzione di Manager delle risorse, è necessario continuare a usare operations Manager delle risorse. Questa distinzione accessibile confusione quando l'abbonamento contiene una combinazione di risorse creato tramite Gestione risorse e distribuzione classica. Questa combinazione di risorse è possibile creare risultati imprevisti perché le risorse non supportano le stesse operazioni.

In alcuni casi, un comando di gestione risorse possibile recuperare le informazioni relative a una risorsa creata tramite distribuzione classica o esegue un'attività amministrativa, ad esempio lo spostamento di una risorsa classica a un altro gruppo di risorse. Tuttavia, questi casi non devono fornire la stampa che il tipo di supporta le operazioni di gestione risorse. Si supponga ad esempio, che si dispone di un gruppo di risorse contenente una macchina virtuale che è stata creata con la distribuzione classica. Se si esegue il comando di Manager delle risorse PowerShell seguente:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Restituisce la macchina virtuale:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Tuttavia, la gestione risorse cmdlet **Get-AzureRmVM** restituisce solo macchine virtuali distribuite tramite Gestione risorse. Il comando seguente non restituire macchina virtuale creata tramite distribuzione classica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Solo le risorse create tramite Gestione risorse supporto tag. Non è possibile applicare contrassegni alle risorse classiche.

## <a name="resource-manager-characteristics"></a>Caratteristiche di gestione risorse

Per comprendere meglio i due modelli, esaminare le caratteristiche dei tipi di Manager delle risorse:

- Creato tramite il [portale di Azure](https://portal.azure.com/).

     ![Portale di Azure](./media/resource-manager-deployment-model/portal.png)

     Per l'elaborazione, lo spazio di archiviazione e risorse di rete, avere la possibilità di usare distribuzione Manager delle risorse o classica. Selezionare **Gestione risorse**.

     ![Distribuzione di Manager delle risorse](./media/resource-manager-deployment-model/select-resource-manager.png)

- Creato con la versione di Manager delle risorse dei cmdlet di PowerShell Azure. Questi comandi con il formato *AzureRmNoun verbali*.

        New-AzureRmResourceGroupDeployment

- Creato tramite l' [REST API di Azure Manager delle risorse](https://msdn.microsoft.com/library/azure/dn790568.aspx) per le operazioni di resto.

- Creato tramite i comandi di Azure CLI eseguiti in modalità **arm** .

        azure config mode arm
        azure group deployment create 

- Il tipo di risorsa non include **(classica)** nel nome. Nella figura seguente mostra il tipo di **account di archiviazione**.

    ![Web app](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Caratteristiche di distribuzione classica

È inoltre possibile conoscere il modello di distribuzione classica come modello di gestione dei servizi.

Le risorse create nel modello di distribuzione classica condividono le caratteristiche seguenti:

- Creato tramite il [portale classica](https://manage.windowsazure.com)

     ![Portale classica](./media/resource-manager-deployment-model/classic-portal.png)

     In alternativa, il portale di Azure e si specifica distribuzione **classico** (per elaborazione, lo spazio di archiviazione e la rete).

     ![Distribuzione classica](./media/resource-manager-deployment-model/select-classic.png)

- Creato tramite la versione di gestione dei cmdlet di PowerShell Azure. I nomi di comando con il formato *AzureNoun verbali*.

        New-AzureVM 

- Creato tramite l' [API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) per le operazioni di resto.
- Creato tramite i comandi di Azure CLI eseguiti in modalità di **asm** .

        azure config mode asm
        azure vm create 

- Il tipo di risorsa include **(classica)** nel nome. Nella figura seguente mostra il tipo di **account di archiviazione (classica)**.

    ![tipo classico](./media/resource-manager-deployment-model/classic-type.png)

È possibile usare il portale di Azure per gestire le risorse che sono state create tramite distribuzione classica.

## <a name="changes-for-compute-network-and-storage"></a>Modifiche per l'elaborazione, di rete e di archiviazione

Nel diagramma seguente consente di visualizzare le risorse di elaborazione, di rete e di archiviazione distribuite tramite Gestione risorse.

![Architettura di Manager delle risorse](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Nota le relazioni tra le risorse seguenti:

- All'interno di un gruppo di risorse sono presenti tutte le risorse.
- La macchina virtuale dipende da un account di archiviazione specifico definito nel provider di risorse di spazio di archiviazione per l'archiviazione dischi nell'archiviazione blob (obbligatorio).
- La macchina virtuale fa riferimento a una scheda di rete specifico definito in una disponibilità impostare definito nel provider di risorse di calcolo (facoltativo) e il provider di risorse di rete (obbligatorio).
- La scheda di rete fa riferimento indirizzo IP della macchina virtuale (obbligatorio) subnet della rete virtuale per la macchina virtuale (obbligatorio) e di un gruppo di sicurezza di rete (facoltativo).
- Subnet all'interno di una rete virtuale fa riferimento a un gruppo di sicurezza di rete (facoltativo).
- L'istanza di bilanciamento del carico fa riferimento il pool di back-end di indirizzi IP che includono la scheda di rete di una macchina virtuale (facoltativa) e fa riferimento a un carico bilanciamento pubblico o privato indirizzo IP (facoltativo).

Ecco i componenti e delle rispettive relazioni per la distribuzione classica:

![architettura classica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

Soluzione classica per ospitare una macchina virtuale include:

- Un servizio cloud necessari che funge da contenitore per l'hosting macchine virtuali (calcolo). Macchine virtuali sono fornite automaticamente con una scheda di rete (NIC) e un indirizzo IP assegnato da Azure. Inoltre, il servizio cloud contiene un'istanza di bilanciamento del carico esterno, un indirizzo IP pubblico ed endpoint predefiniti per consentire il traffico Secure Shell (SSH) per basati su Linux macchine virtuali e remote desktop e remoto PowerShell il traffico verso macchine virtuali basato su Windows.
- Un account di archiviazione necessario contenente dischi rigidi virtuali per una macchina virtuale, inclusi il sistema operativo dischi temporaneo e altri dati (archiviazione).
- Una rete virtuale facoltativa che funge da contenitore aggiuntivo, in cui è possibile creare una struttura di subnet e definire subnet in cui si trova la macchina virtuale (rete).

Nella tabella seguente vengono descritte le modifiche in modalità di interazione di provider di risorse di elaborazione, di rete e di archiviazione:

 Elemento | Classica | Manager delle risorse
 ---|---|---
| Servizio cloud per macchine virtuali |  Servizio cloud è stato un contenitore per l'esenzione macchine virtuali necessari disponibilità dalla piattaforma e il bilanciamento del carico. | Servizio cloud non è più un oggetto richiesto per la creazione di una macchina virtuale utilizzando il nuovo modello. |
| Reti virtuali | Una rete virtuale è facoltativa per la macchina virtuale. Se incluso, la rete virtuale non può essere distribuita con Gestione risorse. | Macchina virtuale richiede una rete virtuale che è stata distribuita con Gestione risorse. |
| Account di archiviazione | La macchina virtuale richiede un account di archiviazione che archivia dischi rigidi virtuali per il sistema operativo, dischi temporaneo e altri dati. | La macchina virtuale richiede un account di archiviazione per l'archiviazione dischi nell'archiviazione blob. |
| Set di disponibilità | Disponibilità della piattaforma indicata configurando stesso "AvailabilitySetName" nelle macchine virtuali. Il numero massimo di domini guasto: 2. | Set di disponibilità è una risorsa esposta da Microsoft.Compute Provider. Macchine virtuali che richiedono disponibilità devono essere incluse nel Set di disponibilità. Il numero massimo di domini guasto è 3. |
| Gruppi di affinità | Gruppi di affinità erano necessari per la creazione di reti virtuali. Tuttavia, con l'introduzione di reti virtuali internazionali, che non è obbligatorio più. |Per semplificare, il concetto di gruppi di affinità non esiste nel API esposte tramite Gestione risorse di Azure. |
| Il bilanciamento del carico    | Creazione di un servizio Cloud offre un servizio di bilanciamento del carico impliciti per macchine virtuali di distribuzione. | Bilanciamento del carico rappresenta una risorsa esposta dal provider di Microsoft.Network. L'interfaccia di rete principale macchine virtuali che deve essere bilanciamento del carico deve essere fa riferimento a bilanciamento del carico. Servizi di bilanciamento del carico può essere interno o esterno. Un'istanza di bilanciamento del carico fa riferimento il pool di back-end di indirizzi IP che includono la scheda di rete di una macchina virtuale (facoltativa) e fa riferimento a un carico bilanciamento pubblico o privato indirizzo IP (facoltativo). [Altre informazioni.](../articles/resource-groups-networking.md) |
|Indirizzo IP virtuale | Quando una macchina virtuale viene aggiunto a un servizio cloud, servizi cloud visualizzato un VIP predefinito (Virtual IP Address). All'indirizzo IP è l'indirizzo associato il bilanciamento del carico impliciti.    | Indirizzo IP pubblico è una risorsa esposta dal provider di Microsoft.Network. Indirizzo IP pubblico può essere statico (riservato) o dinamico. IP di pubblico dinamico possono essere assegnate a un servizio di bilanciamento del carico. È possibile proteggere IP pubblico con i gruppi di sicurezza. |
|Indirizzo IP riservato|   È possibile prenotare un indirizzo IP in Azure e associare a un servizio Cloud per garantire che l'indirizzo IP è permanente.   | Indirizzo IP pubblico possono essere creata in modalità "Statica" e che offre le stesse funzionalità come "Indirizzo IP riservate". IP di pubblico statico può essere solo a un servizio di bilanciamento del carico immediatamente. |
|Indirizzo IP pubblico (PIP) per macchine Virtuali | Indirizzi IP può essere associati a una macchina virtuale direttamente. | Indirizzo IP pubblico è una risorsa esposta dal provider di Microsoft.Network. Indirizzo IP pubblico può essere statico (riservato) o dinamico. Tuttavia, solo IP pubblico dinamico possono essere assegnate a un'interfaccia di rete per ottenere un indirizzo IP pubblico per macchine Virtuali immediatamente. |
|Punti finali| Endpoint input necessarie per configurare un computer virtuale per aprire la connettività per determinate porte. Uno dei modi comuni di connessione a macchine virtuali di completare la configurazione endpoint di input. | Le regole in entrata NAT possono essere configurate in servizi di bilanciamento del carico per ottenere le stesse funzionalità di attivazione endpoint su porte specifiche per la connessione alle macchine virtuali. |
|Nome DNS| Un servizio cloud che otterrebbe un nome DNS univoci impliciti. Ad esempio: `mycoffeeshop.cloudapp.net`. | I nomi DNS sono parametri facoltativi che possono essere specificati per una risorsa indirizzo IP pubblico. È il nome di dominio completo nel formato seguente - `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfacce di rete | Principale e secondaria dell'interfaccia di rete e le relative proprietà sono state definite come configurazione di rete di una macchina virtuale. | Interfaccia di rete è una risorsa esposta da Microsoft.Network Provider. Ciclo di vita dell'interfaccia di rete non è associato a una macchina virtuale. Fa riferimento indirizzo IP della macchina virtuale (obbligatorio) subnet della rete virtuale per la macchina virtuale (obbligatorio) e di un gruppo di sicurezza di rete (facoltativo). |

Per informazioni sulla connessione di reti virtuali da diversi modelli di distribuzione, vedere [connettere le reti virtuali diversi modelli di distribuzione nel portale](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Eseguire la migrazione da classica di Manager delle risorse

Se si è pronti eseguire la migrazione delle risorse da distribuzione classica alla distribuzione di Manager delle risorse, vedere:

1. [Tecnico approfondimento su migrazione piattaforma supportata da classico per gestione di risorse di Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Piattaforma supportata la migrazione delle risorse IaaS da classico per gestione di risorse di Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Eseguire la migrazione di risorse IaaS da classico per gestione di risorse di Azure tramite PowerShell Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Eseguire la migrazione di risorse IaaS da classico per gestione di risorse di Azure mediante CLI Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Domande frequenti

**È possibile creare una macchina virtuale utilizzando Gestione risorse di Azure per distribuire in un account di archiviazione creati con distribuzione classica o virtuali?**

Non è supportata. È possibile usare Gestione risorse di Azure per distribuire una macchina virtuale in una rete virtuale che è stata creata mediante la distribuzione classica.

**È possibile creare una macchina virtuale tramite Gestione risorse Azure da un'immagine utente creata utilizzando API di gestione del servizio di Azure?**

Non è supportata. Tuttavia, è possibile copiarli sul disco rigido virtuale da un account di archiviazione che è stato creato utilizzando API di gestione di servizio e aggiungerli a un nuovo account creato tramite Gestione risorse di Azure.

**Che cos'è l'impatto sulla quota per l'abbonamento?**

Le quote per macchine virtuali, reti virtuali e gli account di archiviazione creati tramite Gestione risorse Azure sono separate da altre quote. Ogni abbonamento Ottiene le quote per creare le risorse utilizzando le nuove API. È possibile leggere informazioni sulle altre quote [qui](../articles/azure-subscription-service-limits.md).

**È possibile continuare a usare gli script automatizzati per il provisioning di macchine virtuali, reti virtuali e gli account di archiviazione tramite l'API di gestione risorse?**

Tutte le automazione e gli script che è già stata creata continuano a funzionare per macchine virtuali esistenti, reti virtuali create in modalità di gestione dei servizi di Azure. Tuttavia, gli script devono essere aggiornate per utilizzare il nuovo schema per la creazione delle stesse risorse tramite la modalità di gestione risorse.

**Dove reperibili esempi di modelli di Manager delle risorse di Azure**

Una serie completa di modelli di partenza sono disponibili nei [Modelli di Guida introduttiva di Azure Manager delle risorse](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla creazione del modello che definisce una macchina virtuale, l'account di archiviazione e virtuali, vedere [procedura dettagliata modello Manager delle risorse](resource-manager-template-walkthrough.md).
- Per visualizzare i comandi per la distribuzione di un modello, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](resource-group-template-deploy.md).
