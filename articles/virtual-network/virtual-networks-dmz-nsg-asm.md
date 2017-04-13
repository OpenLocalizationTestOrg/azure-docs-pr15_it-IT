<properties
   pageTitle="Esempio di rete Perimetrale: creare una semplice rete Perimetrale con NSGs | Microsoft Azure"
   description="Creare una rete Perimetrale con gruppi di sicurezza di rete (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>Esempio 1: creare una semplice rete Perimetrale con NSGs

[Tornare alla sicurezza limite Best Practices pagina][HOME]

In questo esempio verrà creato un semplice rete Perimetrale con quattro server windows e gruppi di sicurezza di rete. Inoltre consentono al ciascuno dei comandi pertinenti per fornire un approfondimento di ogni passaggio. È inoltre disponibile una sezione di uno Scenario di traffico per fornire un'analisi approfondita dettagliate come il traffico procede i livelli di difesa a più livelli di rete Perimetrale. Infine, nei riferimenti di sezione è il codice completo e istruzioni per creare l'ambiente per testare e sperimentare diversi scenari. 

![Rete Perimetrale in ingresso con NSG][1]

## <a name="environment-description"></a>Descrizione di ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Due servizi cloud: "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con due subnet; "Front-end" e "Back-end"
- Un gruppo di sicurezza di rete che viene applicata a entrambi subnet
- Windows Server che rappresenta un server web di applicazioni ("IIS01")
- Server di due finestre che rappresentano applicazione indietro terminare server ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

Nella sezione riferimenti sotto esiste uno script di PowerShell che creerà la maggior parte dell'ambiente descritta in precedenza. Creazione di macchine virtuali e le reti virtuali, anche se vengono eseguite da script di esempio, non sono descritti in modo dettagliato in questo documento. 

Per creare l'ambiente;

  1.    Salvare il file xml di configurazione rete incluso nella sezione riferimenti in base ai nomi, luogo e IP indirizzi in modo che corrisponda lo scenario specificato in
  2.    Aggiornare le variabili di utente di uno script per garantire corrispondenza tra l'ambiente che lo script è deve essere eseguita (abbonamenti, nomi dei servizi, e così via)
  3.    Eseguire lo script di PowerShell

**Nota**: l'area geografica indicato nello script di PowerShell deve corrispondere al paese indicato nel file xml di configurazione della rete.

Una volta lo script viene eseguito correttamente ulteriori passaggi facoltativi possono essere prese, nella sezione riferimenti sono due script per configurare il server web e app con un'applicazione web semplice per consentire la verifica con la configurazione di rete Perimetrale.

Nelle sezioni seguenti vengono fornirà una descrizione dettagliata dei gruppi di sicurezza di rete e il relativo funzionamento in questo esempio esaminando chiave righe dello script di PowerShell.

## <a name="network-security-groups-nsg"></a>Gruppi di sicurezza di rete (NSG)
In questo esempio, un gruppo NSG è integrato e caricato con sei regole. 

>[AZURE.TIP] In generale, è consigliabile creare regole di "Consenti" specifiche prima di tutto e quindi le regole "Nega" più generiche ultimo. Determina il livello di priorità il regole possono valutata prima. Una volta trovato il traffico da applicare a una regola specifica, non vengono valutate ulteriormente regole. NSG regole possono essere applicate in uno nella direzione in ingresso o in uscita (dal punto di vista della subnet).

Per il traffico in ingresso in modo dichiarativo, vengono create le regole seguenti:

1.  È consentito il traffico DNS interno (porta 53)
2.  È consentito il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale
3.  È consentito il traffico HTTP (porta 80) da Internet al server web (IIS01)
4.  Tutto il traffico (tutte le porte) da IIS01 a AppVM1 consentito
5.  Tutto il traffico (tutte le porte) da Internet all'intera negato VNet (entrambe le subnet)
6.  Tutto il traffico (tutte le porte) dalla subnet Frontend alla subnet back-end negato

Con queste regole associato a ogni subnet, se una richiesta HTTP è stato in ingresso da Internet al server web, sia le regole di 3 (Consenti) e 5 (negare) da applicare, ma poiché regola 3 ha una priorità più alta solo da applicare e regola 5 non si presenterà nei Riproduci. In questo modo la richiesta HTTP è autorizzata al server web. Se il traffico stesso ha tentato di raggiungere il server DNS01, regola 5 (Nega) è la prima di applicare e il traffico da non sia possibile passare al server. Regola 6 (Nega) Blocca subnet front-end da parlando alla subnet back-end (ad eccezione di traffico consentito nelle regole 1 e 4), consente di proteggere la rete back-end in maiuscole/minuscole compromettere l'applicazione web nel front-end, il pirata informatico sarebbe accesso limitato alla rete back-end "protetta" (solo alle risorse esposti nel server AppVM01).

Esiste una regola in uscita predefinito che consente il traffico su internet. In questo esempio abbiamo stiamo consentire il traffico in uscita e non modificare le regole in uscita. Per bloccare il traffico in entrambe le direzioni, il Routing definite dall'utente è obbligatoria, questa viene esaminata in "Esempio 3" sotto.

Ogni regola viene descritto come indicato di seguito in modo più dettagliato (**Nota**: qualsiasi elemento il sotto elenco in a partire da un segno di dollaro (ad esempio: $NSGName) è una variabile definite dall'utente dallo script nella sezione Guida di riferimento di questo documento):

1. Prima di tutto un gruppo di sicurezza di rete deve essere compilato per tenere le regole:

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  La prima regola in questo esempio verrà consentire il traffico DNS tra tutte le reti interne al server DNS nella subnet back-end. La regola presenta alcune importanti parametri:
  - "Digitare" indica la direzione in cui del traffico questa regola avrà effetto; si tratta dal punto di vista della subnet o macchina virtuale (a seconda nel punto in cui è associato questo NSG). In questo modo se è di tipo "In ingresso" e il traffico passa alla subnet, verrà applicata la regola e il traffico lasciando subnet non sono interessato da questa regola.
  - "Priorità" imposta l'ordine in cui il flusso di traffico verrà valutato. Il numero maggiore la priorità più bassa. Come una regola viene applicata a un flusso di traffico specifico, ulteriore regole non vengono elaborate. In questo modo se una regola con priorità 1 consente il traffico e una regola con priorità 2 Nega il traffico e le regole vengono applicati al traffico quindi il traffico sarà in grado di flusso (poiché regola 1 ha una priorità più alta impiegato effetto e non ulteriormente sono state applicate regole).
  - "Azioni" indica se bloccare o consentire il traffico verrà applicato la regola.

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  Questa regola consentirà RDP il traffico da internet alla porta RDP su qualsiasi server in uno dei due subnet il VNET. Questa regola vengono utilizzati due tipi speciali dei prefissi degli indirizzi; "VIRTUAL_NETWORK" e "INTERNET". Questo è un modo semplice per una categoria più ampia dei prefissi degli indirizzi di indirizzi.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  Questa regola consente il traffico internet in ingresso raggiunga il server web. Ciò non modificare il comportamento di routing; consente solo il traffico destinato IIS01 passare. In questo modo in cui il traffico da Internet abbia il server web come destinazione la regola da consentita l'esecuzione e Interrompi l'elaborazione di altre regole. (La regola priorità 140 tutti gli altri traffico internet in arrivo è bloccato). Se si sta elaborare solo il traffico HTTP, questa regola potrebbe essere ulteriormente limitata per consentire solo 80 porta di destinazione.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  Questa regola consente il traffico passare dal server IIS01 al server AppVM01, una versione successiva di regola Blocca tutti gli altri front-end per il traffico back-end. Per migliorare la regola, se si conosce la porta che deve essere aggiunto. Ad esempio, se il server IIS sta superando solo da SQL Server in AppVM01, l'intervallo di porta di destinazione deve essere modificato da "*" (Any) a 1433 (porta SQL) in modo da consentire una superficie attacco in ingresso più piccolo nel AppVM01 se mai deve essere compromessa l'applicazione web.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  Questa regola impedisce il traffico da internet a tutti i server di rete. In combinazione con la regola priorità 110 e 120, consente solo il traffico internet in ingresso per il firewall e le porte RDP da altri server e blocchi di tutti gli altri elementi. 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  La regola finale Nega il traffico dalla subnet Frontend alla subnet back-end. Poiché si tratta di una regola sola in entrata, il traffico inverso consentito (da back-end per il front-end).

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>Scenari di traffico
#### <a name="allowed-web-to-web-server"></a>(*Consentiti*) Server Web sul Web
1.  Pagina di Internet utente richieste HTTP da FrontEnd001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Punto finale aperto sulla porta 80 verso IIS01 il traffico di passate servizio cloud (il server web)
3.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    Applicare NSG regola 3 (Internet a IIS01), il traffico è l'elaborazione delle regole consentiti, interrompere
4.  Il traffico visitate interno indirizzo IP del server web IIS01 (10.0.1.5)
5.  Iis01 è in attesa per il traffico web, riceve la richiesta e inizia elaborazione della richiesta
6.  Iis01 chiedono SQL Server in AppVM01 informazioni
7.  Nessuna regola in uscita subnet front-end, il traffico consentito
8.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    NSG regola 3 (Internet a Firewall) non applicare, passare alla regola successiva
  4.    La regola NSG 4 applicare (IIS01 a AppVM01), il traffico consentito, Interrompi l'elaborazione delle regole
9.  AppVM01 riceve la Query SQL e risponde
10. Poiché non esistono regole in uscita nella subnet back-end è consentita la risposta
11. Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito.
12. Il server IIS riceve la risposta SQL e completa la risposta HTTP e invia al richiedente
13. Perché non ci sono non consentito regole in uscita nella subnet Frontend la risposta e Internet viene visualizzata una pagina web richiesta.

#### <a name="allowed-rdp-to-backend"></a>(*Consentiti*) RDP a back-end
1.  Amministratori di server su internet richiede una sessione RDP alla AppVM01 in BackEnd001.CloudApp.Net:xxxxx dove xxxxx è il numero di porta assegnato in modo casuale per RDP a AppVM01 (la porta assegnata sono disponibili nel portale di Azure o tramite PowerShell)
2.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    Applicare NSG regola 2 (RDP), il traffico è l'elaborazione delle regole consentiti, interrompere
3.  Con nessuna regola in uscita, applicate le regole predefinite e restituito il traffico consentito
4.  Sessione RDP è abilitata
5.  AppVM01 richiede le password nome utente

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(*Consentiti*) Ricerca di DNS Server Web su server DNS
1.  Web Server, IIS01, esigenze feed di dati in www.data.gov, ma è necessario risolvere l'indirizzo.
2.  Configurazione della rete per gli elenchi di VNet DNS01 (10.0.2.4 subnet back-end) come server DNS primario, IIS01 invia la richiesta DNS per DNS01
3.  Nessuna regola in uscita subnet front-end, il traffico consentito
4.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.     Applicare NSG regola 1 (DNS), il traffico è l'elaborazione delle regole consentiti, interrompere
5.  Server DNS riceve la richiesta
6.  Server DNS non è presente l'indirizzo di cache e richiede un server DNS principale su internet
7.  Nessuna regola in uscita subnet back-end, il traffico consentito
8.  Server DNS Internet risponde, poiché questa sessione è stata avviata internamente, la risposta è consentita
9.  Server DNS memorizza la risposta e risposta alla richiesta di iniziale al IIS01
10. Nessuna regola in uscita subnet back-end, il traffico consentito
11. Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito
12. Iis01 riceve la risposta da DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Consentiti*) File di access Web Server in AppVM01
1.  Iis01 chiede di un file in AppVM01
2.  Nessuna regola in uscita subnet front-end, il traffico consentito
3.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    NSG regola 3 (Internet a IIS01) non applicare, passare alla regola successiva
  4.    La regola NSG 4 applicare (IIS01 a AppVM01), il traffico consentito, Interrompi l'elaborazione delle regole
4.  AppVM01 riceve la richiesta e risposta con file (presupponendo che l'accesso è autorizzato)
5.  Poiché non esistono regole in uscita nella subnet back-end è consentita la risposta
6.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito.
7.  Il server IIS riceve il file

#### <a name="denied-web-to-backend-server"></a>(*Negato*) Web al Server di back-end
1.  Utente Internet tenta di accedere a un file in AppVM01 tramite il servizio BackEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperta per la condivisione di file, questo non passa il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, regola NSG 5 (Internet a VNet) Blocca il traffico

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(*Negato*) Ricerca DNS Web su server DNS
1.  Internet utente tenta di cercare un record DNS interno nel DNS01 tramite il servizio BackEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperto per DNS, questo non passa il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, regola NSG 5 (Internet a VNet) Blocca il traffico (Nota: tale regola 1 (DNS) non si applicano per due motivi, prima di tutto l'indirizzo di origine è internet, questa regola si applica solo a VNet locale come origine, anche si tratta di una regola di autorizzazione, non procedura impedire mai il traffico)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Negato*) Web per l'accesso SQL tramite Firewall
1.  Utente Internet richiede dati SQL FrontEnd001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Dal momento che non sono presenti endpoint aperto per SQL, questo non passa il servizio Cloud e non raggiunga il firewall
3.  Se i punti finali erano aperti per qualche motivo, subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    Applicare NSG regola 3 (Internet a IIS01), il traffico è l'elaborazione delle regole consentiti, interrompere
4.  Il traffico visitate indirizzo IP interno della IIS01 (10.0.1.5)
5.  Iis01 non è in attesa sulla porta 1433, pertanto nessuna risposta alla richiesta

## <a name="conclusion"></a>Conclusioni
Questo è un modo semplice e diretto di isolamento subnet back-end dal traffico in ingresso.

Altri esempi e una panoramica dei limiti di sicurezza di rete sono disponibili [qui][HOME].

## <a name="references"></a>Riferimenti
### <a name="main-script-and-network-config"></a>Script principale e configurazione di rete
Salvare lo Script completo in un file di script di PowerShell. Salvare la configurazione di rete in un file denominato "NetworkConf1.xml".
Modificare le variabili definite dall'utente in base alle esigenze. Eseguire lo script e quindi seguire le istruzioni di configurazione della regola Firewall contenute nella sezione esempio 1.

#### <a name="full-script"></a>Script completo
Questo script verrà, in base alle variabili definite dall'utente.

1.  Connettersi a un abbonamento a Azure
2.  Creare un nuovo account di archiviazione
3.  Creare un nuovo VNet e due subnet come definito nel file di configurazione di rete
4.  Creare 4 windows macchine virtuali di server
5.  Configurare NSG inclusi:
  - Creazione di un NSG
  - Compilare con le regole
  - Associazione di NSG a subnet appropriata

Questo script di PowerShell deve essere eseguito in locale via che Internet connessi PC o nel server.

>[AZURE.IMPORTANT] Quando si esegue questo script, è possibile avvisi o altri messaggi informativi pop in PowerShell. Solo i messaggi di errore in rosso vengono causa problemi.


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>File di configurazione di rete
Salvare il file xml con posizione aggiornata e aggiungere il collegamento al file per la variabile di $NetworkConfigFile lo script precedente.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Script di applicazione di esempio
Se si desidera installare un'applicazione di esempio per questo e altri esempi di rete Perimetrale, si dispone del collegamento seguente: [Script di applicazione di esempio][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Rete Perimetrale in ingresso con NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

