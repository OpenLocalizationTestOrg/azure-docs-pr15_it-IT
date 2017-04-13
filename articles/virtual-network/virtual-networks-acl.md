<properties
   pageTitle="Che cos'è una rete controllo elenco di accesso?"
   description="Informazioni sulle ACL"
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

# <a name="what-is-an-endpoint-access-control-list-acls"></a>Che cos'è un endpoint elenco di controllo di accesso (ACL)?

Un endpoint controllo elenco di accesso è un miglioramento della protezione disponibile per la distribuzione di Azure. Un ACL offre la possibilità di in modo selettivo consentire o impedire il traffico verso un endpoint macchina virtuale. Questa funzionalità di filtro pacchetto offre un livello di sicurezza aggiuntivo. È possibile specificare ACL di rete per solo gli endpoint. Non è possibile specificare un ACL in una rete virtuale o subnet specifica contenute in una rete virtuale.

> [AZURE.IMPORTANT] È consigliabile utilizzare gruppi di sicurezza di rete (NSGs) anziché ACL laddove possibile. Per ulteriori informazioni su NSGs, vedere [che cos'è un gruppo di sicurezza della rete?](virtual-networks-nsg.md).

ACL possono essere configurati tramite PowerShell o il portale di gestione. Per configurare una rete ACL tramite PowerShell, vedere [Gestione ACL () per gli endpoint tramite PowerShell](virtual-networks-acl-powershell.md). Per configurare una rete ACL tramite il portale di gestione, vedere [come impostare la punti finali a una macchina virtuale](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

Uso degli ACL di rete, è possibile eseguire le operazioni seguenti:

- Consentire o impedire il traffico in arrivo in base a subnet remota intervallo indirizzi IPv4 a un endpoint input macchina virtuale in modo selettivo.

- Indirizzi IP di disattivazione degli indirizzi

- Creare più regole per endpoint macchina virtuale

- Specificare un massimo di 50 regole ACL per endpoint macchina virtuale

- Usa ordine delle regole per assicurarsi che il set di regole corretto vengono applicati a un endpoint specificato macchina virtuale (dal più basso al più alto)

- Specificare un ACL per una specifica subnet remota indirizzo IPv4.

## <a name="how-acls-work"></a>Funzionano di ACL

Un ACL è un oggetto che contiene un elenco delle regole. Quando si crea un ACL e applicarlo a un endpoint macchina virtuale, il filtraggio dei pacchetti entrerà in vigore sul nodo host della macchina virtuale. Di conseguenza, il traffico da indirizzi IP remoti viene filtrato in base del nodo di host per corrispondente ACL invece di informazioni sulle regole di una macchina virtuale. In questo modo la macchina virtuale spesa preziosi cicli di CPU sul filtraggio dei pacchetti.

Dopo avere creata una macchina virtuale, un ACL predefinito viene inserito nella posizione in cui bloccare tutto il traffico in arrivo. Tuttavia, se viene creato un endpoint per (porta 3389), quindi il valore predefinito ACL viene modificato per consentire il traffico in ingresso per l'endpoint. Traffico in ingresso da qualsiasi subnet remota consentito quindi a tale endpoint e nessuna firewall il provisioning è necessario. Tutte le altre porte sono bloccate per il traffico in ingresso a meno che non vengono creati i punti finali per le porte. Per impostazione predefinita è consentito, il traffico in uscita.

**Tabella di esempio ACL predefinita**

| **Regola n.** | **Subnet remota** | **Punto finale** | **Negare l'autorizzazione /** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Consentire      |

## <a name="permit-and-deny"></a>Consentire e negare

In modo selettivo, è possibile consentire o negare il traffico di rete per un endpoint input macchina virtuale mediante la creazione di regole che specificano "consentire" o "negare". È importante tenere presente che per impostazione predefinita, quando si crea un endpoint, tutto il traffico è consentito all'endpoint. Per questo motivo, è importante sapere come creare regole di autorizzazione/negare e collocarli nell'ordine corretto di precedenza se si desidera controllare granulare il traffico di rete che si desidera consentire a raggiungere l'endpoint macchina virtuale.

Aspetti da considerare:

1. **Nessun ACL:** Per impostazione predefinita quando viene creato un endpoint, è consentire a tutti per l'endpoint.

1. **Consentire-** Quando si aggiunge una o più "consentire" intervalli, si nega tutti gli altri intervalli per impostazione predefinita. Solo i pacchetti dall'intervallo di indirizzi IP consentito saranno in grado di comunicare con l'endpoint macchina virtuale.

1. **Negare-** Quando si aggiunge una o più "Nega" intervalli, si concede tutti gli altri intervalli di traffico per impostazione predefinita.

1. **Combinazione di consentire e negare-** È possibile utilizzare una combinazione di "consentire" e "Nega" quando si desidera ritagliare un determinato intervallo di indirizzi IP consentiti o negare.

## <a name="rules-and-rule-precedence"></a>Le regole e la precedenza delle regole

ACL di rete può configurare automaticamente gli endpoint macchina virtuale specifica. Ad esempio, è possibile specificare una rete ACL per un endpoint RDP creato in un computer virtuale quali blocchi verso il basso l'accesso per alcuni indirizzi IP. Nella tabella seguente mostra un modo per concedere l'accesso al pubblico virtuale gli indirizzi IP (VIP) di un determinato intervallo di consentire l'accesso per RDP. Tutti gli altri gli indirizzi IP remoto negato. Si seguono un ordine di regola *più basso avrà la precedenza* .

### <a name="multiple-rules"></a>Più regole

Nell'esempio seguente, se si desidera consentire l'accesso all'endpoint RDP solo da due pubblici intervalli di indirizzi IPv4 (65.0.0.0/8 e 159.0.0.0/8) per ottenere questo specificando due regole di *consentire* . In questo caso, poiché RDP è stato creato per impostazione predefinita per una macchina virtuale, può essere necessario bloccare l'accesso alla porta RDP in base a una subnet remota. Nell'esempio seguente mostra un modo per concedere l'accesso al pubblico virtuale gli indirizzi IP (VIP) di un determinato intervallo di consentire l'accesso per RDP. Tutti gli altri gli indirizzi IP remoto negato. Infatti rete ACL può essere configurato per un endpoint macchina virtuale specifica e accesso negato per impostazione predefinita.

**Esempio: più regole**

| **Regola n.** | **Subnet remota** | **Punto finale** | **Negare l'autorizzazione /** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Consentire      |
| 200    | 159.0.0.0/8   | 3389     | Consentire      |

### <a name="rule-order"></a>Ordine delle regole

Poiché per un endpoint, è possono specificare più regole, devono essere presenti una soluzione per organizzare le regole per determinare la regola che avrà la precedenza. L'ordine di regola specifica la precedenza. Rete ACL seguono un ordine di regola *più basso avrà la precedenza* . Nell'esempio seguente l'endpoint sulla porta 80 in modo selettivo è concesso l'accesso a solo determinati intervalli di indirizzi IP. A questo scopo, abbiamo una regola di negazione (regola \# 100) per gli indirizzi nell'area 175.1.0.1/24. La seconda regola viene quindi specificata con la precedenza 200 che consente l'accesso a tutti gli altri indirizzi in 175.0.0.0/8.

**Esempio: la precedenza delle regole**

| **Regola n.** | **Subnet remota** | **Punto finale** | **Negare l'autorizzazione /** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Negare        |
| 200    | 175.0.0.0/8   | 80       | Consentire      |

## <a name="network-acls-and-load-balanced-sets"></a>ACL di rete e caricare set non bilanciate

ACL di rete può essere specificato in un endpoint di bilanciamento del carico set (kg impostate). Se per un Set di kg viene specificato un ACL, ACL rete viene applicata a tutte le macchine virtuali in tale Set di kg. Ad esempio, se viene creato un Set di kg con "Porte 80" e il Set di kg contiene 3 macchine virtuali, ACL rete creata nell'endpoint "Porta 80" di una che macchina virtuale verrà automaticamente applicato alle altre macchine virtuali.

![ACL di rete e caricare set non bilanciate](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Passaggi successivi

[Come gestire ACL () per gli endpoint tramite PowerShell](virtual-networks-acl-powershell.md)
