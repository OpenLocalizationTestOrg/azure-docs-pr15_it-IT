<properties
    pageTitle="Progettazione di macchina virtuale scala imposta per scala | Microsoft Azure"
    description="Informazioni su come progettare il set di scala macchina virtuale per scala"
    keywords="Imposta scala macchina virtuale macchina virtuale Linux," 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Progettazione di macchine Virtuali scala imposta per scala

In questo argomento vengono illustrate considerazioni sulla progettazione per il set di scala macchina virtuale. Per informazioni su quali sono i set di scala macchina virtuale, vedere [Panoramica di set di scala macchina virtuale](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Spazio di archiviazione

Impostare una scala utilizza gli account di archiviazione per archiviare i dischi del sistema operativo delle macchine virtuali nel set di. È consigliabile un rapporto di 20 macchine virtuali per account di archiviazione o meno. È anche consigliabile distribuire tra alfabeto i primi caratteri dei nomi degli account di archiviazione. Eseguire in modo che consente di distribuire carico tra diversi sistemi interni. Ad esempio, nel modello seguente serve uniqueString funzione Manager delle risorse Template per generare hash prefisso che sono contrassegnati per i nomi degli account di archiviazione: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Overprovisioning

Iniziare con la versione dell'API di "2016-03-30", macchine Virtuali scala set predefinito "overprovisioning" macchine virtuali. Con overprovisioning attivata, la scala imposta effettivamente rotazioni su più macchine virtuali di frequenti per, quindi Elimina le macchine virtuali aggiuntive che caricare un cognome. Overprovisioning migliora successo provisioning. Non è effettuata per queste macchine virtuali aggiuntive e non contano verso i limiti di quota.

Sebbene overprovisioning migliorare provisioning elevate, può causare confusione comportamento per un'applicazione che non è possibile gestire macchine virtuali scomparire senza preavviso. Per attivare overprovisioning disattivare, ottenere la stringa seguente nel modello: "overprovision": "false". Per ulteriori informazioni disponibili nella [documentazione all'API REST di impostare la scala di macchine Virtuali](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se si disattiva overprovisioning, è possibile ottenere non al computer con un rapporto di dimensioni maggiore di macchine virtuali per conto di spazio di archiviazione, ma non è consigliabile passare sopra 40.


## <a name="limits"></a>Limiti
Un set di scala basato su un'immagine personalizzata, una creata dall'utente, è necessario creare dischi rigidi virtuali disco OS tutti all'interno di un account di archiviazione. Di conseguenza, il numero massimo consigliato di macchine virtuali in un set di scala basato su un'immagine personalizzata è 20. Se si disattiva overprovisioning, è possibile accedere fino a 40.

Un set di scala basato su un'immagine della piattaforma è attualmente limitato a 100 macchine virtuali (è consigliabile 5 account di archiviazione per la scala).

Per altre macchine virtuali rispetto a questi limiti consentono, è necessario distribuire più set di scala, come illustrato in [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).