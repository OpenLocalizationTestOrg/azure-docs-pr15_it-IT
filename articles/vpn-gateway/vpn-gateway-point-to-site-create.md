<properties
   pageTitle="Configurare una connessione di gateway VPN punto da sito a una rete virtuale Azure tramite il portale classico | Microsoft Azure"
   description="Garantire una connessione alla rete virtuale Azure mediante la creazione di una connessione di gateway VPN punto al sito."
   services="vpn-gateway"
   documentationCenter="na"
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
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurare una connessione punto a sito a un VNet tramite il portale classico

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classico: portale Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Classico: portale classica](vpn-gateway-point-to-site-create.md)

Configurazione di un punto al sito (P2S) consente di creare una connessione protetta da un computer client a una rete. Una connessione P2S risulta utile quando si desidera connettersi il VNet da una posizione remota, ad esempio da casa o una conferenza o quando è necessario solo alcuni client necessari per connettersi a una rete.

In questo articolo illustra la creazione di un VNet con una connessione punto a sito nel **modello di distribuzione classica** tramite il **portale classica**.

Punto al sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per l'uso. Una connessione VPN avviando la connessione dal computer client. Per ulteriori informazioni sulle connessioni punto al sito, vedere [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [pianificazione e progettazione](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelli di distribuzione e i metodi per le connessioni P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra le due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Flusso di lavoro di base

![Punto-a--diagramma sito] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "punto al sito")
 
Nella procedura seguente viene descritta la procedura per creare una connessione punto a sito protetta a una rete. 

La configurazione di una connessione punto a sito viene suddiviso in quattro sezioni. L'ordine in cui si configura ognuna di queste sezioni è importante. Non è possibile ignorare i passaggi o subito.

- **Sezione 1** Creare una rete virtuale e gateway VPN.
- **Sezione 2** Creare i certificati usati per l'autenticazione e caricarli.
- **Sezione 3** Esportare e installare i certificati client.
- **Sezione 4** Configurare il client VPN.

## <a name="vnetvpn"></a>Sezione 1 - creare un gateway VPN e una rete virtuale

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Creare una rete virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com/). Questa procedura utilizza il portale classico, non il portale di Azure. Attualmente, è possibile creare una connessione P2S tramite il portale di Azure.

2. Nell'angolo inferiore sinistro dello schermo, fare clic su **Nuovo**. Nel riquadro di spostamento, fare clic su **Servizi di rete**e quindi fare clic su **Rete virtuale**. Fare clic su **Crea personalizzato** per iniziare la configurazione guidata.

3. Nella pagina **Dettagli sulla rete virtuale** immettere le informazioni seguenti e quindi fare clic sulla freccia successiva in basso a destra.
    - **Nome**: nome rete virtuale. Ad esempio, 'VNet1'. Questo è il nome che è necessario fare riferimento a se si distribuiscono macchine virtuali questo VNet.
    - **Posizione**: la posizione direttamente correlata alla posizione fisica (area) in cui si desidera (macchine virtuali) a cui si trovano le risorse. Ad esempio, se si vuole macchine virtuali che si distribuiscono a questa rete virtuale per fisica trovarsi negli Stati Uniti orientali, selezionare tale posizione. Non è possibile modificare l'area associata alla rete virtuale dopo averlo creato.

4. Nella pagina **DNS e la connettività VPN** immettere le informazioni seguenti e quindi fare clic sulla freccia successiva in basso a destra.
    - **I server DNS**: immettere il nome del server DNS e l'indirizzo IP o selezionare un server DNS registrato in precedenza dal menu di scelta rapida. Questa impostazione non viene creato un server DNS. Consente di specificare i server DNS che si desidera utilizzare per la risoluzione dei nomi per la rete virtuale. Se si desidera utilizzare il servizio di risoluzione dei nomi predefiniti Azure, lasciare vuota in questa sezione.
    - **Configura punto to a Site VPN**: selezionare la casella di controllo.

5. Nella pagina **Connettività punto al sito** , specificare l'intervallo di indirizzi IP da cui i client VPN verranno visualizzato un indirizzo IP quando la connessione. Esistono alcune regole relative agli intervalli di indirizzi che è possibile specificare. È importante verificare che l'intervallo specificato non può essere sovrapposta con tutti gli intervalli che si trova nella rete locale.

6. Immettere le informazioni seguenti e quindi fare clic sulla freccia avanti.
 - **Spazio di indirizzi**: Include gli indirizzi IP iniziale e CIDR (conteggio indirizzo).
 - **Aggiungere spazio di indirizzi**: aggiungere spazio di indirizzi solo se è necessaria per la struttura di rete.

7. Nella pagina **Virtuale spazi degli indirizzi di rete** , specificare l'intervallo di indirizzi che si desidera utilizzare per la rete virtuale. Si tratta di indirizzi IP dinamici (DIP) assegnati a macchine virtuali e altre istanze di ruolo che si distribuiscono a questa rete virtuale.<br><br>È particolarmente importante selezionare un intervallo che si sovrappongono con tutti gli intervalli utilizzati per la rete locale. È necessario coordinare con l'amministratore di rete, che potrebbe essere necessario tesori un intervallo di indirizzi IP dallo spazio degli indirizzi di rete locale per l'uso della rete virtuale.

8. Immettere le informazioni seguenti e quindi fare clic sul segno di spunta per iniziare a creare la rete virtuale.
 - **Spazio di indirizzi**: aggiungere l'intervallo di indirizzi IP interno che si desidera utilizzare per la rete virtuale, tra cui IP iniziale e conteggio. È importante selezionare un intervallo che si sovrappongono con tutti gli intervalli utilizzati per la rete locale. 
 - **Aggiungi subnet**: subnet aggiuntive non sono obbligatori, ma può essere necessario creare una subnet separata per macchine virtuali contenenti DIP statico. Oppure, è consigliabile avere nelle macchine virtuali in una subnet è distinta dal istanze del ruolo.
 - **Aggiungi subnet di gateway**: subnet gateway è necessaria una connessione VPN punto al sito. Fare clic per aggiungere subnet gateway. Subnet gateway vengono usate solo per il gateway di rete virtuale.

9. Dopo aver creata la rete virtuale, si vedrà **che creata** elencata con **lo stato** nella pagina reti nel portale di classica Azure. Dopo aver creata la rete virtuale, è possibile creare il gateway routing dinamico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: Creare un gateway routing dinamico

Il tipo di gateway deve essere configurato come dinamica. Gateway routing statico non funzionano con questa caratteristica.

1. Nel portale di classica Azure, nella pagina **reti** , fare clic su virtuali creata e passare alla pagina del **Dashboard** .

2. Fare clic su **Creare Gateway**, si trova nella parte inferiore della pagina **Dashboard** . Verrà visualizzato un messaggio in cui viene chiesto **Se si vuole creare un gateway per virtuali "VNet1"**. Fare clic su **Sì** per iniziare a creare il gateway. Può richiedere circa 15 minuti per creare il gateway.

## <a name="generate"></a>Sezione 2 - generare e caricare i certificati

Certificati vengono utilizzati per autenticare i client VPN per le connessioni VPN punto al sito. È possibile usare un certificato generato da una soluzione di certificato dell'organizzazione oppure è possibile usare un certificato autofirmato. È possibile caricare un massimo di 20 certificati in Azure. Al caricamento di file. cer, Azure possa usare le informazioni contenute al suo interno per autenticare i client che è installato un certificato client. Il certificato client deve essere generato dallo stesso certificato che rappresenta il file. cer.

In questa sezione verranno eseguire le operazioni seguenti:

- Ottenere il file. cer per un certificato. Può trattarsi di un certificato autofirmato oppure è possibile utilizzare il sistema di certificato dell'organizzazione.
- Caricare il file. cer in Azure.
- Generare i certificati client.

### <a name="root"></a>Parte 1: Ottenere il file. cer per il certificato

Se si usa un sistema di certificato aziendale, ottenere il file. cer per il certificato che si desidera utilizzare. Nella [parte 3](#createclientcert)è generare i certificati client dal certificato principale.

Se non si usa una soluzione di certificato aziendale, è necessario generare un certificato autofirmato. Per la procedura di Windows 10, è possibile fare riferimento [all'utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md). L'articolo illustra l'uso makecert per generare un certificato autofirmato e quindi esportare il file. cer.

### <a name="upload"></a>Parte 2: Caricare il file. cer certificato radice al portale di classica Azure

Aggiungere un certificato attendibile Azure. Quando si aggiunge un file con codifica base 64 x. 509 (CER) in Azure, si indica Azure per considerare attendibile il certificato che rappresenta il file.

1. Nel portale di classica Azure, nella pagina **certificati** per la rete virtuale, fare clic su **Carica un certificato**.

2. Nella pagina **Carica certificato** selezionare il certificato di autenticazione CER e quindi fare clic sul segno di spunta.

### <a name="createclientcert"></a>Parte 3: Generare un certificato client

Successivamente, generare i certificati client. È possibile generare un certificato univoco uno per ogni cliente che si connetterà oppure è possibile utilizzare lo stesso certificato su più client. Il vantaggio di generare i certificati client univoco è la possibilità di revocare un certificato solo se necessario. In caso contrario, se è necessario revocare il certificato per un client che utilizzano lo stesso certificato client, sarà necessario generare e installare nuovi certificati per tutti i client che consente di eseguire l'autenticazione del certificato.

- Se si utilizza una soluzione di certificato aziendale, generare un certificato client con il formato del valore nome comuni 'name@yourdomain.com', anziché NetBIOS 'DOMINIO\nome' formattare. 

- Se si utilizza un certificato autofirmato, vedere [utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md) per generare un certificato client.

## <a name="installclientcert"></a>Sezione 3 - esportazione e installare il certificato client

Installare un certificato client in ogni computer in cui si desidera connettersi alla rete virtuale. È necessario per l'autenticazione certificato client. È possibile automatizzare l'installazione del certificato client oppure è possibile installare manualmente. La procedura seguente guidano l'utente attraverso l'esportazione e installare manualmente il certificato client.

1. Per esportare un certificato client, è possibile utilizzare *certmgr*. Pulsante destro del mouse il certificato client che si desidera esportare, fare clic su **tutte le attività**e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file *. pfx* . Assicurarsi che consente di registrare o ricordare la password (chiave) che è impostato per il certificato.
3. Copiare il file *. pfx* nel computer client. Nel computer client, fare doppio clic sul file *. pfx* per installarlo. Immettere la password quando richiesto. Non modificare il percorso di installazione.

## <a name="vpnclientconfig"></a>Sezione 4 - configurare il client VPN

Per connettersi alla rete virtuale, è anche necessario configurare un client VPN. Il client richiede un certificato client e la configurazione di client VPN corretta per la connessione. Per configurare un client VPN, eseguire i passaggi seguenti, nell'ordine.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Creare il pacchetto di configurazione di client VPN

1. Nel portale di classica Azure, nella pagina **Dashboard** per la rete virtuale, passare al menu di scelta rapida panoramica nell'angolo destro. Per un elenco di sistemi operativi supportati, vedere le connessioni [punto al sito](vpn-gateway-vpn-faq.md#point-to-site-connections) sezione delle domande frequenti Gateway VPN. Il pacchetto di client VPN contiene informazioni di configurazione per configurare il software client VPN incorporato in Windows. Il pacchetto non viene installato software aggiuntivo. Le impostazioni sono specifiche per la rete virtuale che si desidera connettersi.<br><br>Selezionare il pacchetto di download che corrisponde al sistema operativo client in cui verrà installato:
 - Per i client di versione a 32 bit, selezionare **scaricare il pacchetto di VPN Client versione a 32 bit**.
 - Per i client a 64 bit, selezionare **scaricare il pacchetto di VPN Client 64 bit**.

2. Bastano pochi minuti per creare un pacchetto di client. Una volta completato il pacchetto, è possibile scaricare il file. Il file *.exe* scaricato può essere creato in modo sicuro nel computer locale.

3. Dopo aver generato e scaricare il pacchetto di client VPN dal portale classico Azure, è possibile installare il pacchetto client nel computer client da cui si desidera connettersi alla rete virtuale. Se si intende installare il pacchetto di client VPN in più computer client, assicurarsi che anche hanno installato un certificato client.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: Installare il pacchetto di configurazione VPN sul client

1. Copiare il file di configurazione in locale nel computer in cui si desidera connettersi alla rete virtuale e fare doppio clic sul file .exe. 

2. Dopo che è installato il pacchetto, è possibile iniziare la connessione VPN. Il pacchetto di configurazione non è firmato da Microsoft. Si desidera firmare il pacchetto di utilizzo del servizio di firma dell'organizzazione oppure accedere se stessi con [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). È possibile utilizzare il pacchetto senza eseguire l'accesso. Tuttavia, se non è stato effettuato il pacchetto, viene visualizzato un avviso quando si installa il pacchetto.

3. Nel computer client, passare alle **Impostazioni di rete** e fare clic su **VPN**. Verrà visualizzata la connessione nell'elenco. Verrà visualizzato il nome della rete virtuale che si connetterà e sarà simile alla seguente: 

    ![Client VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Client VPN")


### <a name="part-3-connect-to-azure"></a>Parte 3: Connettere Azure

1. Per connettere il VNet del computer client, passare alle connessioni VPN e individuare la connessione VPN creato. È denominato lo stesso nome di rete virtuale. Fare clic su **Connetti**. Venga visualizzato un messaggio a comparsa che fa riferimento a utilizzando il certificato. In questo caso, fare clic su **Continua** per utilizzare i privilegi elevati. 

2. Nella pagina di stato di **connessione** , fare clic su **Connetti** per avviare la connessione. Se viene visualizzata una schermata di **Selezione certificato** , verificare che il certificato client con sia quello che si desidera utilizzare per la connessione. In caso contrario, utilizzare la freccia in giù per selezionare il certificato corretto e quindi fare clic su **OK**.

    ![Client VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Connessione client VPN")

3. Stabilire a questo punto la connessione.

    ![Client VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Connessione tramite VPN client 2")

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Verificare la connessione VPN

1. Per verificare che sia attiva la connessione VPN, aprire un prompt dei comandi con privilegi elevato ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto uno degli indirizzi all'interno dell'intervallo di indirizzo di connettività al sito specificato al momento della creazione del VNet. Il risultato dovrebbe essere simile alla seguente:

Esempio:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere macchine virtuali alla rete virtuale. Informazioni su [come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Se si desiderano ulteriori informazioni sulle reti virtuali, vedere la pagina [Virtuale documentazione della rete](https://azure.microsoft.com/documentation/services/virtual-network/) .
