<properties
   pageTitle="Esempio di rete Perimetrale: creare una rete Perimetrale la protezione delle applicazioni con un Firewall e NSGs | Microsoft Azure"
   description="Creare una rete Perimetrale con un Firewall e gruppi di sicurezza di rete (NSG)"
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

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Esempio 2: creare una rete Perimetrale la protezione delle applicazioni con un Firewall e NSGs

[Tornare alla sicurezza limite Best Practices pagina][HOME]

In questo esempio verrà creata una rete Perimetrale con un firewall e server quattro windows gruppi di sicurezza di rete. Inoltre consentono al ciascuno dei comandi pertinenti per fornire un approfondimento di ogni passaggio. È inoltre disponibile una sezione di uno Scenario di traffico per fornire un'analisi approfondita dettagliate come il traffico procede i livelli di difesa a più livelli di rete Perimetrale. Infine, nei riferimenti di sezione è il codice completo e istruzioni per creare l'ambiente per testare e sperimentare diversi scenari. 

![Rete Perimetrale in ingresso con NVA e NSG][1]

## <a name="environment-description"></a>Descrizione di ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Due servizi cloud: "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con due subnet: "Front-end" e "Back-end"
- Un gruppo di sicurezza di rete singola applicato a entrambi subnet
- Un dispositivo virtuale di rete, in questo esempio un Firewall di NextGen Barracuda connesso alla subnet front-end
- Windows Server che rappresenta un server web di applicazioni ("IIS01")
- Server di due finestre che rappresentano applicazione indietro terminare server ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

>[AZURE.NOTE] Anche se in questo esempio viene utilizzato un Firewall di NextGen Barracuda, molti dei diversi accessori virtuale di rete potrebbero essere utilizzate per questo esempio.

Nella sezione riferimenti sotto esiste uno script di PowerShell che creerà la maggior parte dell'ambiente descritta in precedenza. Creazione di macchine virtuali e le reti virtuali, anche se vengono eseguite da script di esempio, non sono descritti in modo dettagliato in questo documento.
 
Per creare l'ambiente:

  1.    Salvare il file xml di configurazione rete incluso nella sezione riferimenti in base ai nomi, luogo e IP indirizzi in modo che corrisponda lo scenario specificato in
  2.    Aggiornare le variabili di utente di uno script per garantire corrispondenza tra l'ambiente che lo script è deve essere eseguita (abbonamenti, nomi dei servizi, e così via)
  3.    Eseguire lo script di PowerShell

**Nota**: l'area geografica indicato nello script di PowerShell deve corrispondere al paese indicato nel file xml di configurazione della rete.

Una volta lo script viene eseguito correttamente la procedura seguente post-script può essere adottata:

1.  Impostare le regole firewall, questo argomento viene trattato nella sezione seguente: regole del Firewall.
2.  Facoltativamente nella sezione riferimenti sono due script per configurare il server web e app con un'applicazione web semplice per consentire la verifica con la configurazione di rete Perimetrale.

La sezione seguente illustra la maggior parte delle istruzioni script rispetto gruppi di sicurezza di rete.

## <a name="network-security-groups-nsg"></a>Gruppi di sicurezza di rete (NSG)
In questo esempio, un gruppo NSG è integrato e caricato con sei regole. 

>[AZURE.TIP] In generale, è consigliabile creare regole di "Consenti" specifiche prima di tutto e quindi le regole "Nega" più generiche ultimo. Determina il livello di priorità il regole sono valutata prima. Una volta trovato il traffico da applicare a una regola specifica, non vengono valutate ulteriormente regole. NSG regole possono essere applicate in uno nella direzione in ingresso o in uscita (dal punto di vista della subnet).

Per il traffico in ingresso in modo dichiarativo, vengono create le regole seguenti:

1.  È consentito il traffico DNS interno (porta 53)
2.  È consentito il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale
3.  È consentito il traffico HTTP (porta 80) da Internet a NVA (firewall)
4.  Tutto il traffico (tutte le porte) da IIS01 a AppVM1 consentito
5.  Tutto il traffico (tutte le porte) da Internet all'intera negato VNet (entrambe le subnet)
6.  Tutto il traffico (tutte le porte) dalla subnet Frontend alla subnet back-end negato

Con queste regole associato a ogni subnet, se una richiesta HTTP è stato in ingresso da Internet al server web, sia le regole di 3 (Consenti) e 5 (negare) da applicare, ma poiché regola 3 ha una priorità più alta solo da applicare e regola 5 non si presenterà nei Riproduci. In questo modo la richiesta HTTP sarà in grado del firewall. Se il traffico stesso ha tentato di raggiungere il server DNS01, regola 5 (Nega) è la prima di applicare e il traffico da non sia possibile passare al server. Regola 6 (Nega) Blocca subnet front-end da parlando alla subnet back-end (ad eccezione di traffico consentito nelle regole 1 e 4), consente di proteggere la rete back-end in maiuscole/minuscole compromettere l'applicazione web nel front-end, il pirata informatico sarebbe accesso limitato alla rete back-end "protetta" (solo alle risorse esposti nel server AppVM01).

Esiste una regola in uscita predefinito che consente il traffico su internet. In questo esempio abbiamo stiamo consentire il traffico in uscita e non modificare le regole in uscita. Per bloccare il traffico in entrambe le direzioni, il Routing definite dall'utente è necessario, questo viene esaminato in altri esempi che possono essere presenti nel [documento limite di protezione principale][HOME].

Le regole NSG descritte in precedenza sono molto simili alle regole di NSG [nell'esempio 1 - creare un semplice rete Perimetrale con NSGs][Example1]. Rivedere la descrizione di NSG presenti nel documento per informazioni dettagliate su ogni regola NSG e attributi è.

## <a name="firewall-rules"></a>Regole firewall
È necessario che un client di gestione installato in un PC per gestire il firewall e creare le configurazioni necessarie. Vedere la documentazione del firewall (o altri NVA) imparare a gestire il dispositivo. Il resto di questa sezione venga descritti la configurazione del firewall, tramite il client di gestione dei fornitori (ad esempio, non il portale di Azure e PowerShell).

Istruzioni per il download di client e la connessione a Barracuda utilizzati in questo esempio sono disponibili qui: [Barracuda gas amministratore](https://techlib.barracuda.com/NG61/NGAdmin)

Sul firewall, regole di inoltro saranno necessario creare. Poiché questo esempio viene distribuito solo il traffico internet in ingresso per il firewall e quindi per il server web, è necessario un solo inoltro regola NAT. Sul NextGen Firewall Barracuda utilizzati in questo esempio la regola è una regola NAT di destinazione ("ora legale NAT") per passare il traffico.

Per creare la regola seguente (o verificare regole predefinito esistenti), partendo dal dashboard client Barracuda gas amministratore, passare alla scheda configurazione, in configurazione operativa sezione, fare clic su set di regole. Griglia denominata, "Principali regole" illustra le regole attive e disattivate nel firewall. Nell'angolo superiore destro della griglia è un piccolo, verde "+" pulsante, fare clic qui per creare una nuova regola (Nota: il firewall "bloccato" per le modifiche, se è presente un pulsante contrassegnato "Bloccare" ma non è possibile creare o modificare le regole, fare clic su questo pulsante per il set di regole "sbloccare" e consentire la modifica). Se si desidera modificare una regola esistente, selezionare tale regola, mouse e scegliere Modifica regola.

Creare una nuova regola e specificare un nome, ad esempio "WebTraffic". 

L'icona di regola NAT di destinazione è simile alla seguente: ![Sull'icona NAT di destinazione][2]

La regola risulterebbe simile al seguente:

![Regola firewall][3]

Di seguito uno in ingresso indirizzo raggiunti il Firewall tentando di contattare HTTP (porta 80 o 443 per HTTPS) verrà inviato interfaccia "IP locale DHCP1" del Firewall e reindirizzato al Server Web con l'indirizzo IP del 10.0.1.5. Poiché il traffico in entrata sulla porta 80 e andare al server web sulla porta 80 non era necessario nessun cambiamento porta. Tuttavia, l'elenco di destinazione abbiano potuto 10.0.1.5:8080 se il Server Web ascolto sulla porta 8080 in questo modo la traduzione la porta in ingresso 80 sul firewall alla porta 8080 in ingresso il server web.

Un metodo di connessione deve anche essere indicato, per la regola di destinazione da Internet, "Dinamico SNAT" è più appropriato. 

Anche se è stata creata solo una regola è importante che la priorità sia impostata correttamente. Se nella griglia di tutte le regole del firewall questa nuova regola nella parte inferiore (sotto la regola "BLOCKALL") non verrà mai entrano in gioco. Verificare la regola appena creata per il traffico web sopra la regola BLOCKALL.

Una volta è stata creata la regola, deve essere inserito il firewall e quindi attivata, se questa operazione non viene eseguita la modifica regola non avrà effetto. Il processo di attivazione e push viene descritto nella sezione successiva.

## <a name="rule-activation"></a>Attivazione di regola
Con il set di regole modificato per aggiungere questa regola, set di regole deve essere caricato nel firewall e attivato.

![Attivazione di regole firewall][4]

Nell'angolo superiore destro del client management sono un cluster di pulsanti. Fare clic sul pulsante "Invia delle modifiche" per inviare le regole di modificate per il firewall e quindi fare clic sul pulsante "Attiva".

Con l'attivazione del set di regole firewall compilazione di ambiente in questo esempio è stata completata. Facoltativamente, è possono eseguire gli script di compilazione post nella sezione Riferimenti per aggiungere un'applicazione all'ambiente per testare il sotto scenari di traffico.

>[AZURE.IMPORTANT] È importante tenere presente che non verrà raggiunto di direttamente il server web. Quando un browser richiede una pagina HTTP FrontEnd001.CloudApp.Net, l'endpoint HTTP (porta 80) passa il traffico al firewall non dal server web. Il firewall, quindi, in base alla regola creata in precedenza, NAT che richiedono al Server Web.

## <a name="traffic-scenarios"></a>Scenari di traffico

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Consentito) Server Web sul Web con Firewall
1.  Pagina di Internet utente richieste HTTP da FrontEnd001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Cloud punto finale aperto sulla porta 80 interfaccia locale firewall 10.0.1.4:80 il traffico di passaggi di servizio
3.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    Applicare NSG regola 3 (Internet a Firewall), il traffico è l'elaborazione delle regole consentiti, interrompere
4.  Il traffico visitate indirizzo IP interno del firewall (10.0.1.4)
5.  Regola di inoltro del firewall vedere questo è il traffico di porta 80, reindirizza al server web IIS01
6.  Iis01 è in attesa per il traffico web, riceve la richiesta e inizia elaborazione della richiesta
7.  Iis01 chiedono SQL Server in AppVM01 informazioni
8.  Nessuna regola in uscita subnet front-end, il traffico consentito
9.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    NSG regola 3 (Internet a Firewall) non applicare, passare alla regola successiva
  4.    La regola NSG 4 applicare (IIS01 a AppVM01), il traffico consentito, Interrompi l'elaborazione delle regole
10. AppVM01 riceve la Query SQL e risponde
11. Poiché non esistono regole in uscita nella subnet back-end è consentita la risposta
12. Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito.
13. Il server IIS riceve la risposta SQL e completa la risposta HTTP e invia al richiedente
14. Poiché si tratta di una sessione di NAT dal firewall, la destinazione di risposta è (inizialmente) per il Firewall
15. Il firewall riceve la risposta dal Server Web e inoltra all'utente di Internet
16. Perché non ci sono non consentito regole in uscita nella subnet Frontend la risposta e Internet viene visualizzata una pagina web richiesta.

#### <a name="allowed-rdp-to-backend"></a>(Consentito) RDP a back-end
1.  Amministratori di server su internet richiede una sessione RDP alla AppVM01 in BackEnd001.CloudApp.Net:xxxxx dove xxxxx è il numero di porta assegnato in modo casuale per RDP a AppVM01 (la porta assegnata sono disponibili nel portale di Azure o tramite PowerShell)
2.  Poiché il Firewall solo in attesa sull'indirizzo FrontEnd001.CloudApp.Net, non è coinvolto con questo flusso di traffico
3.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    Applicare NSG regola 2 (RDP), il traffico è l'elaborazione delle regole consentiti, interrompere
4.  Con nessuna regola in uscita, applicate le regole predefinite e restituito il traffico consentito
5.  Sessione RDP è abilitata
6.  AppVM01 richiede le password nome utente

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Consentito) Ricerca di DNS Server Web su server DNS
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Consentito) File di access Web Server in AppVM01
1.  Iis01 chiede di un file in AppVM01
2.  Nessuna regola in uscita subnet front-end, il traffico consentito
3.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    NSG regola 3 (Internet a Firewall) non applicare, passare alla regola successiva
  4.    La regola NSG 4 applicare (IIS01 a AppVM01), il traffico consentito, Interrompi l'elaborazione delle regole
4.  AppVM01 riceve la richiesta e risposta con file (presupponendo che l'accesso è autorizzato)
5.  Poiché non esistono regole in uscita nella subnet back-end è consentita la risposta
6.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito.
7.  Il server IIS riceve il file

#### <a name="denied-web-direct-to-web-server"></a>(Negato) Web direttamente al Server Web
Poiché il Server Web, IIS01 e il Firewall si trovano lo stesso servizio Cloud condividono lo stesso indirizzo IP esposto pubblico. In questo modo tutto il traffico HTTP sarà indirizzato al firewall. Durante la richiesta viene fornita correttamente, non è possibile passare direttamente al Server Web, passata, come previsto, attraverso il Firewall prima di tutto. Vedere lo Scenario prima di questa sezione per il flusso di traffico.

#### <a name="denied-web-to-backend-server"></a>(Negato) Web al Server di back-end
1.  Utente Internet tenta di accedere a un file in AppVM01 tramite il servizio BackEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperta per la condivisione di file, questo non passa il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, regola NSG 5 (Internet a VNet) Blocca il traffico

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Negato) Ricerca DNS Web su server DNS
1.  Internet utente tenta di cercare un record DNS interno nel DNS01 tramite il servizio BackEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperto per DNS, questo non passa il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, regola NSG 5 (Internet a VNet) Blocca il traffico (Nota: tale regola 1 (DNS) non si applicano per due motivi, prima di tutto l'indirizzo di origine è internet, questa regola si applica solo a VNet locale come origine, anche si tratta di una regola di autorizzazione, non procedura impedire mai il traffico)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Negato) Web per l'accesso SQL tramite Firewall
1.  Utente Internet richiede dati SQL FrontEnd001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Dal momento che non sono presenti endpoint aperto per SQL, questo non passa il servizio Cloud e non raggiunga il firewall
3.  Se i punti finali erano aperti per qualche motivo, subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (DNS)
  2.    NSG regola 2 (RDP) non applicare, passare alla regola successiva
  3.    Applicare la regola NSG 2 (Internet a Firewall), il traffico è l'elaborazione delle regole consentiti, interrompere
4.  Il traffico visitate indirizzo IP interno del firewall (10.0.1.4)
5.  Firewall non ha le regole di inoltro per SQL e inserisce il traffico

## <a name="conclusion"></a>Conclusioni
Si tratta in modo relativamente diretto di protezione dell'applicazione con il firewall e isolare subnet back-end dal traffico in ingresso.

Altri esempi e una panoramica dei limiti di sicurezza di rete sono disponibili [qui][HOME].

## <a name="references"></a>Riferimenti
### <a name="main-script-and-network-config"></a>Script principale e configurazione di rete
Salvare lo Script completo in un file di script di PowerShell. Salvare la configurazione di rete in un file denominato "NetworkConf2.xml".
Modificare le variabili definite dall'utente in base alle esigenze. Eseguire lo script e quindi seguire le istruzioni configurazione regola Firewall sopra.

#### <a name="full-script"></a>Script completo
Questo script verrà, in base alle variabili definite dall'utente:

1.  Connettersi a un abbonamento a Azure
2.  Creare un nuovo account di archiviazione
3.  Creare un nuovo VNet e due subnet come definito nel file di configurazione di rete
4.  Creare 4 windows macchine virtuali di server
5.  Configurare NSG inclusi:
  - Creazione di un NSG
  - Compilare con le regole
  - Associazione di NSG a subnet appropriata

Questo script di PowerShell deve essere eseguito in locale via che Internet connessi PC o nel server.

>[AZURE.IMPORTANT] Quando si esegue questo script, è possibile avvisi o altri messaggi informativi pop in PowerShell. Solo i messaggi di errore in rosso sono causa problemi.


    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Rete Perimetrale in ingresso con NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icona NAT di destinazione"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regola firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Attivazione di regole firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
