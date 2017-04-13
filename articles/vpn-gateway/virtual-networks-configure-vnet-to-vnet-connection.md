<properties
   pageTitle="Configurare una connessione VNet-VNet per il modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come connettersi Azure reti virtuali collaborare usando il portale classico Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurare una connessione VNet-VNet per il modello di distribuzione classica

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Classico: portale classica](virtual-networks-configure-vnet-to-vnet-connection.md)



In questo articolo sono illustrati i passaggi per creare e connettere reti virtuali collaborare usando il modello di distribuzione classico (noto anche come servizio di gestione). La procedura seguente usa il portale classico Azure per creare i VNets e i gateway e PowerShell per configurare la connessione VNet a VNet. È possibile configurare la connessione nel portale.

![VNet al diagramma di integrazione applicativa VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelli di distribuzione e i metodi per le connessioni VNet-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Nella tabella seguente mostra i metodi per le configurazioni VNet a VNet e modelli di distribuzione attualmente disponibile. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Informazioni sulle connessioni VNet-VNet

La connessione di rete virtuale a un'altra rete virtuale (VNet-VNet) è simile alla connessione di rete virtuale in un punto del sito locale. Entrambi i tipi di connettività per utilizzano un gateway VPN per fornire un tunnel protetto tramite IPsec/IKE. 

VNets che ci si connette possono essere diversi abbonamenti e aree geografiche diverse. È possibile combinare VNet per la comunicazione VNet con le configurazioni più siti. Consente di stabilire topologie di rete che combinano la connettività tra locale con la connettività di rete tra virtuale.


### <a name="why-connect-virtual-networks"></a>Perché la connessione reti virtuali?

È consigliabile connettersi reti virtuali per i motivi seguenti:

- **Area cross geografico-ridondanza e geografico presenza**
    - È possibile impostare il proprio geografico replica o la sincronizzazione con la connettività protetta senza superando il endpoint esposto a Internet.
    - Con bilanciamento del carico di Azure e Microsoft o grazie alla tecnologia cluster di terze parti, è possibile impostare disponibilità carico di lavoro con la ridondanza geografico in più regioni Azure. Un esempio importante consiste nel configurare SQL sempre attive con i gruppi di disponibilità diffusione in più regioni Azure.

- **Internazionali applicazioni a più livelli con limite di isolamento sicuro**
    - All'interno dell'area stessa, è possibile impostare applicazioni a più livelli con più VNets connessi insieme alla forte isolamento e protezione delle comunicazioni tra livello.

- **Cross abbonamento, le comunicazioni tra organizzazioni di Azure**
    - Se si hanno più abbonamenti Azure, è possibile connettersi carichi di lavoro da diversi abbonamenti insieme in modo sicuro tra reti virtuali.
    - Per le aziende o un provider di servizi, è possibile attivare le comunicazioni tra organizzazioni con tecnologia VPN sicura all'interno di Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>Domande frequenti su VNet-VNet per VNets classica

- Le reti virtuali possono essere negli abbonamenti stessi o in altre.

- Le reti virtuali possono essere nelle stesse o in altre Azure regioni (posizioni).

- Un servizio cloud o endpoint di bilanciamento del carico non interessano le reti virtuali, anche se sono collegati tra loro.

- Connessione tra loro più reti virtuali non sono necessarie eventuali dispositivi VPN.

- VNet-VNet supporta la connessione di reti virtuali Azure. Non supporta la connessione macchine virtuali o servizi che non vengono distribuiti a una rete virtuale cloud.

- VNet-VNet richiede gateway routing dinamico. Azure gateway routing statico non sono supportate.

- La connettività di rete virtuale può essere utilizzata contemporaneamente con più siti VPN. Esiste un massimo di 10 tunnel VPN per un gateway VPN virtuali connessione su altre reti virtuali o siti locale.

- Gli spazi di indirizzi dei siti di rete locale virtuali reti e locale devono non si sovrappongono. Spazi degli indirizzi sovrapposti impedirà la creazione di reti virtuali o il caricamento di file di configurazione netcfg l'esito negativo.

- Tunnel ridondanti tra una coppia di reti virtuali non sono supportati.

- Tutti i tunnel VPN per VNet, tra cui P2S VPN e condividono la larghezza di banda disponibile per il gateway VPN e la stessa VPN gateway tempi di attività contratto di servizio in Azure.

- Il traffico VNet-VNet attraversano dorsale Azure.


## <a name="step1"></a>Passaggio 1: pianificare gli intervalli di indirizzi IP

È importante decidere gli intervalli che verrà utilizzato per configurare le reti virtuali. Per questa configurazione, è necessario assicurarsi che nessuno degli intervalli di VNet si sovrappongono tra loro o con una delle reti locali che si connettono a.

Nella tabella seguente viene illustrato un esempio su come definire il VNets. Utilizzare gli intervalli serve solo da. Annotare gli intervalli per le reti virtuali. Queste informazioni sono necessarie per la procedura successiva.

**Impostazioni di esempio**

|Rete virtuale  |Spazio di indirizzi               |Area geografica      |Si connette al sito di rete locale|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |Stati Uniti ovest     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Giappone est  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Passaggio 2 - creare VNet1

In questo passaggio vengono create VNet1. Quando si usa uno degli esempi, assicurarsi di sostituire i valori desiderati. Se il VNet esiste già, non è necessario eseguire questo passaggio. Tuttavia, è necessario verificare che gli intervalli di indirizzi IP non si sovrappongono con gli intervalli per il secondo VNet o con qualsiasi altro VNets a cui si desidera connettersi.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com). In questo articolo è utilizzare il portale classico perché alcune delle impostazioni di configurazione necessarie non sono ancora disponibili nel portale di Azure.

2. Nell'angolo inferiore sinistro dello schermo, fare clic su **Nuovo** > **Servizi di rete** > **Virtuali** > **Creare personalizzato** per iniziare la configurazione guidata. Come si sposta attraverso la procedura guidata, aggiungere i valori specificati a ogni pagina.

### <a name="virtual-network-details"></a>Dettagli sulla rete virtuale

Nella pagina dettagli sulla rete virtuale immettere le informazioni seguenti:

  ![Dettagli sulla rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** - nome rete virtuale. Ad esempio VNet1.
  - **Posizione** : quando si crea una rete virtuale, associarlo un percorso di Azure (area). Ad esempio, se si vuole nelle macchine virtuali distribuiti in rete virtuale per fisica trovarsi negli Stati Uniti ovest, selezionare tale posizione. Non è possibile modificare il percorso associato alla rete aziendale virtuale dopo averlo creato.

### <a name="dns-servers-and-vpn-connectivity"></a>I server DNS e la connettività VPN

Nella pagina DNS e la connettività VPN immettere le informazioni seguenti e quindi fare clic sulla freccia successiva in basso a destra.

  ![I server DNS e la connettività VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **I server DNS** - immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza nell'elenco a discesa. Questa impostazione non viene creato un server DNS. Consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale. Se si desidera ottenere la risoluzione dei nomi tra le reti virtuali, è necessario configurare i server DNS, anziché utilizzare la risoluzione dei nomi fornita da Azure.
- Non selezionare le caselle di controllo per la connettività P2S o S2S. Fare clic sulla freccia in basso a destra per passare alla schermata successiva.

### <a name="virtual-network-address-spaces"></a>Spazi di indirizzi virtuali

Nella pagina virtuale spazi degli indirizzi di rete, specificare l'intervallo di indirizzi che si desidera utilizzare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) assegnati a macchine virtuali e altre istanze di ruolo che si distribuiscono a questa rete virtuale. 

Se si sta creando un VNet che sarà disponibile anche la connessione alla rete locale, è particolarmente importante selezionare un intervallo che si sovrappongono con tutti gli intervalli utilizzati per la rete locale. In questo caso, è necessario per la coordinazione con l'amministratore di rete. L'amministratore di rete potrebbe essere necessario tesori un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale per è possibile utilizzare per il VNet.

  ![Pagina spazi degli indirizzi di rete virtuale](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Spazio di indirizzi** - inclusi IP iniziale e indirizzo conteggio. Verificare che gli spazi indirizzo specificato non si sovrappongono con uno degli spazi di indirizzi che si sono nella rete locale. In questo esempio, verranno utilizzate 10.1.0.0/16 per VNet1.
  - **Aggiungi subnet** - inclusi IP iniziale e indirizzo conteggio. Subnet aggiuntive non sono obbligatorie, ma può essere necessario creare una subnet separata per macchine virtuali contenenti DIP statico. Oppure, è consigliabile avere nelle macchine virtuali in una subnet è distinta dal istanze del ruolo.
 
**Fare clic sul segno di spunta** in basso a destra della pagina e la rete virtuale viene avviata la creazione. Al termine, verrà visualizzato "Creato" elencata con lo stato nella pagina reti.

## <a name="step-3---create-vnet2"></a>Passaggio 3 - Creazione VNet2

Successivamente, ripetere i passaggi precedenti per creare un'altra VNet. Nei passaggi successivi, si è connessi due VNets. È possibile fare riferimento alle [impostazioni di esempio](#step1) nel passaggio 1. Se il VNet esiste già, non è necessario eseguire questo passaggio. Tuttavia, è necessario verificare che gli intervalli di indirizzi IP non si sovrappongono con una delle altre reti VNets o locale che si desidera connettersi.

## <a name="step-4---add-the-local-network-sites"></a>Passaggio 4: aggiungere i siti di rete locale

Quando si crea una configurazione VNet a VNet, è necessario configurare i siti di rete locale, vengono visualizzati nella pagina del portale di **Reti locali** . Azure utilizza le impostazioni specificate in ogni sito di rete locale per determinare come indirizzare il traffico tra il VNets. Determinare il nome che si desidera utilizzare per fare riferimento a ogni sito di rete locale. È consigliabile utilizzare un nome descrittivo, quando si seleziona il valore da un elenco a discesa nei passaggi successivi.

Ad esempio, VNet1 si connette a un rete locale di un sito denominato "VNet2Local". Le impostazioni per VNet2Local contengono prefissi per VNet2 e un indirizzo IP pubblico per il gateway VNet2. VNet2 si connette a un sito di rete locale è creare denominato "VNet1Local" che contiene i prefissi indirizzo per VNet1 e l'indirizzo IP pubblico per il gateway VNet1.

### <a name="localnet"></a>Aggiungere il sito di rete locale VNet1Local

1. Nell'angolo inferiore sinistro dello schermo, fare clic su **Nuovo** > **Servizi di rete** > **Virtuali** > **Aggiungere rete locale**.

2. Nella pagina **specificare i dettagli di rete locale** per **nome**immettere un nome che si desidera utilizzare per rappresentare la rete che si desidera connettersi. In questo esempio, è possibile utilizzare "VNet1Local" per fare riferimento a intervalli di indirizzi IP e al gateway per VNet1.

3. Per **indirizzo IP dispositivo VPN (facoltativo)**specificare qualsiasi indirizzo IP pubblico valido. In genere, utilizzare l'indirizzo IP esterno effettivo per un dispositivo VPN. Per le configurazioni VNet-VNet, utilizzare l'indirizzo IP pubblico assegnato al gateway per il VNet. Tuttavia, dato che il gateway non ancora creato, è possibile specificare qualsiasi indirizzo IP pubblico valido come segnaposto. Non lasciare vuoto il campo, non è facoltativa per la configurazione. In un passaggio successivo, tornare in queste impostazioni e configurare gli indirizzi IP di gateway corrispondenti dopo Azure che genera l'errore. Fare clic sulla freccia per passare alla schermata successiva.

4. Nella **pagina indirizzo specifica**, immettere il IP indirizzo intervallo e indirizzo conteggio per VNet1. Che deve corrispondere esattamente all'intervallo configurato per VNet1. Azure Usa gli intervalli di indirizzi IP che specificano per indirizzare il traffico destinato VNet1. Fare clic sul segno di spunta per creare la rete locale.

### <a name="add-the-local-network-site-vnet2local"></a>Aggiungere il sito di rete locale VNet2Local

Utilizzare la procedura indicata sopra per creare il sito di rete locale "VNet2Local". È possibile fare riferimento ai valori nelle [impostazioni di esempio](#step1) nel passaggio 1, se necessario.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurare ogni VNet in modo che puntino a una rete locale

Ogni VNet deve puntare a rispettiva rete locale che si desidera indirizzare il traffico a. 

#### <a name="for-vnet1"></a>Per VNet1

1. Passare alla pagina di **configurazione** per virtuali **VNet1**. 
2. In connettività al sito, selezionare "Connetti alla rete locale" e quindi selezionare **VNet2Local** come la rete locale nell'elenco a discesa. 
3. Salvare le impostazioni.

#### <a name="for-vnet2"></a>Per VNet2

1. Passare alla pagina di **configurazione** per virtuali **VNet2**. 
2. In connettività al sito, selezionare "Connetti alla rete locale", quindi selezionare **VNet1Local** nell'elenco a discesa della rete locale. 
3. Salvare le impostazioni.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Passaggio 5 - configurare un gateway per ogni VNet

Configurare un gateway Routing dinamico per ogni rete virtuale. Questa configurazione non supporta il Routing statico gateway. Se si utilizza VNets che in precedenza sono stati configurati e che ha già gateway Routing dinamico, non è necessario eseguire questo passaggio. Se il gateway Routing statico, è necessario eliminare e ricreare come gateway Routing dinamico. Se si elimina un gateway, l'indirizzo IP pubblico assegnato viene rilasciato ed è necessario tornare indietro e riconfigurare le le reti locali e i dispositivi VPN con il nuovo indirizzo IP pubblico per il nuovo gateway.

1. Nella pagina **reti** , verificare che nella colonna stato per la rete virtuale **creato**.

2. Nella colonna **nome** fare clic sul nome della rete virtuale. In questo esempio abbiamo utilizzare "VNet1".

3. Nella pagina **Dashboard** si noti che questa VNet non è configurato ancora un gateway. Verrà visualizzato questo stato è stata modificata passare i passaggi per configurare il gateway.

4. Nella parte inferiore della pagina, fare clic su **Creare Gateway** e **Il Routing dinamico**. Quando viene chiesto di confermare che si desidera il gateway creato, fare clic su Sì.

    ![Tipo di gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Durante la creazione di gateway, si noterà grafico gateway nella pagina cambia a giallo e legge "Creazione di Gateway". In genere bastano circa 30 minuti per creare il gateway.

6. Ripetere gli stessi passaggi per VNet2. Non è necessario il primo gateway VNet per completare prima di iniziare a creare il gateway per le altre VNet.

7. Quando lo stato del gateway diventa "Connessione", l'indirizzo IP pubblico per ogni gateway è visibile nel Dashboard. Prendere nota dell'indirizzo IP corrispondente a ogni VNet, facendo attenzione a non combinarli. Questi sono gli indirizzi IP utilizzati quando si modificano gli indirizzi IP di segnaposto per il dispositivo VPN per ogni rete locale.

## <a name="step-6---edit-the-local-network"></a>Passaggio 6: modificare la rete locale

1. Nella pagina **Reti locali** , fare clic sul nome del nome di rete locale che si desidera modificare e quindi fare clic su **Modifica** nella parte inferiore della pagina. Per **indirizzo IP dispositivo VPN**, immettere l'indirizzo IP del gateway che corrisponde al VNet. Ad esempio, per VNet1Local, inserire l'indirizzo IP di gateway assegnata a VNet1. Fare clic sulla freccia nella parte inferiore della pagina.

2. Nella pagina **specificare lo spazio di indirizzi** , fare clic sul segno di spunta in basso a destra senza apportare alcuna modifica.

## <a name="step-7---create-the-vpn-connection"></a>Passaggio 7: creare una connessione VPN

Quando sono stati completati tutti i passaggi precedenti, impostare le chiavi già condivise IPsec/IKE e creare la connessione. Questa serie di passaggi utilizza PowerShell e non può essere configurata nel portale. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell di Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Assicurarsi di scaricare la versione più recente dei cmdlet di gestione dei servizi (ROSS). 

1. Aprire Windows PowerShell e accedere.

        Add-AzureAccount

2. Selezionare l'abbonamento che si trovano i VNets in.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Creare le connessioni. Negli esempi, si noti che la chiave condivisa corrisponde esattamente. La chiave condivisa sempre deve corrispondere.


    VNet1 a VNet2 connessione

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 a VNet1 connessione

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Attendere che le connessioni inizializzare. Dopo il gateway è inizializzato, il gateway è simile nell'immagine seguente.

    ![Stato del gateway - connesso](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere la [documentazione macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/) per altre informazioni.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
