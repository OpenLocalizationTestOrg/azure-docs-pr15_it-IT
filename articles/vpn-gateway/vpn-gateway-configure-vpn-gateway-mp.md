<properties 
   pageTitle="Configurare un Gateway VPN nel portale di classica Azure | Microsoft Azure"
   description="In questo articolo viene descritto come la configurazione della rete virtuale gateway VPN e modificare un tipo di routing VPN gateway."
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
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configurare un gateway VPN per il modello di distribuzione classica


Se si desidera creare una connessione sicura tra locale tra Azure e la posizione dell'utente locale, è necessario configurare una connessione di gateway VPN. Nel modello di distribuzione classica un gateway può essere uno dei due tipi di routing VPN: statica o dinamica. Il tipo che scelto dipende dal piano di progettazione di rete e il dispositivo VPN locale che si desidera utilizzare. 

Ad esempio, alcune opzioni di integrazione applicativa, ad esempio una connessione punto a siti, richiedono un gateway routing dinamico. Se si desidera configurare il gateway per supportare le connessioni punto da sito (P2S) e una connessione (S2S) al sito, è necessario configurare un gateway routing dinamico anche se da sito può essere configurato con uno dei due gateway il tipo di routing VPN. 

Inoltre, è necessario assicurarsi che il dispositivo che si desidera utilizzare per la connessione supporta il tipo di routing VPN che si desidera creare. Vedere [informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).


**Articolo** 

In questo articolo è destinato il modello di distribuzione classica tramite il [portale classico](https://manage.windowsazure.com) (non il portale di Azure). 

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Cenni preliminari sulla configurazione

La procedura seguente consentono la configurazione del gateway VPN nel portale di classica Azure. Questa procedura si applica a gateway per le reti virtuali che sono stati creati usando il modello di distribuzione classica. Non tutte le impostazioni di configurazione di gateway non sono attualmente disponibili nel portale di Azure. In caso affermativo, si creerà un nuovo set di istruzioni che si applicano al portale di Azure.


1. [Creare un gateway VPN per il VNet](#create-a-vpn-gateway)

1. [Raccogliere informazioni per la configurazione di dispositivi VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurare il dispositivo VPN](#configure-your-vpn-device)

1. [Verificare l'indirizzo IP del gateway VPN e il intervalli di rete locale](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Prima di iniziare

Prima di configurare il gateway, è innanzitutto necessario creare la rete virtuale. Per la procedura creare una rete virtuale per la connettività tra locale, vedere [configurare una rete virtuale con una connessione VPN da sito](vpn-gateway-site-to-site-create.md)o [Configura una rete virtuale con una connessione VPN punto al sito](vpn-gateway-point-to-site-create.md). Quindi, utilizzare la procedura seguente per configurare il gateway VPN e raccogliere le informazioni necessarie per configurare il dispositivo VPN. 

Se si dispone già di un gateway VPN e si desidera modificare il tipo di routing VPN, vedere [come cambiare il tipo di routing VPN per il gateway](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Creare un gateway VPN

1. Nel [portale classica Azure](https://manage.windowsazure.com), nella pagina **reti** , verificare che nella colonna stato per la rete virtuale **creato**.

1. Nella colonna **nome** fare clic sul nome della rete virtuale.

1. Nella pagina **Dashboard** si noti che questa VNet non è configurato ancora un gateway. Verrà visualizzato questo stato durante il lavoro i passaggi per configurare il gateway.

![Gateway non creato](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Quindi, nella parte inferiore della pagina, fare clic su **Creare Gateway**. È possibile selezionare *Il Routing statico* o *Il Routing dinamico*. Il tipo di routing VPN selezionato dipende da alcuni fattori. Ad esempio, il dispositivo VPN supporta e se è necessario supportare le connessioni al sito. Selezionare [Sui dispositivi VPN virtuale la connettività di rete](vpn-gateway-about-vpn-devices.md) per verificare il tipo di routing VPN che è necessario. Dopo aver creato il gateway, non è possibile modificare tra tipi di routing VPN gateway senza eliminare e ricreare il gateway. Quando viene chiesto di confermare che si desidera il gateway creato, fare clic su **Sì**.

![Tipo di routing VPN gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Durante la creazione di gateway, si noterà l'elemento grafico gateway nella pagina cambia a giallo e indica che *La creazione di Gateway*. Può richiedere fino a 45 minuti per creare il gateway. Attendere che il gateway è stato completo prima di poter spostare in avanti con altre impostazioni di configurazione.

![Creazione di gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Quando la *connessione*al gateway, è possibile raccogliere le informazioni che necessarie per il dispositivo VPN.

![Connessione di gateway](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Raccogliere informazioni per la configurazione di dispositivi VPN

Dopo aver creato il gateway, raccogliere informazioni per la configurazione di dispositivi VPN. Queste informazioni si trovano nella pagina **Dashboard** per la rete virtuale:

1. **Indirizzo IP del gateway-** L'indirizzo IP sono disponibili nella pagina **Dashboard** . Non sarà possibile visualizzarlo fino al termine della creazione del gateway.

1. **Chiave condiviso:** Fare clic su **Gestisci chiave** nella parte inferiore dello schermo. Fare clic sull'icona accanto al tasto per copiarlo negli Appunti, incollare e salvare la chiave. Questo pulsante funziona solo se è presente un singolo tunnel S2S VPN. Se si dispone di più tunnel VPN S2S, usare la *Guida virtuale rete condivisi chiave di Gateway* API o cmdlet di PowerShell.

![Gestire chiave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurare il dispositivo VPN

Dopo aver completato la procedura precedente, si o l'amministratore di rete necessario configurare il dispositivo VPN per creare la connessione. Per ulteriori informazioni sui dispositivi VPN, vedere [Sui dispositivi VPN virtuale la connettività di rete](vpn-gateway-about-vpn-devices.md) .

Dopo aver configurato il dispositivo VPN, è possibile visualizzare le informazioni di connessione aggiornato nella pagina Dashboard per le VNet.

È anche possibile eseguire uno dei comandi seguenti per verificare la connessione:

|                      | Cisco ASA             | Cisco ISR/ripristino automatico di sistema         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Controllare SA in modalità principale**  | Mostra crittografia isakmp sa | Mostra crittografia isakmp sa | ottenere cookie ike  | Mostra sicurezza associazione di protezione ike   |
| **Controllare SA in modalità rapida** | Mostra ipsec crittografia sa  | Mostra ipsec crittografia sa  | Guida dell'amministratore di sistema          | Mostra associazione di protezione di sicurezza ipsec |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Verificare l'indirizzo IP del gateway VPN e il intervalli di rete locale

### <a name="verify-your-vpn-gateway-ip-address"></a>Verificare l'indirizzo IP del gateway VPN

Per gateway per connettersi correttamente, l'indirizzo IP del dispositivo VPN deve essere configurato correttamente per la rete locale specificata per la configurazione di croce locale. In genere, questo viene configurato durante il processo di configurazione del sito al sito. Tuttavia, se è stata utilizzata la rete locale con un dispositivo diverso o l'indirizzo IP è stato modificato per la rete locale, modificare le impostazioni per specificare l'indirizzo IP di Gateway corretto.

1. Per verificare l'indirizzo IP del gateway, fare clic su **reti** nel riquadro sinistro del portale e quindi selezionare **Reti locali** nella parte superiore della pagina. Verrà visualizzato l'indirizzo del Gateway VPN per ogni rete locale che è stato creato. Per modificare l'indirizzo IP, selezionare la VNet e fare clic su **Modifica** nella parte inferiore della pagina.

1. Nella pagina **specificare i dettagli di rete locale** modificare l'indirizzo IP e quindi fare clic sulla freccia nella parte inferiore della pagina successiva.

1. Nella pagina **specificare lo spazio di indirizzi** , fare clic sul segno di spunta in basso a destra per salvare le impostazioni.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Verificare gli intervalli di indirizzi per le reti locali

Per il corretto flusso del traffico attraverso il gateway alla posizione locale, è necessario verificare che ogni intervallo di indirizzi IP non viene specificato. Ogni intervallo deve essere elencato nella configurazione Azure **Reti locali** . A seconda della configurazione di rete della propria ubicazione locale, può essere un'attività piuttosto grande. Il traffico di cui è associato l'indirizzo IP che contenuto negli intervalli elencati verrà inviato tramite il gateway VPN virtuali. Gli intervalli che si elenco non devono essere privati intervalli, anche se è necessario verificare che la configurazione locale può ricevere il traffico in ingresso.

Per aggiungere o modificare gli intervalli per una rete locale, utilizzare la procedura seguente.

1. Per modificare gli intervalli di indirizzi IP per una rete locale, fare clic su **reti** nel riquadro sinistro del portale e quindi selezionare **Reti locali** nella parte superiore della pagina. Nel portale, il modo più semplice per visualizzare gli intervalli che si sono elencati è nella pagina **Modifica** . Per visualizzare gli intervalli, selezionare la VNet e fare clic su **Modifica** nella parte inferiore della pagina.

1. Nella pagina **specificare i dettagli di rete locale** non apportare le modifiche. Fare clic sulla freccia nella parte inferiore della pagina successiva.

1. Nella pagina **specificare lo spazio di indirizzi** , apportare le modifiche di spazio indirizzo di rete. Fare clic sul segno di spunta per salvare la configurazione.

## <a name="how-to-view-gateway-traffic"></a>Come visualizzare il traffico di gateway

È possibile visualizzare i gateway e il traffico gateway dalla pagina virtuali **Dashboard** .

Nella pagina **Dashboard** è possibile visualizzare le operazioni seguenti:

- La quantità di dati fluisca attraverso il gateway, dati e quelli indietro.

- I nomi dei server DNS specificata per la rete virtuale.

- Connessione tra il gateway e il dispositivo VPN.

- Chiave condivisa che viene utilizzata per configurare la connessione di gateway al dispositivo VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Come modificare il tipo di routing VPN per il gateway

Poiché alcune configurazioni di connettività sono disponibili solo per alcuni tipi di routing gateway, è possibile che è necessario modificare il tipo di routing VPN di un gateway VPN esistente gateway. Ad esempio, può essere necessario aggiungere una connessione da sito già esistente che contiene un gateway statico connettività punto al sito. Le connessioni al sito richiedono un gateway dinamico. Questo errore indica configurare una connessione P2S, è necessario modificare il tipo di routing VPN gateway da statico a dinamico.

Se è necessario modificare il tipo di routing VPN un gateway, è necessario eliminare il gateway esistente e quindi creare un nuovo gateway con il nuovo tipo di routing. Non è necessario eliminare l'intera rete virtuale per modificare il tipo di routing del gateway.

Prima di modificare il tipo di routing VPN gateway, assicurarsi di verificare che il dispositivo VPN supporta il tipo di routing che si desidera utilizzare. Per scaricare nuovi esempi di configurazione routing e verificare i requisiti di dispositivo VPN, vedere [Sui dispositivi VPN virtuale la connettività di rete](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Quando si elimina un gateway VPN virtuali, vengono rilasciati VIP assegnato al gateway. Quando si ricrea il gateway, viene assegnato un nuovo VIP.

1. **Eliminare il gateway VPN esistente.**

    Nella pagina **Dashboard** per la rete virtuale, passare alla fine della pagina e fare clic su **Elimina Gateway**. Attendere che la notifica che il gateway è stato eliminato. Dopo aver ricevuto la notifica sullo schermo che il gateway è stato eliminato, è possibile creare un nuovo gateway.

1. **Creare un nuovo gateway VPN.**

    Utilizzare la procedura nella parte superiore della pagina per creare un nuovo gateway: [creare un gateway VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere macchine virtuali alla rete virtuale. Informazioni su [come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se si desidera configurare una connessione VPN punto al sito, vedere [configurare una connessione VPN punto al sito](vpn-gateway-point-to-site-create.md).

 
