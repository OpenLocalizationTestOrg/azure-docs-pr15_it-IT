<properties
   pageTitle="Accesso a macchine Virtuali ID"
   description="Descrive l'accesso e l'uso di ID univoco macchine Virtuali di Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Accesso e l'uso di ID univoco macchine Virtuali di Azure

Azure ID univoco macchine Virtuali è un identificatore 128 bit con codifica e archiviati in SMBIOS tutti Azure IaaS Virtual Machine e può essere letti attualmente usando i comandi BIOS piattaforma.

ID univoco di Azure macchine Virtuali è una proprietà di sola lettura. Alla chiusura di riavviare il computer non è possibile modificare Azure ID univoco di macchine Virtuali (uno pianificate non pianificato), inizio/fine rimozione dell'assegnazione, service correzione o ripristinare in posizione. Tuttavia, se la macchina virtuale è uno snapshot e copiata per creare una nuova istanza, nuovo ID di macchine Virtuali di Azure viene configurato.

> [AZURE.NOTE] Se si dispone di versioni precedenti macchine virtuali creato e in esecuzione poiché questa nuova caratteristica se si ha distribuita (18 settembre 2014), vedere il riavvio di una macchina virtuale per ottenere automaticamente un Azure univoco ID.


Per accedere Azure macchine Virtuali ID univoco da all'interno della macchina virtuale:


## <a name="create-a-vm"></a>Creare una macchina virtuale
 

Per ulteriori informazioni, vedere [creare una macchina virtuale](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Connettere la macchina virtuale
 

Per ulteriori informazioni, vedere [SSH da Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>ID univoco macchine Virtuali di query

Comando (viene illustrato come utilizzare **Ubuntu**):

    sudo dmidecode | grep UUID
    
Esempio di risultati previsti:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
A causa di grande Endian bit ordinamento, l'ID di macchine Virtuali univoco effettivo in questo caso sarà:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Se la macchina virtuale è in esecuzione in Azure o locale e può aiutare i requisiti di gestione delle licenze, report o generale che potrebbe essere nelle distribuzioni IaaS Azure, è possibile utilizzare Azure ID univoco macchine Virtuali in scenari diversi. Molti fornitori di software indipendenti la creazione di applicazioni e la loro certificazione su Azure potrebbero essere necessarie per identificare un macchine Virtuali Azure in tutto il ciclo di vita e per indicare se la macchina virtuale è in esecuzione in Azure, locale o in altri provider di servizi cloud. L'identificatore di piattaforma, ad esempio consente di rilevare se il software viene correttamente concesso o la Guida per correlare tutti i dati all'origine come macchine Virtuali che faciliteranno sull'impostazione metriche appropriate per la piattaforma appropriata e per rilevare e correlare queste metriche tra altri utilizzi.
