<properties
   pageTitle="Si noti 1 pensionistico famiglia di sistemi operativi guest | Microsoft Azure"
   description="Fornisce informazioni su come stabilire se si è interessati e quando si è verificato pensionistico Azure Guest OS famiglia 1"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Avviso pensionistico guest OS famiglia 1

Ritiro dei OS famiglia 1 annunciata prima di tutto il 1 giugno 2013.

**2 settembre 2014** Il sistema operativo Guest Azure (sistema operativo Guest) famiglia 1. x, è basato sul sistema operativo Windows Server 2008, ufficiale è stata ritirata. Tutti i tentativi di distribuire nuovi servizi o aggiornare i servizi esistenti utilizzando 1 famiglia avrà esito negativo con un messaggio di errore indicante che è stata ritirata Guest OS famiglia 1.

**3 novembre 2014** Supporto "Extended" per Guest OS famiglia 1 scaduta e ritirata completamente. Tutti i servizi di sempre nella famiglia 1 verranno interessati. Si possono disattivare tali servizi in qualsiasi momento. Non ci sono garanzie che il provider di servizi continuerà a essere eseguito a meno che non è manualmente aggiornarli se stessi.

Se hai altre domande, visitare i [Forum di servizi Cloud](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) oppure [contattare il supporto di Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>Sono interessati?

Servizi Cloud sono interessati se si applica una delle operazioni seguenti:

1. Si dispone di un valore di "osFamily ="1"specificato in modo esplicito nel file ServiceConfiguration. cscfg per il servizio Cloud.
2. Non è un valore per osFamily specificato in modo esplicito nel file ServiceConfiguration. cscfg per il servizio Cloud. Attualmente, verrà utilizzato il valore predefinito di "1" in questo caso.
3. Il portale classico Azure viene elencato il valore della famiglia di sistema operativo Guest come "Windows Server 2008".

Per trovare la famiglia di sistemi che dei servizi cloud in esecuzione, è possibile eseguire lo script seguente in Azure PowerShell, anche se è necessario prima [impostare Azure PowerShell](../powershell-install-configure.md) . Per informazioni dettagliate sullo script, vedere [Azure Guest OS famiglia 1 fine del ciclo di vita: giugno 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Servizi cloud hanno effetto, pensionistico OS famiglia 1 se la colonna osFamily nell'output dello script non contiene alcun dato o contiene "1".

## <a name="recommendations-if-you-are-affected"></a>Suggerimenti Se si è interessati

È consigliabile che eseguire la migrazione dei ruoli servizio Cloud a una delle famiglie di sistema operativo Guest supportati:

**Del sistema operativo Guest 4 famiglia** -Windows Server 2012 R2 *(scelta consigliata)*

1. Assicurarsi che l'applicazione utilizza SDK 2.1 o versioni successive con .NET framework 4.0, 4.5 o 4.5.1.
2. Impostare l'attributo osFamily su "4" nel file ServiceConfiguration. cscfg e ridistribuire il servizio cloud.


**Del sistema operativo Guest 3. x famiglia** -Windows Server 2012

1. Assicurarsi che l'applicazione utilizza SDK 1,8 o versioni successive con .NET framework 4.5 o 4.0.
2. Impostare l'attributo osFamily su "3" nel file ServiceConfiguration. cscfg e ridistribuire il servizio cloud.


**Del sistema operativo Guest 2. x famiglia** -Windows Server 2008 R2

1. Assicurarsi che l'applicazione utilizzi SDK 1.3 e versioni successive con .NET framework 3.5 o 4.0.
2. Impostare l'attributo osFamily su "2" nel file ServiceConfiguration. cscfg e ridistribuire il servizio cloud.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Supporto "Extended" per Guest OS famiglia 1 terminata 3 novembre 2014
Servizi cloud nella famiglia di sistemi operativi Guest 1 non sono più supportati. Migrazione disattivata famiglia 1 per evitare di interruzione del servizio.  

## <a name="next-steps"></a>Passaggi successivi
Esaminare il più recente [del sistema operativo Guest rilascia](cloud-services-guestos-update-matrix.md).
