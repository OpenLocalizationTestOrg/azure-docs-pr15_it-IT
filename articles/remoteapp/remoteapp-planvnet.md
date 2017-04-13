<properties
    pageTitle="Come pianificare la rete virtuale per una raccolta di Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come pianificare la rete virtuale per una raccolta di Azure RemoteApp."
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
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Come pianificare la rete virtuale RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

In questo documento viene descritto come configurare la rete virtuale Azure (VNET) e alla subnet per RemoteApp di Azure. Se si ha familiarità con le reti virtuali Azure, questa è una funzionalità che facilita la virtualizzazione dell'infrastruttura di rete nel cloud e consente di creare soluzioni ibrido con Azure e le risorse locali. È possibile leggere altre informazioni [di seguito](../virtual-network/virtual-networks-overview.md).

Se si desidera definire i criteri di sicurezza per il traffico (in ingresso e in uscita) della rete virtuale in cui si sta distribuendo RemoteApp di Azure, è consigliabile creare una subnet separata per Azure RemoteApp rispetto al resto delle distribuzioni nella rete virtuale Azure. Per ulteriori informazioni su come definire i criteri di sicurezza sulla propria subnet virtuali Azure, leggere [che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Tipi di raccolte di Azure RemoteApp con Azure reti virtuali

Grafico seguente mostra le due opzioni di raccolta diverso quando si desidera utilizzare una rete virtuale.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Raccolta di cloud RemoteApp Azure con VNET

 ![Azure RemoteApp - raccolta Cloud con un VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Si tratta di un insieme di Azure RemoteApp in tutte le risorse che la sessione RemoteApp host è necessario accedere sono distribuite in Azure. È possibile nello stesso VNET come VNET RemoteApp o un altro VNET in Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Raccolta di ibrido RemoteApp Azure con VNET

![Azure RemoteApp - raccolta ibrido con un VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Questo rappresenta un insieme di Azure RemoteApp in alcune delle risorse che ospita la sessione RemoteApp desidera poter accedere sono distribuiti in locale. VNET RemoteApp è collegato alla rete locale utilizzando tecnologie ibride Azure ad esempio VPN da sito o strada.


## <a name="how-the-system-works"></a>Come funziona il sistema

Dietro le quinte Azure RemoteApp distribuisce Azure macchine virtuali (con l'immagine caricata) alla subnet virtuali che si è scelto durante il provisioning. Se si è scelto per una raccolta ibrida, si tenta di risolvere il nome di dominio completo del controller di dominio che è stata immessa il provisioning del flusso di lavoro con il server DNS illustrato nella rete virtuale.  
Se si è connessi a una rete virtuale esistente, assicurarsi che per esporre le porte necessarie in gruppi di sicurezza di rete in subnet RemoteApp di Azure. 

È consigliabile che utilizzare una [subnet sufficiente per RemoteApp di Azure](remoteapp-vnetsizing.md). Il valore più grande supportato da Azure virtuali è /8 (con le definizioni di subnet CIDR). Subnet dovrebbe essere sufficiente per tutte le macchine virtuali RemoteApp di Azure durante scalabilità quando più gli utenti accedono le app. 

Ecco le operazioni che sarà necessario abilitare la rete virtuale subnet: 

2.  Consentire il traffico in uscita dalla subnet in intervallo di porte 10101 10175 per comunicare con uno dei servizi Azure RemoteApp interni.
3.  Il traffico in uscita deve essere consentito da subnet per connettersi all'archivio di Azure sulla porta 443
4.  Se si dispone di Active Directory ospitato in Azure, assicurarsi che qualsiasi macchine Virtuali all'interno della subnet virtuali per Azure RemoteApp è in grado di connettersi a tale controller di dominio. Il DNS nella rete virtuale dovrebbe essere possibile risolvere il nome di dominio completo del controller di dominio.


## <a name="virtual-network-with-forced-tunneling"></a>Rete virtuale con tunneling forzata

[Forzata tunneling](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) ora è supportata per tutte le nuove raccolte RemoteApp di Azure. Attualmente non è supportata la migrazione di un insieme esistente per il supporto tunneling forzata.  È necessario eliminare tutte le raccolte esistenti utilizzando VNET che si sta collegando a RemoteApp Azure e crearne uno nuovo per ottenere forzata tunneling attivata per le raccolte. 
