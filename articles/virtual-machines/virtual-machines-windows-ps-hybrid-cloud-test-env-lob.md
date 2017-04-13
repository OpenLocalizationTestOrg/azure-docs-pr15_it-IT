<properties 
    pageTitle="Ambiente di testing applicazione line | Microsoft Azure" 
    description="Informazioni su come creare una riga, basato sul web di applicazione aziendale in un ambiente ibrido di cloud per IT pro o il test di sviluppo." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurare un'applicazione line basato sul web in un'area ibrido per il testing

In questo argomento è passaggi per la creazione di un ambiente ibrido simulata di cloud per il testing un basato sul web applicazione line-of unità aziendali ospitato in Microsoft Azure. Ecco la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Questa configurazione è costituito da:

- Rete locale simulata ospitato in Azure (TestLab VNet).
- Una croce locale virtuali ospitato in Azure (TestVNET).
- Una connessione VPN VNet a VNet.
- Un server Line basato sul web, SQL server e controller di dominio secondario nella rete virtuale TestVNET.

Questa configurazione fornisce una base e il punto di partenza comune da cui è possibile:

- Sviluppare e testare le applicazioni line ospitate su Internet Information Services (IIS) con un database SQL Server 2014 back-end in Azure.
- Eseguire il test di questo ibrido simulata basate su cloud IT carico di lavoro.

Esistono tre fasi principali per configurare l'ambiente di test ibrido cloud:

1.  Configurare l'ambiente di cloud ibrida simulata.
2.  Configurare i computer SQL server (SQL1).
3.  Configurare il server Line (LOB1).

Questo carico di lavoro è necessario un abbonamento a Azure. Se si ha un abbonamento MSDN o Visual Studio, vedere [Azure mensile carta di credito per gli abbonati a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Per un esempio di un'applicazione line contenuta in Azure di produzione, vedere linee guida per la architettura delle **applicazioni aziendali** in [diagrammi di architetture Software Microsoft e piantine](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurare l'ambiente di cloud ibrida simulato

Creare [simulata ambiente di testing cloud ibrida](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Perché questo ambiente di testing non richiede la presenza di server APP1 subnet Corpnet, è possibile chiudere verso il basso per ora.

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurare i computer SQL server (SQL1)

Dal portale di Azure, avviare il computer DC2 se necessario.

Creare una macchina virtuale per SQL1 con questi comandi al prompt dei comandi di PowerShell Azure nel computer locale. Prima di eseguire questi comandi, inserire i valori delle variabili e rimuovere i caratteri < e >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usare il portale di Azure a cui connettersi SQL1 usando l'account di amministratore locale di SQL1.

Configurare regole di Windows Firewall per consentire il traffico di SQL Server e verificare la connettività di base. Da un livello di amministratore di Windows PowerShell Prompt dei comandi in SQL1, eseguire questi comandi.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Il comando ping dovrebbe produrre quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere il disco dati aggiuntivi sul SQL1 come nuovo volume con la lettera f.

1.  Nel riquadro sinistro di Server Manager, fare clic su **File e servizi di archiviazione**e quindi fare clic su **dischi**.
2.  Nel riquadro contenuto, nel gruppo **dischi** fare clic su **disco 2** (con **partizione** impostata su **sconosciuto**).
3.  Fare clic su **attività**e quindi fare clic su **Nuovo Volume**.
4.  Nella prima pagina della creazione guidata Volume di iniziare, fare clic su **Avanti**.
5.  Nella pagina Seleziona il server e del disco, fare clic su **disco 2**e quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.  In specificare le dimensioni della pagina volume, fare clic su **Avanti**.
7.  In assegna a una pagina di unità lettera o una cartella, fare clic su **Avanti**.
8.  Nella pagina Impostazioni di sistema selezionare il file, fare clic su **Avanti**.
9.  Nella pagina Conferma selezioni, fare clic su **Crea**.
10. Al termine, fare clic su **Chiudi**.

Eseguire questi comandi al prompt dei comandi di Windows PowerShell su SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Aggiungere quindi SQL1 al dominio CORP Windows Server Active Directory con questi comandi al prompt di Windows PowerShell in SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usare l'account CORP\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Aggiungi Computer** .

Dopo il riavvio, usare il portale di Azure a cui connettersi SQL1 *con l'account di amministratore locale di SQL1*.

Quindi configurare SQL Server 2014 per usare l'unità f per i nuovi database e per le autorizzazioni di account utente.

1.  Dalla schermata Start digitare **Gestione di SQL Server**e quindi fare clic su **SQL Server 2014 Management Studio**.
2.  In **connessione al Server**fare clic su **Connetti**.
3.  Nel riquadro della struttura Esplora oggetti destro **SQL1**e quindi fare clic su **proprietà**.
4.  Nella finestra **Proprietà Server** , fare clic su **Impostazioni del Database**.
5.  Individuare i **percorsi predefiniti Database** e impostare questi valori: 
    - Per i **dati**, digitare il percorso **f:\Data**.
    - Per **Log**, digitare il percorso **f:\Log**.
    - Per il **Backup**, digitare il percorso **f:\Backup**.
    - Nota: Solo i nuovi database utilizzano questi percorsi.
6.  Fare clic su **OK** per chiudere la finestra.
7.  Nel riquadro della struttura **Esplora oggetti** aprire **sicurezza**.
8.  Pulsante destro del mouse **gli account di accesso** e quindi fare clic su **Nuovo account di accesso**.
9.  In **nome di accesso**, digitare **CORP\User1**.
10. Nella pagina **Ruoli Server** , fare clic su **sysadmin**e quindi fare clic su **OK**.
11. Chiudere Microsoft SQL Server Management Studio.

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurare il server Line (LOB1)

Creare una macchina virtuale per LOB1 con questi comandi prompt dei comandi PowerShell di Azure nel computer locale.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Successivamente, utilizzare il portale di Azure a cui connettersi LOB1 con le credenziali dell'account di amministratore locale del LOB1.

Configurare una regola di Windows Firewall per consentire il traffico per verificare la connettività di base. Da un livello di amministratore di Windows PowerShell Prompt dei comandi in LOB1, utilizzare questi comandi.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Il comando ping dovrebbe produrre quattro risposte dall'indirizzo IP 192.168.0.4.

Aggiungere quindi LOB1 al dominio CORP Active Directory con questi comandi al prompt dei comandi di Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Usare l'account CORP\User1 quando viene richiesto di specificare le credenziali dell'account di dominio per il comando **Aggiungi Computer** .

Dopo il riavvio, usare il portale di Azure per connettersi a LOB1 con l'account CORP\User1 e la password.

Quindi configurare LOB1 per IIS e verificare l'accesso da CLIENT1.

1.  Da Server Manager, fare clic su **Aggiungi ruoli e funzionalità**.
2.  Nella pagina **prima di iniziare** , fare clic su **Avanti**.
3.  Nella pagina **Seleziona tipo di installazione** fare clic su **Avanti**.
4.  Nella pagina **Selezionare il server di destinazione** , fare clic su **Avanti**.
5.  Nella pagina **ruoli Server** , fare clic su **Server Web (IIS)** nell'elenco dei **ruoli**.
6.  Quando richiesto, fare clic su **Aggiungi funzionalità**e quindi fare clic su **Avanti**.
7.  Nella pagina **selezionare caratteristiche** , fare clic su **Avanti**.
8.  Nella pagina **Web Server (IIS)** fare clic su **Avanti**.
9.  Nella pagina **selezionare servizi ruolo** selezionare o deselezionare le caselle di controllo relative ai servizi che è necessario per testare l'applicazione line e quindi fare clic su **Avanti**.
10. Nella pagina **Conferma selezioni per l'installazione** , fare clic su **Installa**.
11. Attendere finché non viene completata l'installazione dei componenti e quindi fare clic su **Chiudi**.
12. Dal portale di Azure, connettersi al computer CLIENT1 con le credenziali dell'account CORP\User1 e quindi avviare Internet Explorer.
13. Nella barra degli indirizzi digitare **http://lob1/** e quindi premere INVIO. Verrà visualizzato la pagina web di IIS 8 predefinita.

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Questo ambiente è pronto per distribuire l'applicazione basata sul web in LOB1 e verificare la funzionalità da CLIENT1 subnet Corpnet.

## <a name="next-step"></a>Passaggio successivo

- Aggiungere una nuova macchina virtuale tramite il [portale di Azure](virtual-machines-windows-hero-tutorial.md).
