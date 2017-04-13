<properties 
    pageTitle="Usare i privilegi di radice in macchine virtuali Linux | Microsoft Azure" 
    description="Informazioni su come utilizzare i privilegi di radice in un computer virtuale Linux in Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Utilizzo privilegi root in macchine virtuali Linux in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per impostazione predefinita, la `root` utente è disabilitati in macchine virtuali Linux in Azure. Gli utenti possono eseguire comandi con privilegi elevati tramite il `sudo` comando. Tuttavia, l'esperienza può variare a seconda di come è stato effettuato il provisioning del sistema.

1. **Chiave SSH e la password o solo** - macchina virtuale è stato effettuato il provisioning con uno dei certificati (`.CER` file) o chiave SSH, nonché una password o solo un nome utente e la password. In questo caso `sudo` richiederà la password dell'utente prima di eseguire il comando.

2. È stato effettuato il provisioning **solo chiave SSH** - macchina virtuale con un certificato (`.cer`, `.pem`, o `.pub` file) o chiave SSH, ma senza password.  In questo caso `sudo` **non verrà** richiesta la password dell'utente prima di eseguire il comando.


## <a name="ssh-key-and-password-or-password-only"></a>SSH chiave e la Password o Password solo

Accedere al computer virtuale Linux autenticazione SSH chiave o una password, quindi eseguire comandi utilizzando `sudo`, ad esempio:

    # sudo <command>
    [sudo] password for azureuser:

In questo caso all'utente verrà richiesto di immettere una password. Dopo aver immesso la password `sudo` verrà eseguito il comando con `root` privilegi.

È inoltre possibile abilitare sudo passwordless modificando la `/etc/sudoers.d/waagent` file, ad esempio:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Questa modifica consentirà passwordless sudo dall'utente "azureuser".

## <a name="ssh-key-only"></a>SSH principali solo

Accedere al computer virtuale Linux mediante l'autenticazione chiave SSH, quindi eseguire comandi utilizzando `sudo`, ad esempio:

    # sudo <command>

In questo caso all'utente verrà **non** essere richiesta una password. Dopo aver premuto `<enter>`, `sudo` verrà eseguito il comando con `root` privilegi.

 
