<properties 
   pageTitle="Connettere una rete virtuale a più siti con Gateway VPN e PowerShell | Microsoft Azure"
   description="In questo articolo consentono all'utente la connessione di più siti locale locale a una rete utilizzando un Gateway VPN per il modello di distribuzione classica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Aggiungere una connessione a siti VNet con una connessione di gateway VPN esistente

> [AZURE.SELECTOR]
- [Gestione risorse - portale](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Classico - PowerShell](vpn-gateway-multi-site.md)

In questo articolo illustra l'uso di PowerShell per aggiungere connessioni a siti (S2S) a un gateway VPN che ha una connessione esistente. Questo tipo di connessione viene spesso come configurazione "più siti". 

Questo articolo si applica a reti virtuali create con il modello di distribuzione classico (noto anche come servizio di gestione). Questa procedura si applica alle configurazioni connessione dominio ExpressRoute/a siti. Per informazioni sulle connessioni dominio, vedere [connessioni dominio ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-classic.md) .

### <a name="deployment-models-and-methods"></a>Metodi e modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Viene aggiornato in questa tabella come nuovi articoli e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collegare direttamente a questa tabella.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Informazioni sulla connessione

È possibile connettersi più siti locale a una singola rete. Si tratta in particolare accattivante per la creazione di soluzioni basate su cloud ibrida. Creazione di una connessione di più siti per il gateway di Azure virtuali è molto simile alla creazione di altre connessioni al sito. Infatti, è possibile utilizzare un gateway VPN Azure esistente, come il gateway è dinamico (basato su route).

Se si dispone già di un gateway statico connessi alla rete virtuale, è possibile modificare il tipo di gateway in dinamico senza dover ricreare la rete virtuale per supportare più siti. Prima di modificare il tipo di registrazione, verificare che il gateway VPN locale supporta configurazioni VPN basate su route. 

![diagramma più siti] (./media/vpn-gateway-multi-site/multisite.png "più siti")

## <a name="points-to-consider"></a>Aspetti da considerare

**Non sarà possibile usare il portale classica di Azure per apportare modifiche a questa rete virtuale.** In questa versione, è necessario apportare modifiche al file di configurazione della rete invece che tramite il portale classica Azure. Se si apportano modifiche nel portale classica di Azure, verrà sovrascrivono le impostazioni di più siti di riferimento per la rete virtuale. 

Dovrebbero essere abbastanza chiari usando il file di configurazione della rete nel momento in cui che è stato completato la procedura più siti. Tuttavia, se si dispone di più persone che lavorano alla configurazione della rete, è necessario assicurarsi che tutti sappiano su questa limitazione. Ciò non significa che non è possibile usare il portale classica Azure tutto. È possibile utilizzare tutti gli altri simboli, ad eccezione di apportare modifiche alla configurazione a questa rete virtuale specifica.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di avere le operazioni seguenti:

- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- Hardware VPN compatibile per ogni posizione in locale. Selezionare [Sui dispositivi VPN virtuale la connettività di rete](vpn-gateway-about-vpn-devices.md) per verificare se il dispositivo che si desidera utilizzare è qualcosa che sicuramente compatibili.

- Un esternamente esposto IPv4 indirizzo IP pubblico per ogni dispositivo VPN. Non è possibile individuare l'indirizzo IP dietro al NAT. Si tratta requisito.

- È necessario installare l'ultima versione dei cmdlet di PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

- Utente che si trova la configurazione hardware VPN in modo corretto. Non sarà possibile utilizzare gli script VPN generato automaticamente dal portale classica di Azure per configurare i dispositivi VPN. Di conseguenza, che è necessario disporre di una conoscenza approfondita di come configurare il dispositivo VPN o funziona con un utente che esegue.

- Intervalli di indirizzi IP che si desidera utilizzare per la rete virtuale (se non è già stata creata). 

- Intervalli di indirizzi IP per ognuno dei siti che si saranno la connessione a rete locale. È necessario assicurarsi che per ciascuno dei siti di rete locale che si desidera effettuare la connessione non si sovrappongono gli intervalli di indirizzi IP. In caso contrario, il portale classica Azure o API REST rifiuta la configurazione in fase di caricamento. 

    Ad esempio, se si dispone di due siti di rete locale che contengono la 10.2.3.0/24 intervallo indirizzo IP e si dispone di un pacchetto con un indirizzo di destinazione 10.2.3.3, Azure non sa sito a cui si desidera inviare il pacchetto perché sono sovrapposti gli intervalli di indirizzi. Per evitare problemi di routing, Azure non consente di caricare un file di configurazione con intervalli sovrapposti.



## <a name="1-create-a-site-to-site-vpn"></a>1. creare una connessione VPN da sito

Se si dispone già di una connessione VPN da sito con un gateway routing dinamico, grande! È possibile procedere per [esportare le impostazioni di configurazione della rete virtuale](#export). In caso contrario, eseguire le operazioni seguenti:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se si dispone già di una rete virtuale da sito, ma presenta un gateway di routing statico (basato su criteri):

1. Modificare il tipo di gateway per il routing dinamico. Una rete VPN più siti richiede un gateway di routing dinamico (noto anche come route basato). Per modificare il tipo di gateway, è necessario prima di tutto eliminare il gateway esistente e quindi crearne uno nuovo. Per ulteriori informazioni, vedere [come cambiare il tipo di routing VPN per il gateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurare il nuovo gateway e creare il tunnel VPN. Per ulteriori informazioni, vedere [configurare un Gateway VPN nel portale classica di Azure](vpn-gateway-configure-vpn-gateway-mp.md). Prima di tutto, cambiare il tipo di gateway per il routing dinamico. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se non si dispone di una rete virtuale da sito:

1. Creare la rete virtuale da sito queste istruzioni: [creare una rete virtuale con una connessione VPN da sito nel portale classica di Azure](vpn-gateway-site-to-site-create.md).  

2. Configurare un gateway routing dinamico queste istruzioni: [configurare un Gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md). Assicurarsi di selezionare **il routing dinamico** per il tipo di gateway.

## <a name="export"></a>2. esportare il file di configurazione della rete 

Esportare il file di configurazione della rete. Il file esportato verrà utilizzato per configurare le nuove impostazioni di più siti. Se è necessario istruzioni su come esportare un file, vedere la sezione vedere l'articolo: [come creare un VNet tramite un file di configurazione della rete nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. aprire il file di configurazione di rete

Aprire il file di configurazione di rete che è stato scaricato nell'ultimo passaggio. Utilizzare un editor xml che si è soddisfatti. Il file avrà un aspetto simile al seguente:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. aggiungere più riferimenti a siti

Quando si aggiungono o si rimuovono informazioni di riferimento del sito, è necessario apportare modifiche alla configurazione ConnectionsToLocalNetwork/LocalNetworkSiteRef. Aggiunta di un nuovo sito locale riferimento i trigger di Azure per creare un nuovo tunnel. Nell'esempio seguente, la configurazione della rete riguarda connessione a un singolo sito. Salvare il file dopo aver apportato le modifiche.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importare il file di configurazione della rete

Importare il file di configurazione della rete. Quando si importa questo file con le modifiche, verrà aggiunto il nuovo tunnel. I tunnel utilizzerà il gateway dinamico creata in precedenza. Se è necessario istruzioni su come importare il file, vedere la sezione vedere l'articolo: [come creare un VNet tramite un file di configurazione della rete nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. scaricare tasti

Dopo avere aggiunto il nuovo tunnel, utilizzare il cmdlet di PowerShell `Get-AzureVNetGatewayKey` per ottenere le chiavi già condivise IPsec/IKE per ogni tunnel.

Per esempio:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Se si preferisce, è possibile usare anche l'API REST *Ottenere virtuale Gateway condiviso chiave di rete* per ottenere le chiavi già condivise.

## <a name="7-verify-your-connections"></a>7. verificare le connessioni

Controllare lo stato di tunnel più siti. Dopo avere scaricato i tasti per ogni tunnel, è consigliabile verificare le connessioni. Usare `Get-AzureVnetConnection` per ottenere un elenco di tunnel virtuali, come illustrato nell'esempio riportato di seguito. VNet1 è il nome della VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su gateway VPN, vedere [Su gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

