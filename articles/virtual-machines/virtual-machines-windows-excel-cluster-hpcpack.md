<properties
 pageTitle="Cluster HPC Pack per Excel e SOA | Microsoft Azure"
 description="Guida introduttiva in esecuzione su larga scala carichi di lavoro di Excel e SOA in un cluster HPC Pack in Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Guida introduttiva carichi di lavoro di Excel e SOA in esecuzione in un cluster HPC Pack in Azure

In questo articolo viene illustrato come distribuire un cluster di Microsoft HPC Pack in macchine virtuali di Azure utilizzando un modello di Guida introduttiva di Azure o facoltativamente uno script di distribuzione di Azure PowerShell. Il cluster utilizza le immagini di macchine Virtuali di Azure Marketplace progettate per l'esecuzione di Microsoft Excel o carichi di lavoro di architettura orientata ai servizi (SOA) con HPC Pack. È possibile utilizzare il cluster per eseguire semplici HPC di Excel e SOA servizi da un computer client locale. I servizi di Excel HPC includono carico di lavoro di Excel e le funzioni definite dall'utente di Excel o funzioni definite dall'utente.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Un alto livello, il diagramma seguente illustra il cluster HPC Pack create.

![Cluster HPC con nodi che eseguono carichi di lavoro di Excel][scenario]

## <a name="prerequisites"></a>Prerequisiti

*   **Computer client** , è necessario un computer basato su Windows client per inviare i processi di Excel e SOA esempio al cluster. È necessario anche un computer Windows eseguire lo script di distribuzione di Azure PowerShell cluster (se si sceglie il metodo di distribuzione) e

*   **Abbonamento azure** , se non si dispone di un abbonamento a Azure, è possibile creare un [account gratuiti](https://azure.microsoft.com/pricing/free-trial/) in solo un paio di minuti.

*   **Quota core** - è possibile aumentare la quota di core, soprattutto se si distribuiscono diversi nodi cluster con dimensioni macchine Virtuali multi-core. Se si utilizza un modello di Guida introduttiva di Azure, la quota di core in Gestione risorse è regione Azure. In questo caso, potrebbe essere necessario aumentare la quota di un'area specifica. Vedere [i vincoli, le quote e limiti di Azure abbonamento](../azure-subscription-service-limits.md). Per aumentare una quota, [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza costi aggiuntivi.

*   **Licenza di Microsoft Office** - se si distribuiscono nodi di calcolo con un'immagine di macchine Virtuali di Marketplace HPC Pack con Microsoft Excel, una versione di valutazione di 30 giorni di Microsoft Excel Professional Plus 2013 è installato. Al termine del periodo di valutazione, è necessario fornire una licenza di Microsoft Office valida per attivare Excel per continuare a eseguire carichi di lavoro. Vedere [attivazione di Excel](#excel-activation) più avanti in questo articolo. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Passaggio 1. Configurare un cluster HPC Pack in Azure

Ecco due opzioni per impostare il cluster: prima di tutto, utilizzando un modello di Guida introduttiva di Azure e Azure portale; e i secondi, usare uno script di distribuzione di Azure PowerShell.


### <a name="option-1-use-a-quickstart-template"></a>Opzione 1. Usare un modello di Guida introduttiva
Utilizzare un modello di Guida introduttiva di Azure distribuire un cluster HPC Pack nel portale di Azure in modo semplice e rapido. Quando si apre il modello nel portale, si ottiene un'interfaccia utente semplice in cui è immettere le impostazioni per il cluster. Ecco i passaggi. 

>[AZURE.TIP]Se si desidera, utilizzare un [modello di Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) che viene creato un cluster simile specificamente per carichi di lavoro di Excel. La procedura leggermente diversa da quanto segue.

1.  Visitare la [pagina dei modelli di creare un Cluster HPC su GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se si desidera iniziare a rivedere informazioni sul modello e il codice sorgente.

2.  Fare clic su **Distribuisci in Azure** per avviare una distribuzione con il modello nel portale di Azure.

    ![Distribuire modello Azure][github]

3.  Nel portale, seguire questa procedura per immettere i parametri del modello di cluster HPC.

    un. Nella pagina **parametri** immettere o modificare i valori per i parametri del modello. Fare clic sull'icona accanto a ogni impostazione per le informazioni della Guida. Valori di esempio vengono visualizzati nella schermata seguente. In questo esempio viene creato un cluster denominato *hpc01* nel dominio di *hpc.local* costituito da un nodo principale e 2 nodi di calcolo. I nodi di elaborazione vengono creati da un'immagine di macchine Virtuali Pack HPC che include Microsoft Excel.

    ![Immettere i parametri][parameters]

    >[AZURE.NOTE]Il nodo principale macchine Virtuali viene automaticamente creata dall' [immagine Marketplace più recente](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) di HPC Pack 2012 R2 in Windows Server 2012 R2. Attualmente l'immagine si basa su HPC Pack 2012 R2 aggiornamento 3.
    >
    >Calcolare che macchine virtuali di nodo vengono create dall'immagine più recente della famiglia nodo calcolo selezionato. Selezionare l'opzione **ComputeNodeWithExcel** per l'immagine di nodo calcolo HPC Pack più recente di che include una versione di valutazione di Microsoft Excel Professional Plus 2013. Per distribuire un cluster per le sessioni di SOA generale o per scaricare utente di Excel, scegliere l'opzione **ComputeNode** (senza sia installato Excel).

    b. Scegliere l'abbonamento.

    c. Creare un gruppo di risorse per il cluster, ad esempio *hpc01RG*.

    d. Scegliere un percorso per il gruppo di risorse, ad esempio centrale USA.

    e. Nella pagina **condizioni legali** , leggere le condizioni. Se l'utente acconsente, fare clic su **Acquista**. Quindi, dopo aver impostato i valori per il modello, fare clic su **Crea**.

4.  Al termine della distribuzione (in genere bastano circa 30 minuti), esportare il file di certificato cluster dal nodo principale cluster. In un passaggio successivo, si importa il certificato pubblico del computer client per l'autenticazione sul lato server per l'associazione HTTP protetto.

    un. Connettere il nodo principale da Desktop remoto dal portale di Azure.

     ![Connettersi a nodo principale][connect]

    b. Procedure standard in Gestione certificati per esportare il certificato di nodo principale (disponibile in certificato: \LocalMachine\My) senza la chiave privata. In questo esempio, esportare *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Esportare il certificato][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opzione 2. Utilizzare lo script HPC Pack IaaS distribuzione

Script di distribuzione HPC Pack IaaS fornisce un altro modo versatile per distribuire un cluster HPC Pack. Crea un cluster nel modello di distribuzione classico, mentre il modello viene utilizzato il modello di distribuzione di Manager delle risorse di Azure. Inoltre, lo script è compatibile con una sottoscrizione nel servizio di Azure globali o Azure Cina.

**Ulteriori prerequisiti**

* **Azure PowerShell** - [installare e configurare Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o versione successiva) nel computer client.

* **Script di distribuzione HPC Pack IaaS** - scaricare e decomprimere l'ultima versione di script dall' [Area Download Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verificare la versione dello script eseguendo `New-HPCIaaSCluster.ps1 –Version`. In questo articolo si basa sulla versione 4.5.0 o in un secondo momento dello script.

**Creare il file di configurazione**

 Script di distribuzione HPC Pack IaaS viene utilizzato un file di configurazione XML come input che descrive l'infrastruttura di cluster HPC. Per distribuire un cluster costituito da un nodo principale e 18 nodi di calcolo creati dall'immagine nodo calcolo che include Microsoft Excel, sostituire i valori per l'ambiente nel file di configurazione di esempio seguente. Per ulteriori informazioni sul file di configurazione, vedere il file Manual.rtf nella cartella script e [creare un cluster HPC con lo script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Note relative al file di configurazione**

* **VMName** del nodo principale **deve** essere lo stesso **nome**o i processi SOA esito negativo per l'esecuzione.

* Assicurarsi che si specifichi **EnableWebPortal** in modo che il certificato di nodo principale viene generato ed esportato.

* Il file specifica uno script di PowerShell la configurazione delle impostazioni PostConfig.ps1 che viene eseguito sul nodo principale. Script di esempio seguente consente di configurare la stringa di connessione di archiviazione Azure, rimuove il ruolo di nodo elaborazione dal nodo principale e visualizza tutti i nodi online quando vengono distribuiti. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Eseguire lo script**

1.  Aprire la console di PowerShell nel computer client come amministratore.

2.  Passare alla cartella di script (E:\IaaSClusterScript in questo esempio).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Per distribuire cluster HPC Pack, eseguire il comando seguente. In questo esempio si presuppone che il file di configurazione si trova in E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

Script di distribuzione HPC Pack viene eseguita per un certo periodo. Una cosa che significa lo script è esportare e scaricare il certificato di cluster e salvarla nella cartella documenti dell'utente corrente nel computer client. Lo script genera un messaggio simile al seguente. In un passaggio seguente, si importerà il certificato nell'archivio certificati appropriato.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Passaggio 2. Scaricare le cartelle di lavoro di Excel ed eseguire funzioni definite dall'utente da un client locale

### <a name="excel-activation"></a>Attivazione di Excel

Quando si usa l'immagine ComputeNodeWithExcel VM per carichi di lavoro di produzione, è necessario fornire una chiave di licenza Microsoft Office valida per attivare i nodi di calcolo di Excel. In caso contrario, la versione di valutazione di Excel scade dopo 30 giorni e in esecuzione le cartelle di lavoro di Excel non verrà completato con COMException (0x800AC472). 

È possibile riattivare periodo di prova Excel per un'altra 30 giorni del periodo di valutazione: accedere al nodo principale e clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` su Excel tutti i nodi tramite HPC Cluster Manager di calcolo. È possibile riattivare periodo di prova un massimo di due volte. In seguito, è necessario specificare una chiave di licenza di Office valida.

Office Professional Plus 2013 siano installati nello immagine macchine Virtuali è un contratto multilicenza con generico Volume License chiave (GVLK). È possibile attivare tramite servizio di gestione delle chiavi (KMS) / Active Directory-Based attivazione (Active Directory BA) o l'attivazione chiave Multipla. 

    * Per utilizzare KMS/Active Directory-BA, utilizzare un server KMS esistente oppure impostare uno nuovo utilizzando Microsoft Office 2013 Volume License Pack. (Se si desidera, impostare il server sul nodo principale.) Quindi, attivare il codice di host KMS tramite Internet o telefono. Quindi clusrun `ospp.vbs` per impostare il server KMS e la porta e attivare Office in tutti nodi di calcolo di Excel. 
    
    * Usare Product, clusrun prima `ospp.vbs` per il tasto di input e quindi attivare tutti nodi tramite Internet o telefono di calcolo di Excel. 

>[AZURE.NOTE]Codici product key Retail per Office Professional Plus 2013 non può essere utilizzato con questa immagine macchine Virtuali. Se si dispone di chiavi valide ed elementi multimediali di installazione per le edizioni di Office o Excel diverso da questo contratto multilicenza Office Professional Plus 2013, è possibile utilizzare le risorse. Prima di tutto disinstallare questo contratto multilicenza e installare la versione in uso. È possibile acquisire reinstallato nodo di calcolo di Excel come immagine macchine Virtuali personalizzata da utilizzare in una distribuzione in scala.

### <a name="offload-excel-workbooks"></a>Scaricare le cartelle di lavoro di Excel

Seguire questi passaggi per scaricare una cartella di lavoro di Excel in modo che venga eseguito su cluster HPC Pack in Azure. A questo scopo è necessario disporre di Excel 2010 o 2013 già installato nel computer client.

1. Usare una delle opzioni nel passaggio 1 per distribuire un cluster HPC Pack con Excel per calcolare l'immagine di nodo. Ottenere il file di certificato cluster (. cer) e cluster nome utente e la password.

2. Nel computer client, importare il certificato di cluster in certificato: \CurrentUser\Root.

3. Assicurarsi che sia installato Excel. Creare un file Excel.exe.config con il seguente contenuto nella stessa cartella in Excel.exe nel computer client. In questo modo che il componente aggiuntivo COM di Excel di HPC Pack 2012 R2 caricato correttamente.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configurare il client per inviare i processi a cluster HPC Pack. Un'opzione possibile consiste completa [installazione HPC Pack 2012 R2 aggiornamento 3](http://www.microsoft.com/download/details.aspx?id=49922) di scaricare e installare il client di HPC Pack. In alternativa, scaricare e installare l' [utilità client HPC Pack 2012 R2 aggiornamento 3](https://www.microsoft.com/download/details.aspx?id=49923) e appropriato Visual C++ 2010 redistributable per il computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  In questo esempio viene utilizzata una cartella di lavoro di esempio denominata ConvertiblePricing_Complete.xlsb. È possibile scaricare [qui](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copiare la cartella di lavoro di Excel in una cartella di lavoro, ad esempio D:\Excel\Run.

7.  Aprire la cartella di lavoro di Excel. Sulla barra multifunzione di **elaborazione** , fare clic su **Componenti aggiuntivi COM** e verificare che il componente aggiuntivo COM di Excel HPC Pack è stato caricato correttamente.

    ![Componente aggiuntivo per il pacchetto HPC di Excel][addin]

8.  Modificare la macro VBA HPCControlMacros in Excel modificando le righe di commento, come illustrato nella seguente script. Sostituire i valori appropriati per l'ambiente.

    ![Macro di Excel per HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copiare la cartella di lavoro di Excel in una directory di upload, ad esempio D:\Excel\Upload. Questa directory viene specificata nella costante di HPC_DependsFiles nella macro VBA.

10. Per eseguire la cartella di lavoro in cluster di Azure, fare clic sul pulsante **Cluster** nel foglio di lavoro.

### <a name="run-excel-udfs"></a>Eseguire funzioni definite dall'utente di Excel

Per eseguire funzioni definite dall'utente di Excel, seguire i passaggi precedenti da 1 a 3 per configurare il computer client. Per Excel funzioni definite dall'utente, non è necessario che l'applicazione di Excel installata nodi di calcolo. Pertanto, durante la creazione del cluster nodi di calcolo, è possibile scegliere una normale calcolare invece l'immagine di nodo calcolo di Excel.

>[AZURE.NOTE] Esiste un limite di 34 caratteri in Excel 2010 e finestra di dialogo connettore cluster 2013. Utilizzare questa finestra di dialogo per specificare il cluster che esegue le funzioni definite dall'utente. Se il nome completo del cluster è più lungo (ad esempio hpcexcelhn01.southeastasia.cloudapp.azure.com), non rientra nella finestra di dialogo. La soluzione consiste nell'impostare una variabile a livello di computer, ad esempio *CCP_IAASHN* con il valore del nome del cluster lungo. Immettere quindi *CCP_IAASHN %* nella finestra di dialogo come nome del nodo principale cluster. 

Dopo il cluster verrà distribuito correttamente, continuare con la procedura seguente per l'esecuzione di un campione incorporato utente di Excel. Per personalizzate Excel funzioni definite dall'utente, vedere queste [risorse](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) per creare il XLL e distribuirle cluster IaaS.

1.  Aprire una nuova cartella di lavoro di Excel. Sulla barra multifunzione **sviluppare** fare clic su **Componenti aggiuntivi**. Quindi, nella finestra di dialogo fare clic su **Sfoglia**, passare alla cartella %CCP_HOME%Bin\XLL32 e selezionare l'esempio ClusterUDF32.xll. Se il ClusterUDF32 non esiste nel computer client, copiarlo nella cartella %CCP_HOME%Bin\XLL32 nodo principale.

    ![Selezionare l'utente][udf]

2.  Fare clic su **File** > **Opzioni** > **Avanzate**. In **formule**, selezionare **Consenti funzioni XLL definite dall'utente per eseguire un cluster di elaborazione**. Quindi fare clic su **Opzioni** e immettere il nome completo del cluster in **nome Cluster nodo principale**. (Come indicato in precedenza la casella di input è limitata ai 34 caratteri, in modo che un nome lungo cluster potrebbe non essere adatto. È possibile utilizzare una variabile di livello un nome cluster lungo.)

    ![Configurare l'utente][options]

3.  Per eseguire il calcolo utente sul cluster, fare clic sulla cella con valore =XllGetComputerNameC() e premere INVIO. La funzione recupera semplicemente il nome del nodo di calcolo sul quale viene eseguito l'utente. Per la prima esecuzione, una finestra di dialogo credenziali richiede il nome utente e la password per connettersi a cluster IaaS.

    ![Eseguire l'utente][run]

    Quando sono presenti più celle per calcolare, premere Alt-Maiusc-Ctrl + F9 per eseguire il calcolo in tutte le celle.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Passaggio 3. Eseguire un carico di lavoro SOA da un client locale

Per eseguire le applicazioni SOA generali su cluster HPC Pack IaaS, usare uno dei metodi nel passaggio 1 per distribuire il cluster. Specificare un generico calcolare immagine nodo in questo caso, perché non è necessario Excel sui nodi di calcolo. Seguire la procedura seguente.

1. Dopo avere recuperato il certificato di cluster, importarlo nel computer client in certificato: \CurrentUser\Root.

2. Installare [HPC Pack 2012 R2 aggiornamento 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) e [utilità client HPC Pack 2012 R2 aggiornamento 3](https://www.microsoft.com/download/details.aspx?id=49923). Questi strumenti consentono di sviluppare ed eseguire le applicazioni client SOA.

3. Scaricare il HelloWorldR2 [codice di esempio](https://www.microsoft.com/download/details.aspx?id=41633). Aprire la HelloWorldR2.sln in Visual Studio 2010 o 2012.

4. Compilare il progetto EchoService prima di tutto. Quindi, distribuire il servizio cluster IaaS nello stesso modo che si distribuisce a un cluster locale. Per informazioni dettagliate, vedere Leggimi in HelloWordR2. Modificare e compilare il HellWorldR2 e altri progetti come descritto nella sezione seguente per generare le applicazioni client SOA che eseguono in un cluster di Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Usare binding Http con coda di archiviazione Azure

Per utilizzare l'associazione Http a una coda di archiviazione Azure, apportare alcune modifiche al codice di esempio.

* Aggiornare il nome del cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Facoltativamente, è possibile usare il valore predefinito TransportScheme in SessionStartInfo o impostarlo in modo esplicito a Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utilizzare l'associazione predefinita per il BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    O impostare in modo esplicito utilizzando basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Facoltativamente, è possibile impostare il flag UseAzureQueue su true in SessionStartInfo. Se non è impostata, verrà impostato true per impostazione predefinita quando il nome del cluster contiene suffissi Azure e la TransportScheme è Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Utilizzare l'associazione Http senza coda di archiviazione Azure

Per utilizzare in modo esplicito binding Http senza una coda di archiviazione Azure, impostare il contrassegno UseAzureQueue su false nel SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utilizzare l'associazione NetTcp

Per utilizzare l'associazione NetTcp, la configurazione è simile alla connessione a un cluster locale. È necessario aprire alcuni endpoint sul nodo principale macchine Virtuali. Se lo script di distribuzione HPC Pack IaaS sono usate per creare il cluster, ad esempio, impostare i punti finali nel portale di classica Azure come indicato di seguito.


1. Interrompere la macchina virtuale.

2. Aggiungere le porte TCP 9090, 9087, 9091, 9094 per la sessione del Broker, del Broker lavoro e i servizi di dati, rispettivamente

    ![Configurare i punti finali][endpoint]

3. Avviare la macchina virtuale.

L'applicazione client SOA non richiede alcuna modifica ad eccezione di modificare il nome per il nome completo del cluster IaaS testa.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [queste risorse](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) per ulteriori informazioni sull'esecuzione di carichi di lavoro di Excel con HPC Pack.

* Per ulteriori informazioni sulla distribuzione e gestione dei servizi SOA con HPC Pack, vedere [Gestione servizi SOA in Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) .

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
