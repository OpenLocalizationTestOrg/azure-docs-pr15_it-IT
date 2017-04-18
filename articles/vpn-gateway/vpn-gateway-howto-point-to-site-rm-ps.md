<properties 
   pageTitle="Configurare una connessione di gateway punto to a Site VPN alla rete virtuale utilizzando il modello di distribuzione di gestione risorse | Microsoft Azure"
   description="Garantire una connessione alla rete virtuale Azure mediante la creazione di una connessione di gateway VPN punto al sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurare una connessione punto a sito a un VNet tramite PowerShell

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classico: portale Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configurazione di un punto al sito (P2S) consente di creare una connessione protetta da un computer client a una rete. Una connessione P2S risulta utile quando si desidera connettersi il VNet da una posizione remota, ad esempio da casa o una conferenza o quando è necessario solo alcuni client necessari per connettersi a una rete. 

Punto al sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per l'uso. Una connessione VPN avviando la connessione dal computer client. Per ulteriori informazioni sulle connessioni punto al sito, vedere [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [pianificazione e progettazione](vpn-gateway-plan-design.md). 

In questo articolo illustra la creazione di un VNet con una connessione punto a sito nel modello di distribuzione Manager delle risorse tramite PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelli di distribuzione e i metodi per le connessioni P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra le due modelli di distribuzione e i metodi di distribuzione disponibili per le configurazioni P2S. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flusso di lavoro di base 

![Punto-a--diagramma sito] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "punto al sito")

In questo scenario, è necessario creare una rete virtuale con una connessione punto a sito. Le istruzioni anche consentono di generare certificati sono necessari per la configurazione. Una connessione P2S è composto da quanto segue: VNet risposte con un gateway VPN, un file CER certificato principale (chiave pubblica), un certificato client e la configurazione VPN sul client. 

Serve i valori seguenti per la configurazione. Si impostano le variabili in sezione [1](#declare) dell'articolo. Utilizzare la procedura descritta come una panoramica e utilizzare i valori senza convertirli oppure modificare in modo da rispecchiare l'ambiente. 

### <a name="example"></a>Esempi di valori

- **Nome: VNet1**
- **Spazio di indirizzi: 192.168.0.0/16** e **10.254.0.0/16**<br>In questo esempio vengono utilizzati più di uno spazio indirizzo per illustrare che questa configurazione sarà compatibili con più spazi degli indirizzi. Tuttavia, doppi spazi indirizzo non sono necessari per la configurazione.
- **Nome subnet: front-end**
    - **Intervallo di indirizzi subnet: 192.168.1.0/24**
- **Nome subnet: back-end**
    - **Intervallo di indirizzi subnet: 10.254.1.0/24**
- **Nome subnet: GatewaySubnet**<br>Il nome di Subnet *GatewaySubnet* è obbligatorio per il gateway VPN per l'uso.
    - **Intervallo di indirizzi subnet: 192.168.200.0/24** 
- **Pool di indirizzi client VPN: 172.16.201.0/24**<br>Client VPN che si connettono a VNet connessione punto a siti di ricevere un indirizzo IP dal pool di indirizzi client VPN.
- **Abbonamento:** Se si dispone di più di una sottoscrizione, verificare che si sta utilizzando quella corretta.
- **Gruppo risorse: TestRG**
- **Posizione: Stati Uniti orientali**
- **Al Server DNS: indirizzo IP** del server DNS che si desidera utilizzare per la risoluzione dei nomi.
- **Nome GW: Vnet1GW**
- **Nome IP pubblico: VNet1GWPIP**
- **VpnType: RouteBased**

## <a name="before-beginning"></a>Prima di iniziare

- Verificare di avere un abbonamento a Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
    
- Installare la versione più recente dei cmdlet di Manager delle risorse PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Quando si lavora con PowerShell per la configurazione, assicurarsi che venga eseguito come amministratore. 

## <a name="declare"></a>Parte 1 - Log e set di variabili

In questa sezione, accedere e dichiarare i valori utilizzati per la configurazione. I valori dichiarati vengono usati negli script di esempio. Modificare i valori in base al proprio ambiente. In alternativa, è possibile utilizzare i valori dichiarati e passare i passaggi come esercizio.

1. Nella console di PowerShell, accedere al proprio account Azure. Questo cmdlet richiede le credenziali di accesso per il proprio Account Azure. Dopo avere effettuato l'accesso, il download delle impostazioni dell'account, in modo che siano disponibili per Azure PowerShell.

        Login-AzureRmAccount 

2. È possibile ottenere un elenco delle sottoscrizioni Azure.

        Get-AzureRmSubscription

3. Specificare l'abbonamento a cui si desidera utilizzare. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Dichiarare le variabili che si desidera utilizzare. Utilizzare l'esempio seguente, sostituendo i valori per il proprio quando necessario.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Parte 2: configurare un VNet 

1. Creare un gruppo di risorse.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Creare le configurazioni subnet per la rete virtuale denominazione di *GatewaySubnet* *front-end*e *back-end*. Questi prefissi devono fare parte dello spazio di indirizzi VNet dichiarato in.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Creare la rete virtuale. Il server DNS specificato deve essere un server DNS che può consentire di risolvere i nomi delle risorse che si è connessi. In questo esempio è stato usato un indirizzo IP pubblico. Assicurarsi di usare i valori desiderati.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Specificare le variabili per la rete virtuale che è stato creato.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Richiedere un indirizzo IP pubblico assegnato dinamicamente. Questo indirizzo IP è necessario per il gateway per il corretto funzionamento. È possibile connettersi in seguito il gateway alla configurazione IP gateway.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>Parte 3 - certificati

I certificati sono utilizzati da Azure per autenticare i client VPN per le connessioni VPN punto al sito. Esportare dati certificato pubblico (non la chiave privata) come file con estensione cer x. 509 da un certificato generati da una soluzione di certificato aziendale o un certificato autofirmato con codifica Base 64. Quindi come importare i dati del certificato pubblico dal certificato principale in Azure. Inoltre, è necessario generare un certificato client dal certificato principale per i client. Ogni client da connettere alla rete virtuale tramite una connessione P2S deve essere installato un certificato client, generato dal certificato principale.

### <a name="cer"></a>1. ottenere il file. cer per il certificato

È necessario ottenere i dati del certificato pubblico per il certificato che si desidera utilizzare.

- Se si usa un sistema di certificato aziendale, ottenere il file. cer per il certificato che si desidera utilizzare. 

- Se non si usa una soluzione di certificato aziendale, è necessario generare un certificato autofirmato. Per la procedura di Windows 10, è possibile fare riferimento [all'utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md).


1. Per ottenere un file CER da un certificato, aprire **certmgr. msc** e individuare il certificato di autenticazione. Pulsante destro del mouse il certificato autofirmato, fare clic su **tutte le attività**e quindi fare clic su **Esporta**. Verrà visualizzata l' **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **No, esportare la chiave privata**e quindi fare clic su **Avanti**.

3. Nella pagina **Formato File di esportazione** , selezionare **Base 64 codificato x. 509 (. CER).** Scegliere **Avanti**. 

4. Sul **File da esportare**, **passare** al percorso in cui si vuole esportare il certificato. Per **Nome File**, assegnare un nome di file di certificato. Fare clic su **Avanti**.

5. Fare clic su **Fine** per esportare il certificato.

### <a name="generate"></a>2. generare il certificato client

Successivamente, generare i certificati client. È possibile generare un certificato univoco uno per ogni cliente che si connetterà oppure è possibile utilizzare lo stesso certificato su più client. Il vantaggio di generare i certificati client univoco è la possibilità di revocare un certificato solo se necessario. In caso contrario, se è necessario revocare il certificato per un client che utilizzano lo stesso certificato client, sarà necessario generare e installare nuovi certificati per tutti i client che consente di eseguire l'autenticazione del certificato. I certificati client sono installati in ogni computer client più avanti in questo esercizio.

- Se si utilizza una soluzione di certificato aziendale, generare un certificato client con il formato del valore nome comuni 'name@yourdomain.com', anziché NetBIOS 'DOMINIO\nome' formattare. 

- Se si utilizza un certificato autofirmato, vedere [utilizzo di certificati autofirmato per le configurazioni punto al sito](vpn-gateway-certificates-point-to-site.md) per generare un certificato client.

### <a name="exportclientcert"></a>3. esportare il certificato client

È necessario per l'autenticazione certificato client. Dopo aver generato il certificato client, esportarle. Verrà installato il certificato client che si esporta in un secondo momento in ogni computer client.

1. Per esportare un certificato client, è possibile utilizzare *certmgr*. Pulsante destro del mouse il certificato client che si desidera esportare, fare clic su **tutte le attività**e quindi fare clic su **Esporta**.
2. Esportare il certificato client con la chiave privata. Si tratta di un file *. pfx* . Assicurarsi che consente di registrare o ricordare la password (chiave) che è impostato per il certificato.

### <a name="upload"></a>4. caricare il file. cer del certificato radice

Dichiarare la variabile per il proprio nome certificato, il valore sostituito con il proprio:

        $P2SRootCertName = "Mycertificatename.cer"

Aggiungere i dati del certificato pubblico per il certificato in Azure. È possibile caricare file per un massimo di 20 certificati. È non caricare la chiave privata per il certificato in Azure. Al caricamento di file. cer, Azure viene usato per autenticare i client connessi alla rete virtuale. 

Sostituire il percorso del file con il proprio e quindi eseguire i cmdlet.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Parte 4: creare il gateway VPN

Configurare e creare il gateway di rete virtuale per la VNet. *-VpnType* deve essere **RouteBased** *-GatewayType* deve essere **Vpn** . Possono essere necessari fino a 45 minuti.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Parte 5 - Download del pacchetto di configurazione di client VPN

Client che si connettono a Azure utilizzando P2S deve avere un certificato client e un pacchetto di configurazione di client VPN installato. Pacchetti di configurazione di client VPN sono disponibili per i client di Windows. Il pacchetto di client VPN contiene informazioni necessarie per configurare il software client VPN che è incorporato in Windows è specifico per la rete VPN che si desidera connettersi. Il pacchetto non viene installato software aggiuntivo. Vedere le [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) per altre informazioni.

1. Dopo aver creato il gateway, è possibile scaricare il pacchetto di configurazione di client. In questo esempio consente di scaricare il pacchetto per client a 64 bit. Se si desidera scaricare il client di versione a 32 bit, sostituire 'Amd64' con 'x86'. È anche possibile scaricare il client VPN tramite il portale di Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. Il cmdlet di PowerShell restituisce un collegamento URL. Questo è un esempio delle quali la restituito URL è simile alla:

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copiare e incollare il collegamento viene restituito un web browser per scaricare il pacchetto. Quindi installare il pacchetto del computer client. Se viene visualizzato un popup SmartScreen, fare clic su **altre informazioni**, quindi **eseguire comunque** per installare il pacchetto.

4. Nel computer client, passare alle **Impostazioni di rete** e fare clic su **VPN**. Verrà visualizzata la connessione nell'elenco. Verrà visualizzato il nome della rete virtuale che si connette al e ha un aspetto simile a questo esempio: 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Client VPN")


## <a name="clientcertificate"></a>Parte 6 - installare il certificato client

Ogni computer client deve disporre di un certificato client per l'autenticazione. Quando si installa il certificato client, sarà necessario la password creata quando il certificato client esportato.

1. Copiare il file. pfx nel computer client.
2. Fare doppio clic sul file. pfx per installarlo. Non modificare il percorso di installazione.

## <a name="connect"></a>Parte 7 - connettersi a Azure

1. Per connettere il VNet del computer client, passare alle connessioni VPN e individuare la connessione VPN creato. È denominato lo stesso nome di rete virtuale. Fare clic su **Connetti**. Venga visualizzato un messaggio a comparsa che fa riferimento a utilizzando il certificato. In questo caso, fare clic su **Continua** per utilizzare i privilegi elevati. 

2. Nella pagina di stato di **connessione** , fare clic su **Connetti** per avviare la connessione. Se viene visualizzata una schermata di **Selezione certificato** , verificare che il certificato client con sia quello che si desidera utilizzare per la connessione. In caso contrario, utilizzare la freccia in giù per selezionare il certificato corretto e quindi fare clic su **OK**.

    ![Connessione client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connessione client VPN")

3. Stabilire a questo punto la connessione.

    ![Connessione] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connessione")

## <a name="verify"></a>Parte 8 - verificare la connessione

1. Per verificare che sia attiva la connessione VPN, aprire un prompt dei comandi con privilegi elevato ed eseguire *ipconfig/all*.

2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto uno degli indirizzi del punto to a Site VPN Client indirizzo pool specificato nella configurazione. Il risultato dovrebbe essere simile alla seguente:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="addremovecert"></a>Per aggiungere o rimuovere un certificato attendibile

Certificati vengono utilizzati per autenticare i client VPN per le connessioni VPN punto al sito. La procedura seguente guidano l'utente tramite l'aggiunta e rimozione di certificati. Quando si aggiunge un file con codifica base 64 x. 509 (CER) in Azure, si indica Azure per considerare attendibile il certificato che rappresenta il file. 

È possibile aggiungere o rimuovere i certificati principali attendibili tramite PowerShell o nel portale di Azure. Se si desidera eseguire questa operazione tramite il portale di Azure, passare al **gateway virtuali > Impostazioni > configurazione del punto a sito > i certificati principali**. La procedura seguente scorrere queste attività tramite PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Aggiungere un certificato attendibile

È possibile aggiungere fino a 20 file con estensione cer certificato attendibile in Azure. Seguire la procedura seguente per aggiungere un certificato principale.

1. Creare e preparare il nuovo certificato principale che verranno aggiunti al Azure. Esportare la chiave pubblica come Base 64 codificato x. 509 (. CER) e aprirlo con un editor di testo. Copiare solo la sezione riportata di seguito. 
 
    Copiare i valori, come illustrato nell'esempio seguente:

    ![certificato] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificato")
    
2. Specificare il nome del certificato e le informazioni chiave come una variabile. Sostituire le informazioni con il proprio, come illustrato di seguito esempio:

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Aggiungere il nuovo certificato. È possibile aggiungere solo un certificato alla volta.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. È possibile verificare che il nuovo certificato è stato aggiunto correttamente utilizzando il seguente cmdlet.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Per rimuovere un certificato attendibile

È possibile rimuovere di certificazione attendibile comuni. Quando si rimuove un certificato attendibile, i certificati client generati dal certificato principale non saranno non è più possibile connettersi Azure tramite punto al sito. Se si desidera client per la connessione, è necessario installare un nuovo certificato client generati da un certificato considerato attendibile in Azure.

1. Per rimuovere un certificato principale, modificare l'esempio seguente:

    Dichiarare le variabili.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Rimuovere il certificato.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Utilizzare il cmdlet seguente per verificare che il certificato è stato rimosso correttamente. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Per gestire l'elenco dei certificati client revocato

È possibile revocare i certificati client. L'elenco di revoche di certificati consente di negare in modo selettivo connettività punto al sito in base a singoli certificati client. Se si rimuove un CER certificato radice da Azure, revocare l'accesso per tutti i certificati client generato/effettuato l'accesso per il certificato revocato. Se si vuole revocare un certificato client specifico, non il documento, è possibile eseguire questa operazione. In questo modo i certificati generati dal certificato principale verranno comunque essere validi.

Pratica comune consiste nell'usare il certificato per gestire l'accesso a livello del team o dell'organizzazione, durante l'uso di certificati client revocato diffusamente il controllo dell'accesso per singoli utenti.

### <a name="revoke-a-client-certificate"></a>Revocare un certificato client

1. È possibile ottenere l'identificazione personale del certificato client da revocare.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Aggiungere l'identificazione personale all'elenco di identificazione personale revocato.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Verificare che l'identificazione personale è stato aggiunto all'elenco di revoche di certificati. È necessario aggiungere identificazione uno personale alla volta.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Ripristinare un certificato client

È possibile ripristinare un certificato client rimuovendo l'identificazione personale dall'elenco dei certificati client revocato.

1.  Rimuovere il segno di identificazione dall'elenco di identificazione personale certificato client revocato.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verificare se l'identificazione personale viene rimosso dall'elenco revocato.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Passaggi successivi

È possibile aggiungere una macchina virtuale per la rete virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.