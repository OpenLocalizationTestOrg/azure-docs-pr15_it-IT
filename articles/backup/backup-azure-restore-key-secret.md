<properties
    pageTitle="Ripristinare un archivio di chiave chiave e segreto per macchine virtuali crittografate tramite Backup Azure | Microsoft Azure"
    description="Informazioni su come ripristinare chiave archivio chiave e segreto in Backup Azure tramite PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Ripristinare un archivio di chiave chiave e segreto per macchine virtuali crittografate tramite Backup Azure
In questo articolo parla sull'utilizzo di Backup di macchine Virtuali di Azure per eseguire il ripristino delle macchine virtuali di Azure crittografato, se la chiave e segreto non sono presenti nell'archivio di chiave. Questa procedura può essere utilizzata anche se si desidera mantenere una copia distinta della chiave (chiave di crittografia) e segreto (chiave di crittografia BitLocker) per la macchina virtuale ripristinata.

## <a name="pre-requisites"></a>Prerequisiti

1. **Copia di backup crittografati macchine virtuali** - macchine virtuali di Azure crittografati è stato eseguito il backup utilizzo del Backup Azure. Per informazioni dettagliate su come eseguire il backup crittografato macchine virtuali di Azure, vedere l'articolo [gestire backup e ripristino delle macchine virtuali di Azure tramite PowerShell](backup-azure-vms-automation.md) .

2. **Configurare Azure chiave archivio** : assicurarsi che i chiave archivio a cui chiavi e informazioni riservate dovranno essere ripristinati è già presente. Per informazioni dettagliate sulla gestione delle chiavi archivio, vedere l'articolo [Introduzione Azure chiave archivio](../key-vault/key-vault-get-started.md) .

## <a name="setup-recovery-services-vault"></a>Configurazione dell'archivio di servizi di recupero 
Utilizzare la procedura seguente per accedere a PowerShell e impostare contesto di archivio di ripristino dei servizi

### <a name="log-in-to-azure-powershell"></a>Accedere a PowerShell Azure 

Accedere all'account Azure mediante il seguente cmdlet

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Contesto di archivio di set di ripristino dei servizi

Dopo aver effettuato l'accesso, utilizzare il cmdlet seguente per ottenere l'elenco delle sottoscrizioni disponibili

```
PS C:\> Get-AzureRmSubscription
```

Selezionare l'abbonamento in cui sono disponibili risorse

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Impostare il contesto di archivio con archivio di servizi di recupero nel punto in cui è stato attivato backup per macchine virtuali crittografate

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Ottenere un punto di ripristino 

Selezionare contenitore nell'archivio di che rappresenta crittografato macchina virtuale Azure

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Usa il contenitore, tornare l'elemento per la macchina virtuale corrispondente

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Ottenere una matrice di punti di ripristino per l'elemento selezionato backup in rp variabile

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Ripristino crittografati macchina virtuale
Utilizzare la procedura seguente per ripristinare macchine Virtuali crittografato, la chiave e segreto.

### <a name="restore-key"></a>Ripristinare chiave

La matrice $rp riportati sopra, viene ordinata in ordine inverso di tempo con il punto di ripristino più recente indice 0. Ad esempio: $rp [0] seleziona il punto di ripristino più recente.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Dopo che questo cmdlet viene eseguito correttamente, viene generato un file blob nella cartella specificata nel computer in cui viene eseguito. Il file di archivio blob rappresenta chiave di crittografia in formato crittografato.

Ripristinare l'archivio di chiave utilizzando il seguente cmdlet chiave indietro. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Ripristinare segreto

Ripristinare dati riservati dal punto di ripristino ottenuto sopra

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
Il testo prima di vault.azure.net rappresenta nome archivio chiave originale. Il testo dopo informazioni riservate / rappresenta nome segreta. 

È possibile ottenere segreta nome e il valore dall'output del cmdlet eseguire sopra, nel caso in cui si desidera utilizzare lo stesso nome segreto. In altri casi, $secretname seguente deve essere aggiornata per utilizzare il nuovo nome segreto. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Impostare i contrassegni per il segreto, nel caso in cui devono essere ripristinato anche macchine Virtuali. Per il contrassegno DiskEncryptionKeyFileName valore deve contenere nome del segreto che si prevede di utilizzare. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valore DiskEncryptionKeyFileName è uguale al nome segreta ottenuto sopra. Valore per DiskEncryptionKeyEncryptionKeyURL può essere ottenuto dall'archivio chiave dopo il ripristino di nuovo i tasti e utilizzando il cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Impostare indietro segreta per l'archivio di chiave

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Ripristinare macchina virtuale
I cmdlet di PowerShell sopra consentono di ripristinare chiave e indietro segreta per l'archivio di chiave, se è stato eseguito il backup crittografato macchine Virtuali utilizzo del Backup macchine Virtuali di Azure. Dopo aver sul ripristino di utenti, consultare l'articolo [gestire backup e ripristino delle macchine virtuali di Azure tramite PowerShell](backup-azure-vms-automation.md) per ripristinare macchine virtuali crittografate.
