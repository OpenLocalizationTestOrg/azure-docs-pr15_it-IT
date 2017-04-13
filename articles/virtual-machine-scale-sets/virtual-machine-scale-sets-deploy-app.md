<properties
    pageTitle="Distribuire un'App nel set di scala macchina virtuale | Microsoft Azure"
    description="Distribuire un'app nel set di scala macchina virtuale"
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
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Distribuire un'App nel set di scala macchina virtuale

Un'applicazione eseguita in un Set di scala macchine Virtuali in genere è distribuita in uno dei tre modi:

- Installazione di nuovo software in un'immagine della piattaforma in fase di distribuzione. Un'immagine della piattaforma in questo contesto è un'immagine del sistema operativo da Azure Marketplace, ad esempio 16.04 Ubuntu, Windows Server 2012 R2, e così via.

È possibile installare il software di nuovo su un'immagine di piattaforma utilizzando l' [Estensione macchine Virtuali](../virtual-machines/virtual-machines-windows-extensions-features.md). Un'estensione macchine Virtuali è software da eseguire quando si distribuisce una macchina virtuale. È possibile eseguire il codice desiderato in fase di distribuzione utilizzando l'estensione script personalizzato. [Di seguito](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) è illustrato un esempio Azure Manager delle risorse modello con due estensioni macchine Virtuali: estensione di Script personalizzati Linux per installare Apache e PHP e diagnostica estensione per generare dati sulle prestazioni utilizzati da Azure Autoscale.

Il vantaggio di questo approccio è dispone di un livello di separazione tra il codice dell'applicazione e il sistema operativo e possibile mantenere separati l'applicazione. Naturalmente potrebbe essere più se c'è molto per lo script scaricare e configurare il che significa che non vi sono anche parti più mobili e l'ora di distribuzione macchine Virtuali.

**Se si passano con informazioni riservate nel comando estensione di Script personalizzati (ad esempio una password), assicurarsi di specificare il `commandToExecute` nel `protectedSettings` attributo dell'estensione di Script personalizzati anziché il `settings` attributo.**

- Creare un'immagine macchine Virtuali personalizzata che include il sistema operativo e sull'applicazione di un singolo disco rigido virtuale. Di seguito il set di scala è costituito da un set di macchine virtuali copiato da un'immagine creata dall'utente, è necessario mantenere. Questo approccio non richiede alcuna configurazione aggiuntiva in fase di distribuzione macchine Virtuali. Tuttavia, nel `2016-03-30` versione del set di scala macchine Virtuali (e versioni precedenti), sono limitati a un account di archiviazione singola dischi del sistema operativo per macchine virtuali nel set di scala. In questo modo, è possibile avere un massimo di 40 macchine virtuali in un set di scala, anziché macchine Virtuali 100 per scala impostare limite con immagini piattaforme. Per ulteriori informazioni, vedere [Scala impostare progettazione Panoramica](./virtual-machine-scale-sets-design-overview.md) .

- Distribuire una piattaforma o un'immagine personalizzata, ovvero un host contenitore e installare l'applicazione di uno o più contenitori da gestire con uno strumento di gestione orchestrator o configurazione. Interessante questo approccio è sono estratti dell'infrastruttura cloud dal livello dell'applicazione e possono mantenerle separatamente.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Cosa accade quando un Set di scala macchine Virtuali scale fuori?

Quando si aggiunge una o più macchine virtuali a una scala impostata aumentando la capacità-se manualmente o mediante autoscale – l'applicazione viene installato automaticamente. Per esempio se la scala imposta include le estensioni definite, eseguono in una nuova macchina virtuale ogni volta che è stata creata. Se il set di scala è basato su un'immagine personalizzata, qualsiasi nuova macchina virtuale è una copia dell'immagine personalizzata di origine. Se la scala imposta macchine virtuali sono contenitore host, quindi potrebbe avere il codice di avvio per caricare i contenitori di un'estensione di Script personalizzati o un'estensione può installare un agente che registra con un orchestrator cluster (ad esempio servizio contenitore Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Come si gestiscono gli aggiornamenti dell'applicazione in macchine Virtuali scala set?

Aggiornamenti dell'applicazione in macchine Virtuali scala set, tre approcci principali seguono uno dei tre metodi di distribuzione applicazione precedente:

* Aggiornamento con le estensioni macchine Virtuali. Tutte le estensioni macchine Virtuali definiti per un Set di scala macchine Virtuali vengono eseguiti ogni volta che viene distribuita una nuova macchina virtuale, una macchina virtuale esistente ricreato l'immagine o un'estensione macchine Virtuali viene aggiornata. Se è necessario aggiornare l'applicazione, direttamente aggiornamento di un'applicazione tramite le estensioni è disponibile un approccio possibile, è sufficiente aggiornare la definizione di estensione. Un modo semplice per eseguire questa operazione è modificando fileUris in modo che punti al nuovo software.

* L'approccio immagine personalizzata non modificabile. Quando si dolci applicazione (o componenti app) in un'immagine di macchine Virtuali consente di tracciare sulla creazione di una pipeline affidabile per automatizzare build, test e distribuzione delle immagini. È possibile progettare l'architettura per facilitare la sostituzione rapida di un set di scala a fasi nell'ambiente di produzione. Un buon esempio di questo approccio è il [driver di Azure Spinnaker funziona](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Chi Terraform sono supportati anche e gestione di risorse di Azure, in modo è inoltre possibile definire le immagini "come codice" e generarle in Azure, quindi usare il disco rigido virtuale nel set di scala. Tuttavia, eseguire in modo da critiche per le immagini Marketplace, nel punto in cui gli script estensioni/personalizzati diventano più importanti poiché non di modificare direttamente i bit da Marketplace.

* Aggiornare i contenitori. Estrarre la gestione del ciclo di vita dell'applicazione a un livello sopra l'infrastruttura cloud, ad esempio da incapsulante applicazioni e componenti di app in contenitori e gestire questi contenitori tramite orchestrators contenitore e responsabili della configurazione come Chef/Marionetta.

La scala imposta macchine virtuali quindi verranno convertiti in un supporto stabile per i contenitori e richiede solo occasionale sicurezza e gli aggiornamenti relativi al sistema operativo. Come detto, il servizio di contenitore Azure è un ottimo esempio di questo approccio e la creazione di un servizio attorno a essa.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Come fare in modo che un aggiornamento del sistema operativo domini aggiornamento?

Si supponga che si desidera aggiornare l'immagine del sistema operativo e mantenendo al contempo l'esecuzione di macchine Virtuali scala impostata. È possibile eseguire questa operazione è per aggiornare la macchina virtuale immagini macchine uno Virtuali alla volta. È possibile eseguire con PowerShell o CLI Azure. Sono disponibili comandi separati per aggiornare il modello macchine Virtuali scala impostata (come la configurazione è definita) e per le chiamate "aggiornamento manuale" su singole macchine virtuali di problema.

[Di seguito](https://github.com/gbowerman/vmsstools) è illustrato un esempio script Python che consente di automatizzare il processo di aggiornamento di un dominio di un aggiornamento di macchine Virtuali scala impostare alla volta. (Avvertenza: più di una prova pratica rispetto a una soluzione di produzione pronto per la protezione avanzata, è consigliabile aggiungere alcuni ecc controllo errori.).
