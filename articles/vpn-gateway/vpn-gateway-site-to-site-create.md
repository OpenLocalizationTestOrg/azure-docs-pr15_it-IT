<properties
   pageTitle="Creare una rete virtuale con una connessione di Gateway VPN da sito portale classica Azure | Microsoft Azure"
   description="Creare un VNet con una connessione di Gateway VPN S2S per tra locale e le configurazioni ibride utilizzando il modello di distribuzione classica."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Creare un VNet con una connessione al sito portale di Azure classica

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classico: portale classica](vpn-gateway-site-to-site-create.md)

In questo articolo illustra la creazione di una rete virtuale e una connessione di gateway da sito VPN alla rete locale utilizzando il modello di distribuzione classica e il portale classico. Le connessioni al sito possono essere usate per tra locali e ibride configurazioni.

![Diagramma del sito al sito] (./media/vpn-gateway-site-to-site-create/site2site.png "da sito")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelli di distribuzione e i metodi per le connessioni al sito

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra i metodi per le configurazioni di siti e modelli di distribuzione attualmente disponibile. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurazioni aggiuntive 

Se si vuole connettere VNets, vedere [configurare una connessione VNet-VNet per il modello di distribuzione classica](virtual-networks-configure-vnet-to-vnet-connection.md). Se si desidera aggiungere una connessione da sito a un VNet che contiene già una connessione, vedere [aggiungere una connessione S2S a VNet con una connessione di gateway VPN esistente](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione.

- Un dispositivo VPN e chi è in grado di configurarlo. Vedere [informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non ha familiarità con la configurazione del dispositivo VPN o si ha familiarità con l'indirizzo IP intervalli nella configurazione di rete le in locale, è necessario per la coordinazione con qualcuno in grado di fornire i dettagli dell'utente.

- Esternamente esposto pubblico indirizzo IP del dispositivo VPN. Non è possibile individuare l'indirizzo IP dietro al NAT.

- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Creare la rete virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com/).

2. Nell'angolo inferiore sinistro dello schermo, fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**e quindi fare clic su **Rete virtuale**. Fare clic su **Crea personalizzato** per iniziare la configurazione guidata.

3. Per creare il VNet, immettere le impostazioni di configurazione nelle pagine seguenti:

## <a name="Details"></a>Pagina dei dettagli virtuali

Immettere le informazioni seguenti:

- **Nome**: nome rete virtuale. Ad esempio *EastUSVNet*. Quando si distribuiscono le istanze di macchine virtuali e PaaS, in modo che non è possibile rendere il nome troppo complicato, utilizzerà il nome di rete virtuale.
- **Posizione**: la posizione direttamente correlata alla posizione fisica (area) in cui si desidera (macchine virtuali) a cui si trovano le risorse. Ad esempio, se si vuole macchine virtuali che si distribuiscono a questa rete virtuale per fisica trovarsi negli *Stati Uniti orientali*, selezionare tale posizione. Non è possibile modificare l'area associata alla rete virtuale dopo averlo creato.

## <a name="DNS"></a>Pagina di connettività VPN e i server DNS

Immettere le informazioni seguenti e quindi fare clic sulla freccia successiva in basso a destra.

- **I server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non viene creato un server DNS. Consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale.
- **Configurazione VPN da sito**: selezionare la casella di controllo per **configurare una connessione VPN da sito**.
- **Rete locale**: una rete locale rappresenta la posizione fisica in locale. È possibile selezionare una rete locale creato in precedenza oppure è possibile creare una nuova rete locale. Tuttavia, se si sceglie di utilizzare una rete locale creato in precedenza, passare alla pagina di configurazione di **Reti locali** e verificare che l'indirizzo IP dispositivo VPN (esposto indirizzo IPv4 pubblico) per il dispositivo VPN sia accurato.

## <a name="Connectivity"></a>Pagina di connettività al sito

Se si sta creando una nuova rete locale, verrà visualizzata la pagina di **Connettività al sito** . Se si desidera utilizzare una rete locale creato in precedenza, è possibile passare alla sezione successiva non verrà visualizzata questa pagina della procedura guidata.

Immettere le informazioni seguenti e quindi fare clic sulla freccia avanti.

-   **Nome**: il nome che si desidera chiamare locali (locale) di rete del sito.
-   **Indirizzo IP del dispositivo VPN**: l'indirizzo IPv4 pubblico esposto del dispositivo VPN locale che consente di connettersi a Azure. Non è possibile individuare il dispositivo VPN dietro al NAT.
-   **Spazio di indirizzi**: includere l'avvio di indirizzi IP e CIDR (conteggio indirizzo). Specificare l'indirizzo range(s) che si desidera inviare attraverso il gateway di rete virtuale per la posizione dell'utente locale locale. Se un indirizzo IP di destinazione rientra in intervalli che è possibile specificare, viene indirizzato attraverso il gateway di rete virtuale.
-   **Aggiungere spazio di indirizzi**: se si dispone di più intervalli di indirizzi che si desidera inviare attraverso il gateway virtuali, specificare ogni intervallo indirizzo aggiuntivo. È possibile aggiungere o rimuovere gli intervalli in un secondo momento nella pagina **Rete locale** .

## <a name="Address"></a>Pagina di spazi di indirizzi virtuali

Specificare l'intervallo di indirizzi che si desidera utilizzare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) assegnati a macchine virtuali e altre istanze di ruolo che si distribuiscono a questa rete virtuale.

È particolarmente importante selezionare un intervallo che si sovrappongono con tutti gli intervalli utilizzati per la rete locale. È necessario per la coordinazione con l'amministratore di rete. L'amministratore di rete potrebbe essere necessario tesori un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale per l'uso della rete virtuale.

Immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete.

- **Spazio di indirizzi**: includere partire IP e indirizzo conteggio. Verificare che gli spazi indirizzo specificato non si sovrappongono uno qualsiasi degli spazi di indirizzo che si sono nella rete locale.
- **Aggiungi subnet**: includere IP iniziale e indirizzo conteggio. Subnet aggiuntive non sono obbligatorie, ma può essere necessario creare una subnet separata per macchine virtuali contenenti DIP statico. Oppure, è consigliabile avere nelle macchine virtuali in una subnet è distinta dal istanze del ruolo.
- **Aggiungi subnet di gateway**: fare clic per aggiungere alla subnet gateway. Subnet gateway vengono usate solo per il gateway virtuali ed è necessaria per la configurazione.

Fare clic sul segno di spunta nella parte inferiore della pagina e viene avviata la rete virtuale creare. Al termine, si vedrà **che creata** elencata con **lo stato** nella pagina **reti** nel portale classica di Azure. Dopo aver creato il VNet, è quindi possibile configurare il gateway di rete virtuale.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurare il gateway di rete virtuale

Configurare il gateway di rete virtuale per creare una connessione protetta al sito. Vedere [configurare un gateway virtuali nel portale di classica Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la connessione, è possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere la documentazione [macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/) per altre informazioni.
