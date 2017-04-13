
<properties
   pageTitle="Indicazioni Azure | modelli e procedure | Microsoft Azure"
   description="Architetture di riferimento Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Architetture di riferimento Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Il contenuto è in fase di sviluppo attivo. È utile oggi, in modo che stiamo disponibile per l'anteprima. Abbiamo valore i commenti._

Il nostro architetture di riferimento sono disposti per scenario con più architetture correlate raggruppate.
Ogni singolo architettura offre le procedure consigliate e procedure, nonché un componente eseguibile che incorpora i suggerimenti.
Molte delle architetture siano graduale; creazione di nella parte superiore di architetture precedente che dispongono di un numero minore di requisiti.

## <a name="designing-your-infrastructure-for-resiliency"></a>Progettazione di un'infrastruttura sulla resilienza

Questa serie inizia con le procedure consigliate per la configurazione ottimale macchine Virtuali e termina con cardine in una distribuzione a più aree con failover.

- [Una macchina virtuale di Windows in esecuzione su Azure](guidance-compute-single-vm.md)
- [Esecuzione di una macchina virtuale Linux su Azure](guidance-compute-single-vm-linux.md)
- [Esecuzione di più macchine virtuali per prestazioni e scalabilità disponibilità](guidance-compute-multi-vm.md)
- [Esecuzione macchine virtuali di Windows per un'architettura a più livelli](guidance-compute-n-tier-vm.md)
- [Esecuzione macchine virtuali Linux per un'architettura a più livelli](guidance-compute-n-tier-vm-linux.md)
- [Macchine virtuali di Windows in esecuzione in più aree di disponibilità](guidance-compute-multiple-datacenters.md)
- [Esecuzione di macchine virtuali Linux in più aree di disponibilità](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>La connessione di rete locale in Azure

Questa serie innanzitutto che illustra i metodi per la connessione di rete esistente in Azure. Quindi si espande per soddisfare le esigenze di disponibilità e sicurezza.

- [Implementare un'architettura di rete ibrido con Azure e VPN locale](guidance-hybrid-network-vpn.md)
- [Implementare un'architettura di rete ibrido con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Implementare un'architettura di rete ibrido disponibilità](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Protezione della rete ibrido

Questa serie vengono illustrate le procedure consolidate sulla creazione di rete Perimetrale in Azure per proteggere le connessioni provenienti dal Data Center locale e Internet.

- [L'implementazione di una rete Perimetrale tra Azure e la data center locale](guidance-iaas-ra-secure-vnet-hybrid.md)
- [L'implementazione di una rete Perimetrale tra Azure e Internet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Servizi di identità

Questa serie in cui viene illustrato come utilizzare Azure Active Directory per fornire l'autenticazione utente in Azure viene avviata. Quindi si espande per coprire scenari complessi estensione infrastruttura aggiunge in Azure e utilizzo di ADFS per la delega.

- [Implementazione di Azure Active Directory](./guidance-identity-aad.md)
- [Estensione servizi Directory Active Directory (somma) per Azure](./guidance-identity-adds-extend-domain.md)
- [Creazione di un insieme di strutture di risorse di servizi di Directory Active Directory (somma) in Azure](./guidance-identity-adds-resource-forest.md)
- [Implementazione di Active Directory Federation Services (ADFS) in Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Architettura di applicazione web scalable utilizzando PaaS Azure

Questa serie vengono forniti consigli per la creazione di applicazioni web scalable e disponibilità. 

- [Applicazione web di base](guidance-web-apps-basic.md)
- [Migliorare la scalabilità in un'applicazione web](guidance-web-apps-scalability.md)
- [Applicazione Web con disponibilità](guidance-web-apps-multi-region.md)
