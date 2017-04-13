<properties
        pageTitle="Reimpostazione password Linux VM e chiave SSH CLI | Microsoft Azure"
        description="Come usare l'estensione VMAccess da Azure interfaccia riga di comando (CLI) per reimpostare una password Linux VM o chiave SSH, correggere la configurazione di SSH e controllare la coerenza del disco"
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
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Come reimpostare una password Linux VM o chiave SSH, correggere la configurazione di SSH e controllare la coerenza del disco utilizzando l'estensione VMAccess


Se non è possibile connettersi a una macchina virtuale Linux su Azure a causa di una password dimenticata, un tasto di Secure Shell (SSH) non corretti o un problema con la configurazione di SSH, utilizzare l'estensione VMAccessForLinux con CLI Azure per reimpostare la password o chiave SSH, correggere la configurazione di SSH e controllare la coerenza del disco. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Con CLI Azure utilizzare il comando **set estensione macchine virtuali di azure** dall'interfaccia della riga di comando (Bash, terminale, il prompt dei comandi) per accedere ai comandi. Eseguire **Guida azure macchine virtuali estensione impostata** per l'utilizzo di estensione dettagliate.

Con CLI Azure, è possibile eseguire le operazioni seguenti:

+ [Reimpostare la password](#pwresetcli)
+ [Reimpostare la chiave SSH](#sshkeyresetcli)
+ [Reimpostare la password e la chiave SSH](#resetbothcli)
+ [Creare un nuovo account utente sudo](#createnewsudocli)
+ [Reimpostare la configurazione di SSH](#sshconfigresetcli)
+ [Eliminare un utente](#deletecli)
+ [Visualizzare lo stato dell'estensione VMAccess](#statuscli)
+ [Controllare la coerenza dei dischi aggiunti](#checkdisk)
+ [Ripristinare dischi aggiunti nelle VM Linux](#repairdisk)


## <a name="prerequisites"></a>Prerequisiti

È necessario eseguire le operazioni seguenti:

- È necessario [installare CLI Azure](../xplat-cli-install.md) e [connettersi all'abbonamento a](../xplat-cli-connect.md) utilizzare risorse Azure associate al proprio account.
- Impostare la modalità corretta per il modello di distribuzione classica digitando quanto segue al prompt dei comandi:
        
        azure config mode asm
        
- Fare in una nuova password o un insieme di chiavi SSH, se si desidera ripristinare uno. Non è necessario questi se si desidera ripristinare la configurazione di SSH.


## <a name="pwresetcli"></a>Reimpostare la password

1. Creare un file denominato PrivateConf.json con le righe. Sostituire le parentesi quadre e & #60; segnaposto & #62; valori con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Eseguire questo comando, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Reimpostare la chiave SSH

1. Creare un file denominato PrivateConf.json con il contenuto. Sostituire le parentesi quadre e & #60; segnaposto & #62; valori con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Eseguire questo comando, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Reimpostare la password e la chiave SSH

1. Creare un file denominato PrivateConf.json con il contenuto. Sostituire le parentesi quadre e & #60; segnaposto & #62; valori con le informazioni personalizzate.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Eseguire questo comando, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Creare un nuovo account utente sudo

Se si dimentica il proprio nome utente, è possibile utilizzare VMAccess per crearne uno nuovo con l'autorizzazione sudo. In questo caso, il nome utente e la password esistente non verrà modificati.

Per creare un nuovo utente sudo con accesso tramite password, utilizzare lo script di [reimpostare la password](#pwresetcli) e specificare il nuovo nome utente.

Per creare un nuovo utente sudo con l'accesso alla chiave SSH, utilizzare lo script di [reimpostare la chiave SSH](#sshkeyresetcli) e specificare il nuovo nome utente.

È anche possibile usare [reimpostare la password e la chiave SSH](#resetbothcli) per creare un nuovo utente con password e l'accesso alla chiave SSH.

## <a name="sshconfigresetcli"></a>Reimpostare la configurazione di SSH

Se la configurazione di SSH in uno stato indesiderato, è anche potrebbero perdere l'accesso per la macchina virtuale. È possibile utilizzare l'estensione VMAccess per reimpostare la configurazione allo stato predefinito. A tale scopo, è sufficiente impostare la chiave "reset_ssh" a "True". L'estensione verrà riavviare il server SSH, aprire la porta SSH le macchine Virtuali e ripristinare la configurazione di SSH valori predefiniti. L'account utente (nome, password o chiavi SSH) non essere modificata.

> [AZURE.NOTE] Il file di configurazione SSH reimpostato si trova in /etc/ssh/sshd_config.

1. Creare un file denominato PrivateConf.json con il contenuto.

        {
        "reset_ssh":"True"
        }

2. Eseguire questo comando, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Eliminare un utente

Se si desidera eliminare un account utente senza accesso per la macchina virtuale direttamente, è possibile usare questo script.

1. Creare un file denominato PrivateConf.json con il contenuto, sostituendo il nome utente da rimuovere per #60; usernametoremove & #62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Eseguire questo comando, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Visualizzare lo stato dell'estensione VMAccess

Per visualizzare lo stato dell'estensione VMAccess, eseguire il comando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< name = 'checkdisk' <</a>controllare la coerenza dei dischi aggiunti

Per eseguire fsck in tutti i dischi macchina virtuale Linux, sarà necessario eseguire le operazioni seguenti:

1. Creare un file denominato PublicConf.json con il contenuto. Controllo del disco richiede un valore boolean che indica se controllare dischi collegati alla macchina virtuale o meno. 

        {   
        "check_disk": "true"
        }

2. Eseguire questo comando da eseguire, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Dischi di ripristino 

Per ripristinare dischi che non vengono installati o errori di configurazione di montaggio, utilizzare l'estensione VMAccess per reimpostare la configurazione di montaggio sul computer virtuale Linux. Sostituendo il nome del disco per #60; yourdisk & #62;.

1. Creare un file denominato PublicConf.json con il contenuto. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Eseguire questo comando da eseguire, sostituendo il nome del computer virtuale per #60; macchine virtuali nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Passaggi successivi

* Se si desidera utilizzare i cmdlet di PowerShell Azure o Gestione risorse Azure modelli per reimpostare la password o chiave SSH, correggere configurazione SSH e controllare la coerenza del disco, vedere la [documentazione di estensione VMAccess su GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Utilizzare il [portale di Azure](https://portal.azure.com) per reimpostare la password o chiave SSH di una VM Linux distribuiti nel modello di distribuzione classica. Non è possibile utilizzare il portale eseguire a questo per una VM Linux distribuiti nel modello di distribuzione Manager delle risorse.

* Per ulteriori informazioni sull'utilizzo estensioni macchine Virtuali per macchine virtuali di Azure, vedere [sulle caratteristiche e le estensioni macchina virtuale](virtual-machines-linux-extensions-features.md) .
