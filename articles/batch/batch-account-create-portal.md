<properties
    pageTitle="Creare un account Azure Batch | Microsoft Azure"
    description="Informazioni su come creare un account Azure Batch nel portale di Azure per l'esecuzione su larga scala carichi di lavoro in parallelo nel cloud"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Creare un account Azure Batch tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](batch-account-create-portal.md)
- [Gestione batch .NET](batch-management-dotnet.md)

Informazioni su come creare un account Azure Batch nel [portale di Azure][azure_portal]e dove trovare importante proprietà account come tasti di scelta e account URL. Verranno illustrati anche Batch prezzi e collegare un account di archiviazione Azure al proprio account di Batch in modo che è possibile utilizzare [pacchetti di applicazioni](batch-application-packages.md) e [mantenere output mansione e delle attività](batch-task-output.md).

## <a name="create-a-batch-account"></a>Creare un account di Batch

1. Accedere al [portale di Azure][azure_portal].

2. Fare clic su **Nuovo** > **calcolare** > **servizio Batch**.

    ![Batch di mercato][marketplace_portal]

3. Viene visualizzato e il **Nuovo Batch di Account** . Vedere elementi *un* tramite *e* sotto per le descrizioni degli elementi blade.

    ![Creare un account di Batch][account_portal]

    un. **Nome dell'account**: un nome univoco per il proprio account Batch. Questo nome deve essere univoco all'interno dell'area di Azure che l'account viene creato (vedere *posizione* ). Possono contenere solo lettere minuscole caratteri, numeri e deve essere 3-24 caratteri.

    b. **Abbonamento**: una sottoscrizione in cui si desidera creare l'account Batch. Se si dispone di un solo abbonamento, sia selezionata per impostazione predefinita.

    c. **Gruppo risorse**: una risorsa esistente raggruppare per il nuovo account Batch o se si desidera crearne uno nuovo.

    d. **Posizione**: l'area geografica di Azure in cui si desidera creare l'account Batch. Solo le aree geografiche supportate per l'abbonamento e il gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione** (facoltativo): un account di archiviazione **Generale** è associare (collegamento) al nuovo account di Batch. Per ulteriori informazioni, vedere [account di archiviazione di Azure collegato](#linked-azure-storage-account) di sotto.

4. Fare clic su **Crea** per creare l'account.

  Il portale indica che si tratta di **distribuzione** l'account e al termine, verrà visualizzata una notifica **distribuzioni ha avuto esito positivo** per *le notifiche*.

## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch

Dopo aver creato l'account, è possibile aprire **blade account Batch** per accedere alle impostazioni e proprietà. È possibile accedere a tutte le impostazioni dell'account e le proprietà utilizzando il menu a sinistra della stessa e account Batch.

![Blade account batch nel portale di Azure][account_blade]

* **URL di account batch**: applicazioni create con l' [API di sviluppo Batch](batch-technical-overview.md#batch-development-apis) necessitano un URL di account per gestire le risorse ed eseguire i processi nella finestra account. Un URL di account Batch è nel formato seguente:

    `https://<account_name>.<region>.batch.azure.com`

![URL di account batch nel portale][account_url]

* **Tasti di scelta**: le applicazioni necessitano anche un tasto quando si utilizzano le risorse nell'account Batch. Per visualizzare o rigenera i tasti di scelta del proprio account Batch, immettere `keys` nella casella **Cerca** dal menu a sinistra e account il Batch, quindi selezionare **i tasti**.

    ![Tasti di account batch nel portale di Azure][account_keys]

## <a name="pricing"></a>Prezzi

Account batch sono disponibili solo in un "gratuito livello," che indica che non vengono addebitate per l'account Batch stesso. Addebitate per le risorse di elaborazione Azure sottostante che utilizzano le soluzioni Batch e per le risorse utilizzate da altri servizi eseguendo i carichi di lavoro. Ad esempio, vengono addebitati i nodi di calcolo nei pool e per i dati archiviati in archiviazione Azure come input o output per le proprie attività. Analogamente, se si utilizza la caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch, addebitate per le risorse di archiviazione di Azure utilizzate per archiviare i pacchetti di applicazioni. Vedere [Batch prezzi] [ batch_pricing] per ulteriori informazioni.

## <a name="linked-azure-storage-account"></a>Account di archiviazione Azure collegato

Come detto in precedenza, è possibile collegare un account di archiviazione **Generale** , facoltativamente, al proprio account Batch. La caratteristica di [pacchetti di applicazioni](batch-application-packages.md) del Batch utilizza archiviazione blob in generale collegata account di archiviazione, come la libreria di [.NET convenzioni di File Batch](batch-task-output.md) . Queste funzioni opzionali supporto per la distribuzione delle applicazioni, che eseguire le attività Batch e il mantenimento dei dati che producono.

Batch attualmente supporta *solo* il tipo di account di archiviazione **Generale** come descritto nel passaggio 5, [Crea un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account), [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md). Quando si collega un account di archiviazione Azure al proprio account Batch, essere sicuri collegamento *solo* un account di archiviazione **Generale** .

![Creazione di un account di archiviazione "Generale"][storage_account]

È consigliabile creare un account di archiviazione in accesso esclusivo dal proprio account di Batch.

>[AZURE.WARNING] Prestare particolare attenzione durante la rigenerazione i tasti di scelta di un account di archiviazione collegato. Rigenerare solo una chiave di account di archiviazione e scegliere **Sincronizza tasti** e il conto dello spazio di archiviazione collegato. Attendere cinque minuti per consentire i tasti la propagazione dei nodi di elaborazione nei pool, quindi rigenerare e sincronizzare l'altro tasto, se necessario. Se si rigenera entrambi tasti contemporaneamente, i nodi di calcolo non sarà possibile sincronizzare uno chiave e perdono l'accesso all'account di archiviazione.

  ![Rigenerare chiavi per l'account archiviazione][4]

## <a name="batch-service-quotas-and-limits"></a>Limiti e le quote di servizio batch

Tenere presente che con l'abbonamento Azure e altri servizi di Azure, alcuni [limiti e le quote di](batch-quota-limit.md) applicare a Batch gli account. Le quote di corrente di un account di Batch vengono visualizzati nel portale di nella finestra **delle proprietà**di account.

![Quote di account batch nel portale di Azure][quotas]

Tenere presente le quote durante la progettazione e scalabilità i carichi di lavoro di Batch. Ad esempio, se il pool non è raggiunto il numero di destinazione dei nodi di calcolo specificati, potrebbero hai raggiunto il limite di quota di base per l'account Batch.

Si noti inoltre che non sono limitate a un singolo account Batch per l'abbonamento Azure. È possibile eseguire più carichi di lavoro Batch in un singolo account Batch o distribuire i carichi di lavoro tra gli account di Batch nello stesso abbonamento, ma in diverse aree geografiche Azure.

Molte delle quote è possibile aumentare semplicemente con una richiesta di assistenza gratuiti del prodotto inviata nel portale Azure. Per informazioni sulla richiesta aumenti della quota, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione di account Batch

Oltre al portale di Azure, è possibile creare e gestire gli account Batch con le operazioni seguenti:

* [Cmdlet di PowerShell batch](batch-powershell-cmdlets-get-started.md)
* [CLI Azure](../xplat-cli-install.md)
* [Gestione di batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

* Vedere la [Panoramica delle funzionalità Batch di Azure](batch-api-basics.md) per ulteriori informazioni sulle caratteristiche e concetti relativi al servizio Batch. L'articolo vengono illustrate le risorse Batch principale, ad esempio pool, nodi di calcolo, processi e attività e viene fornita una panoramica delle funzionalità del servizio che consentono l'esecuzione di carico di lavoro di elaborazione su larga scala.

* Informazioni sulle nozioni fondamentali di sviluppo di un'applicazione abilitata Batch utilizzando la [libreria client .NET Batch](batch-dotnet-get-started.md). L' [articolo introduttivo](batch-dotnet-get-started.md) semplificato un'applicazione che utilizza il servizio di Batch per eseguire un carico di lavoro su più nodi di elaborazione e include utilizzando lo spazio di archiviazione di Azure per il recupero e gestione temporanea file carico di lavoro.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Rigenerare chiavi per l'account archiviazione"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
