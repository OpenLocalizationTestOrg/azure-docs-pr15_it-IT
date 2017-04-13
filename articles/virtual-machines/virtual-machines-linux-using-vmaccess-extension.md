<properties
    pageTitle="Reimpostare l'accesso in macchine virtuali Linux Azure utilizzando l'estensione VMAccess | Microsoft Azure"
    description="Reimpostare l'accesso in macchine virtuali Linux Azure utilizzando l'estensione VMAccess."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Gestire gli utenti, SSH e controllo o ripristinare dischi in macchine virtuali Linux Azure utilizzando l'estensione VMAccess

In questo articolo viene illustrato come utilizzare l'estensione di VMAcesss Azure per controllare o ripristinare un disco, reimpostare l'accesso degli utenti, gestire gli account utente o reimpostare la configurazione SSHD su Linux. Richiede l'articolo:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con `azure login`.

- la modalità di gestione di risorse Azure _deve essere_ Azure CLI `azure config mode arm`.

## <a name="quick-commands"></a>Comandi rapidi

Esistono due modi per usare VMAccess su nelle macchine virtuali Linux:

- Utilizzo di CLI Azure e i parametri necessari.
- Utilizzo dei file JSON non elaborati che VMAccess elabora e quindi sul.

Per la sezione comando rapido, occorre usare CLI Azure `azure vm reset-access` metodo. Nell'esempio seguente comando, sostituire i valori che contengono "esempio" con i valori da al proprio ambiente.

## <a name="create-a-resource-group-and-linux-vm"></a>Creare un gruppo di risorse e macchine Virtuali Linux

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Creare una macchina virtuale Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Reimpostare la password principale

Per reimpostare la password di primo livello:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Reimposta chiave SSH

Per reimpostare la chiave SSH di un utente non di primo livello:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Creare un utente

Per creare un utente:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Rimuovere un utente

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Reimposta SSHD

Per reimpostare la configurazione di SSHD:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Descrizione dettagliata

### <a name="vmaccess-defined"></a>VMAccess definiti:

Il disco le VM Linux Mostra gli errori. Si è reimpostare la password principale per il VM Linux o eliminato per la chiave privata SSH. In tal caso nel periodo del Data Center, sarà necessario per unità sono e quindi aprire KVM per accedere alla console del server. Considerare l'estensione di Azure VMAccess come tale switch KVM che consente di accedere alla console per ripristinare l'accesso a Linux o eseguire la manutenzione di livello disco.

Per la procedura dettagliata, occorre usare il formato esteso della VMAccess, che utilizza file JSON non elaborati.  Questi file VMAccess JSON anche dai modelli Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Utilizzo di VMAccess per esaminare o ripristinare il disco di una VM Linux

Utilizzando VMAccess è possibile effettuare una fsck eseguire sul disco nelle VM Linux.  È anche possibile eseguire una verifica del disco e il ripristino del disco utilizzando un VMAccess.

Per controllare e quindi ripristinare il disco usare questo script VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Utilizzo di VMAccess per reimpostare l'accesso degli utenti a Linux

Se si è più possibile accedere alla radice nelle VM Linux, è possibile avviare uno script VMAccess per reimpostare la password principale.

Per reimpostare la password di root, usare questo script VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Per reimpostare la chiave SSH di un utente non radice, usare questo script VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Utilizzo di VMAccess per gestire gli account utente su Linux

VMAccess è uno script Python che può essere usato per gestire gli utenti le VM Linux senza accesso e utilizzo sudo oppure con l'account principale.

Per creare un utente, usare questo script VMAccess:

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Per eliminare un utente, usare questo script VMAccess:

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Utilizzo di VMAccess per reimpostare la configurazione di SSHD

Se si apportano modifiche alla configurazione Linux macchine virtuali SSHD e chiuderà la connessione SSH prima di verificare le modifiche, potrebbe non essere da SSH'ing di nuovo in.  VMAccess può essere utilizzato per ripristinare la configurazione di SSHD una configurazione corretta senza accedere su SSH.

Per reimpostare la configurazione di SSHD utilizzare questo script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Eseguire lo script VMAccess con:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Passaggi successivi

Aggiornamento Linux utilizzando le estensioni VMAccess Azure è un metodo per apportare modifiche in un VM Linux in esecuzione.  È anche possibile utilizzare strumenti come cloud-inizializzazione e modelli di Azure per modificare le VM Linux all'avvio.

[Informazioni sulle funzionalità e le estensioni macchina virtuale](virtual-machines-linux-extensions-features.md)

[Creazione di modelli di gestione risorse Azure con estensioni Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Per personalizzare una VM Linux durante la creazione di usare cloud-inizializzazione](virtual-machines-linux-using-cloud-init.md)
