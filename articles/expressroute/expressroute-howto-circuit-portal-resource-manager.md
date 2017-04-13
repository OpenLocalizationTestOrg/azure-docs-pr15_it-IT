<properties
   pageTitle="Creare e modificare un circuito ExpressRoute tramite Gestione risorse e del portale di Azure | Microsoft Azure"
   description="In questo articolo viene descritto come creare, eseguire il provisioning, verificare, aggiornare, eliminare e il deprovisioning un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Creare e modificare un circuito ExpressRoute

> [AZURE.SELECTOR]
[Portale di Azure - Gestione risorse](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestione risorse](expressroute-howto-circuit-arm.md)
[PowerShell - classico](expressroute-howto-circuit-classic.md)

In questo articolo viene descritto come creare un circuito Azure ExpressRoute tramite il portale di Azure e il modello di distribuzione di Manager delle risorse di Azure. La procedura seguente mostra anche come controllare lo stato del circuito, aggiornarla, o eliminare e il deprovisioning.

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 


## <a name="before-you-begin"></a>Prima di iniziare


- Esaminare i [Prerequisiti](expressroute-prerequisites.md) e [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- Assicurarsi di avere accesso al [portale di Azure](https://portal.azure.com).
- Verificare di disporre delle autorizzazioni necessarie per creare nuove risorse di rete. Se non si dispone delle autorizzazioni appropriate, contattare l'amministratore di account.

## <a name="create-and-provision-an-expressroute-circuit"></a>Creare ed eseguire il provisioning di un circuito ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. accedere al portale di Azure

Da un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. creare un nuovo circuito ExpressRoute

>[AZURE.IMPORTANT] Il circuito ExpressRoute per la fatturazione dal momento in cui che viene inviata una chiave di servizio. Assicurarsi di eseguire questa operazione quando il provider di servizi di integrazione applicativa è pronta per eseguire il provisioning di circuito.

1. È possibile creare un circuito ExpressRoute selezionando l'opzione per creare una nuova risorsa. Fare clic su **Nuovo** > **rete** > **ExpressRoute**, come illustrato nell'immagine seguente:

    ![Creare un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

2. Dopo aver fatto clic **ExpressRoute**, verrà visualizzato e il **circuito creare ExpressRoute** . Quando sta inserimento dei valori in questa blade, assicurarsi di specificare il livello SKU corretto e dati di misurazione.

    - **Livello** determina se uno standard di ExpressRoute o un componente aggiuntivo premium ExpressRoute è abilitato. È possibile specificare **Standard** per ottenere il SKU o **Premium** standard per il componente aggiuntivo premium.

    - **Dati di misurazione** determina il tipo di fatturazione. È possibile specificare **a consumo** per un piano dati a consumo e **illimitato** per un piano dati illimitato. Si noti che è possibile modificare il tipo di fatturazione da **a consumo** su **illimitata**, ma non è possibile modificare il tipo da **illimitato** per **a consumo**.

    ![Configurare il livello SKU e dati di misurazione](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

>[AZURE.IMPORTANT] Tenere presente che il percorso Peering indica la [posizione](expressroute-locations.md) in cui peering con Microsoft. Questo **è collegato alla proprietà "Posizione", che si intende l'area geografica in cui si trova il Provider di risorse di rete di Azure** . Sebbene non sono collegati, è consigliabile scegliere un Provider di risorse di rete distribuiti in corrispondenza Peering della posizione del circuito. 

### <a name="3-view-the-circuits-and-properties"></a>3. consente di visualizzare le proprietà e circuiti

**Visualizzare tutti i circuiti**

È possibile visualizzare tutti i circuiti creato, selezionare **tutte le risorse** dal menu a sinistra.
    
![Circuiti di visualizzazione](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visualizzare le proprietà**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Visualizzare le proprietà](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. inviare la chiave del servizio per il proprio provider di integrazione applicativa per il provisioning

In questo blade **stato Provider** fornisce informazioni sullo stato corrente di provisioning sul lato provider di servizi. **Stato circuito** fornisce lo stato sul lato Microsoft. Per ulteriori informazioni su circuito il provisioning di stati, vedere l'articolo di [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando si crea un nuovo circuito ExpressRoute, il circuito sarà nello stato seguente:

Stato provider: non viene completato il provisioning<BR>
Circuiti stato: attivata

![Avviare il processo di provisioning](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Il circuito verrà modificato in stato seguente quando il provider di servizi di integrazione applicativa è il processo di attivazione dell'utente:

Stato provider: il Provisioning<BR>
Circuiti stato: attivata

Per poter essere in grado di utilizzare un circuito ExpressRoute, deve essere nello stato seguente:

Stato provider: viene completato il provisioning<BR>
Circuiti stato: attivata


### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. periodicamente verificare lo stato e lo stato del tasto circuito

È possibile visualizzare le proprietà del circuito che è interessati selezionandolo. Controllare lo **stato di Provider** e assicurarsi che sono spostate **Provisioned** prima di continuare.


![Circuiti e provider di stato](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### <a name="6-create-your-routing-configuration"></a>6. creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo [configurazione routing circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare circuito peerings.

>[AZURE.IMPORTANT] Queste istruzioni vengono applicate solo alle circuiti creato con provider di servizi che offrono servizi di integrazione applicativa 2 layer. Se si usa un provider di servizi che offre gestito livello 3 servizi (in genere un IP VPN, ad esempio MPLS), il provider di integrazione applicativa verrà configurare e gestire il routing dell'utente.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. collegamento virtuali a un circuito ExpressRoute

Successivamente, collegare una rete virtuale il circuito ExpressRoute. Usare l'articolo [Linking reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si lavora con il modello di distribuzione di Manager delle risorse.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Ottenere lo stato di un circuito ExpressRoute

È possibile visualizzare lo stato di un circuito selezionandolo. 

![Stato di un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## <a name="modifying-an-expressroute-circuit"></a>Modifica di un circuito ExpressRoute

È possibile modificare alcune proprietà di un circuito ExpressRoute senza influenzare la connettività. Al momento, è possibile modificare le proprietà circuito ExpressRoute tramite il portale di Azure. Tuttavia, è possibile utilizzare PowerShell per modificare le proprietà di circuito. Per ulteriori informazioni, vedere la sezione [Modifica un circuito ExpressRoute tramite PowerShell](expressroute-howto-circuit-arm.md#modify).

È possibile eseguire le operazioni seguenti con senza tempi di inattività:

- Abilitare o disabilitare un componente aggiuntivo premium ExpressRoute per il circuito ExpressRoute.

- Aumentare la larghezza di banda del circuito ExpressRoute. Si noti che il downgrade la larghezza di banda di un circuito non è supportato. 

- Modificare il piano di misurazione dai dati a consumo ai dati illimitato. Si noti che modifica il piano di misurazione dai dati illimitato a consumo dati non è supportata.

-  È possibile attivare e disattivare **Consenti operazioni classica**.

Per ulteriori informazioni su limiti e limitazioni, vedere la sezione [Domande frequenti su ExpressRoute](expressroute-faqs.md).


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annullamento dell'implementazione e l'eliminazione di un circuito ExpressRoute

È possibile eliminare il circuito ExpressRoute, fare clic sull'icona **Elimina** . Tenere presente quanto segue:

- È necessario scollegare tutte le reti virtuali da circuito ExpressRoute. Se questa operazione non riesce, controllare se le reti virtuali sono collegate al circuito.

- Se lo stato provisioning con provider del servizio ExpressRoute circuito **Provisioning** o **Provisioned** è necessario collaborare con il provider di servizi per il deprovisioning circuito sul lato "i". Si continuerà a prenotare le risorse con addebito fino a quando il provider di servizi completa deprovisioning circuito e notifica Contattaci.

- Se il provider di servizi ha rimosso con deprovisioning circuito (lo stato di provisioning provider del servizio è impostato su **non viene completato il provisioning**) è possibile eliminare il circuito. Non verranno più fatturazione per il circuito

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, assicurarsi di eseguire le operazioni seguenti:

- [Creare e modificare il routing per il circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
- [Collegare il circuito ExpressRoute rete virtuale](expressroute-howto-linkvnet-arm.md)
