<properties
   pageTitle="VIP multipli per un servizio cloud"
   description="Panoramica di multiVIP e su come impostare VIP più di un servizio cloud"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurare VIP più di un servizio cloud

È possibile accedere a servizi cloud Azure su Internet tramite un indirizzo IP fornito da Azure. L'indirizzo IP pubblico viene definito un VIP (virtual IP) perché è collegato al sistema di bilanciamento del carico Azure e non la macchine () istanze all'interno del servizio cloud. È possibile accedere a qualsiasi istanza macchine Virtuali all'interno di un servizio cloud utilizzando un singolo VIP.

Esistono tuttavia scenari in cui potrebbe essere necessario più VIP come voce di scegliere lo stesso servizio cloud. Ad esempio, il servizio cloud può ospitare più siti Web che richiedono connettività SSL tramite la porta predefinita 443, come ogni sito è ospitato per un cliente diverso o tenant. In questo scenario, è necessario disporre di un indirizzo IP esposto a pubblico diverso per ogni sito Web. L'illustrazione seguente mostra un tipico web multi-tenant hosting con la necessità di più certificati SSL sulla stessa porta pubblico.

![Scenario con più VIP SSL](./media/load-balancer-multivip/Figure1.png)

Nell'esempio precedente, tutti i VIP utilizzano la stessa porta pubblica (443) e il traffico viene reindirizzato a uno o più bilanciato macchine virtuali in una porta privata univoca per l'indirizzo IP interno del servizio cloud hosting tutti i siti Web.

>[AZURE.NOTE] Un'altra situazione richiedere l'utilizzo di più VIP ospita più listener di gruppo di disponibilità di SQL AlwaysOn sullo stesso set di macchine virtuali.

VIP sono dinamici per impostazione predefinita, il che significa che l'indirizzo IP effettivo assegnato al servizio cloud può cambiare nel tempo. Per evitare che accada, è possibile prenotare un indirizzo VIP del servizio. Per ulteriori informazioni sulle VIP riservato, vedere [IP pubblico riservato](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Per informazioni sui prezzi in VIP e indirizzi IP riservato, vedere [prezzi indirizzo IP](https://azure.microsoft.com/pricing/details/ip-addresses/) .

È possibile usare PowerShell per verificare VIP utilizzato per i servizi cloud, nonché aggiungere e rimuovere VIP, associare un indirizzo VIP a un endpoint e configurare il bilanciamento del carico in uno specifico VIP.

## <a name="limitations"></a>Limitazioni

In questo momento funzionalità Multi VIP è limitata a scenari seguenti:

- **Solo IaaS**. È possibile abilitare più VIP solo per i servizi cloud che contengono macchine virtuali. Non è possibile utilizzare più VIP in scenari PaaS con istanze del ruolo.
- **Solo PowerShell**. È possibile gestire più VIP solo tramite PowerShell.

Queste limitazioni sono temporanee e possono cambiare in qualsiasi momento. Assicurarsi di rivedere la pagina per verificare le modifiche future.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Come aggiungere un VIP a un servizio cloud

Per aggiungere un indirizzo VIP del servizio, eseguire il comando PowerShell seguente:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Questo comando consente di visualizzare un risultato simile all'esempio seguente:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Come rimuovere un VIP da un servizio cloud

Per rimuovere l'indirizzo VIP aggiunto al servizio nell'esempio precedente, eseguire il comando PowerShell seguente:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] È possibile rimuovere un VIP solo se non dispone di alcun endpoint ad esso associati.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Come recuperare le informazioni VIP da un servizio Cloud

Per recuperare VIP associato a un servizio cloud, eseguire il seguente script di PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Lo script consente di visualizzare un risultato simile all'esempio seguente:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

In questo esempio, il servizio cloud ha 3 VIP:

- **Vip1** è l'indirizzo VIP predefinito, si è certi che perché il valore di IsDnsProgrammedName è impostato su true.
- **Vip2** e **Vip3** non vengono utilizzati come non dispongono di indirizzi IP. Solo essere utilizzati se si associa un endpoint all'indirizzo VIP.

>[AZURE.NOTE] L'abbonamento verrà addebitato per VIP aggiuntivi solo una volta siano associate a un punto finale. Per ulteriori informazioni sui prezzi, vedere [prezzi indirizzo IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Come associare un indirizzo VIP a un endpoint

Per associare un indirizzo VIP in un servizio cloud a un endpoint, eseguire il comando PowerShell seguente:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

Il comando crea un endpoint collegato all'indirizzo VIP invitato *Vip2* porte *80*e collegamenti al macchina virtuale denominato *myVM1* in un servizio cloud denominato *servizio* utilizzando *TCP* sulla porta *8080*.

Per verificare la configurazione, eseguire il comando PowerShell seguente:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

L'output sarà simile alla seguente:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Come abilitare il bilanciamento del carico in uno specifico VIP

È possibile associare un singolo VIP a più macchine virtuali per scopi di bilanciamento del carico. Ad esempio, si dispone di un servizio cloud denominato *servizio*e due macchine virtuali denominate *myVM1* e *myVM2*. Il servizio cloud è VIP più uno di essi denominato *Vip2*. Se si desidera verificare che tutto il traffico alla porta *81* in *Vip2* viene bilanciato tra *myVM1* e *myVM2* nella porta *8181*, eseguire il seguente script di PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

È inoltre possibile aggiornare il bilanciamento del carico per usare un VIP diverso. Ad esempio, se si esegue il comando PowerShell riportato di seguito, si modificherà insieme a usare un VIP denominato Vip1 di bilanciamento del carico:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Passaggi successivi

[Registro analitica per bilanciamento del carico di Azure](load-balancer-monitor-log.md)

[Panoramica di bilanciamento carico esposto Internet](load-balancer-internet-overview.md)

[Guida introduttiva su Internet affiancate di bilanciamento del carico](load-balancer-get-started-internet-arm-ps.md)

[Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)

[API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx)