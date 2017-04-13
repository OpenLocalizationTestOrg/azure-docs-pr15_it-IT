<properties
    pageTitle="Installare MongoDB in un Windows macchine Virtuali | Microsoft Azure"
    description="Informazioni su come installare MongoDB in una macchina virtuale Azure creato con il modello di distribuzione classica che eseguono Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Installare MongoDB in macchine Virtuali di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per installare e configurare MongoDB utilizzando il modello di distribuzione di Manager delle risorse, vedere [l'articolo](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] sui database NoSQL Apri origine e prestazioni elevate popolari. In questo articolo viene descritta la creazione di una Server di Windows macchine () tramite il [portale classica Azure][AzurePortal]. Creare e connettere la macchina virtuale prima di installare e configurare MongoDB un disco dati. Se si dispone di una macchina virtuale esistente in Azure che si desidera utilizzare, è possibile passare direttamente [all'installazione e configurazione MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Creare una macchina virtuale che eseguono Windows Server

Seguire queste istruzioni per creare una macchina virtuale.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] È possibile aggiungere un endpoint per MongoDB durante la creazione di macchina virtuale e configurare nel modo seguente: nome come **Mongo**, utilizzare **il protocollo TCP** come protocollo e impostato le porte pubbliche e private su **27017**.

## <a name="attach-a-data-disk"></a>Allegare un disco dati
Per specificare lo spazio di archiviazione per la macchina virtuale, allegare un disco dati e quindi inizializzare Windows utilizzabile dagli. Se si dispone già di un disco dati, è possibile allegare tale disco esistente oppure è possibile allegare un disco vuoto.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Per istruzioni sulla inizializzazione del disco, vedere "procedura: inizializzare un nuovo disco di dati in Windows Server" in [come collegare un disco di dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installare ed eseguire MongoDB sul computer virtuale

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Riepilogo
In questa esercitazione, è stato descritto come creare una macchina virtuale che eseguono Windows Server, effettuare la connessione remota e allegare un disco dati.  Inoltre appreso come installare e configurare MongoDB sul computer virtuale basato su Windows. È ora possibile accedere MongoDB computer virtuale basato su Windows seguendo scenari avanzati nella [documentazione MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
