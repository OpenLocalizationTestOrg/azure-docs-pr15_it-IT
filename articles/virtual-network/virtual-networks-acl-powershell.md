<properties
   pageTitle="Come gestire ACL () per gli endpoint tramite PowerShell"
   description="Informazioni su come la gestione degli ACL con PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Come gestire ACL () per gli endpoint tramite PowerShell

È possibile creare e gestire rete ACL () per gli endpoint tramite PowerShell Azure o nel portale di gestione. In questo argomento sono disponibili procedure per le attività comuni ACL che è possibile completare tramite PowerShell. Per l'elenco dei cmdlet di PowerShell Azure vedere [Cmdlet per la gestione Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Per ulteriori informazioni sugli ACL, vedere [che cos'è una rete controllo elenco di accesso?](virtual-networks-acl.md). Se si desidera gestire i ACL tramite il portale di gestione, vedere [come impostare la punti finali a una macchina virtuale](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gestione degli ACL rete tramite PowerShell Azure

È possibile utilizzare i cmdlet di PowerShell Azure per creare, rimuovere e configurare (set) rete ACL (). Abbiamo incluso alcuni esempi di alcuni modi per configurare un ACL tramite PowerShell.

Per recuperare un elenco completo dei cmdlet di PowerShell ACL, è possibile utilizzare una delle operazioni seguenti:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Creare un ACL di rete con le regole che consente l'accesso da una subnet remota

Nell'esempio seguente viene illustrato un modo per creare un nuovo ACL che contiene le regole. Questo ACL viene quindi applicato a un endpoint macchina virtuale. Le regole ACL nell'esempio seguente verranno concesso l'accesso da una subnet remota. Per creare un nuovo ACL di rete con le regole di autorizzazione per una subnet remota, aprire un PowerShell ISE Azure. Copiare e incollare lo script sotto configurazione dello script con valori personalizzati e quindi eseguire lo script.

1. Creare il nuovo oggetto ACL di rete.

        $acl1 = New-AzureAclConfig

1. Impostare una regola che consente l'accesso da una subnet remota. Nell'esempio seguente, impostare regola *100* (che ha la priorità sul regola 200 e versioni successiva) per consentire l'accesso *10.0.0.0/8* subnet remota all'endpoint macchina virtuale. Sostituire i valori con i propri requisiti di configurazione. Il nome "SharePoint ACL config" deve essere sostituito con il nome che si desidera chiamare questa regola.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Per altre regole, ripetere il cmdlet, sostituire i valori con i propri requisiti di configurazione. Assicurarsi di modificare la regola numero ordine per riflettere l'ordine in cui si desidera le regole da applicare. Il numero di regola più basso avrà la priorità sul numero più alto.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Successivamente, è possibile creare un nuovo endpoint (Aggiungi) o impostare l'ACL per un endpoint esistente (Set). In questo esempio, si verrà aggiunto un nuovo endpoint macchina virtuale denominato "web" e aggiornare l'endpoint macchina virtuale con le impostazioni degli ACL.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Successivamente, combinare i cmdlet ed eseguire lo script. In questo esempio, i cmdlet combinati sarebbero simile al seguente:

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Rimuovere una regola ACL di rete che consente l'accesso da una subnet remota

Nell'esempio seguente viene illustrato un modo per rimuovere una regola ACL di rete.  Per rimuovere una regola ACL di rete con le regole di autorizzazione per una subnet remota, aprire un PowerShell ISE Azure. Copiare e incollare lo script sotto configurazione dello script con valori personalizzati e quindi eseguire lo script.

1. Primo passaggio consiste nell'eseguire l'oggetto ACL di rete per l'endpoint macchina virtuale. Sarà quindi rimuovere la regola ACL. In questo caso, viene pertanto rimosso dall'ID regola. L'ID della regola 0 verranno rimossi solo dall'ACL. Non rimuove l'oggetto ACL da endpoint macchina virtuale.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Successivamente, è necessario applicare l'oggetto rete ACL all'endpoint macchina virtuale e aggiornare la macchina virtuale.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Rimuovere un ACL di rete da un endpoint macchina virtuale

In alcuni casi è consigliabile rimuovere un oggetto di rete ACL da un endpoint macchina virtuale. A tale scopo, aprire un PowerShell ISE Azure. Copiare e incollare lo script sotto configurazione dello script con valori personalizzati e quindi eseguire lo script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è una rete controllo elenco di accesso?](virtual-networks-acl.md)
