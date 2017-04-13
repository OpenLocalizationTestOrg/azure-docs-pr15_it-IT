<properties
   pageTitle="Configurare una rete virtuale e al Gateway per ExpressRoute nel portale di classica | Microsoft Azure"
   description="In questo articolo illustra la configurazione della rete virtuale per ExpressRoute utilizzando il modello di distribuzione classica e il portale classico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Creare una rete virtuale per ExpressRoute nel portale di classica

La procedura descritta in questo articolo consentono all'utente la configurazione di una rete virtuale e un gateway di rete virtuale per l'utilizzo con ExpressRoute utilizzando il modello di distribuzione classica e il portale classico.

Se si desidera utilizzare le istruzioni per il modello di distribuzione di Manager delle risorse, è possibile utilizzare i seguenti articoli: [creare una rete virtuale tramite PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) e [aggiungere un Gateway VPN a una Manager delle risorse VNet per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Creare un VNet e al gateway classica

La procedura seguente crea un VNet classica e un gateway di rete virtuale. Se si dispone già di un VNet classico, vedere la sezione [Configura un VNet classica esistente](#config) in questo articolo.

1. Accedere al [portale classica Azure](http://manage.windowsazure.com).

2. Nell'angolo inferiore sinistro dello schermo, fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**e quindi fare clic su **Rete virtuale**. Fare clic su **Crea personalizzato** per iniziare la configurazione guidata.

3. Nella pagina **Dettagli sulla rete virtuale** immettere quanto segue:

    - **Nome** : nome rete virtuale. Quando si distribuiscono le istanze di macchine virtuali e PaaS, in modo che non è possibile rendere il nome troppo complicato, utilizzerà il nome di rete virtuale.
    - **Posizione** , il percorso direttamente correlato alla posizione fisica (area) in cui si desidera (macchine virtuali) a cui si trovano le risorse. Ad esempio, se si vuole macchine virtuali che si distribuiscono a questa rete virtuale per fisica trovarsi negli Stati Uniti orientali, selezionare tale posizione. Non è possibile modificare l'area associata alla rete virtuale dopo averlo creato.

4. Nella pagina **DNS e la connettività VPN** immettere le informazioni seguenti e quindi fare clic sulla freccia successiva in basso a destra. 

    - **I server DNS** - immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non viene creato un server DNS. Consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale.
    - **La connettività a siti** , selezionare la casella di controllo per **configurare una connessione VPN da sito**.
    - **ExpressRoute** : selezionare la casella di controllo **Usa ExpressRoute**. Questa opzione viene visualizzata solo se è selezionata l'opzione **Configura una connessione VPN da sito**.
    - **Rete locale** , è necessario disporre di un sito di rete locale per ExpressRoute. Tuttavia, nel caso di una connessione ExpressRoute prefissi indirizzo specificati per il sito di rete locale verranno ignorati. Se, tuttavia, verranno usati prefissi indirizzo annunciati a Microsoft attraverso il circuito ExpressRoute per il routing.<BR>Se si dispone già di una rete locale creata per la connessione ExpressRoute, è possibile selezionare nell'elenco a discesa. In caso contrario selezionare **specificare una nuova rete locale**.

5. Se è selezionata l'opzione per specificare una nuova rete locale nel passaggio precedente, verrà visualizzata la pagina di **connettività al sito** . Per configurare la rete locale, immettere le informazioni seguenti e quindi fare clic sulla freccia avanti. 

    - **Nome** - il nome che si desidera chiamare locali (locale) di rete del sito.
    - **Spazio di indirizzi** , tra cui IP iniziale e CIDR (indirizzo conteggio). È possibile specificare un intervallo di indirizzi, purché non sovrapporla all'intervallo di indirizzi per la rete virtuale. In genere, questa operazione, specificare gli intervalli di indirizzi per la rete locale, ma in caso di ExpressRoute, queste impostazioni non vengono utilizzate. Tuttavia, questa impostazione è necessario per creare la rete locale quando si utilizza il portale classico.
    - **Aggiungere spazio di indirizzi** - questa impostazione non è rilevante per ExpressRoute.


6. Nella pagina **Virtuale spazi degli indirizzi di rete** , immettere le informazioni seguenti e quindi fare clic sul segno di spunta in basso a destra per configurare la rete. 

    - **Spazio di indirizzi** , inclusi l'avvio conteggio IP e l'indirizzo. Verificare che gli spazi indirizzo specificato non si sovrappongono uno qualsiasi degli spazi di indirizzo che si dispongono di rete locale.
    - **Aggiungi subnet** - inclusi IP iniziale e indirizzo conteggio. Subnet aggiuntive non sono necessarie.
    - **Aggiungi subnet di gateway** - fare clic per aggiungere alla subnet gateway. Subnet gateway vengono usate solo per il gateway virtuali ed è necessaria per la configurazione.<BR>La subnet gateway CIDR (conteggio indirizzo) per ExpressRoute deve essere /28 pollici (/ 27/26 ecc.). In questo modo per indirizzi IP sufficiente subnet per consentire la configurazione per l'uso. Nel portale classico, se è stata selezionata la casella di controllo da utilizzare ExpressRoute, il portale specifica una subnet gateway con /28.  Non è possibile regolare il conteggio di indirizzo CIDR nel portale di classica. Subnet gateway verrà visualizzato come **Gateway** nel portale classico, anche se il nome effettivo della subnet gateway creato è effettivamente **GatewaySubnet**. È possibile visualizzare questo nome tramite PowerShell o nel portale di Azure.

7. Fare clic sul segno di spunta nella parte inferiore della pagina e viene avviata la rete virtuale creare. Al termine, si vedrà **che creata** elencata con **lo stato** nella pagina **reti** nel portale di classica.

## <a name="gw"></a>Creare il gateway

1. Nella pagina **reti** , fare clic su virtuali appena creato, quindi fare clic su **Dashboard** nella parte superiore della pagina.

2. Nella parte inferiore della pagina del **Dashboard** , fare clic su **Creare Gateway** e selezionare **Il Routing dinamico**. Fare clic su **Sì** per confermare che si desidera creare un gateway.

3. Quando si inizia a creare il gateway, si noterà una possibilità di messaggio che si sa che il gateway sia stato avviato. Può richiedere fino a 45 minuti per creare il gateway.

4. Creare un collegamento a un circuito alla rete aziendale. Seguire le istruzioni vedere l'articolo [come collegare VNets a ExpressRoute circuiti](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurare un VNet classica esistente per ExpressRoute

Se si dispone già di un VNet classico, è possibile configurare connetterlo al ExpressRoute nel portale di classica. Le impostazioni sono diverso da quello di sezioni precedenti, pertanto leggere le sezioni per acquisire familiarità con le informazioni necessarie. Se si desidera creare una connessione di dominio ExpressRoute/a siti, vedere [questo articolo](expressroute-howto-coexist-classic.md) per la procedura. Sono diversi rispetto i passaggi descritti in questo articolo.
 
1. È necessario creare la rete locale prima di aggiornare le altre impostazioni di VNet. Per creare una nuova rete locale, è necessaria quando si configura ExpressRoute tramite il portale classico, fare clic su **Nuovo** **>** **Servizi di rete** **>** **Virtuali** **>** **Aggiungi rete locale**. Seguire la procedura guidata per creare la rete locale.

2. Nella finestra **Configura** per aggiornare le altre impostazioni per il VNet e associare VNet alla rete locale.

3. Dopo aver configurato le impostazioni, passare alla sezione [creare il gateway](#gw) di questo articolo per creare il gateway.


## <a name="next-steps"></a>Passaggi successivi

- Se si desidera aggiungere macchine virtuali alla rete virtuale, vedere [macchine virtuali di percorsi formativi](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Per ulteriori informazioni su ExpressRoute, vedere la [Panoramica ExpressRoute](expressroute-introduction.md).


 
