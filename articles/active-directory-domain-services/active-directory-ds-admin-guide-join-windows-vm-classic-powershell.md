<properties
    pageTitle="Servizi di dominio Azure Active Directory: Guida di amministrazione | Microsoft Azure"
    description="Partecipare a una macchina virtuale di Windows a un dominio gestito tramite PowerShell Azure e il modello di distribuzione classica."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Partecipare a una macchina virtuale di Windows Server a un dominio gestito tramite PowerShell

> [AZURE.SELECTOR]
- [Azure portale classico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../resource-manager-deployment-model.md). In questo articolo è descritta utilizzando il modello di distribuzione classica. Servizi di dominio Active Directory Azure attualmente non supporta il modello di Manager delle risorse.

Questa procedura viene illustrato come personalizzare un insieme di comandi PowerShell di Azure che creano e preconfigurare basato su Windows Azure virtuale computer utilizzando un approccio blocco predefinito. Questa procedura consentono di creare una macchina virtuale Azure basato su Windows e partecipare a un dominio gestito di servizi di dominio Active Directory Azure.

Questi passaggi un approccio selezione-in di-vuote per la creazione di gruppi di comandi PowerShell di Azure. Questo approccio può essere utile se si ha familiarità con PowerShell o si vuole conoscere i valori per specificare per la corretta configurazione. Gli utenti esperti di PowerShell è possono eseguire i comandi e sostituire i valori per le variabili (le linee che iniziano con "$").

Se già fatto, attenersi alle istruzioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare PowerShell Azure nel computer locale. Aprire un prompt dei comandi di Windows PowerShell.

## <a name="step-1-add-your-account"></a>Passaggio 1: Aggiungere l'account

1. Al prompt dei comandi PowerShell, digitare **Aggiungi AzureAccount** e premere **INVIO**.
2. Digitare l'indirizzo di posta elettronica associato all'abbonamento Azure e fare clic su **Continua**.
3. Digitare la password dell'account.
4. Fare clic su **Accedi**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Passaggio 2: Configurare l'abbonamento e l'account di archiviazione

Impostare l'abbonamento Azure e account di archiviazione eseguendo questi comandi al prompt dei comandi di Windows PowerShell. Sostituire tutti gli elementi all'interno di offerte, incluse le < e > caratteri con i nomi corretti.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

È possibile ottenere il nome dell'abbonamento corretto dalla proprietà SubscriptionName di output del comando **Get-AzureSubscription** . È possibile ottenere il nome dell'account archiviazione corretto dalla proprietà etichetta di output del comando **Get-AzureStorageAccount** dopo aver eseguito il comando **Seleziona AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Passaggio 3: Procedura dettagliata - provisioning la macchina virtuale e aggiungere al dominio gestito
Ecco il comando PowerShell Azure corrispondente per creare la macchina virtuale con le righe vuote tra ogni blocco per migliorare la leggibilità.

Specificare le informazioni relative alla macchina virtuale di Windows per eseguire il provisioning.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Per i valori di InstanceSize per D, DS o macchine virtuali di serie G, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fornire informazioni sul dominio gestito.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Specificare il nome del servizio cloud.

    $svcname="Contoso100-test"

Specificare il nome della rete virtuale a cui è necessario aggiunto la macchina virtuale. Assicurarsi che sia disponibile in questa rete virtuale Active Directory AAD del dominio gestito.

    $vnetname="MyPreviewVnet"

Selezionare l'immagine macchine Virtuali da utilizzare per effettuare il provisioning di macchina virtuale.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurare la macchina virtuale - nome del set di macchine Virtuali, istanza di dimensioni e immagine da utilizzare.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Ottenere le credenziali di amministratore locale per la macchina virtuale. Scegliere una password di amministratore locale sicuro.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Ottenere le credenziali per un account utente che appartengono al gruppo 'AAD controller di dominio amministratori' per aggiungere macchine Virtuali al dominio gestito. Non si specifica il nome di dominio, ad esempio, in questo esempio, "Roberto" è specificare il nome utente.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurare la macchina virtuale - specificare requisito di join dominio & credenziali necessarie.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Impostare una subnet per la macchina virtuale.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Facoltativo: Scegliere l'indirizzo IP del dominio. Se si imposta gli indirizzi IP del dominio gestito servizi di dominio Active Directory Azure per i server DNS per la rete virtuale, questo passaggio non è più necessario.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

A questo punto, eseguire il provisioning della macchina virtuale Windows dominio.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script per preparare una macchina virtuale di Windows e partecipare automaticamente a un dominio gestito AAD servizi di dominio
Questa serie di comandi PowerShell crea una macchina virtuale per un server line-of-business che:

- Utilizza l'immagine Data Center di Windows Server 2012 R2.
- È una macchina virtuale molto piccola.
- È il nome contoso-test.
- Viene automaticamente dominio aggiunto al dominio gestito contoso100.
- Viene aggiunta alla stessa rete virtuale come dominio gestito.

Ecco lo script di esempio completo per creare la macchina virtuale di Windows e partecipare automaticamente al dominio gestito servizi di dominio Active Directory Azure.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenuti correlati
- [Servizi di dominio Active Directory Azure - Guida introduttiva](./active-directory-ds-getting-started.md)

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](./active-directory-ds-admin-guide-administer-domain.md)
