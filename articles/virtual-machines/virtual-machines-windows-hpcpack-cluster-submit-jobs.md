<properties
 pageTitle="Inviare processi a un pacchetto di HPC cluster in Azure | Microsoft Azure"
 description="Informazioni su come configurare un computer locale per l'invio di processi a un cluster HPC Pack in Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Inviare i processi di HPC da un computer locale a un cluster HPC Pack distribuita in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurare un computer client locale per inviare i processi in un cluster di [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) in Azure. In questo articolo viene illustrato come configurare un computer locale con gli strumenti client per inviare processo su HTTPS in cluster di Azure. In questo modo, più utenti cluster possano inviare i processi a un cluster di HPC Pack basato su cloud, ma senza la connessione direttamente al nodo principale macchine Virtuali o l'accesso a un abbonamento a Azure.

![Inviare un processo in un cluster di Azure][jobsubmit]

## <a name="prerequisites"></a>Prerequisiti

* **Nodo principale HPC Pack distribuita in una macchina virtuale Azure** , è consigliabile utilizzare strumenti automatici, ad esempio un [modello di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/) o uno [script di PowerShell Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per distribuire il nodo principale e cluster. È necessario il nome DNS del nodo principale e le credenziali dell'amministratore del cluster per completare la procedura descritta in questo articolo.

* **Computer client** , è necessario un computer client di Windows o Windows Server che è possibile eseguire l'utilità di client Pack HPC (vedere [requisiti di sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se si desidera utilizzare il portale web HPC Pack o API REST per inviare i processi, è possibile utilizzare qualsiasi computer client di propria scelta.

* **Supporto di installazione HPC Pack** - per installare l'utilità di client HPC Pack, il pacchetto di installazione per la versione più recente di HPC Pack (HPC Pack 2012 R2) è disponibile dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Assicurarsi di scaricare la stessa versione di HPC Pack installati nel nodo principale macchine Virtuali.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Passaggio 1: Installare e configurare i componenti web sul nodo principale

Per attivare un'interfaccia resto l'invio di processi al cluster su HTTPS, assicurarsi che i componenti di web HPC Pack siano configurati sul nodo principale HPC Pack. Se non sono già installati, installare componenti web eseguendo il file di installazione HpcWebComponents.msi. Quindi configurare i componenti eseguendo lo script di PowerShell HPC **HPCWebComponents.ps1 Set**.

Per procedure dettagliate, vedere [installare Microsoft HPC Pack Web Components](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Alcuni modelli di Guida introduttiva di Azure per HPC Pack installare e configurare automaticamente i componenti web. Se si usa lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per creare il cluster, è possibile se lo si desidera installare e configurare i componenti web come parte della distribuzione.

**Per installare componenti web**

1. Connettere il nodo principale macchine Virtuali utilizzando le credenziali di amministratore del cluster.

2. Dalla cartella HPC Pack configurazione eseguire HpcWebComponents.msi sul nodo principale.

3. Seguire i passaggi della procedura guidata per installare web components

**Per configurare i componenti web**

1. Sul nodo principale, avviare PowerShell HPC come amministratore.

2. Per modificare directory al percorso del file di script di configurazione, digitare il comando seguente:

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Per configurare l'interfaccia REST e avviare il servizio Web HPC, digitare il comando seguente:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Quando viene richiesto di selezionare un certificato, scegliere il certificato che corrisponde al nome DNS pubblico del nodo principale. Ad esempio, se si distribuisce il nodo principale macchine Virtuali utilizzando il modello di distribuzione classico, il nome del certificato è simile alla CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Se si usa il modello di distribuzione di Manager delle risorse, il nome del certificato è simile alla CN =&lt;*HeadNodeDnsName*&gt;. &lt; *area*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Selezionare il certificato in un secondo momento quando si invia processi per il nodo principale da un computer locale. Non selezionare o configurare un certificato che corrisponde al nome del computer di nodo principale nel dominio di Active Directory (ad esempio, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Per configurare il portale web per l'invio di processi, digitare il comando seguente:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Al termine dell'esecuzione script, arrestare e riavviare il servizio di utilità di pianificazione processo HPC digitando i comandi seguenti:

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Passaggio 2: Installare l'utilità di client HPC Pack in un computer locale

Se si desidera installare l'utilità di client HPC Pack nel computer in uso, è possibile scaricare i file di configurazione HPC Pack (installazione completa) dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Quando si inizia l'installazione, scegliere l'opzione di configurazione per le **utilità client HPC Pack**.

Per utilizzare gli strumenti client di HPC Pack per inviare i processi di nodo principale macchine Virtuali, è necessario esportare un certificato dal nodo principale e installarlo nel computer client. Il certificato deve essere. Formato CER.

**Per esportare il certificato da nodo principale**

1. Sul nodo principale, aggiungere lo snap-in per Microsoft Management Console per l'account Computer locale. Per la procedura aggiungere il componente aggiuntivo, vedere [aggiungere Snap-in certificati a MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Nella struttura della console espandere **certificati – Computer locale** > **personali**e quindi fare clic su **certificati**.

3. Individuare il certificato è configurato per i componenti web HPC Pack [passaggio 1: installare e configurare i componenti web sul nodo principale](#step-1:-install-and-configure-the-web-components-on-the-head-node) (ad esempio, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Pulsante destro del mouse sul certificato e fare clic su **Tutte le attività** > **esportare**.

5. Esportazione guidata certificati fare clic su **Avanti**e verificare che sia selezionato **No, esportare la chiave privata** .

6. Seguire i passaggi rimanenti della procedura guidata per esportare il certificato x. 509 binario codificato DER (. Formato CER).


**Per importare il certificato nel computer client**


1. Copiare il certificato esportati dal nodo principale in una cartella nel computer client.

2. Nel computer client eseguire certmgr.

3. In Gestione certificati espandere **certificati – utente corrente** > **Autorità di certificazione radice attendibili**, rapida **certificati**e quindi fare clic su **Tutte le attività** > **importazione**.

4. Nell'Importazione guidata certificati fare clic su **Avanti** e seguire le istruzioni visualizzate per importare il certificato esportato da nodo principale per l'archivio Autorità di certificazione attendibile.



>[AZURE.TIP] Può essere visualizzato un avviso di sicurezza, perché l'autorità di certificazione sul nodo principale non viene riconosciuto dal computer client. Scopo di verifica, è possibile ignorare l'avviso e completamento dell'importazione certificati.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Passaggio 3: Esecuzione di processi di test sul cluster

Per verificare la configurazione, provare a eseguire processi in cluster di Azure dal computer locale. Ad esempio, è possibile utilizzare strumenti HPC Pack grafica o riga di comando per inviare i processi al cluster. È anche possibile utilizzare un portale basato sul web l'invio di processi.


**Per eseguire i comandi di invio processo nel computer client**


1. In un computer client in cui le utilità client HPC Pack sono installate, avviare un prompt dei comandi.

2. Digitare un comando di esempio. Ad esempio, per elencare tutti i processi sul cluster, digitare un comando simile a uno dei seguenti, a seconda di nome DNS completo del nodo principale:

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    o
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Usare il nome DNS completo del nodo principale, non è l'indirizzo IP, l'URL di utilità di pianificazione. Se si specifica l'indirizzo IP, viene visualizzato un errore simile a "il certificato server deve per avere una catena valida di protezione o per essere inserito nell'archivio attendibile".

3. Quando richiesto, digitare il nome utente (nel modulo &lt;NomeDominio&gt;\\&lt;nome utente&gt;) e la password dell'amministratore del cluster HPC o un altro utente cluster che è stato configurato. È possibile scegliere di archiviare le credenziali locale per ulteriori operazioni di processo.

    Viene visualizzato un elenco dei processi.


**Usare HPC processo Manager nel computer client**

1. Se si in precedenza non memorizzano le credenziali di dominio per un utente cluster quando si invia un processo, è possibile aggiungere le credenziali in Gestione credenziali.

    un. Nel Pannello di controllo del computer client, avviare Gestione credenziali.

    b. Fare clic su **Credenziali Windows** > **Aggiungi credenziali generica**.

    c. Specificare l'indirizzo Internet (ad esempio, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt; area&gt;.cloudapp.azure.com/HpcScheduler) e il nome utente (&lt;NomeDominio&gt;\\&lt;nome utente&gt;) e la password dell'amministratore di cluster o un altro utente cluster che è stato configurato.

2. Nel computer client avviare HPC processo Manager.

3. Nella finestra di dialogo **Seleziona nodo testa** digitare l'URL per il nodo principale in Azure (ad esempio, https://&lt;HeadNodeDnsName&gt;. cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt; area&gt;. cloudapp.azure.com).

    HPC processo Manager si apre e viene visualizzato un elenco dei processi sul nodo principale.

**Usare il portale web in esecuzione sul nodo principale**

1. Avviare un web browser nel computer client e immettere uno degli indirizzi seguenti, a seconda il nome DNS completo del nodo principale:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    o
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Nella finestra di dialogo protezione digitare le credenziali di dominio dell'amministratore del cluster HPC. (È anche possibile aggiungere a altri utenti cluster ruoli diversi. Vedere [gestione degli utenti Cluster](https://technet.microsoft.com/library/ff919335.aspx)).

    Il portale web verrà visualizzata la visualizzazione elenco processo.

3. Per inviare un processo di esempio che restituisce la stringa "Hello World" dal cluster, fare clic su **nuovo processo** nella barra di spostamento sinistro.

4. Nella pagina **Nuovo processo** in **da pagine di presentazione**, fare clic su **HelloWorld**. Verrà visualizzata la pagina di invio del processo.

5. Fare clic su **Invia**. Se richiesto, specificare le credenziali di dominio dell'amministratore del cluster HPC. Il processo viene inviato e l'ID di processo verrà visualizzato nella pagina **Processi personali** .

6. Per visualizzare i risultati del processo che è stata inviata, fare clic su ID del processo e quindi fare clic su **Visualizzazione attività** per visualizzare l'output di comando (in **Output**).

## <a name="next-steps"></a>Passaggi successivi

* È inoltre possibile inviare processi al cluster Azure con l' [API REST di HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Se si desidera inviare i processi di cluster da un client Linux, vedere gli esempi di Python in [codice di esempio e HPC Pack 2012 R2 SDK](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
