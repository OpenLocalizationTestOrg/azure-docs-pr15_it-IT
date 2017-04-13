<properties
    pageTitle="Piattaforma supportata la migrazione delle risorse IaaS da classico per gestione di risorse di Azure | Microsoft Azure"
    description="In questo articolo illustra la migrazione di piattaforma supportata delle risorse da classico per gestione di risorse di Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Piattaforma supportata la migrazione delle risorse IaaS da classico per gestione di risorse di Azure

In questo articolo vengono descritte come abbiamo stiamo che la migrazione dell'infrastruttura come una risorsa di servizio (IaaS) dalla visualizzazione classica di modelli di distribuzione di Manager delle risorse. È possibile leggere informazioni sui [vantaggi e le funzionalità di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md). Abbiamo viene illustrato come connettere le risorse tra i modelli di due distribuzione coesistono nell'abbonamento tramite gateway da sito virtuali. 

## <a name="goal-for-migration"></a>Obiettivo per la migrazione

Manager delle risorse consente la distribuzione di applicazioni complesse tramite modelli, configura macchine virtuali utilizzando le estensioni macchine Virtuali e incorpora la gestione degli accessi e tagging. Gestione risorse di Azure include distribuzione scalable e in parallelo per macchine virtuali in set di disponibilità. Il nuovo modello di distribuzione vengono forniti anche la gestione del ciclo di vita di elaborazione, di rete e di archiviazione in modo indipendente. Infine, esiste un stato attivo sull'attivazione di sicurezza per impostazione predefinita con l'applicazione di macchine virtuali in una rete virtuale.

Quasi tutte le funzionalità del modello di distribuzione classica sono supportate per l'elaborazione, di rete e di archiviazione in Gestione risorse di Azure. Per trarre vantaggio da nuove funzionalità di gestione di risorse di Azure, è possibile eseguire la migrazione distribuzioni esistenti dal modello di distribuzione classica.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifiche alle automazione e utensili dopo la migrazione

Durante la migrazione delle risorse dal modello di distribuzione classica al modello di distribuzione di Manager delle risorse, è necessario aggiornare l'automazione esistente o utensili per garantire che continua a funzionare dopo la migrazione.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Significato della migrazione di risorse IaaS da classico a Gestione risorse

Prima è drill-down i dettagli, osservare la differenza tra dati piano e piano di gestione delle operazioni sulle risorse IaaS.

- *Piano di gestione* descrive le chiamate entrano il piano di gestione o l'API per la modifica delle risorse. Operazioni come la creazione di una macchina virtuale, riavviare una macchina virtuale e aggiornamento di una rete virtuale con una nuova subnet, ad esempio, gestire le risorse in esecuzione. Poiché non influiscono direttamente sul connessione alle istanze.
- *Piano di dati* (applicazione) descrive il runtime dell'applicazione stessa e a altro comporta l'interazione con le istanze che non attraversano l'API di Azure. Accesso al sito Web o estrazione dei dati di esecuzione di un'istanza di SQL Server o un server MongoDB viene considerato dati interazione piano o dell'applicazione. Copia di un blob da un account di archiviazione e l'accesso a un indirizzo IP pubblico RDP o SSH a macchina virtuale sono inoltre piano dati. Queste operazioni mantengono l'applicazione in esecuzione su elaborazione, di rete e di archiviazione.

>[AZURE.NOTE] In alcuni scenari di migrazione, la piattaforma Azure arresta, ne esegue e riavvia macchine virtuali. In questo caso verrà una breve tempo di inattività di piano di dati.

## <a name="supported-scopes-of-migration"></a>Ambiti supportati della migrazione

Esistono tre ambiti di migrazione destinati principalmente a elaborazione, di rete e di archiviazione. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrazione di macchine virtuali (non in una rete virtuale)

Nel modello di distribuzione di Manager delle risorse, la protezione viene applicata per le applicazioni per impostazione predefinita. Tutte le macchine virtuali devono essere in una rete virtuale nel modello di Manager delle risorse. Il riavvio del piattaforma Azure (`Stop`, `Deallocate`, e `Start`) macchine virtuali come parte della migrazione. Sono disponibili due opzioni per le reti virtuali:

- È possibile richiedere la piattaforma per creare una nuova rete virtuale e spostare la macchina virtuale nella nuova rete virtuale.
- È possibile eseguire la migrazione di macchina virtuale in una rete virtuale esistente in Gestione risorse.

>[AZURE.NOTE] In questo ambito di migrazione, le operazioni di piano di gestione e le operazioni di piano di dati non possono essere autorizzate per un periodo di tempo durante la migrazione.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrazione di macchine virtuali (in una rete virtuale)

Per la maggior parte delle configurazioni macchine Virtuali, solo i metadati migrazione tra i modelli di distribuzione classica e Manager delle risorse. Macchine virtuali sottostante sono in esecuzione sullo stesso hardware nella stessa rete e con lo stesso spazio di archiviazione. Le operazioni di piano di gestione potrebbero non consentite per un determinato periodo di tempo durante la migrazione. Tuttavia, il piano dati continua a funzionare. Le applicazioni in esecuzione nella parte superiore di macchine virtuali (classiche), ovvero non comportano i tempi di inattività durante la migrazione.

Le configurazioni seguenti non sono attualmente supportate. Se il supporto viene aggiunto in futuro, alcune macchine virtuali in questa configurazione vengono addebitati i tempi di inattività (andare tramite Interrompi, rilasciare e riavviare le operazioni di macchine Virtuali).

-   Si dispone di più disponibilità impostare in un servizio cloud singola.
-   Si dispone di uno o più set di disponibilità e macchine virtuali che non fanno parte di una disponibilità imposta in un servizio cloud singola.

>[AZURE.NOTE] In questo ambito di migrazione, è possibile non consentito il piano di gestione per un periodo di tempo durante la migrazione. Per alcune configurazioni come descritto in precedenza, si verifica il tempo di inattività di piano di dati.

### <a name="storage-accounts-migration"></a>Migrazione degli account di archiviazione

Per consentire la migrazione completa, è possibile distribuire macchine virtuali di Manager delle risorse in un account di archiviazione classica. Con questa funzionalità, le risorse di calcolo e di rete possono e devono essere migrate indipendente gli account di archiviazione. Dopo la migrazione su macchine virtuali e virtuali, è necessario eseguire la migrazione tramite l'account di archiviazione per completare il processo di migrazione. 

>[AZURE.NOTE] Il modello di distribuzione di Manager delle risorse non è disponibile il concetto di immagini classica e dischi. Quando l'account di archiviazione è immagini incluse nella migrazione e classiche e dischi non sono visibili in pila Manager delle risorse, ma il supporto dischi rigidi virtuali rimangono in account di archiviazione. 

## <a name="unsupported-features-and-configurations"></a>Configurazioni e le caratteristiche non supportate

Non è attualmente supportato alcune funzionalità e le configurazioni. Le sezioni seguenti descrivono i suggerimenti attorno ad esse.

### <a name="unsupported-features"></a>Caratteristiche non supportate

Le caratteristiche seguenti non sono attualmente supportate. È possibile rimuovere queste impostazioni, eseguire la migrazione di macchine virtuali e abilitare nuovamente le impostazioni nel modello di distribuzione Manager delle risorse.

Provider di risorse | Caratteristica
---------- | ------------
Calcolare | Dischi macchina virtuale non associate.
Calcolare | Immagini di macchina virtuale.
Rete | Endpoint ACL.
Rete | Gateway di rete virtuale (da un sito, Azure ExpressRoute, gateway applicazioni scegliere sito).
Rete | Reti virtuali mediante VNet Peering. (Eseguire la migrazione VNet a ARM quindi peer) Altre informazioni [VNet Peering] (... /Virtual-Network/Virtual-Network-peering-Overview.MD).
Rete | Il traffico Gestione profili.

### <a name="unsupported-configurations"></a>Configurazioni non supportate

Le configurazioni seguenti non sono attualmente supportate.

Servizio | Configurazione | Suggerimento
---------- | ------------ | ------------
Manager delle risorse | Ruolo in base a Access controllo (RBAC) per le risorse classiche | Poiché l'URI delle risorse viene modificato dopo la migrazione, si consiglia di pianificare gli aggiornamenti di criteri RBAC che devono essere eseguite dopo la migrazione.
Calcolare | Più subnet associata a una macchina virtuale | Aggiornare la configurazione di subnet per fare riferimento solo subnet.
Calcolare | Macchine virtuali che appartengono a una rete virtuale ma non si ha una subnet esplicita assegnata | Facoltativamente, è possibile eliminare la macchina virtuale.
Calcolare | Macchine virtuali contenenti avvisi, Autoscale criteri | La migrazione attraversa e queste impostazioni vengono eliminate. Si consiglia di valutare l'ambiente prima di eseguire la migrazione. In alternativa, è possibile riconfigurare le impostazioni degli avvisi al termine della migrazione.
Calcolare | Estensioni XML VM (1. * BGInfo, Debugger di Visual Studio, distribuzione Web e il debug remoto) | Non è supportata. È consigliabile che consente di rimuovere queste estensioni dal computer virtuale per continuare la migrazione o essere eliminati automaticamente durante il processo di migrazione.
Calcolare | Avvio diagnostica con lo spazio di archiviazione Premium | Disattivare la funzionalità di diagnostica di avvio per le macchine virtuali prima di procedere con la migrazione. Una volta completata la migrazione, è possibile abilitare nuovamente l'avvio diagnostica in pila Manager delle risorse. Inoltre, è necessario eliminare BLOB utilizzati per i registri seriali e schermata in modo che non è più addebitate per tali BLOB.
Calcolare | Servizi cloud che contengono i ruoli web/lavoro | Questa operazione non è supportata.
Rete | Reti virtuali che contengono macchine virtuali di ruoli e di web/lavoro |  Questa operazione non è supportata.
Servizio App Azure | Reti virtuali che contengono ambienti servizio App | Questa operazione non è supportata.
Azure HDInsight | Reti virtuali che contengono servizi HDInsight | Questa operazione non è supportata.
Servizi di ciclo di vita Microsoft Dynamics | Reti virtuali che contengono macchine virtuali gestiti da servizi di ciclo di vita Dynamics | Questa operazione non è supportata.
Calcolare | Estensioni di Centro protezione di Azure con un VNET che contiene un gateway VPN o gateway ER con server DNS locale | Azure Security Center viene installato automaticamente le estensioni in macchine virtuali per controllare la sicurezza e generare avvisi. Queste estensioni in genere visualizzato installate automaticamente se il criterio di Azure Security Center è attivato per l'abbonamento. Durante la migrazione di gateway non è attualmente supportata e il gateway è necessario eliminare prima di confermare la migrazione, l'accesso a internet all'account di archiviazione di macchine Virtuali andrà perduta quando viene eliminato il gateway. La migrazione non verrà eseguita quando si verifica questo evento come non popolato il blob di stato agente guest. È consigliabile disattivare il criterio di Azure Security Center dell'abbonamento 3 ore prima di procedere con la migrazione.

## <a name="the-migration-experience"></a>L'esperienza di migrazione

Prima di iniziare l'esperienza di migrazione, è consigliabile le operazioni seguenti:

- Assicurarsi che le risorse che si desidera eseguire la migrazione di non utilizzano le caratteristiche non supportate o configurazioni. In genere la piattaforma rileva questi problemi e genera un errore.
- Se si dispone di macchine virtuali che non sono presenti in una rete virtuale, si verrà venga arrestati e rilasciati come parte dell'operazione di preparazione. Se non si vuole perdere l'indirizzo IP pubblico, esaminare l'indirizzo IP la prenotazione prima di attivare l'operazione di preparazione. Tuttavia, se le macchine virtuali si trovano in una rete virtuale, non sono arrestati e rilasciati.
- Pianificare la migrazione durante le ore di inattività per fare spazio per gli eventuali errori imprevisti che potrebbero verificarsi durante la migrazione.
- Scaricare la configurazione corrente delle proprie macchine virtuali tramite PowerShell, i comandi dell'interfaccia della riga di comando (CLI) o API REST per rendere più semplice per la convalida dopo il passaggio di preparazione è stato completo.
- Aggiornare gli script di automazione/operationalization per gestire il modello di distribuzione di Manager delle risorse prima di iniziare la migrazione. Facoltativamente, è possibile eseguire operazioni GET quando le risorse sono nello stato preparato.
- Valutare i criteri di RBAC configurati per le risorse IaaS classiche e piano una volta completata la migrazione.

Il flusso di lavoro di migrazione è il seguente

![Schermata che mostra il flusso di lavoro di migrazione](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Tutte le operazioni descritte nelle sezioni seguenti sono idempotenti. Se si dispone di un problema diverso da una caratteristica non supportata o un errore di configurazione, è consigliabile riprovare Prepara, interrompere o eseguire il commit operazione. La piattaforma Azure tenta l'azione.

### <a name="validate"></a>Convalidare

L'operazione di convalida è il primo passaggio del processo di migrazione. Lo scopo di questo passaggio consiste nel analizzare i dati in background per le risorse in fase di migrazione e restituito successo/un errore se le risorse sono in grado di migrazione.

Selezionare la rete virtuale o il servizio di hosting (se non si tratta di una rete virtuale) che si desidera convalidare per la migrazione.

* Se la risorsa non è in grado di migrazione, la piattaforma Azure sono elencati tutti i motivi per perché non è supportata per la migrazione.

### <a name="prepare"></a>Preparare l'ambiente

L'operazione di preparazione è il secondo passaggio del processo di migrazione. Lo scopo di questo passaggio consiste nel simulare la trasformazione di risorse IaaS da classico alle risorse di Manager delle risorse e presentare side-by-side per la visualizzazione.

Selezionare la rete virtuale o il servizio di hosting (se non si tratta di una rete virtuale) che si desidera preparare per la migrazione.

* Se la risorsa non è in grado di migrazione, la piattaforma Azure interrompe il processo di migrazione e specifica il motivo perché l'operazione di preparazione non è riuscita.
* Se la risorsa è in grado di migrazione, i blocchi prima piattaforma Azure verso il basso le operazioni di piano di gestione per le risorse in fase di migrazione. Ad esempio, non sono in grado di aggiungere un disco dati a una macchina virtuale in fase di migrazione.

La piattaforma Azure avvia la migrazione dei metadati da classico a Manager delle risorse per le risorse di migrazione.

Al termine dell'operazione di preparazione, avere la possibilità di visualizzare le risorse in entrambi classica e Manager delle risorse. Per ogni servizio cloud nel modello di distribuzione classica, la piattaforma Azure consente di creare un nome di un gruppo di risorse contenente il modello `cloud-service-name>-migrated`.

>[AZURE.NOTE] Macchine virtuali che non sono presenti in una rete virtuale classica vengono interrotti deallocato in questa fase di migrazione.

### <a name="check-manual-or-scripted"></a>Controllo (manuale o script)

Nella schermata di controllo, è possibile utilizzare facoltativamente la configurazione è stato scaricato in precedenza per convalidare che la migrazione siano corretta. In alternativa, è possibile accedere al portale e controllo posto le proprietà e le risorse per convalidare che la migrazione dei metadati è soddisfacente.

Se si esegue la migrazione di una rete virtuale, la maggior parte delle configurazione macchine virtuali non si riavvia. Per le applicazioni in tali macchine virtuali, è possibile convalidare che l'applicazione sia ancora in esecuzione.

È possibile testare il monitoraggio/automazione e script operativi per verificare se le macchine virtuali funzionino correttamente e se gli script aggiornati funzionano correttamente. Sono supportate solo operazioni GET quando le risorse sono nello stato preparato.

Non esiste alcun intervallo di tempo impostare prima del quale è necessario eseguire il commit della migrazione. Si può richiedere più tempo desiderato in questo stato. Tuttavia, il piano di gestione è bloccato per queste risorse finché non si interrompere o eseguire il commit.

Se viene visualizzato eventuali problemi, è sempre possibile interrompere la migrazione e tornare al modello di distribuzione classica. Quando si torna indietro, la piattaforma Azure aprirà le operazioni di piano di gestione delle risorse in modo che è possibile riprendere le normali operazioni su tali macchine virtuali nel modello di distribuzione classica.

### <a name="abort"></a>Interruzione

L'interruzione è un passaggio facoltativo che consente di annullare le modifiche al modello di distribuzione classica e interrompere la migrazione.

>[AZURE.NOTE] Non è possibile eseguire questa operazione dopo avere attivato il commit.  

### <a name="commit"></a>Eseguire il commit

Dopo aver completato la convalida, è possibile applicare la migrazione. Risorse non vengono visualizzate più classica e sono disponibili solo nel modello di distribuzione di Manager delle risorse. Le risorse migrate possono essere gestite solo nel portale di nuovo.

>[AZURE.NOTE] Questa è un'operazione idempotente. In caso contrario, si consiglia di ripetere l'operazione. Se problema persiste, creare un ticket di supporto o creare un post nel forum con un tag di ClassicIaaSMigration al [forum di macchine Virtuali](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Domande frequenti

**Il piano di migrazione influisce qualsiasi delle applicazioni da eseguire in macchine virtuali di Azure o servizi esistenti?**

No. Macchine virtuali (classiche) sono completamente supportate i servizi di disponibilità generale. È possibile continuare a usare queste risorse per espandere il consumi sull'ambiente in Microsoft Azure.

**Cosa succede ai personale macchine virtuali se non intende migrare nel prossimo futuro?**

Non è in corso sostituire l'API classiche esistenti e il modello di risorse. Vogliamo facilitare la migrazione, prendere in considerazione le caratteristiche avanzate sono disponibili nel modello di distribuzione Manager delle risorse. È consigliabile esaminare [alcune dei miglioramenti](virtual-machines-windows-compare-deployment-models.md) che fanno parte di IaaS in Gestione risorse.

**Che cosa significa questo piano di migrazione per gli utensili esistenti?**

Aggiornare l'utensili nel modello di distribuzione di Manager delle risorse corrisponde a uno delle modifiche più importanti che è necessario includere le nei piani di migrazione.

**Quanto tempo il tempo di inattività di piano di gestione sarà?**

Dipende il numero di risorse che viene eseguita la migrazione. Per distribuzioni più piccole (alcune decine di macchine virtuali), la migrazione di tutta necessario meno di un'ora. Per distribuzioni su larga scala (centinaia di macchine virtuali), la migrazione può richiedere alcune ore.

**È possibile implementazione dopo le risorse di migrazione vengono eseguito il commit in Gestione risorse**

È possibile interrompere la migrazione, purché le risorse sono nello stato preparato. Ripristino dello stato precedente non è supportata quando le risorse sono state migrate tramite il commit.

**È possibile ripristinare la migrazione se l'operazione di commit non riesce?**

Si non è possibile interrompere la migrazione in caso di esito di commit. Tutte le operazioni di migrazione, tra cui l'operazione di commit siano idempotenti. È consigliabile ripetere l'operazione dopo alcuni secondi. Se si resta un errore, creare un ticket di supporto o creare un post nel forum con il tag ClassicIaaSMigration al [forum di macchine Virtuali](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**È necessario acquistare un altro circuito strada se è necessario utilizzare IaaS in Gestione risorse?**

No. È abilitata recente [spostamento circuiti ExpressRoute da classico per il modello di distribuzione di Manager delle risorse](../expressroute/expressroute-move.md). Non è necessario acquistare un nuovo circuito ExpressRoute se si ha già uno.

**Cosa fare se ho configurato Criteri di controllo dell'accesso basato sui ruoli per le risorse IaaS classiche?**

Durante la migrazione, le risorse trasformano da classico a Manager delle risorse. È consigliabile in modo che si intende gli aggiornamenti di criteri RBAC che devono essere eseguite dopo la migrazione.

**Cosa fare se si usa il ripristino del sito di Azure o Backup di Azure oggi?**

Per eseguire la migrazione macchina virtuale che sono state abilitate per backup, vedere [ho backup personale classiche macchine virtuali nell'archivio di backup. A questo punto si desidera eseguire la migrazione personale macchine virtuali dalla modalità classica alla modalità di gestione risorse. Come è eseguire il backup loro nell'archivio di servizi di recupero?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**È possibile convalidare la sottoscrizione o risorse per verificare che siano in grado di migrazione**

Sì. Nell'opzione di migrazione di piattaforma supportata, il primo passaggio per la preparazione per la migrazione è per verificare che le risorse siano in grado di migrazione. Nel caso in cui l'operazione di convalida non riesce, si ricevono messaggi per tutti i motivi che non può essere completata la migrazione.

**Cosa succede se verificano un errore di quota durante la preparazione di risorse IaaS per la migrazione?**

È consigliabile interrompere la migrazione e quindi eseguire una richiesta di assistenza per aumentare le quote di area geografica in cui si esegue la migrazione di macchine virtuali. Dopo l'approvazione la richiesta della quota, è possibile iniziare eseguendo nuovamente la procedura di migrazione.

**Come segnalano un problema?**

Registrare i problemi e domande sulla migrazione in al [forum di macchine Virtuali](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), con la parola chiave ClassicIaaSMigration. È consigliabile inviare tutte le domande su questo forum. Se si dispone di un contratto di supporto, si è benvenuto agli utenti di accedere anche un ticket di supporto.

**Cosa fare se non è soddisfacente i nomi delle risorse che la piattaforma scelto durante la migrazione?**

Tutte le risorse che si specificare in modo esplicito i nomi nel modello di distribuzione classica vengono mantenute durante la migrazione. In alcuni casi, vengono create nuove risorse. Ad esempio: viene creata un'interfaccia di rete per ogni macchina virtuale. La possibilità di controllare i nomi di queste nuove risorse creati durante la migrazione non è attualmente supportato. Registrare i voti per questa caratteristica in [Azure area feedback](http://feedback.azure.com).

* *viene visualizzato un messaggio *"macchine Virtuali segnala lo stato dell'agente globale come non pronta. Di conseguenza, non eseguire la migrazione della macchina virtuale. Assicurarsi che l'agente di macchine Virtuali è la segnalazione stato agente complessivo pronto"* o *"macchine Virtuali di estensione il cui stato non viene segnalato da macchina virtuale contiene. Di conseguenza, questa macchina virtuale non è possibile eseguire la migrazione."***

Questo messaggio viene visualizzato quando la macchina virtuale non dispone di connettività in uscita a internet. L'agente di macchine Virtuali utilizza la connettività in uscita per raggiungere l'account di archiviazione Azure per aggiornare lo stato dell'agente ogni cinque minuti.


## <a name="next-steps"></a>Passaggi successivi
Dopo avere appreso la migrazione delle risorse IaaS classiche di Manager delle risorse, è possibile iniziare la migrazione di risorse.

- [Tecnico approfondimento su migrazione piattaforma supportata da classico per gestione di risorse di Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usare PowerShell per eseguire la migrazione di risorse IaaS classico per gestione di risorse di Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Consente di eseguire la migrazione di risorse IaaS classico per gestione di risorse di Azure CLI](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Duplicare una macchina virtuale classica per gestione di risorse di Azure usando gli script di PowerShell della community](virtual-machines-windows-migration-scripts.md)
