<properties
    pageTitle="Installare MySQL in una macchina virtuale OpenSUSE | Microsoft Azure"
    description="Informazioni su come installare MySQL in un computer OpenSUSE Linux VMirtual in Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installare MySQL in un computer virtuale che esegue OpenSUSE Linux in Azure

[MySQL] [ MySQL] è un database SQL comuni, Apri origine. In questa esercitazione viene illustrato come creare una macchina virtuale che eseguono OpenSUSE Linux e quindi installare MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Creare una macchina virtuale che eseguono OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installare ed eseguire MySQL sul computer virtuale

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate su MySQL, vedere la [Documentazione MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

