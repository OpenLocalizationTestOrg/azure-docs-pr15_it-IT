<properties
    pageTitle="Utilizzare lo spazio di archiviazione di Azure Premium con SQL Server | Microsoft Azure"
    description="In questo articolo utilizza risorse create con il modello di distribuzione classica e fornisce indicazioni su come utilizzare lo spazio di archiviazione di Azure Premium con SQL Server in macchine virtuali di Microsoft Azure in esecuzione."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizzare lo spazio di archiviazione di Azure Premium con SQL Server in macchine virtuali


## <a name="overview"></a>Panoramica

[Lo spazio di archiviazione di Azure Premium](../storage/storage-premium-storage.md) è la nuova generazione di spazio di archiviazione che fornisce latenza bassa e alta velocità IO. Adatto chiave IO intensivo carichi di lavoro, ad esempio SQL Server in IaaS [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Questo articolo fornisce indicazioni per la migrazione di un computer virtuale che esegue SQL Server da utilizzare lo spazio di archiviazione Premium e pianificazione. Sono inclusi infrastruttura di Azure (social network, lo spazio di archiviazione) e i passaggi macchine Virtuali di Windows guest. L'esempio nell' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) illustra una migrazione completa esaustivo to end su come spostare più grande macchine virtuali di sfruttare i vantaggi di maggiore spazio di archiviazione locale SSD con PowerShell.

È importante comprendere il processo-to-end di archiviazione che utilizzano Premium su Azure con SQL Server in macchine virtuali IAAS. Sono inclusi:

- Identificazione del prerequisiti per l'utilizzo di spazio di archiviazione Premium.
- Esempi di distribuzione di SQL Server in IaaS allo spazio di archiviazione Premium per le distribuzioni di nuove.
- Esempi di migrazione distribuzioni esistenti, autonomi e distribuzioni utilizzo SQL sempre sulla disponibilità dei gruppi.
- Migrazione possibili approcci.
- Esempio di full-to-end con passaggi Azure, Windows e SQL Server per la migrazione di un'implementazione esistente sempre attiva.

Per ulteriori informazioni su SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

**Autore:** Daniel Sol **Revisori tecnici:** Agostino Luis Vargas aringhe, Raffaella Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Prerequisiti per l'archiviazione Premium

Esistono diversi prerequisiti per l'uso di spazio di archiviazione Premium.

### <a name="machine-size"></a>Dimensioni del computer

Per l'uso di spazio di archiviazione Premium sarà necessario utilizzare serie DS macchine virtuali (macchine Virtuali). Se non è stato utilizzato macchine serie DS nel servizio cloud prima, è necessario eliminare la macchina virtuale esistente, mantenere dischi collegati e quindi si crea un nuovo servizio cloud prima di ricreare la macchina virtuale come dimensione ruolo DS *. Per ulteriori informazioni sulle dimensioni macchina virtuale, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Servizi cloud

È possibile utilizzare solo DS * macchine virtuali con lo spazio di archiviazione Premium quando vengono creati in un nuovo servizio cloud. Se si usano SQL Server sempre in Azure, sempre nella comunicare ascoltatore riguarderanno l'indirizzo di Azure interna o esterna IP di bilanciamento del carico associato a un servizio cloud. In questo articolo illustra come eseguire la migrazione di mantenere la disponibilità in questo scenario.

> [AZURE.NOTE] Una serie DS * deve essere la macchina virtuale prima che viene distribuita con il nuovo servizio Cloud.

### <a name="regional-vnets"></a>VNETS internazionali

Per le macchine virtuali DS * è necessario configurare i virtuale rete (VNET) che ospita le macchine virtuali per essere internazionali. Questo "amplia" il VNET consente macchine virtuali più grande essere effettuato il provisioning in altri cluster e consentire le comunicazioni tra di esse. Nella schermata seguente nella posizione evidenziata mostra internazionali VNETs, mentre il risultato prima Mostra un VNET "strette".

![RegionalVNET][1]

È possibile generare un ticket di supporto Microsoft per eseguire la migrazione a un VNET internazionali, Microsoft si apporta una modifica, quindi per completare la migrazione a internazionali VNETs, modificare la proprietà AffinityGroup nella configurazione di rete. Esportare la configurazione di rete di PowerShell e quindi sostituire la proprietà **AffinityGroup** nell'elemento **VirtualNetworkSite** con una proprietà di **posizione** . Specificare `Location = XXXX` in `XXXX` è un'area di Azure. Quindi importare la configurazione di nuova.

Ad esempio, prendere in considerazione la configurazione di VNET seguente:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Per spostare un VNET internazionali in Europa occidentale, modificare la configurazione per le operazioni seguenti:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Account di archiviazione

È necessario creare un nuovo account di archiviazione è configurato per l'archiviazione Premium. Si noti che l'utilizzo dell'archiviazione Premium è impostata su account di archiviazione, non su singoli dischi rigidi virtuali, tuttavia, quando si utilizza una macchina virtuale serie DS * è possibile allegare del disco rigido virtuale dagli account Premium e lo spazio di archiviazione Standard. Se non si desidera inserire il disco rigido virtuale OS all'account di archiviazione Premium, è possibile effettuare questa operazione.

Il comando **Nuovo AzureStorageAccountPowerShell** seguente con il **tipo** di "Premium_LRS" Crea un Account di archiviazione Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Impostazioni della cache dei dischi rigidi virtuali

La differenza tra la creazione di dischi che fanno parte di un account di archiviazione Premium principale è l'impostazione della cache disco. Per dischi volume di dati di SQL Server, è consigliabile utilizzare '**La memorizzazione nella cache di lettura**'. Per volumi registri delle transazioni, impostare l'impostazione della cache disco su '**Nessuno**'. Questa è la differenza tra i suggerimenti per gli account di archiviazione Standard.

Una volta i dischi rigidi virtuali allegati non è possibile modificare l'impostazione della cache. È necessario disconnettersi e riconnettersi il disco rigido virtuale con un'impostazione della cache aggiornata.

### <a name="windows-storage-spaces"></a>Spazi di archiviazione di Windows

È possibile utilizzare [Gli spazi di archiviazione di Windows](https://technet.microsoft.com/library/hh831739.aspx) in modo analogo al precedente lo spazio di archiviazione Standard, in questo modo sarà possibile eseguire la migrazione di una macchina virtuale che sta già utilizzando spazi di archiviazione. Il codice di Powershell per estrarre e importare una macchina virtuale con più dischi rigidi virtuali allegati illustrato nell'esempio [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (passaggio 9 e in avanti).

Pool di archiviazione sono stati utilizzati con account di archiviazione di Azure Standard per ottimizzare la velocità e ridurre la latenza. Possono risultare valore test pool di archiviazione con Premium lo spazio di archiviazione per le nuove distribuzioni, ma aggiungere ulteriore complessità con l'installazione di archiviazione.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Come trovare il mapping tra Azure dischi virtuali al pool di archiviazione

Come esistono impostazioni consigliate diversa per la cache dei dischi rigidi virtuali allegati, è possibile copiare i dischi rigidi virtuali di un account di archiviazione Premium. Tuttavia, quando si riconnette loro per la nuova serie di DS macchine Virtuali, potrebbe essere necessario modificare le impostazioni della cache. È più semplice applicare lo spazio di archiviazione Premium consigliato le impostazioni della cache quando si dispone di dischi rigidi virtuali separati per i file di dati SQL e i file di log (anziché un singolo disco rigido virtuale che contiene sia).

> [AZURE.NOTE] Se si dispone di file di dati e log di SQL Server nello stesso volume, memorizzazione nella cache l'opzione che scelta dipende i modelli di access IO per i carichi di lavoro di database. Solo i test può mostrare quale opzione di memorizzazione più adatto per questo scenario.

Tuttavia, se si utilizza spazi di archiviazione di Windows che sono costituiti da più dischi rigidi virtuali è necessario esaminare gli script originali per identificare quale collegato dischi rigidi virtuali si nel pool scopo specifico, pertanto è quindi possibile impostare le impostazioni della cache conseguenza per ogni disco.

Se non è disponibile per dimostrare di essere dischi rigidi virtuali corrispondenti al pool di archiviazione di script originale, è possibile utilizzare la procedura seguente per determinare il mapping di pool di archiviazione/su disco.

Per ogni disco, utilizzare la procedura seguente:

1. Ottenere l'elenco di dischi collegati a macchine Virtuali con il comando **Get-AzureVM** :

    Get-AzureVM - nome <servicename> -nome <vmname> | Get-AzureDataDisk

1. Prendere nota del Diskname e LUN.

    ![DisknameAndLUN][2]

1. Desktop remoto in macchina virtuale. Quindi passare alla sezione **Gestione Computer** | **Gestione dispositivi** | **unità disco**. Esaminare le proprietà di ' Microsoft Virtual dischi '

    ![VirtualDiskProperties][3]

1. Il numero LUN è un riferimento al numero LUN che specificare se si installa il disco rigido virtuale per la macchina virtuale.
1. Per "Microsoft Virtual disco" fare clic sulla scheda **Dettagli** , quindi nell'elenco **proprietà** , passare alla **Chiave Driver**. Nella casella **valore**nota **scarto**, ovvero 0002 nella schermata seguente. Il 0002 indica PhysicalDisk2 che fa riferimento il pool di archiviazione.

    ![VirtualDiskPropertyDetails][4]

2. Per ogni pool di archiviazione, Scarica fuori dischi associati:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-fisico

    ![GetStoragePool][5]

È ora possibile utilizzare queste informazioni per associare associati dischi rigidi virtuali a dischi nel pool di archiviazione.

Dopo che hanno mappato dischi rigidi virtuali a dischi nel pool di archiviazione è quindi possibile scollegare e copiarli in un account di archiviazione Premium, quindi collegarli con l'impostazione corretta della cache. Vedere l'esempio in [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)passaggi 8 e 12. Questa procedura viene illustrato come estrarre una configurazione disco rigido virtuale collegato macchine Virtuali in un file CSV, copiare i dischi rigidi virtuali, modificare le impostazioni della cache di configurazione del disco e infine ridistribuire la macchina virtuale come una serie di DS macchine Virtuali con tutti i dischi connessi.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>La larghezza di banda di macchine Virtuali lo spazio di archiviazione e la velocità effettiva dello spazio di archiviazione disco rigido virtuale

Le prestazioni di archiviazione dipende dalla dimensione DS * VM specificata e le dimensioni del disco rigido virtuale. Macchine virtuali hanno consentito diverso per il numero di dischi rigidi virtuali che può essere collegata e la larghezza di banda massima supporteranno (MB/s). Per i numeri di larghezza di banda specifica, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure](virtual-machines-linux-sizes.md).

IOPS maggiore si ottengono con dimensioni maggiori di disco. Questa operazione è necessario considerare quando si pensa il percorso di migrazione. Per informazioni dettagliate, [vedere la tabella per secondo e tipi di disco](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Infine, è consigliabile che macchine virtuali abbiano larghezze di banda altro disco massimo che supporteranno per tutti i dischi associati. In condizioni di carico elevato, è possibile saturare la larghezza di banda massima disco disponibile per la dimensione di ruolo macchine Virtuali. Ad esempio un Standard_DS14 supporteranno fino a 512 MB/s. di conseguenza, con tre dischi P30 è Impossibile saturare la larghezza di banda disco della macchina virtuale. Ma in questo esempio, è possibile superato il limite di velocità a seconda la combinazione di lettura e scrittura IOs.

## <a name="new-deployments"></a>Nuove distribuzioni

Le prossime due sezioni per dimostrare come è possibile distribuire macchine virtuali di SQL Server allo spazio di archiviazione Premium. Come detto in precedenza, non necessariamente essere necessario inserire il disco OS nello spazio di archiviazione Premium. È possibile scegliere di eseguire questa operazione si intende inserire qualsiasi intensivo carichi di lavoro di IO sul disco rigido virtuale del sistema operativo.

Nel primo esempio viene utilizzando esistente Azure raccolta immagini. Il secondo esempio viene illustrato come utilizzare un'immagine macchine Virtuali personalizzata che si dispone di un account di archiviazione Standard esistente.

> [AZURE.NOTE] In questi esempi presuppongono che è già stato creato un VNET internazionali.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Creare una nuova macchina virtuale con lo spazio di archiviazione Premium con immagine della raccolta

Nell'esempio seguente viene illustrato come inserire il disco rigido virtuale OS nello spazio di archiviazione premium e collegare dischi rigidi virtuali archiviazione Premium. Tuttavia, è possibile anche inserire il disco del sistema operativo in un account di archiviazione Standard e quindi allegare dischi rigidi virtuali che si trovano in un account di archiviazione Premium. Vengono illustrati entrambi gli scenari.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Passaggio 1: Creare un Account di archiviazione Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Passaggio 2: Creare un nuovo servizio Cloud

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passaggio 3: Prenotare un indirizzo VIP del servizio Cloud (facoltativo)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Passaggio 4: Creare un contenitore di macchine Virtuali
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passaggio 5: Immissione disco rigido virtuale OS sul Standard o Premium lo spazio di archiviazione
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Passaggio 6: Creare macchine Virtuali
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Creare una nuova macchina virtuale per utilizzare lo spazio di archiviazione Premium con un'immagine personalizzata

In questo scenario viene nel punto in cui si dispone di immagini personalizzate esistenti che si trovano in un account di archiviazione Standard. Come detto se si desidera inserire il disco rigido virtuale OS allo spazio di memorizzazione Premium sarà necessario copiare l'immagine che è presente nell'account di archiviazione Standard e trasferirli in un archivio Premium prima che può essere utilizzato. Se si dispone di un'immagine in locale, è anche possibile utilizzare questo metodo per copiare che direttamente all'account di archiviazione Premium.

#### <a name="step-1-create-storage-account"></a>Passaggio 1: Creare Account di archiviazione
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Passaggio 2 creare servizio Cloud
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Passaggio 3: Usare immagine esistente
È possibile utilizzare un'immagine esistente. In alternativa, è possibile [acquisire un'immagine di una macchina esistente](virtual-machines-windows-classic-capture-image.md). Nota il computer è immagine non può essere DS* computer. Dopo avere inserito l'immagine, la procedura seguente mostra come copiare all'account di archiviazione Premium con il * *AzureStorageBlobCopy inizio** cmdlet di PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passaggio 4: Copiare Blob tra gli account di archiviazione
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Passaggio 5: Controllare regolarmente copia stato:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passaggio 6: Aggiungere disco immagine su disco Azure archivio in abbonamento
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] È possibile anche se le relazioni sullo stato come esito positivo, è possibile comunque ottenere un errore di leasing del disco. In questo caso, attendere circa 10 minuti.

#### <a name="step-7--build-the-vm"></a>Passaggio 7: Creare la macchina virtuale
Di seguito si creano la macchina virtuale dall'immagine e associare due dischi rigidi virtuali archiviazione Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Distribuzioni esistenti che non utilizzano sempre in gruppi disponibilità

> [AZURE.NOTE] Per le distribuzioni esistenti, vedere prima la sezione [Prerequisiti](#prerequisites-for-premium-storage) di questo argomento.

Esistono considerazioni diversi per le installazioni di SQL Server che non utilizzano sempre in gruppi disponibilità e a quelli da eseguire. Se si ha un autonomo esistente di SQL Server non Usa sempre attiva, è possibile aggiornare allo spazio di archiviazione Premium utilizzando un nuovo account di servizio e lo spazio di archiviazione cloud. Considerare le seguenti opzioni:

- **Creare una nuova macchina virtuale di SQL Server**. È possibile creare una nuova macchina virtuale Server SQL che utilizza un account di archiviazione Premium, come descritto nel nuovo distribuzioni. Quindi eseguire il backup e ripristinare i database di configurazione e dell'utente di SQL Server. L'applicazione sarà necessario aggiornarli per fare riferimento il nuovo SQL Server, se si accede a internamente o esternamente. È necessario copiare tutti gli oggetti 'fuori db"come se si sono state eseguendo la migrazione Side-by (SxS) SQL Server. Sono inclusi gli oggetti, ad esempio gli account di accesso, certificati e server collegati.
- **Eseguire la migrazione una macchina virtuale Server SQL esistente**. Sarà necessario disconnettere macchina virtuale SQL Server, quindi trasferirli in un nuovo servizio cloud, che include la copia di tutti i dischi rigidi virtuali allegati nell'account di archiviazione Premium. Quando la macchina virtuale in linea, l'applicazione farà riferimento il nome del server host come prima. Tenere presente che le dimensioni del disco esistente vengono influenzata dalle caratteristiche di prestazioni. Ad esempio un disco 400 GB viene arrotondato per eccesso a una P20. Se si è sicuri che non è necessario che le prestazioni del disco, è possibile ricreare macchine Virtuali come una macchina virtuale serie DS e collegare dischi rigidi virtuali archiviazione Premium della specifica e prestazioni che si richiede. Quindi è possibile disconnettersi e riconnettersi i file di database SQL.

> [AZURE.NOTE] Quando la copia dischi disco rigido virtuale dovrebbe essere conoscere le dimensioni, a seconda delle dimensioni implica che il tipo di disco di archiviazione Premium rientrano in, questa impostazione determina specifica le prestazioni del disco. La dimensione verrà Azure arrotondare fino a disco più vicino, in modo che se si dispone di un disco 400 GB, questo verrà arrotondato a un P20. A seconda delle esigenze IO esistente del file del sistema operativo, non si potrebbe essere necessario eseguire la migrazione di questo a un account di archiviazione Premium.

Se si accede a SQL Server esternamente, verrà modificato l'indirizzo VIP del servizio cloud. Sarà anche necessario punti finali di aggiornamento, ACL e DNS impostazioni.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Distribuzioni esistenti che utilizzano sempre in gruppi disponibilità

> [AZURE.NOTE] Per le distribuzioni esistenti, vedere prima la sezione [Prerequisiti](#prerequisites-for-premium-storage) di questo argomento.

In questa sezione sono inizialmente osservare sempre in interazione con Azure di rete. È quindi verranno interrotte verso il basso le migrazioni in a due scenari: migrazioni in tollerabile dei tempi di inattività e migrazioni nel punto in cui è necessario ottenere il tempo di inattività minimo.

Locale SQL Server sempre nella disponibilità gruppi di utilizzare un comunicare ascoltatore locale che registra un nome DNS virtuale insieme a un indirizzo IP condiviso tra uno o più SQL Server. Quando i client si connettono passano attraverso IP comunicare ascoltatore a SQL Server primario. Si tratta del server a cui appartiene la risorsa sempre su IP in quel momento.

![DeploymentsUseAlways su][6]

In Microsoft Azure che è consentito un solo indirizzo IP assegnato a una scheda nella macchina virtuale, pertanto per ottenere lo stesso livello di astrazione come locale, Azure utilizza l'indirizzo IP che è stata assegnata i servizi di bilanciamento del carico interni ed esterni (ILB/ELB). La risorsa IP condivisi tra i server è impostata per lo stesso IP ILB/ELB. Questo è pubblicato nel sistema DNS e il traffico client passato alla ILB/ELB di replica di SQL Server primario. ILB/ELB sa che SQL Server primario poiché utilizza le ricerche per verificare la presenza di risorsa sempre su IP. Nell'esempio precedente, analizza ogni nodo che dispone di un endpoint a cui fa riferimento ELB/ILB, seconda risponde è il principale di SQL Server.

> [AZURE.NOTE] La ILB ed ELB vengono assegnati a un servizio cloud di Azure particolare, pertanto qualsiasi migrazione cloud in Azure è probabile che significa che l'indirizzo IP di bilanciamento del carico vengono modificate.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrazione sempre su distribuzioni che possono consentire di inattività

Esistono due metodi per eseguire la migrazione sempre su distribuzioni che consentono di inattività:

1. **Aggiungere ulteriori repliche secondarie a un sempre in Cluster esistente**
1. **Eseguire la migrazione a un nuovo sempre in Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. aggiungere ulteriori repliche secondario a un sempre in Cluster esistente

Una strategia consiste nell'aggiungere più database secondari sempre nel gruppo disponibilità. È necessario aggiungere in un nuovo servizio cloud e aggiornare comunicare ascoltatore con il nuovo PI bilanciamento carico.

##### <a name="points-of-downtime"></a>Punti di inattività:

- Convalida dei cluster.
- Test sempre in failover per nuovi database secondari.

Se si utilizza il pool di archiviazione Windows all'interno della macchina virtuale per maggiore produttività IO, quindi questi verrà applicati offline durante una convalida Cluster completo. Quando si aggiungono nodi al cluster, è necessario il test di convalida. Il tempo che necessario per eseguire il test può variare, in modo che è necessario verificare nel proprio ambiente di test representative per ottenere un tempo approssimativo di quanto tempo verrà.

È necessario effettuare il provisioning di tempo in cui è possibile eseguire il failover manuale e chaos test su nodi appena aggiunti per garantire sempre nella disponibilità come previsto.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] È consigliabile interrompere tutte le istanze di SQL Server in cui vengono utilizzati i pool di archiviazione prima dell'esecuzione della convalida.
##### <a name="high-level-steps"></a>Passaggi di alto livello

1. Creare due nuovi SQL Server in nuovo servizio cloud con archiviazione Premium.
1. Copiare il backup completi e il ripristino senza **recupero**.
1. Copiare "fuori dall'utente DB" oggetti dipendenti, ad esempio gli account di accesso e così via.
1. Creare un nuovo interno carico bilanciamento (ILB) o utilizzare un bilanciamento di carico esterni (ELB) e quindi configurare endpoint di bilanciamento del carico su entrambi i nodi nuovi.
> [AZURE.NOTE] Seleziona che tutti i nodi sono presenti la corretta configurazione dell'Endpoint prima di continuare

1. Interrompere l'accesso utente/applicazione a SQL Server (se si utilizza il pool di archiviazione).
1. Interrompere servizi motore di SQL Server in tutti i nodi (se si utilizza il pool di archiviazione).
1. Aggiungere nuovi nodi per cluster ed eseguire la convalida completa.
1. Una volta convalida viene completata correttamente, iniziare a tutti i servizi di SQL Server.
1. Eseguire il backup dei registri delle transazioni e ripristinare i database utente.
1. Aggiungere nuovi nodi in sempre nel gruppo disponibilità e inserire la replica in **sincrono**.
1. Aggiungere la risorsa indirizzo IP del nuovo Cloud servizio ILB/ELB tramite PowerShell per sempre attiva in base all'esempio più siti nell' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). In cluster di Windows, impostare i **possibili proprietari** della risorsa **Indirizzo IP** per i nuovi nodi precedenti. Vedere la sezione 'Aggiungere risorsa indirizzo IP nella stessa Subnet' dell' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Il controllo a uno dei nodi di nuovo.
1. Verificare i nuovi nodi failover automatico Failover partner e test.
1. Rimuovere i nodi originali dal gruppo di disponibilità.

##### <a name="advantages"></a>Vantaggi

- Nuovo SQL Server possono essere testati (SQL Server e applicazione) prima che vengano aggiunti a sempre attiva.
- È possibile modificare le dimensioni di macchine Virtuali e personalizzare lo spazio di archiviazione per le specifiche esigenze. Tuttavia, sarebbe utile mantenere invariati tutti i percorsi di file SQL.
- È possibile controllare quando vengono avviate il trasferimento di backup DB alle repliche secondario. Questo è diverso da tramite Azure **Start AzureStorageBlobCopy** cmdlet copiare dischi rigidi virtuali, in quanto si tratta di una copia asincrona.

##### <a name="disadvantages"></a>Svantaggi
- Quando si utilizza il pool di archiviazione di Windows, è tempo di inattività Cluster durante la convalida Cluster completa per i nuovi nodi aggiuntivi.
- A seconda la versione di SQL Server e del numero di repliche secondarie esistenti, potrebbe non essere possibile aggiungere ulteriori repliche secondarie senza la rimozione dei secondari esistenti.
- Può essere molto tempo di trasferimento di dati SQL durante l'impostazione database secondario.
- Esiste costi aggiuntivi durante la migrazione mentre si è in esecuzione in parallelo nuovi computer.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. eseguire la migrazione a un nuovo sempre in Cluster

Un'altra strategia consiste nel creare una nuova sempre in Cluster con nodi di un nuovi nel nuovo servizio cloud e quindi reindirizza i client per utilizzarlo.

##### <a name="points-of-downtime"></a>Punti di inattività

Esiste un tempo di inattività quando si trasferisce applicazioni e i relativi utenti a nuovo comunicare sempre attiva ascoltatore. Il tempo di inattività dipende:

- Il tempo necessario per ripristinare i log delle transazioni finale ai database in nuovi server.
- Il tempo necessario per aggiornare le applicazioni client di utilizzare comunicare ascoltatore sempre nella nuova.

##### <a name="advantages"></a>Vantaggi

- È possibile testare l'ambiente di produzione effettiva, SQL Server e OS compilare le modifiche.
- Disponibile l'opzione per personalizzare lo spazio di archiviazione e per ridurre le dimensioni di macchine Virtuali. Questo potrebbe causare riduzione dei costi.
- È possibile aggiornare le build di SQL Server o una versione durante il processo. È inoltre possibile aggiornare il sistema operativo.
- Precedente sempre nel Cluster è simile a un valore di destinazione ripristino a tinta unita.

##### <a name="disadvantages"></a>Svantaggi

- È necessario modificare il nome DNS di comunicare ascoltatore se si desidera che sia sempre sui cluster di esecuzione simultanea. Come le stringhe dell'applicazione client devono riflettere il nuovo nome di comunicare ascoltatore verrà aggiunto amministrazione sovraccarico durante la migrazione.
- È necessario implementare un meccanismo di sincronizzazione tra i due ambienti per mantenerle più vicino possibile a ridurre i requisiti di sincronizzazione finale prima della migrazione.
- Sono aggiunto costo durante la migrazione mentre si è il nuovo ambiente in esecuzione.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrazione sempre in distribuzioni per tempo di inattività minimo

Esistono due strategie per le distribuzioni sempre nella migrazione per tempo di inattività minimo:

1. **Utilizzare un secondario esistente: singolo sito**
1. **Utilizzare replica o le repliche secondarie esistente: più siti**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utilizzare un secondario esistente: singolo sito

Una strategia per tempo di inattività minimo consiste nell'eseguire un cloud esistente secondario e rimuoverlo dal servizio cloud corrente. Quindi copiare i dischi rigidi virtuali al nuovo account di archiviazione Premium e creare la macchina virtuale in nuovo servizio cloud. Aggiornare quindi comunicare ascoltatore in cluster e failover.

##### <a name="points-of-downtime"></a>Punti di inattività

- Esiste un tempo di inattività quando si aggiorna il nodo finale con l'endpoint di bilanciamento del carico.
- La riconnessione del client potrebbe richiedere più tempo a seconda della configurazione di client/DNS.
- Se si sceglie di portare offline il gruppo sempre in Cluster la sostituzione indirizzi IP non esiste il tempo di inattività aggiuntiva. È possibile evitare questa operazione utilizzando una dipendenza OR e i possibili proprietari per la risorsa indirizzo IP aggiunta. Vedere la sezione 'Aggiungere risorsa indirizzo IP nella stessa Subnet' dell' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Quando si desidera il nodo aggiunto a partake come sempre su Failover Partner, è necessario aggiungere un Endpoint di Azure con un riferimento al Set di bilanciamento del carico di caricamento. Quando si esegue il comando **Aggiungi AzureEndpoint** per eseguire questa operazione, non sarà possibile stabilire fino a quando non è aggiornato il sistema di bilanciamento del carico corrente connessioni rimanga aperte, ma nuove connessioni per comunicare ascoltatore. In questo test rilevata per ultimo 90-120seconds, questo deve essere verificato.

##### <a name="advantages"></a>Vantaggi

- Senza aggiuntivi costi sostenuti durante la migrazione.
- Migrazione-a-uno.
- Complessità ridotta.
- Consente maggiore IOPS da SKU di spazio di archiviazione avanzate. Quando i dischi sono disconnessi dal macchina virtuale e copiati al nuovo servizio cloud, di terze parti di un 3o tool è utilizzabile per aumentare la dimensione del disco rigido virtuale, che offre maggiore produttività. Per aumentare le dimensioni disco rigido virtuale, vedere il [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Svantaggi

- Esiste una perdita temporanea di HA e DR durante la migrazione.
- Come si tratta di una migrazione 1:1, è necessario utilizzare una dimensione minima di macchine Virtuali in grado di supportare il numero di dischi rigidi virtuali, in modo che potrebbe non essere possibile soddisfare le macchine virtuali.
- Questo scenario da utilizzare il cmdlet Azure **Start AzureStorageBlobCopy** asincrono. Al termine di copia non esiste alcun contratto di servizio. Il tempo le copie varia, mentre ciò dipende dal tempo di attesa in coda che dipendono anche la quantità di dati da trasferire. Il tempo di copia aumenta se il trasferimento agirà da un altro centro di dati di Azure che supporta l'archiviazione Premium in un'altra area. Se è sufficiente 2 nodi, è consigliabile un attenuazione possibili nel caso in cui la copia richiede più tempo durante i test. Ad esempio le seguenti idee.
    - Aggiungere un nodo di SQL Server 3 ° temporaneo per HA prima della migrazione con i tempi di inattività approvate.
    - Eseguire la migrazione all'esterno di Azure manutenzione pianificata.
    - Verificare di che avere configurato la base cluster correttamente.  

##### <a name="high-level-steps"></a>Passaggi di alto livello

In questo documento non vengono visualizzati un esempio to end completo, tuttavia [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) fornisce dettagli che possono essere utilizzati per eseguire questa operazione.

![MinimalDowntime][8]

- Raccogliere configurazione del disco e rimuovere il nodo (non eliminare l'allegato dischi rigidi virtuali).
- Creare un account di archiviazione Premium e copiare dischi rigidi virtuali dall'account di archiviazione Standard
- Creare nuovo servizio cloud e ridistribuire la macchina virtuale SQL2 in tale servizio cloud. Creare la macchina virtuale utilizzando il disco rigido virtuale OS copiata originale e associare dischi rigidi virtuali di copiato.
- Configurare ILB / ELB e aggiungere i punti finali.
- Aggiornare comunicare ascoltatore in uno dei seguenti:
    - Disconnettere sempre nel gruppo e aggiornamento di sempre nella comunicare ascoltatore con nuovo ILB / indirizzo IP ELB.
    - O aggiungere la risorsa indirizzo IP del nuovo Cloud servizio ILB/ELB tramite PowerShell nel servizio cluster di Windows. Impostare quindi i possibili proprietari della risorsa indirizzo IP al nodo migrato SQL2 e ciò come dipendenza o il nome di rete. Vedere la sezione 'Aggiungere risorsa indirizzo IP nella stessa Subnet' dell' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Selezionare configurazione DNS/propagazione ai client.
- Eseguire la migrazione di macchine Virtuali SQL1 e attraversano i passaggi da 2 a 4.
- Se Usa 5ii passaggi, quindi aggiungere SQL1 come possibile proprietario per la risorsa indirizzo IP aggiunto
- Testare failover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utilizzare replica o le repliche secondarie esistente: più siti

Se si dispongano di nodi in più Data Center Azure (CD) o se si dispone di un ambiente ibrido, è possibile utilizzare una configurazione sempre attiva in questo ambiente per ridurre al minimo il tempo di inattività.

L'approccio consiste nel modificare la sincronizzazione sempre in sincrono per locale o Cc Azure secondario e quindi il controllo su a SQL Server. Quindi copiare i dischi rigidi virtuali a un account di archiviazione Premium e ridistribuire il computer in un nuovo servizio cloud. Aggiornare comunicare ascoltatore e quindi tornare esito negativo.

##### <a name="points-of-downtime"></a>Punti di inattività

Il tempo di inattività è costituito dal tempo necessario per il controllo al alternativo controller di dominio e viceversa. Dipende inoltre la configurazione di client/DNS e può essere ritardata la riconnessione del client.
Valutare la possibilità di esempio di una configurazione ibrida sempre nella seguente:

![MultiSite1][9]

##### <a name="advantages"></a>Vantaggi

- È possibile utilizzare infrastruttura esistente.
- Viene offerta la possibilità di pre-aggiornamento prima di tutto lo spazio di archiviazione Azure sul controller di dominio DR Azure.
- È possibile riconfigurare lo spazio di archiviazione Azure DR controller di dominio.
- Esiste un numero minimo di due failover durante la migrazione, escludendo failover test.
- Non è necessario spostare i dati di SQL Server con backup e ripristino.

##### <a name="disadvantages"></a>Svantaggi

- A seconda di accesso client a SQL Server, potrebbe essere incremento di latenza durante l'esecuzione di SQL Server in un controller di dominio alternativo all'applicazione.
- Fase di copia di dischi rigidi virtuali allo spazio di archiviazione Premium potrebbe essere lungo. Questa operazione può influire sulla decisione su se si desidera mantenere il nodo del gruppo di disponibilità. Aspetto da considerare per l'utilizzo di lavoro in modo intensivo log carichi esegue durante la migrazione necessario, poiché il nodo principale sarà necessario considerare le transazioni non replicate del registro delle transazioni. Pertanto si potrebbero aumentare in modo significativo.
- Questo scenario da utilizzare il cmdlet Azure **Start AzureStorageBlobCopy** asincrono. Al termine non esiste alcun contratto di servizio. Il tempo le copie varia, mentre ciò dipende dal tempo di attesa in coda, variano alla quantità di dati da trasferire. Pertanto è sufficiente un nodo nel centro dati 2a, è necessario eseguire operazioni di attenuazione nel caso in cui la copia richiede più tempo durante i test. Ad esempio le seguenti idee.
    - Aggiungere un nodo SQL 2a temporaneo per HA prima della migrazione con i tempi di inattività approvate.
    - Eseguire la migrazione all'esterno di Azure manutenzione pianificata.
    - Verificare di che avere configurato la base cluster correttamente.

Questo scenario presuppone che si presentano l'installazione e sapere come lo spazio di archiviazione sia associato per apportare modifiche per le impostazioni della cache del disco ottimali.

##### <a name="high-level-steps"></a>Passaggi di alto livello
![Multisite2][10]

- Rendere i locale / alternativi cc Azure SQL Server principale e apportare le altre automatico Failover Partner (AFP).
- Raccogliere informazioni di configurazione del disco rigido da SQL2 e rimuovere il nodo (non eliminare l'allegato dischi rigidi virtuali).
- Creare un account di archiviazione Premium e copiare dischi rigidi virtuali dall'account di archiviazione Standard.
- Creare un nuovo servizio cloud e creare la macchina virtuale SQL2 con dischi premi archiviazione associati.
- Configurare ILB / ELB e aggiungere i punti finali.
- Aggiornare sempre nel comunicare ascoltatore con nuovo ILB / ELB IP indirizzo e il failover.
- Verificare la configurazione del DNS.
- Impostare il AFP SQL2, quindi eseguire la migrazione SQL1 ed elaborata passaggi da 2 a 5.
- Testare failover.
- Tornare la AFP a SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Appendice: Migrazione multisito sempre cluster allo spazio di archiviazione Premium

Il resto di questo argomento fornisce un esempio dettagliato della conversione di un cluster di sempre in più siti allo spazio di archiviazione Premium. Converte inoltre comunicare ascoltatore da tramite un servizio di bilanciamento del carico esterno (ELB) in un servizio di bilanciamento del carico interno (ILB).

### <a name="environment"></a>Ambiente

- Windows 2k 12 / SQL 2k 12
- File di database 1 su SP
- 2x pool di archiviazione per nodo

![Appendix1][11]

### <a name="vm"></a>MACCHINE VIRTUALI:

In questo esempio verranno illustrare il passaggio da un ELB a ILB. ELB era disponibile prima ILB, in modo che spiega come passare a questa durante la migrazione.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>I passaggi precedenti: Connettersi all'abbonamento

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Passaggio 1: Creare nuovi Account di archiviazione e servizio Cloud
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passaggio 2: Aumentare errori consentiti sulle risorse<Optional>
In alcune risorse che appartengono al sempre nella disponibilità gruppo esistono limiti il numero di errori che può verificarsi in un intervallo, dove il servizio cluster tenterà di riavviare il gruppo di risorse. Si consiglia di che aumentare questa mentre scorrere tramite questa procedura, poiché in caso contrario manualmente failover failover e trigger chiudendo computer che è possibile ottenere raggiunto questo limite.

Sarebbe opportuno doppio ammortizzato errore, per eseguire queste operazioni di gestione di Cluster di Failover, passare alle proprietà del gruppo di risorse sempre in:

![Appendix3][13]

Modificare gli errori di massimo a 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passaggio 3: Risorsa indirizzo IP di aggiunta del gruppo Cluster<Optional>

Se si dispone di un solo indirizzo IP del gruppo di Cluster e questo viene allineato alla subnet cloud, tenere presente, se si accidentalmente disconnettere tutti i nodi nel cloud in rete quindi la risorsa Cluster IP e nome di rete Cluster non sarà possibile portare in linea. In questo caso impedirà aggiornamenti ad altre risorse cluster.

#### <a name="step-4-dns-configuration"></a>Passaggio 4: Configurazione del DNS

Per implementare un passaggio uniforme transizione dipende dalla modalità DNS è utilizzato e vengono aggiornati.
Quando viene installato sempre attiva, viene creato un gruppo di risorse Cluster di Windows, se si apre Gestione Cluster di Failover, si noterà che almeno avere le tre risorse, i due che il documento fa riferimento a:

- Nome di rete virtuale (VNN)-questo è il nome DNS del client connettersi quando si desidera connettersi a SQL Server tramite sempre On.
- Risorsa indirizzo IP: questa è l'indirizzo IP associato il VNN, è possibile inserire più di un e una configurazione multisito si avrà un indirizzo IP per ogni sito/subnet.

Quando la connessione a SQL Server, il Client di SQL Server driver verrà recuperare i record DNS associati a comunicare ascoltatore e provare a connettersi a ogni sempre attiva associati indirizzo IP, sotto verranno illustrati alcuni fattori che possono influenzare seguente.

Il numero di record DNS simultanee sono associate con il nome di comunicare ascoltatore dipende non solo il numero di indirizzi IP associati, ma la ' RegisterAllIpProviders'setting nel Failover cluster per la risorsa sempre via VNN.

Quando si distribuisce sempre attiva in Azure sono disponibili diversi passaggi per creare le comunicare ascoltatore e indirizzi IP, è necessario configurare manualmente 'RegisterAllIpProviders' 1, è diverso da un locale attivato sempre nella distribuzione in cui è già impostato su 1.

Se 'RegisterAllIpProviders' è uguale a 0, si vedrà solo un record DNS nel DNS associato a comunicare ascoltatore:

![Appendix4][14]

Se 'RegisterAllIpProviders' 1:

![Appendix5][15]

Il codice riportato di seguito Scarica le impostazioni di VNN e impostare automaticamente, nota per la modifica abbia effetto è necessario disconnettere il VNN e trasformarla in linea, questo impiegando comunicare ascoltatore fuori rete causa problemi di connettività client.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

In un passaggio successivo di migrazione è necessario aggiornare comunicare ascoltatore sempre attive con un indirizzo IP aggiornato che fa riferimento un bilanciamento del carico, questa operazione comporta un rimozione della risorsa indirizzo IP e aggiunta. Dopo l'aggiornamento di indirizzi IP, è necessario assicurarsi che il nuovo indirizzo IP è stato aggiornato nella zona DNS e che il client di aggiornare la cache DNS locale.

Se i clienti si trovano in un segmento di rete diversa e fanno riferimento a un server DNS diverso, è necessario considerare cosa accade sui trasferimenti di zona DNS durante la migrazione, come l'applicazione riconnettere sarà ora vincolato da almeno fuso orario di trasferimento di tutti gli indirizzi IP di nuovo per comunicare ascoltatore. Se si è in seguito i limiti di tempo, è necessario discutere e testare forzare un trasferimento zona incrementale con il team di Windows e inserire anche il record host DNS per un inferiore durata (TTL), aggiornano i client in modo. Per ulteriori informazioni, vedere [I trasferimenti](https://technet.microsoft.com/library/cc958973.aspx) e [DnsServerZoneTransfer Start](https://technet.microsoft.com/library/jj649917.aspx).

Per impostazione predefinita il valore TTL per i Record DNS che è associato a comunicare ascoltatore nella sempre in Azure è 1200 secondi. Si consiglia di ridurre il numero se si è in orario di vincolo durante la migrazione per assicurarsi che i client aggiornare relativi nomi con l'indirizzo IP aggiornato comunicare ascoltatore. È possibile visualizzare e modificare la configurazione eseguendo il dump fuori la configurazione del VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Si noti, minore di 'HostRecordTTL', si verificherà una quantità maggiore di traffico DNS.

##### <a name="client-application-settings"></a>Impostazioni dell'applicazione client

Se l'applicazione client SQL supporta .net 4.5 SQLClient, sarà possibile utilizzare ' MULTISUBNETFAILOVER = TRUE' parola chiave, si consiglia di da applicare, che consente di connessione più veloce per SQL sempre nel gruppo di disponibilità durante il failover. Esso enumerati tutti gli indirizzi IP associati a comunicare ascoltatore sempre attiva in parallelo e si esegue una velocità di tentativi di connessione TCP maggiormente durante caso di errore.

Per ulteriori informazioni relative alle impostazioni precedenti, vedere [caratteristiche associati e parole chiave MultiSubnetFailover](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Vedere anche [Supporto SqlClient disponibilità elevata e ripristino](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Passaggio 5: Cluster le impostazioni di base

Come prevede di estrazione almeno un Server SQL verso il basso alla volta, è necessario modificare le impostazioni di base cluster se Usa controllo condivisione File (FSW) con 2 nodi, è necessario impostare la base per consentire maggior parte di nodo e utilizzare voto dinamico e per consentire per un singolo nodo rimanga permanente.


    Set-ClusterQuorum -NodeMajority  

Per ulteriori informazioni sulla gestione e la configurazione di base cluster, vedere [configurare e gestire la base in un Cluster di Failover di Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passaggio 6: Estrarre ACL ed endpoint esistenti
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Salvare i file di testo.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passaggio 7: Modificare le modalità di replica e Failover partner

Se si dispone di più di 2 SQL Server, è necessario modificare failover un altro database secondario in un altro controller di dominio o distribuzione locale di 'Sincrono' e renderlo un Partner di Failover automatico (AFP), in modo da mantenere HA mentre si apportano modifiche. È possibile farlo tramite TSQL di modificare attraverso SQL Server Management Studio:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passaggio 8: Rimuovere macchine Virtuali secondario dal servizio cloud

È necessario prevede di eseguire la migrazione di un nodo secondario cloud prima di tutto, se si tratta attualmente principale, è necessario avviare manuale caso di errore.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passaggio 9: Cambiare disco memorizzazione nella cache le impostazioni nel file CSV e salvare

Per indicare i volumi dati questi deve disporre di sola lettura.

Per indicare i volumi del log delle transazioni questi deve essere impostati su Nessuno.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Passaggio 10: Copia dischi rigidi VIRTUALI
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



È possibile controllare lo stato di copia di dischi rigidi virtuali per l'account di archiviazione Premium:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Attendere che tutte queste informazioni vengono registrate come esito positivo.

Per informazioni per ogni BLOB:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Passaggio 11: Disco registro del sistema operativo

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passaggio 12: Importare secondario nel nuovo servizio cloud

Il codice seguente utilizza inoltre aggiunta l'opzione qui è possibile importare la macchina e usare l'indirizzo VIP retainable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passaggio 13: Creare ILB nel nuovo Cloud Svc, aggiungere carico bilanciato i punti finali e ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Passaggio 14: Aggiornare sempre sul
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

A questo punto rimuovere il vecchio servizio cloud indirizzo IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Passaggio 15: Controllo degli aggiornamenti DNS

È ora deve verificare se i server DNS sono le reti di client di SQL Server e assicurarsi che il servizio cluster ha aggiunto il record host aggiuntive per l'indirizzo IP aggiunto. Se i server DNS non sono state aggiornate, forzare il trasferimento di zona DNS e assicurarsi che i client sono subnet sono in grado di risolvere sia sempre in indirizzi IP, in modo che non è necessario attendere che la replica DNS automatica.

#### <a name="step-16-reconfigure-always-on"></a>Passaggio 16: Riconfigurare sempre nella

A questo punto è attendere secondario il nodo che è stata eseguita la migrazione per completamente ripetere la sincronizzazione con il nodo locale e attivare replica icona del nodo e renderlo il AFP.  

#### <a name="step-17-migrate-second-node"></a>Passaggio 17: Eseguire la migrazione secondo nodo
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Passaggio 18: Modificare le impostazioni nel file CSV di cache del disco e salvare

Per indicare i volumi dati questi deve disporre di sola lettura.

Per indicare i volumi del log delle transazioni questi deve essere impostati su Nessuno.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passaggio 19: Creare nuovo Account di archiviazione indipendente per nodo secondario
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Passaggio 20: Copia dischi rigidi VIRTUALI
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


È possibile verificare lo stato di copia disco rigido virtuale per dischi rigidi virtuali tutti: ForEach ($disk in $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. EtichettaDisco $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Attendere che tutte queste informazioni vengono registrate come esito positivo.

Per informazioni per ogni BLOB: stato di blob induvidual #Check AzureStorageBlobCopyState Get-Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd"-contenitore $containerName-contesto $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Passaggio 21: Disco registro del sistema operativo
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passaggio 22: Aggiungere carico bilanciato i punti finali e ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Passaggio 23: Test failover

A questo punto, è consigliabile consentire il nodo migrato sincronizzare con il nodo sempre in locale, inserirlo in modalità di replica icona del e attendere finché non viene sincronizzato. Quindi il controllo da locale al primo nodo viene eseguita la migrazione, ovvero il AFP. Dopo aver funzionanti, modificarne l'ultimo nodo migrata alla AFP.

È necessario testare failover tra tutti i nodi ed eseguire se chaos test per assicurarsi che il lavoro failover come previsto in un indicato tempestivo.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passaggio 24: Ripristinare le impostazioni di base cluster / DNS TTL / Failover Pntrs / le impostazioni di sincronizzazione
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Aggiunta di risorsa indirizzo IP nella stessa Subnet

Se si hanno solo 2 SQL Server ed eseguirne la migrazione a un nuovo servizio cloud, ma desidera mantenerle nella stessa subnet, è possibile evitare impiegando comunicare ascoltatore offline per eliminare l'originale sempre in indirizzo IP e aggiungere il nuovo indirizzo IP. Se si esegue la migrazione di macchine virtuali a un'altra subnet non è necessario eseguire questa operazione come sarà una rete cluster aggiuntivi che fa riferimento a tale subnet.

Dopo aver portato secondario migrato e aggiungere la nuova risorsa indirizzo IP per il nuovo servizio cloud prima del failover principale esistente, è necessario eseguire queste operazioni all'interno del gestore Failover Cluster:

Per aggiungere l'indirizzo IP, vedere l' [Appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), passaggio 14.

1. Per la risorsa indirizzo IP corrente, modificare il proprietario possibili 'Esistente SQL Server primario', nell'esempio seguente, 'dansqlams4':

    ![Appendix13][23]

1. Per la nuova risorsa indirizzo IP, modificare il proprietario possibili 'Migrated SQL Server secondario', nell'esempio seguente, 'dansqlams5':

    ![Appendix14][24]

1. Una volta impostata questa operazione è possibile failover e quando viene eseguita la migrazione l'ultimo nodo i possibili proprietari possono essere modificati in modo che tale nodo viene aggiunto come possibile proprietario:

    ![Appendix15][25]

## <a name="additional-resources"></a>Risorse aggiuntive
- [Spazio di archiviazione di Azure Premium](../storage/storage-premium-storage.md)
- [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
