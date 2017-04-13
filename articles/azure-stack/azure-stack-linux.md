<properties
    pageTitle="Gli utenti Guest Linux nella pila Azure | Microsoft Azure"
    description="Informazioni su come creare macchine virtuali basate su Linux nello Stack di Azure."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Distribuire macchine virtuali Linux nella pila di Azure

È possibile distribuire macchine virtuali Linux nella prova di Stack Azure mediante l'aggiunta di un'immagine basata su Linux in Azure Stack Marketplace. Diversi fornitori Linux fornite immagini che possono essere aggiunti in una prova di Stack Azure o crearne una personalizzata.

## <a name="download-an-image"></a>Scaricare un'immagine

 1. Scaricare ed estrarre un'immagine compatibile con Stack Azure uno dei seguenti collegamenti o preparare il proprio:
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 risultati](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 risultati](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Estrarre l'immagine del disco rigido virtuale se necessario e [aggiungere l'immagine Marketplace](azure-stack-add-vm-image.md). Assicurarsi che la `OSType` parametro è impostato su `Linux`.
 
 3. Dopo aver aggiunto l'immagine Marketplace, viene creato un elemento Marketplace e distribuire una macchina virtuale Linux.
  
## <a name="prepare-your-own-image"></a>Preparare la propria immagine

1. Preparare la propria immagine Linux usando una delle procedure seguenti:
 - [Distribuzioni basate su centOS](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Linux Debian](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Linux Oracle](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Red Hat Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Scaricare e installare l' [Agente Linux Azure](https://github.com/Azure/WALinuxAgent/)

    L'agente di Linux Azure versione 2.1.3 o superiore per effettuare il provisioning del VM Linux in pila Azure. Molte delle distribuzioni elencate già includono questa versione dell'agente o versione successiva come pacchetto nella loro archivi (in genere chiamato `WALinuxAgent` o `walinuxagent`). Tuttavia, se la versione del pacchetto agente Azure è minore di 2.1.3 (2.0.18 o inferiore), quindi è necessario installare l'agente manualmente. Da nome pacchetto o eseguendo è possibile determinare la versione installata `/usr/sbin/waagent -version` nella macchina virtuale.

    Seguire le istruzioni seguenti per installare l'agente di Azure Linux manualmente:

 - Prima di tutto, scaricare l'agente di Azure Linux più recente dal [Github](https://github.com/Azure/WALinuxAgent/releases)esempio:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Decomprimere agente di Azure:

            # tar -vzxf v2.2.0.tar.gz

 - Installare python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Installare l'agente di Azure:

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Sistemi con Python 2. x e 3. x installato Python side-by-side potrebbe essere necessario eseguire il comando seguente:

        # sudo python3 setup.py install --register-service

    Per ulteriori informazioni, vedere il [file Leggimi](https://github.com/Azure/WALinuxAgent/blob/master/README.md)dell'agente di Azure Linux.

3. [Aggiungere l'immagine Marketplace](azure-stack-add-vm-image.md). Assicurarsi che la `OSType` parametro è impostato su `Linux`.

4. Dopo aver aggiunto l'immagine Marketplace, viene creato un elemento Marketplace e distribuire una macchina virtuale Linux.

## <a name="next-steps"></a>Passaggi successivi

[Domande frequenti su Stack di Azure](azure-stack-faq.md)