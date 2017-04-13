<properties
    pageTitle="Come creare, gestire o eliminare un account di archiviazione nel portale classica di Azure | Microsoft Azure"
    description="Creare un nuovo account di archiviazione, gestire i tasti di scelta account o eliminare un account di archiviazione nel portale di Azure. Informazioni sull'account di archiviazione standard e premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Informazioni sugli account di archiviazione Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Panoramica

Un account di archiviazione Azure consente di accedere ai servizi Blob Azure, coda, tabella e File in archiviazione Azure. L'account di archiviazione offre lo spazio dei nomi univoco per gli oggetti di dati di archiviazione Azure. Per impostazione predefinita, i dati nel proprio account sono disponibili solo per l'utente, il proprietario dell'account.

Esistono due tipi di account di archiviazione:

- Un account di archiviazione standard include lo spazio di archiviazione Blob, tabella, coda e File.
- Un account di archiviazione premium attualmente supporta solo dischi Azure macchina virtuale. Vedere [archiviazione Premium: High-performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md) per una panoramica approfondita di spazio di archiviazione Premium.

## <a name="storage-account-billing"></a>Fatturazione di account di archiviazione

Vengono addebitate per l'utilizzo di archiviazione Azure in base al tuo account di archiviazione. Costi di archiviazione sono basati su quattro fattori: capacità di archiviazione, schema di replica, transazioni di archiviazione e uscita di dati.

- Capacità di archiviazione fa riferimento a in che misura le unità di account di archiviazione sta utilizzando per archiviare i dati. Il costo è sufficiente archiviazione dei dati dipende dalla quantità di dati vengono archiviati e come viene replicato.
- Replica determina il numero di copie dei dati vengono mantenuto contemporaneamente e in quali posizioni.
- Le transazioni si riferiscono per tutti leggere e scrivere le operazioni di archiviazione Azure.
- Uscita dati fa riferimento ai dati trasferiti all'esterno di un'area di Azure. Quando si accede ai dati nel proprio account di archiviazione da un'applicazione che non è in esecuzione nella stessa regione, se l'applicazione è un servizio cloud o un altro tipo di applicazione, vengono addebitate per uscita di dati. (Per i servizi di Azure, è possibile eseguire i passaggi per raggruppare i dati e servizi nella stessa data Center per ridurre o eliminare dati esterni in base alle tariffe.)  

La pagina [Prezzi lo spazio di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage) fornisce informazioni dettagliate sui prezzi per capacità di archiviazione, replica e transazioni. La pagina [Dettagli prezzi trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/) fornisce informazioni dettagliate sui prezzi per uscita di dati.

Per informazioni dettagliate sulle prestazioni e capacità destinazioni di account di spazio di archiviazione, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Quando si crea una macchina virtuale Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se si dispone già un account di archiviazione in quella posizione. In modo che non è necessario seguire la procedura descritta di seguito per creare un account di archiviazione per i dischi macchina virtuale. Il nome dell'account di archiviazione verrà impostato sul nome del computer virtuale. Vedere la [documentazione macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) per altri dettagli.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).

2. Fare clic su **Nuovo** nella barra degli strumenti nella parte inferiore della pagina. Scegliere **I servizi di dati** | **lo spazio di archiviazione**, quindi fare clic su **Creazione rapida**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. **URL**, immettere un nome per il proprio account di archiviazione.

    > [AZURE.NOTE] I nomi degli account di archiviazione deve essere compreso tra 3 e 24 caratteri e può contenere solo lettere minuscole e numeri.
    >  
    > Il nome dell'account di archiviazione deve essere univoco all'interno di Azure. Portale classica di Azure indica se si seleziona il nome dell'account di archiviazione è già utilizzato.

    Per informazioni dettagliate su come utilizzare il nome dell'account di archiviazione per risolvere gli oggetti in archiviazione Azure, vedere [i punti finali di spazio di archiviazione account](#storage-account-endpoints) sotto.

4. Nel **Gruppo posizione/affinità**, selezionare un percorso per il proprio account di archiviazione vicino si o ai clienti. Se si accederà dati nel proprio account di archiviazione da un altro servizio di Azure, ad esempio una macchina virtuale Azure o un servizio cloud, è consigliabile selezionare un gruppo di affinità dall'elenco per l'account di archiviazione nello stesso centro di dati con altri servizi di Azure che si siano utilizzando per migliorare le prestazioni e ridurre i costi di gruppo.

    Si noti che è necessario selezionare un gruppo di affinità quando viene creato l'account di archiviazione. È possibile spostare un account esistente a un gruppo di affinità. Per ulteriori informazioni sui gruppi di affinità, vedere [posizione condivisa del servizio con un gruppo di affinità](#service-co-location-with-an-affinity-group) .

    >[AZURE.IMPORTANT] Per determinare quali posizioni disponibili per l'abbonamento, è possibile chiamare l'operazione di [tutti i provider di risorse dell'elenco](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Per visualizzare i provider di PowerShell, chiama [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). Da .NET, utilizzare il metodo di [elenco](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) della classe ProviderOperationsExtensions.
    >
    >Inoltre, vedere [Le aree di Azure](https://azure.microsoft.com/regions/#services) per ulteriori informazioni su quali servizi sono disponibili in quale area.


5. Se si dispone di più di una sottoscrizione di Azure, viene visualizzato il campo di **sottoscrizione** . Nella **sottoscrizione**, immettere l'abbonamento Azure a cui si desidera utilizzare l'account di archiviazione con.

6. **Replica**, selezionare il livello di replica per l'account di archiviazione desiderato. L'opzione di replica consigliato è replica geografico ridondanti, che contiene la durata massima per i dati. Per ulteriori informazioni sulle opzioni di replica di spazio di archiviazione di Azure, vedere [replica di archiviazione Azure](storage-redundancy.md).

6. Fare clic su **Crea Account di archiviazione**.

    Può richiedere alcuni minuti per creare l'account di archiviazione. Per controllare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale classica di Azure. Dopo la creazione di account di archiviazione, nuovo account di archiviazione ha lo stato **Online** e pronto per l'utilizzo.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Punti finali account di archiviazione

Tutti gli oggetti archiviati in archiviazione Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione dei moduli il sottodominio dell'indirizzo. La combinazione di nome sottodominio e il dominio è specifico di ogni servizio, costituisce un *endpoint* per l'account di archiviazione.

Ad esempio, se l'account di archiviazione è denominata *mystorageaccount*, quindi endpoint predefiniti per il proprio account di archiviazione sono:

- BLOB servizio: http://*mystorageaccount*. blob.core.windows.net

- Tabella di servizio: http://*mystorageaccount*. table.core.windows.net

- Accodare servizio: http://*mystorageaccount*. queue.core.windows.net

- File servizio: http://*mystorageaccount*. file.core.windows.net

È possibile visualizzare i punti finali per l'account di archiviazione nel dashboard di spazio di archiviazione nel [Portale classica Azure](https://manage.windowsazure.com) dopo aver creato l'account.

L'URL per l'accesso a un oggetto in un account di archiviazione integrato aggiungendo la posizione dell'oggetto nella finestra account di archiviazione all'endpoint. Ad esempio, un indirizzo blob potrebbe avere questo formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare un nome di dominio personalizzato da utilizzare con l'account di archiviazione. Per informazioni dettagliate, vedere [configurare un nome di dominio personalizzato per l'endpoint di spazio di archiviazione blob](storage-custom-domain-name.md) .

### <a name="service-co-location-with-an-affinity-group"></a>Posizione condivisa del servizio con un gruppo di affinità

Un *gruppo affinità* è un raggruppamento geografico dei servizi Azure e macchine virtuali con l'account di archiviazione Azure. Un gruppo di affinità possibile migliorare le prestazioni del servizio individuando carichi di lavoro di computer dello stesso data center prossimità destinato a utenti. Inoltre, spese di fatturazione non addebitate per uscita quando si accede ai dati in un account di archiviazione da un altro servizio che fa parte dello stesso gruppo affinità.

> [AZURE.NOTE]  Per creare un gruppo di affinità, aprire l'area <b>Impostazioni</b> del [Portale classica di Azure](https://manage.windowsazure.com), fare clic su <b>gruppi di affinità</b>e quindi fare clic su <b>Aggiungi un gruppo di affinità</b> o sul pulsante <b>Aggiungi</b> . È anche possibile creare e gestire gruppi di affinità tramite l'API di gestione del servizio di Azure. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">operazioni sui gruppi di affinità</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Visualizzazione, copia e lo spazio di archiviazione rigenera i tasti di scelta

Quando si crea un account di archiviazione, Azure genera due lo spazio di archiviazione di 512 bit tasti, utilizzato per l'autenticazione quando si accede a account di archiviazione. Grazie a due lo spazio di archiviazione i tasti di scelta, Azure consente di rigenerare le chiavi con l'accesso a tale servizio o nessuna interruzione del servizio di archiviazione.

> [AZURE.NOTE] È consigliabile evitare di condividere i tasti di scelta dello spazio di archiviazione con altri utenti. Per consentire l'accesso alle risorse archiviazione senza concedere i tasti di scelta, è possibile utilizzare una *firma di accesso condiviso*. Una firma di accesso condiviso consente di accedere a una risorsa nell'account per un intervallo che si definisce e con le autorizzazioni specificate. Per ulteriori informazioni, vedere [Uso firme di Access condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md) .

Nel [Portale classica di Azure](https://manage.windowsazure.com), usare **Gestione delle chiavi** nel dashboard di o alla pagina di **spazio di archiviazione** per visualizzare, copiare e rigenera i tasti di spazio di archiviazione che vengono utilizzati per accedere ai servizi Blob, tabella e coda.

### <a name="copy-a-storage-access-key"></a>Copiare un tasto di scelta di spazio di archiviazione  

È possibile utilizzare **Gestione delle chiavi** per copiare una chiave di accesso lo spazio di archiviazione da utilizzare in una stringa di connessione. La stringa di connessione richiede il nome dell'account di archiviazione e una chiave da utilizzare con l'autenticazione. Per informazioni sulla configurazione di stringhe di connessione per accedere ai servizi di archiviazione Azure, vedere [Configurare le stringhe di connessione di Azure lo spazio di archiviazione](storage-configure-connection-string.md).

1. Nel [Portale classica di Azure](https://manage.windowsazure.com), fare clic su **archiviazione**e quindi fare clic sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **gestione delle chiavi**.

    Verrà visualizzata la finestra di **Gestione delle chiavi di accesso** .

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Per copiare un tasto di scelta di spazio di archiviazione, selezionare il testo chiave. Pulsante destro del mouse, quindi fare clic su **Copia**.

### <a name="regenerate-storage-access-keys"></a>Rigenerare tasti di scelta dello spazio di archiviazione
È consigliabile modificare le chiavi di accesso al proprio account di archiviazione periodicamente allo scopo di mantenere le connessioni di spazio di archiviazione sicura. Due tasti di scelta rapida assegnati in modo che è possibile gestire le connessioni per l'account di archiviazione utilizzando un tasto di scelta rapida mentre si rigenera altri tasto di scelta rapida.

> [AZURE.WARNING] Rigenerare i tasti di scelta possono influire sulla servizi Azure, nonché applicazioni che dipendono dall'account di archiviazione. Tutti i client che utilizzano il tasto di scelta per accedere all'account di archiviazione devono essere aggiornati per utilizzare la nuova chiave.

**Servizi multimediali** - se si dispongono di servizi di supporto che dipendono dal proprio account di archiviazione, è necessario ripetere la sincronizzazione i tasti di scelta con il servizio di supporto dopo si rigenerare le chiavi.

**Applicazioni** - se si dispone di applicazioni web o servizi cloud che utilizzano l'account di archiviazione, si perderanno le connessioni se si rigenerare chiavi, a meno che non si ottengono le chiavi. 

**Gli elenchi di cartelle di archiviazione** - se si utilizza tutte le [applicazioni di explorer lo spazio di archiviazione](storage-explorers.md), è probabile che sia necessario aggiornare la chiave di spazio di archiviazione utilizzata dalle applicazioni.

Ecco il processo per la rotazione i tasti di scelta dello spazio di archiviazione:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento il tasto di scelta secondario dell'account di archiviazione.

2. Rigenerare il tasto di scelta principale per l'account di archiviazione. Nel [Portale classica di Azure](https://manage.windowsazure.com), dal dashboard di o dalla pagina di **configurazione** , fare clic su **Gestione delle chiavi**. Fare clic su **Rigenera** sotto la chiave primaria access e quindi fare clic su **Sì** per confermare che si desidera generare una nuova chiave.

3. Aggiornare le stringhe di connessione nel codice per fare riferimento alla nuova chiave primaria access.

4. Rigenerare il tasto di scelta secondario.

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

Per rimuovere un account di archiviazione non è più in uso, scegliere **Elimina** il dashboard o della pagina **Configura** . **Eliminare** Elimina account di archiviazione intero, incluse tutte le BLOB, tabelle e code nella finestra account.

> [AZURE.WARNING] Non è possibile ripristinare un account di archiviazione eliminate o recuperare il contenuto in essa contenuto prima dell'eliminazione. Assicurarsi di eseguire il backup tutto ciò che si vuole salvare prima di eliminare l'account. Questa operazione anche vale per tutte le risorse nella finestra account, dopo aver eliminato un blob, tabella, una coda o un file, viene eliminato definitivamente.
>
> Se l'account di archiviazione contiene file disco rigido virtuale per una macchina virtuale Azure, è necessario eliminare le immagini e dischi che utilizzano tali file disco rigido virtuale prima di poter eliminare l'account di archiviazione. Prima di tutto, interrompere la macchina virtuale se è in esecuzione e quindi Elimina. Per eliminare dischi, passare alla scheda **dischi** ed eliminare qualsiasi disco non esiste. Per eliminare le immagini, passare alla scheda **immagini** ed eliminare tutte le immagini vengono memorizzati nella finestra account.

1. Nel [Portale classica di Azure](https://manage.windowsazure.com), fare clic su **archiviazione**.

2. Fare clic in un punto qualsiasi della voce di account di archiviazione ad eccezione del nome e quindi fare clic su **Elimina**.

     - Oppure -

    Fare clic sul nome dell'account di archiviazione per aprire il dashboard e quindi fare clic su **Elimina**.

3. Fare clic su **Sì** per confermare che si desidera eliminare l'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'archiviazione di Azure, vedere la [documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visitare il [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
