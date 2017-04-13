
<properties
    pageTitle="Elenco di porte e protocolli di URL alla proprietà consentite per Azure RemoteApp distribuito in rete virtuale cliente | Microsoft Azure"
    description="Informazioni su quali porte e protocolli di URL è necessario configurare per la comunicazione tramite RemoteApp di Azure."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Elenco delle porte e protocolli di URL per consentire l'accesso per Azure RemoteApp distribuito in cliente virtuali 

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Di seguito si applica a RemoteApp di Azure un insieme cloud o ibrido se si distribuisce in una rete virtuale (VNET). Per ulteriori informazioni sulle reti virtuali, leggere [Virtuale panoramica di rete](../virtual-network/virtual-networks-overview.md). Se è stato creato un gruppo di sicurezza di rete (NSG) limitazione del traffico a risorse di rete virtuale che si sono scelto di Azure RemoteApp, assicurarsi che di seguito sono accessibili e consentito tramite i criteri di sicurezza della rete virtuale. Per ulteriori informazioni sui gruppi di sicurezza di rete, leggere [che cos'è un gruppo di sicurezza della rete? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp subnet deve accedere a questi URL e i punti finali: 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    In uscita: TCP: 443, TCP: 10101 10175 
*    Facoltativo: UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp client deve accedere a questi URL e i punti finali: 

Dai client si intende desktop, dispositivi che gli utenti e così via, consente di connettersi alle App distribuita nella raccolta di Azure RemoteApp.

-  https://telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (le porte UDP facoltative sono disponibili per questo indirizzo) 
-  https://Login.Windows.NET  
-  https://Login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  In uscita: TCP: 443  
-  Facoltativo - UDP: 3391 