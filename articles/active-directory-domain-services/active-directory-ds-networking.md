<properties
    pageTitle="Servizi di dominio Active Directory Azure: Linee guida di rete | Microsoft Azure"
    description="Considerazioni sulla rete per servizi di dominio di Azure Active Directory"
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
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerazioni sulla rete per i servizi di dominio Active Directory di Azure

## <a name="how-to-select-an-azure-virtual-network"></a>Come selezionare una rete virtuale Azure
Le seguenti linee guida consentono di selezionare una rete virtuale da utilizzare con servizi di dominio Active Directory Azure.

### <a name="type-of-azure-virtual-network"></a>Tipo di rete virtuale Azure

- È possibile attivare servizi di dominio Active Directory di Azure in una rete virtuale Azure classica.

- Azure servizi di dominio Active Directory **non può essere abilitato in reti virtuali create con Gestione risorse di Azure**.

- Una rete virtuale basata su Gestione risorse è possibile connettersi a una rete virtuale classica in cui è abilitato Azure servizi di dominio Active Directory. In seguito, è possibile utilizzare servizi di dominio Active Directory Azure nella rete virtuale basate su Manager delle risorse. Per ulteriori informazioni, vedere la sezione [la connettività di rete](active-directory-ds-networking.md#network-connectivity) .

- **Reti virtuali internazionali**: se si prevede di utilizzare una rete virtuale esistente, verificare che si tratti di una rete virtuale internazionali.

    - Reti virtuali che utilizzano il meccanismo di gruppi di affinità legacy non è possibile usare con i servizi di dominio di Azure Active Directory.

    - Per utilizzare i servizi di dominio Active Directory di Azure, [eseguire la migrazione di reti virtuali legacy a reti virtuali internazionali](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Azure area per la rete virtuale

- I servizi di dominio Active Directory Azure dominio gestito viene distribuito nell'area di Azure stesso come la rete virtuale scegliere attivare il servizio in.

- Selezionare una rete virtuale in un'area di Azure supportata da servizi di dominio Active Directory Azure.

- Vedere la pagina [servizi Azure dall'area](https://azure.microsoft.com/regions/#services/) fonti di informazioni sulle aree di Azure in cui è disponibili Azure servizi di dominio Active Directory.


### <a name="requirements-for-the-virtual-network"></a>Requisiti per la rete virtuale

- **Prossimità per i carichi di lavoro di Azure**: selezionare la rete virtuale che ospita attualmente/conterrà macchine virtuali che devono accedere a servizi di dominio Active Directory Azure.

- **I server DNS personalizzati/porta-your-proprietari**: assicurarsi che non sono presenti server DNS personalizzato configurato per la rete virtuale.

- **Domini esistente con lo stesso nome di dominio**: assicurarsi che non si dispone di un dominio esistente con lo stesso nome di dominio disponibile in tale rete virtuale. Si supponga ad esempio, che si dispone di un dominio denominato 'contoso.com' già disponibili nella rete virtuale selezionata. In un secondo momento, si tenta di attivare un dominio gestito di servizi di dominio Active Directory Azure con lo stesso nome dominio (ad esempio 'contoso.com') in rete virtuale. Si verifica un errore quando si tenta di attivare servizi di dominio Active Directory Azure. L'errore è a causa dei conflitti di nome per il nome di dominio in rete virtuale. In questo caso, è necessario utilizzare un nome diverso per configurare il dominio gestito di servizi di dominio Active Directory Azure. In alternativa, è possibile annullare il provisioning del dominio esistente e quindi procedere per attivare servizi di dominio Active Directory Azure.

> [AZURE.WARNING] Dopo aver attivato il servizio, non è possibile spostare i servizi di dominio a una rete diversa.


## <a name="network-security-groups-and-subnet-design"></a>Gruppi di sicurezza di rete e subnet struttura
Un [Gruppo di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) contiene un elenco delle regole di controllo elenco di accesso che consentire o meno il traffico di rete per le istanze di macchine Virtuali in una rete virtuale. NSGs può essere associato subnet o singole istanze di macchine Virtuali all'interno di tale subnet. Quando un NSG è associata a una subnet, vengono applicate le regole ACL a tutte le istanze di macchine Virtuali di tale subnet. Inoltre, è possibile limitare il traffico verso un macchine Virtuali singoli ulteriormente associando un NSG direttamente a quella macchina virtuale.

![Progettazione subnet consigliati](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Procedure consigliate per la scelta di una subnet
- Distribuire i servizi di dominio di Azure Active Directory a una **subnet separata dedicata** all'interno della rete virtuale Azure.

- Si applicano NSGs alla subnet dedicata per il dominio gestito. Se è necessario applicare NSGs alla subnet dedicata, verificare che **non si bloccano le porte richieste di assistenza e gestire il dominio**.

- Non eccessivamente limitare il numero di indirizzi IP all'interno della subnet dedicata per il dominio gestito. Questa limitazione impedisce che il servizio di rendere disponibili per il dominio gestito due controller di dominio.

- **Non attivare servizi di dominio Active Directory di Azure subnet gateway** della rete virtuale.


> [AZURE.WARNING] Quando si associa un NSG con subnet servizi di dominio di Azure Active Directory è attivata, si potrebbe interferire con possibilità di Microsoft di servizio e gestire il dominio. Inoltre, non è disponibile la sincronizzazione tra il tenant di Azure Active Directory e il dominio gestito. **Il contratto di servizio non è valida per le distribuzioni in cui un NSG è stato applicato che blocca i servizi di dominio Active Directory Azure da aggiornare e la gestione del dominio.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Porte necessarie per i servizi di dominio Active Directory di Azure
Le seguenti porte sono necessari per i servizi di dominio Active Directory Azure al servizio e gestire il dominio gestito. Assicurarsi che le porte non vengano bloccate per subnet in cui è stato attivato il dominio gestito.

| Numero di porta | Scopo |
|---|---|
| 443 | Sincronizzazione con il tenant di Azure Active Directory |
| 3389 | Gestione del dominio |
| 5986 | Gestione del dominio |
| 636 | Proteggere l'accesso al proprio dominio gestito LDAP (LDAPS) |



## <a name="network-connectivity"></a>Connettività di rete
È possibile attivare un dominio gestito di servizi di dominio Active Directory Azure solo all'interno di una singola rete virtuale classica in Azure. Reti virtuali create con Gestione risorse di Azure non sono supportate.


### <a name="scenarios-for-connecting-azure-networks"></a>Scenari per la connessione di reti Azure
Connettere Azure reti virtuali per usare il dominio gestito in uno dei seguenti scenari di distribuzione:

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Usare il dominio gestito in più di una rete virtuale classica Azure
È possibile connettersi ad altre reti virtuale classiche Azure a Azure classica rete virtuale in cui è stata attivata servizi di dominio Active Directory Azure. La connessione VPN consente di usare il dominio gestito con i carichi di lavoro distribuite in altre reti virtuale.

![Connettività di rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Usare il dominio gestito in una rete virtuale basata su Manager delle risorse
È possibile connettersi una rete virtuale basata su Manager delle risorse per la rete virtuale classica Azure in cui è stata attivata servizi di dominio Active Directory Azure. Questa connessione consente di usare il dominio gestito con i carichi di lavoro distribuiti nella rete virtuale basate su Manager delle risorse.

![Manager delle risorse per la connettività di rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Opzioni di connessione di rete

- **VNet-VNet delle connessioni mediante le connessioni VPN da sito**: connessione di rete virtuale a un'altra rete virtuale (VNet-VNet) è simile alla connessione di rete virtuale in un punto del sito locale. Entrambi i tipi di connettività per utilizzano un gateway VPN per fornire un tunnel protetto tramite IPsec/IKE.

    ![Connettività di rete virtuale tramite VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Ulteriori informazioni - connettersi reti virtuali mediante gateway VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **VNet-VNet delle connessioni mediante virtuali peering**: virtuali peering è un meccanismo che connette due reti virtuale nella stessa regione tramite la rete dorsale Azure. Una volta peered, le due reti virtuale vengono visualizzati come uno per tutti gli scopi di integrazione applicativa. Se vengono comunque gestiti come risorse distinte, ma macchine virtuali in queste reti virtuali possano comunicare con loro direttamente tramite indirizzi IP privati.

    ![Connettività di rete virtuale utilizzando peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Ulteriori informazioni - virtuale di rete peering](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Contenuti correlati

- [Azure virtuali peering](../virtual-network/virtual-network-peering-overview.md)

- [Configurare una connessione VNet-VNet per il modello di distribuzione classica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Gruppi di sicurezza di rete Azure](../virtual-network/virtual-networks-nsg.md)
