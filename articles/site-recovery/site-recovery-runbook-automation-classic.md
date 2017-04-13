<properties
   pageTitle="Aggiungere l'automazione Azure runbook ai piani di ripristino | Microsoft Azure"
   description="In questo articolo descrive come il ripristino del sito di Azure consente ora di estendere piani di ripristino utilizzando l'automazione di Azure per completare le attività complesse durante il ripristino di Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Aggiungere l'automazione Azure runbook ai piani di ripristino - classico


In questa esercitazione viene illustrato il ripristino del sito di Azure si integra con automazione Azure per fornire estensibilità ai piani di ripristino. Piani di ripristino possano progettare il ripristino di macchine virtuali è protetto con il ripristino del sito di Azure per replica cloud secondario e replica di Azure scenari. Possono inoltre aiutare ripristino **costanti e precise**, **ripetibile**e **automatica**. Se non riescono su macchine virtuali di Azure, integrazione con Azure automazione estende i piani di ripristino e consente l'esecuzione delle runbook, in modo da consentire attività di automazione potenti.

Se non ascoltati sull'automazione di Azure ancora, iscriversi [qui](https://azure.microsoft.com/services/automation/) e scaricare gli script di esempio [qui](https://azure.microsoft.com/documentation/scripts/). Leggere altre informazioni sul [Ripristino del sito di Azure](https://azure.microsoft.com/services/site-recovery/) e su come gestire il ripristino Azure utilizzando piani di ripristino [di seguito](https://azure.microsoft.com/blog/?p=166264).

In questa esercitazione breve esamineremo come è possibile integrare Azure automazione runbook piani di ripristino. Verrà automatizzare attività semplici che in precedenza necessari interventi manuali e informazioni su come convertire il ripristino di un passaggio più in un'operazione di ripristino fare clic su singola. È inoltre esaminare le modalità di risoluzione un semplice script in caso di errato.

## <a name="protect-the-application-to-azure"></a>Proteggere un'applicazione di Azure

Possiamo iniziare con una semplice applicazione costituita da due macchine virtuali. In questo caso, è disponibile un'applicazione di HRweb di Fabrikam. Fabrikam HRweb componenti front-end e back-end Fabrikam Hrweb sono due macchine virtuali protette in Azure tramite il ripristino del sito di Azure. Per proteggere le macchine virtuali utilizzando il ripristino del sito di Azure, seguire la procedura seguente.

1.  Attivare la protezione per macchine virtuali.

2.  Assicurarsi che le macchine virtuali stata completata la replica iniziale e la replica.

3.  Attendere finché non viene completata la replica iniziale e lo stato di replica legge protetta.

![](media/site-recovery-runbook-automation/01.png)
---------------------

In questa esercitazione si creerà un piano di ripristino per l'applicazione Fabrikam HRweb failover dell'applicazione in Azure. Quindi abbiamo effettuare l'integrazione con un runbook che verrà creato un endpoint sul volume su Azure macchina virtuale per fornire pagine web alla porta 80.

Prima di tutto, creare un piano di ripristino per l'applicazione.

## <a name="create-the-recovery-plan"></a>Creare il piano di ripristino

Per ripristinare un'applicazione di Azure, è necessario creare un piano di ripristino.
Utilizzo di un piano di ripristino che è possibile specificare l'ordine di ripristino delle macchine virtuali. La macchina virtuale inserita nel gruppo 1 verrà recuperare e avviare prima e seguire la macchina virtuale nel gruppo 2.

Creare un piano di ripristino che è simile alla seguente.

![](media/site-recovery-runbook-automation/12.png)

Per ulteriori informazioni sui piani di ripristino, leggere la documentazione [qui](https://msdn.microsoft.com/library/azure/dn788799.aspx "di seguito").

Successivamente, creare gli elementi necessari in Azure automazione.

## <a name="create-the-automation-account-and-its-assets"></a>Creare l'account di automazione e delle relative risorse

È necessario un account Azure automazione per creare runbook. Se non si dispone già di un account, passare alla scheda automazione Azure identificata da ![](media/site-recovery-runbook-automation/02.png)e creare un nuovo account.

1.  Assegnare un nome per identificare con l'account.

2.  Specificare un'area geografica in cui si desidera inserire l'account.

È consigliabile posizionare l'account nella stessa regione come archivio ripristino automatico di sistema.

![](media/site-recovery-runbook-automation/03.png)

Creare le risorse seguenti nella finestra Account.

### <a name="add-a-subscription-name-as-asset"></a>Aggiungere un nome di sottoscrizione come bene

1.  Aggiungere una nuova impostazione ![](media/site-recovery-runbook-automation/04.png) nell'attività di automazione Azure e selezionare questa opzione per![](media/site-recovery-runbook-automation/05.png)

2.  Selezionare il tipo di variabile come **stringa**

3.  Specificare il nome di variabile come **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Specificare il nome dell'abbonamento Azure effettivo come il valore della variabile.

    ![](media/site-recovery-runbook-automation/07_1.png)

È possibile identificare il nome dell'abbonamento nella pagina impostazioni dell'account nel portale di Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Aggiungere una credenziali di accesso Azure come bene

Automazione Azure utilizza Azure PowerShell per connettersi all'abbonamento e funziona con gli elementi sono. A tale scopo, è necessario eseguire l'autenticazione mediante l'account Microsoft o un account aziendale o dell'istituto di istruzione.
È possibile archiviare le credenziali dell'account di un bene deve essere eseguita in modo sicuro dal runbook.

1.  Aggiungere una nuova impostazione ![](media/site-recovery-runbook-automation/04.png) nell'attività di automazione Azure e selezionare![](media/site-recovery-runbook-automation/09.png)

2.  Selezionare il tipo di credenziali come **Credenziali di Windows PowerShell**

3.  Specificare il nome come **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Specificare il nome utente e password per effettuare l'accesso con.

A questo punto entrambi queste impostazioni sono disponibili le attività.

![](media/site-recovery-runbook-automation/11.png)

Vengono fornite ulteriori informazioni su come connettersi al proprio abbonamento tramite PowerShell [qui](../powershell-install-configure.md).

Successivamente, verrà creato un runbook nel automazione Azure che è possibile aggiungere un endpoint per la macchina virtuale front-end dopo il failover.

## <a name="azure-automation-context"></a>Contesto di automazione Azure

Ripristino passa una variabile di contesto a runbook per la creazione di script deterministico. È possibile sostenere che i nomi dei servizi Cloud e la macchina virtuale sono prevedibili, ma si verifica che non è sempre le maiuscole/minuscole per alcuni scenari, ad esempio quella in cui il nome del nome della macchina virtuale potrebbe essere cambiate a causa di caratteri non supportati in Azure. Queste informazioni vengono quindi passate al piano di ripristino automatico come parte del *contesto*.

Di seguito è illustrato un esempio dell'aspetto che avrà la variabile di contesto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Nella tabella seguente contiene nome e una descrizione per ogni variabile nel contesto.

**Nome della variabile** | **Descrizione**
---|---
RecoveryPlanName | Nome del piano in esecuzione. Consente di eseguire azioni in base al nome con lo stesso script
FailoverType | Specifica se è testare il failover, pianificato o non pianificato.
FailoverDirection | Specificare se il ripristino su principale o secondario
ID del gruppo | Individuare il numero del gruppo all'interno del piano di ripristino quando il piano è in esecuzione
VmMap | Matrice di tutte le macchine virtuali di gruppo
Chiave VMMap | Chiave univoca (GUID) per ogni macchina virtuale. È diverso da quello di ID VMM della macchina virtuale eventualmente.
RoleName | Nome della macchina virtuale Azure quale viene recuperata
CloudServiceName | Nome servizio Cloud Azure in cui viene creata la macchina virtuale.


Per identificare VmMap Key nel contesto è anche possibile passare alla pagina delle proprietà macchine Virtuali di ripristino automatico di sistema e osservare la proprietà GUID VM.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Creare un runbook di automazione

Creare ora dal runbook per aprire la porta 80 sul computer virtuale front-end.

1.  Creare un nuovo runbook in account Azure automazione con il nome **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Passare alla visualizzazione autore dal runbook e attivare la modalità bozza.

3.  Prima di tutto specificare la variabile da utilizzare come il contesto di piano di ripristino

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Quindi connessione per la sottoscrizione usando il nome delle credenziali e abbonamento

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Nota Utilizzare Azure principali: **AzureCredential** e **AzureSubscriptionName** qui.

5.  Specificare i dettagli sull'endpoint e GUID della macchina virtuale per il quale si desidera esporre il punto finale. In questo caso la macchina virtuale front-end.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Specifica il protocollo dell'endpoint Azure, porta locale nella macchina virtuale e la relativa porta pubblica mappata. Queste variabili sono i parametri richiesti per i comandi di Azure aggiunti i punti finali al macchine virtuali. La VMGUID contiene il GUID della macchina virtuale che è necessario agire.

6.  Lo script verrà estrarre il contesto per il VM GUID e creare un endpoint del computer virtuale contiene i riferimenti.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Al termine, premere pubblica ![](media/site-recovery-runbook-automation/20.png) per consentire lo script sia disponibile per l'esecuzione.

Lo script completo è indicato di seguito per riferimento

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Aggiungere lo script per il piano di ripristino

Quando lo script è pronto, è possibile aggiungere al piano di ripristino creato in precedenza.

1.  Nel piano del ripristino che è stato creato, scegliere di aggiungere uno script dopo il gruppo 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Specificare un nome di script. Questo è solo un nome descrittivo per questo script per mostrare all'interno del piano di ripristino.

3.  In failover a Azure script, selezionare il nome Account Azure automazione.

4.  Nella finestra runbook Azure selezionare runbook creato.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Script sul lato principale

Quando si sta eseguendo un il controllo a Azure, è possibile anche scegliere di eseguire script sul lato principale. Questi script verranno eseguito sul server VMM durante il failover.
Script sul lato primario sono disponibili solo per pre-arresto solo e registrare le fasi di chiusura. In questo modo si prevede che il sito principale sia in genere non è disponibile quando correttive.
Durante un failover non pianificato solo se non si scelga di operazioni del sito principale, tenterà di eseguire gli script sul lato principale. Se non sono raggiungibili o timeout, il failover continuerà a recuperare macchine virtuali.
Script sul lato principale sono non disponibili per i siti VMware/fisici/Hyper-v senza VMM protetto in Azure - anche se è il controllo a Azure.
Tuttavia, quando si failback da Azure nella distribuzione locale, script primaria (runbook) è possibile utilizzare per tutte le destinazioni ad eccezione di VMware.

## <a name="test-the-recovery-plan"></a>Verificare il piano di ripristino

Dopo aver aggiunto dal runbook per il piano è possibile avviare caso di errore di test e vedere in azione. È sempre consigliabile eseguire test caso di errore per testare l'applicazione e il piano di ripristino per garantire che non siano presenti errori.

1.  Selezionare il piano di ripristino e avviare caso di errore di test.

2.  Durante l'esecuzione di piano, è possibile vedere se è stato eseguito dal runbook o non tramite il suo stato.

    ![](media/site-recovery-runbook-automation/17.png)

3.  È anche possibile visualizzare lo stato di esecuzione runbook dettagliate sulla pagina processi Azure automazione dal runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Dopo il failover, oltre il risultato dell'esecuzione runbook, è possibile vedere se l'esecuzione ha esito positivo o meno, visitare la pagina di Azure macchina virtuale ed esaminare i punti finali.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Script di esempio

Mentre è stato illustrato automazione uno comunemente usati attività dell'aggiunta di un estremo a una macchina virtuale Azure in questa esercitazione, è possibile eseguire un numero di altre attività di automazione potenti utilizzando l'automazione Azure. Microsoft e alla community di automazione di Azure offrono runbook di esempio che consentono di iniziare a creare il proprio le soluzioni e le utilità runbook, che è possibile utilizzare come blocchi predefiniti per le attività di automazione più grandi. Iniziare a utilizzare la raccolta e creare piani di ripristino di un solo clic potenti per le applicazioni che utilizzano il ripristino del sito di Azure.

## <a name="additional-resources"></a>Risorse aggiuntive

[Panoramica di automazione Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di automazione Azure")

[Script di automazione Azure di esempio] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Script di automazione Azure di esempio")
