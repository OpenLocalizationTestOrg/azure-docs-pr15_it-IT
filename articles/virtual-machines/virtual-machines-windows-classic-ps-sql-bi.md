<properties
    pageTitle="Business Intelligence di SQL Server | Microsoft Azure"
    description="In questo argomento utilizza risorse create con il modello di distribuzione classica e descrive le caratteristiche di Business Intelligence (BI) disponibili per SQL Server in esecuzione in Azure macchine ()."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Business Intelligence di SQL Server in macchine virtuali di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

La raccolta di Microsoft Azure Virtual Machine include le immagini che contengono le installazioni di SQL Server. Edizioni di SQL Server supportate in queste immagini sono gli stessi file di installazione che è possibile installare al computer locale e macchine virtuali. In questo argomento sono riepilogate le funzionalità di SQL Server Business Intelligence (BI) installata le immagini e i passaggi di configurazione necessari dopo una macchina virtuale viene eseguito il provisioning. In questo argomento viene inoltre topologie di distribuzione supportati per le caratteristiche di Business Intelligence e procedure consigliate.

## <a name="license-considerations"></a>Considerazioni sulle licenze

Esistono due modi per SQL Server in macchine virtuali di Microsoft Azure licenza:

1. Vantaggi di mobilità licenza che fanno parte di Software Assurance. Per ulteriori informazioni, vedere [Mobilità licenza tramite Software Assurance su Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Una tariffa per tariffa oraria di macchine virtuali di Azure con SQL Server. Vedere la sezione "SQL Server" in [Macchine virtuali di prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Per ulteriori informazioni sulla gestione delle licenze e corrente le tariffe, vedere [Domande frequenti sulla gestione delle licenze di macchine virtuali](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Immagini SQL Server disponibili nella raccolta di computer virtuale Azure

La raccolta di Microsoft Azure Virtual Machine include diverse immagini che contengono Microsoft SQL Server. Il software installato su immagini macchina virtuale varia in base alla versione del sistema operativo e la versione di SQL Server. L'elenco delle immagini disponibili nella raccolta di Azure macchina virtuale vengono modificati frequentemente.

![Immagine SQL azure raccolta macchine Virtuali](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Script di PowerShell seguente restituisce l'elenco di Azure immagini che contengono "SQL Server" nel ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Per ulteriori informazioni sulle edizioni e sulle funzionalità supportate da SQL Server, vedere le operazioni seguenti:

- [Edizioni di SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Funzionalità supportate dalle edizioni di SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Caratteristiche di Business Intelligence installate SQL Server virtuale Machine raccolta immagini

Nella tabella seguente sono riepilogate le funzionalità di Business Intelligence installate le immagini di raccolta Microsoft Azure Virtual Machine comuni per SQL Server"

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 Standard

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 Standard

|Funzionalità di Business Intelligence di SQL Server|Installato sull'immagine della raccolta|Note|
|---|---|---|
|**Reporting Services in modalità nativa**|Sì|Installato ma è necessario configurazione, inclusi l'URL di gestione di report. Vedere la sezione [Configurare Reporting Services](#configure-reporting-services).|
|**Modalità di SharePoint di Reporting Services**|No|Immagine della raccolta di Microsoft Azure Virtual Machine non include SharePoint o SharePoint file di installazione. <sup>1</sup>|
|**Data mining multidimensionali di Analysis Services e dati OLAP)**|Sì|Installato e configurato come l'istanza di Analysis Services predefinito|
|**Tabulare di Analysis Services**|No|Supportata in SQL Server 2012, 2014 e 2016 immagini ma non è installato per impostazione predefinita. Installare un'altra istanza di Analysis Services. Vedere la sezione installare altri servizi di SQL Server e funzionalità nel corso dell'argomento.|
|**Analysis Services PowerPivot per SharePoint**|No|Immagine della raccolta di Microsoft Azure Virtual Machine non include SharePoint o SharePoint file di installazione. <sup>1</sup>|

<sup>1</sup> per ulteriori informazioni su SharePoint e macchine virtuali di Azure, vedere [Microsoft Azure architetture per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Distribuzione di SharePoint in macchine virtuali di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Eseguire il seguente comando di PowerShell per ottenere un elenco dei servizi installati che contengono "SQL" nel nome del servizio.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Consigli e procedure consigliate

- La dimensione minima consigliata per una macchina virtuale: **A3** quando si utilizza SQL Server Enterprise Edition. Le dimensioni di macchina virtuale **A4** sono consigliabile per le distribuzioni di Business Intelligence di SQL Server Analysis Services e Reporting Services.

    Per informazioni sulle dimensioni delle macchine Virtuali corrente, vedere [Le dimensioni di macchina virtuale per Azure](virtual-machines-linux-sizes.md).

- Procedure consigliate per la gestione del disco rigido sono per archiviare dati, registro e file di backup su unità diversa da quella **C**: e **D**:. Ad esempio, creare dischi di dati **E**: e **F**:.

    - Unità di memorizzazione nella cache dei criteri per l'unità predefinita **C**: non è ottimale per l'utilizzo di dati.

    - **D**: unità è temporaneo utilizzati principalmente per il file della pagina. **D**: unità non viene mantenuto e non viene salvato in archiviazione blob. Gestione attività come modificarne le dimensioni di macchina virtuale ripristino **D**: unità. È consigliabile **non** usare **D**: unità per i file di database, inclusi tempdb.

    Per ulteriori informazioni sulla creazione e associare dischi, informazioni su [come allegare un disco di dati a una macchina virtuale](virtual-machines-windows-classic-attach-disk.md).

- Interrompere o disinstallare i servizi che non si prevede di utilizzare. Per esempio se la macchina virtuale viene utilizzata esclusivamente per Reporting Services, interrompere o disinstallare Analysis Services e SQL Server Integration Services. Nella figura seguente è illustrato un esempio dei servizi avviati per impostazione predefinita.

    ![Servizi di SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] Motore di database di SQL Server è necessario negli scenari di Business Intelligence supportati. In un server topologia macchine Virtuali, il motore di database deve essere in esecuzione nella stessa macchina virtuale.

    Per ulteriori informazioni, vedere le operazioni seguenti: [Disinstallare Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [Disinstalla un'istanza di Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Selezionare **Windows Update** 'importanti aggiornamenti'. Le immagini di Microsoft Azure Virtual Machine spesso vengono aggiornate; Tuttavia gli aggiornamenti importanti Impossibile diventano disponibili da **Windows Update** dopo l'ultimo aggiornamento di immagine macchine Virtuali.

## <a name="example-deployment-topologies"></a>Esempi di topologie di distribuzione

Di seguito sono distribuzioni di esempio che utilizzano macchine virtuali di Microsoft Azure. Topologie in questi diagrammi sono solo alcune delle tipologie possibili che è possibile utilizzare con le caratteristiche di Business Intelligence di SQL Server e macchine virtuali di Microsoft Azure.

### <a name="single-virtual-machine"></a>Sola macchina virtuale

Analysis Services, Reporting Services, motore di Database di SQL Server e le origini dati in una singola macchina virtuale.

![scenario di BI IAS con 1 macchina virtuale](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Due macchine virtuali

- Analysis Services, Reporting Services e il motore di Database SQL Server in una singola macchina virtuale. La distribuzione include i database di server di report.

- Origini dati in una seconda macchina virtuale. La seconda macchina virtuale include il motore di Database di SQL Server come origine dati.

![scenario di BI iaas con 2 macchine virtuali](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure misto – dati nel database di SQL Azure

- Analysis Services, Reporting Services e il motore di Database SQL Server in una singola macchina virtuale. La distribuzione include i database di server di report.

- Origine dati è database SQL Azure.

![macchine virtuali di scenari di BI iaas e AzureSQL come origine dati](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Ibrido-dati locali

- In questo esempio di distribuzione Analysis Services, Reporting Services e il motore di Database di SQL Server eseguire su un singolo computer virtuale. La macchina virtuale ospita i database di server di report. La macchina virtuale fa parte di un dominio locale tramite la rete virtuale Azure o altri tunneling soluzione VPN.

- Origine dati è locale.

![macchine virtuali di scenari di BI iaas e sulle origini dati locali](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configurazione di Reporting Services in modalità nativa

Immagine della raccolta la macchina virtuale per SQL Server include Reporting Services nativa installato, ma non è configurato il server di report. La procedura descritta in questa sezione Configura il server di report di Reporting Services. Per informazioni più dettagliate sulla configurazione di Reporting Services nativa, vedere [Installare Reporting Services nativo modalità Report Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] I contenuti simili che utilizza script di Windows PowerShell per configurare il server di report, vedere [Usare PowerShell per creare un Azure macchine Virtuali con un Server in modalità nativa Report](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Connettere la macchina virtuale e avviare Gestione configurazione di Reporting Services

Esistono due flussi di lavoro comuni per la connessione a una macchina virtuale Azure:

- Per connettersi in, fare clic sul nome della macchina virtuale e quindi fare clic su **Connetti**. Viene aperta una connessione desktop remoto e viene inserito automaticamente il nome del computer.

    ![connettersi a macchina virtuale azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Connettere la macchina virtuale con connessione Desktop remoto di Windows. Nell'interfaccia utente del desktop remoto:

    1. Digitare il **nome del servizio cloud** come nome del computer.

    1. Digitare i due punti (:) e il numero di porta pubblico configurato per l'endpoint di desktop remoto TCP.

        MyService.cloudapp.NET:63133

        Per ulteriori informazioni, vedere [che cos'è un servizio cloud?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Avviare Gestione configurazione Reporting Services.**

1. In **Windows Server 2012**:

1. Dalla schermata **Start** digitare **Reporting Services** per visualizzare un elenco di App.

1. Pulsante destro del mouse **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.

1. In **Windows Server 2008 R2**:

1. Fare clic su **Start**e quindi fare clic su **Tutti i programmi**.

1. Fare clic su **Microsoft SQL Server 2016**.

1. Fare clic su **Strumenti di configurazione**.

1. Pulsante destro del mouse **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.

O

1. Fare clic su **Start**.

1. Nella finestra di dialogo **Cerca programmi e file** digitare **reporting services**. Se la macchina virtuale è in esecuzione Windows Server 2012, digitare **reporting services** nella schermata Start di Windows Server 2012.

1. Pulsante destro del mouse **Gestione configurazione Reporting Services** e scegliere **Esegui come amministratore**.

    ![cercare Gestione configurazione di ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurare Reporting Services

**URL del servizio web e account di servizio:**

1. Verificare che il **Nome del Server** sia il nome del server locale e fare clic su **Connetti**.

1. Annotare il **Nome di Database Server di Report**vuoto. Dopo avere completato la configurazione, verrà creato il database.

1. Verificare **Lo stato del Server di Report** è impostato su **avviato**. Se si desidera verificare che il servizio in Windows Server Manager, il servizio è il servizio di Windows di **SQL Server Reporting Services** .

1. Fare clic su **Account di servizio** e cambiare l'account in base alle esigenze. Se la macchina virtuale viene utilizzata in un ambiente di join non di dominio, l'account predefinito **ReportServer** è sufficiente. Per ulteriori informazioni sull'account di servizio, vedere [Account del servizio](https://msdn.microsoft.com/library/ms189964.aspx).

1. Fare clic su **URL del servizio Web** nel riquadro sinistro.

1. Fare clic su **Applica** per configurare i valori predefiniti.

1. Nota gli **URL di servizi Web di Server di Report**. Nota la porta TCP predefinita è 80 e parte dell'URL. In un passaggio successivo è creare un Endpoint di macchina virtuale Microsoft Azure per la porta.

1. Nel riquadro **dei risultati** , verificare le azioni che è state completate correttamente.

**Database:**

1. Fare clic su **Database** nel riquadro sinistro.

1. Fare clic su **Cambia Database**.

1. Verificare che sia selezionato **Crea un nuovo database del server di report** e quindi fare clic su Avanti.

1. Verificare il **Nome del Server** e fare clic su **Test connessione**.

1. Se il risultato è **Test connessione ha avuto esito positivo**, fare clic su **OK** e quindi fare clic su **Avanti**.

1. Nota il nome del database è **ReportServer** e la **modalità del Server di Report** è **nativo** , quindi fare clic su **Avanti**.

1. Fare clic su **Avanti** nella pagina delle **credenziali** .

1. Fare clic su **Avanti** nella pagina **Riepilogo** .

1. Fare clic su **Avanti** nella pagina **stato di avanzamento e fine** .

**Web URL del portale o URL gestione Report per 2012 e 2014:**

1. Fare clic su **URL portale Web**o **URL gestione Report** per 2014 e 2012, nel riquadro sinistro.

1. Fare clic su **Applica**.

1. Nel riquadro **dei risultati** , verificare le azioni che è state completate correttamente.

1. Fare clic su **Esci**.

Per informazioni sulle autorizzazioni di server di report, vedere [Concessione di autorizzazioni in un Server di Report in modalità nativa](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Passare a gestione Report locale

Per verificare la configurazione, passare alla gestione di report nella macchina virtuale.

1. In macchine Virtuali, avviare Internet Explorer con privilegi di amministratore.

1. Passare a http://localhost/reports nella macchina virtuale.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Per connettersi a portale web remoto o gestione Report per 2014 e 2012

Se si desidera connettersi al portale web o gestione Report per 2014 e 2012, sul computer virtuale da un computer remoto, creare una nuova macchina virtuale Endpoint TCP. Per impostazione predefinita, il server di report in attesa per le richieste HTTP sulla **porta 80**. Se si configura l'URL del server di report per l'utilizzo di una porta diversa, è necessario specificare il numero di porta nelle istruzioni seguenti.

1. Creare un Endpoint per la macchina virtuale di porta TCP 80. Per ulteriori informazioni, vedere la sezione [endpoint macchina virtuale e porte del Firewall](#virtual-machine-endpoints-and-firewall-ports) in questo documento.

1. Aprire la porta 80 nel firewall della macchina virtuale.

1. Passare al portale web o Gestione report, utilizzando Azure Virtual Machine **Nome DNS** come nome del server nell'URL. Per esempio:

    **Server di report**: http://uebi.cloudapp.net/reportserver  **portale Web**: http://uebi.cloudapp.net/reports

    [Configurare un Firewall per accedere al Server Report](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Per creare e pubblicare report per la macchina virtuale Azure

Nella tabella seguente sono riepilogate alcune delle opzioni disponibili per pubblicare i rapporti esistenti da un computer locale al server di report ospitato in Microsoft Azure Virtual Machine:

- **Generatore report**: la macchina virtuale include fare clic su-una volta sola versione di Generatore Report di Microsoft SQL Server 2012 e SQL 2014. Per Report Generatore la prima data di inizio del computer virtuale con 2016 SQL:

    1. Avviare il browser con privilegi di amministratore.

    1. Passare al portale web, sul computer virtuale e selezionare l'icona di **Download** in alto a destra.
    
    1. Selezionare **Generatore Report**.

    Per ulteriori informazioni, vedere [Avviare Generatore Report](https://msdn.microsoft.com/library/ms159221.aspx).

- **SQL Server Data Tools**: macchine Virtuali: SQL Server Data Tools è installato nel computer virtuale e può essere utilizzato per creare **Progetti Server di Report** e report nel computer virtuale. SQL Server Data Tools possono pubblicare i report nel server di report sul computer virtuale.

- **SQL Server Data Tools: Remote**: nel computer locale, creare un progetto di Reporting Services in SQL Server Data Tools che contiene i report di Reporting Services. Configurare il progetto a cui connettersi l'URL del servizio web.

    ![proprietà del progetto SSDT per progetto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Creare un. Disco rigido virtuale disco rigido che contiene i report e quindi carica e allega l'unità.

    1. Creare un. Disco rigido disco rigido virtuale nel computer locale che contiene i report.

    1. Creare e installare un certificato di gestione.

    1. Caricare il file disco rigido virtuale in Azure utilizzando il cmdlet Aggiungi AzureVHD [creare e caricare un disco rigido virtuale di Windows Server in Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Collegare il disco alla macchina virtuale.

## <a name="install-other-sql-server-services-and-features"></a>Installare altri servizi di SQL Server e le funzionalità

Per installare servizi di SQL Server aggiuntivi, ad esempio Analysis Services in modalità tabulare, eseguire la configurazione guidata SQL server. I file di configurazione sono sul disco locale del computer virtuale.

1. Fare clic su **Start** e quindi fare clic su **Tutti i programmi**.

1. Fare clic su **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** o **Microsoft SQL Server 2012** e quindi fare clic su **Strumenti di configurazione**.

1. Fare clic su **Centro di installazione di SQL Server**.

O eseguire C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe o C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] La prima volta che si esegue l'installazione di SQL Server, più file di installazione è possibile scaricare e richiedono il riavvio del computer virtuale e il riavvio del programma di installazione di SQL Server.
>
>Se è necessario personalizzare ripetutamente l'immagine selezionata da Microsoft Azure Virtual Machine, è consigliabile creare la propria immagine di SQL Server. Funzionalità di Analysis Services SysPrep è stata attivata con SQL Server 2012 SP1 CU2. Per ulteriori informazioni, vedere [Considerazioni per l'installazione di SQL Server tramite SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [Supporto tecnico Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="to-install-analysis-services-tabular-mode"></a>Per installare modalità tabulare di Analysis Services

La procedura descritta in questa sezione **riepilogare** l'installazione della modalità tabulare di Analysis Services. Per ulteriori informazioni, vedere le operazioni seguenti:

- [Installazione di Analysis Services in modalità tabulare](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modellazione tabulare (Adventure Works esercitazione)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Per installare modalità tabulare di Analysis Services:**

1. Nella procedura guidata installazione di SQL Server, fare clic su **installazione** nel riquadro a sinistra e quindi fare clic su **installazione autonoma di nuovo SQL server o aggiungere funzionalità a un'installazione esistente**.

    - Se viene visualizzato **Sfoglia per cartelle**, passare a c:\SQLServer_13.0_full, c:\SQLServer_12.0_full o c:\SQLServer_11.0_full e quindi fare clic su **Ok**.

1. Fare clic su **Avanti** nella pagina aggiornamenti del prodotto.

1. Nella pagina **Tipo** di installazione, selezionare **Esegui nuova installazione di SQL Server** e fare clic su **Avanti**.

1. Nella pagina **Configurazione ruolo** , fare clic su **Caratteristiche di installazione di SQL Server**.

1. Nella pagina **Selezione delle caratteristiche** , fare clic su **Analysis Services**.

1. Nella pagina **Configurazione istanza** digitare un nome descrittivo, ad esempio **tabulare** nelle caselle di testo **Istanza denominata** e **Id istanza** .

1. Nella pagina **Configurazione di Analysis Services** , selezionare **La modalità tabulare**. Aggiungere l'utente corrente all'elenco di autorizzazioni amministrative.

1. Completare e chiudere la procedura guidata installazione di SQL Server.

## <a name="analysis-services-configuration"></a>Configurazione di Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Accesso remoto al server Analysis Services

Server Analysis Services supporta solo l'autenticazione di windows. Per accedere a Analysis Services in modalità remota dalle applicazioni client, ad esempio SQL Server Management Studio o SQL Server Data Tools, la macchina virtuale deve essere aggiunto al proprio dominio locale, tramite la rete virtuale Azure. Per ulteriori informazioni, vedere [Virtuali Azure](../virtual-network/virtual-networks-overview.md).

Un' **istanza predefinita** di Analysis Services in ascolto sulla porta TCP **2383**. Aprire la porta nel firewall macchine virtuali. Un'istanza denominata cluster di Analysis Services in ascolto anche sulla porta **2383**.

Per un' **istanza** di Analysis Services, è necessario il servizio SQL Server Browser per gestire l'accesso porta. La configurazione di SQL Server Browser predefinito è porta **2382**.

Nel firewall macchine virtuali aprire porta **2382** e creare un statico Analysis Services denominato porta istanza.

1. Per verificare le porte già in uso la macchina virtuale e il processo utilizza le porte, eseguire il comando seguente con privilegi di amministratore:

        netstat /ao

1. Usare SQL Server Management Studio per creare un statica di Analysis Services denominato porta istanza aggiornando 'Porta' valore AS tabulare istanza proprietà generali. Per ulteriori informazioni, vedere "utilizzare una porta fissa per un valore predefinito dell'istanza denominata" in [configurare Windows Firewall per consentire l'accesso di Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Riavviare l'istanza tabulare del servizio di Analysis Services.

Per ulteriori informazioni, vedere la sezione **endpoint macchina virtuale e porte del Firewall** in questo documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>I punti finali macchina virtuale e porte del Firewall

In questa sezione vengono riepilogati i punti finali macchina virtuale di Microsoft Azure per creare e porte da aprire nei firewall macchina virtuale. Nella tabella seguente vengono riepilogate le porte **TCP** per gli endpoint per creare e le porte da aprire nel firewall macchine virtuali.

- Se si utilizza una macchina virtuale singola e due elementi seguenti sono veri, non è necessario creare i punti finali macchine Virtuali e non è necessario aprire le porte nel firewall nella macchina virtuale.

    - Non è in modalità remota connettersi alle caratteristiche di SQL Server nella macchina virtuale. Stabilire una connessione desktop remoto per la macchina virtuale e accedere alle funzionalità di SQL Server in locale nella macchina virtuale non è considerato una connessione remota alle caratteristiche di SQL Server.

    - Non si aggiunge la macchina virtuale a un dominio locale tramite la rete virtuale Azure o un'altra soluzione tunneling VPN.

- Se la macchina virtuale non fa parte di un dominio ma si desidera in remoto connettersi alle caratteristiche di SQL Server in macchine Virtuali:

    - Aprire le porte nel firewall nella macchina virtuale.

    - Creare endpoint macchina virtuale per le porte indicate (*).

- Se la macchina virtuale fa parte di un dominio tramite un tunnel VPN, ad esempio la rete virtuale di Azure, i punti finali non sono necessari. Tuttavia aprire le porte nel firewall nella macchina virtuale.

  	|Porta|Tipo|Descrizione|
|---|---|---|
|**80**|TCP|Server di report accesso remoto (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|SQL Server Browser. Questa operazione è necessaria una macchina virtuale in parte di un dominio.|
|**2382**|TCP|SQL Server Browser.|
|**2383**|TCP|Istanza predefinita di SQL Server Analysis Services e raggruppate istanze denominate.|
|**Definite dall'utente**|TCP|Creare un statico Analysis Services denominato porta istanza per un numero di porta che scelto e sbloccare il numero di porta nel firewall.|

Per ulteriori informazioni sulla creazione di endpoint, vedere le operazioni seguenti:

- Creare i punti finali: Informazioni su[come configurare i punti finali a una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server: Vedere la sezione "Completare la configurazione passaggi da eseguire per connettere la macchina virtuale tramite SQL Server Management Studio" di [Provisioning una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Il diagramma seguente illustra le porte da aprire sul firewall macchine Virtuali per consentire l'accesso remoto alle caratteristiche e i componenti nella macchina virtuale.

![porte da aprire per le applicazioni di bi in macchine virtuali di Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Risorse

- Esaminare sulla politica del supporto per il software server Microsoft utilizzata nell'ambiente di Azure Virtual Machine. L'argomento seguente sono riepilogate supporto per le caratteristiche, ad esempio BitLocker Failover cluster e il bilanciamento del carico di rete. [Software server Microsoft supporto per macchine virtuali di Azure](http://support.microsoft.com/kb/2721672).

- [SQL Server in macchine virtuali Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md)

- [Macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Il provisioning di una macchina virtuale SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Come collegare un disco dati a una macchina virtuale](virtual-machines-windows-classic-attach-disk.md)

- [Eseguire la migrazione di un Database di SQL Server in una macchina virtuale Azure](virtual-machines-windows-migrate-sql.md)

- [Determinare la modalità Server di un'istanza di Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modellazione multidimensionale (Adventure Works esercitazione)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centro documentazione Azure](https://azure.microsoft.com/documentation/)

- [Uso di Power BI in un ambiente ibrido](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Inviare commenti e suggerimenti e informazioni di contatto tramite la connessione di Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenuto della community

- [Gestione del Database SQL Azure con PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
