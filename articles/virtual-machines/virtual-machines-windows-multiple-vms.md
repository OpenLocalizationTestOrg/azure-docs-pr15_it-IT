<properties
    pageTitle="Creare più macchine virtuali | Microsoft Azure"
    description="Opzioni per la creazione di più macchine virtuali in Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Creare più macchine virtuali Azure

Sono disponibili molti scenari in cui è necessario creare un numero elevato di simili macchine (). Alcuni esempi includono ad alta-performance computing (HPC), analisi dei dati di grandi dimensioni, scalable e spesso senza stato server intermedio o back-end (ad esempio server Web) e database distribuiti.

In questo articolo vengono illustrate le opzioni disponibili per creare più macchine virtuali in Azure. Queste opzioni andare oltre il semplici casi in cui è creare manualmente una serie di macchine virtuali. Per creare più macchine virtuali, i processi utilizzato in genere non vengono scalate anche se è necessario creare più di una serie di macchine virtuali.

È possibile creare più macchine virtuali simile consiste nell'utilizzare il costrutto di gestione risorse Azure di _cicli di risorsa_.

## <a name="resource-loops"></a>Risorsa cicli

Risorsa cicli sono una forma abbreviata sintattica nei modelli di gestione di risorse Azure. Cicli di risorse è possono creare un set di risorse in modo analogo configurate riprodotta a ciclo continuo. È possibile utilizzare cicli delle risorse per creare più account di archiviazione, interfacce di rete o macchine virtuali. Per ulteriori informazioni sulla risorsa cicli, fare riferimento a [creare macchine virtuali di disponibilità imposta utilizzo dei cicli di risorse](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Problemi di scala

Sebbene risorsa cicli rendono più semplice creare un'infrastruttura cloud in scala e produrre modelli più concisi, rimangono alcuni problemi correlati. Ad esempio, se si utilizza un ciclo di risorse per creare 100 macchine virtuali, è necessario correlare network interface controller (NIC) con macchine virtuali corrispondenti e gli account di archiviazione. Poiché il numero di macchine virtuali è probabile che può essere diverso dal numero di account di archiviazione, è necessario gestire con dimensioni di ciclo risorsa diversa, troppo. Si tratta subito i problemi, ma la complessità aumenta in modo significativo con scala.

Un altro problema si verifica quando è necessaria un'infrastruttura scale illimitate. Ad esempio, è consigliabile un'infrastruttura autoscale che automaticamente aumenta o diminuisce il numero di macchine virtuali in risposta a carico di lavoro. Macchine virtuali non forniscono un meccanismo integrato per variare numero (scalabilità e scala in). Se ridimensionate rimuovendo macchine virtuali, è difficile da garantire disponibilità, assicurandosi che macchine virtuali bilanciate domini aggiornamento e fault.

Infine, quando si utilizza un ciclo di risorse, più chiamate per creare risorse passano all'infrastruttura sottostante. Se più chiamate creano risorse simili, implicita consente di migliorare la progettazione e ottimizzare le prestazioni e affidabilità distribuzione Azure. Verrà visualizzata in _scala macchina virtuale imposta_ risultare.

## <a name="virtual-machine-scale-sets"></a>Set di scala macchina virtuale

Set di scala macchina virtuale sono una risorsa Azure calcolare per distribuire e gestire un insieme di macchine virtuali identici. Con tutte le macchine virtuali configurato la stessa, scala macchine Virtuali set sono facili da ridurre in e scalabilità. Sufficiente modificare il numero di macchine virtuali nel set di. È inoltre possibile configurare macchine Virtuali scala set per scalare automaticamente in base a carico di lavoro.

Se è necessario per le risorse di calcolo e in, le operazioni di ridimensionamento in modo implicito bilanciamento domini guasto e aggiornamento.

Invece di correlazione più risorse, ad esempio schede di rete e macchine virtuali, un insieme di scala macchine Virtuali ha rete, lo spazio di archiviazione, macchina virtuale e le proprietà di estensione che è possibile configurare in modo centralizzato.

Per un'introduzione ai set di scala macchine Virtuali, consultare la [macchina virtuale scala Imposta pagina prodotto](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Per informazioni più dettagliate, vedere la [scala di macchine virtuali imposta documentazione](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
