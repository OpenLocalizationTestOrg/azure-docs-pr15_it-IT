<properties
    pageTitle="Come creare, gestire o eliminare un account di archiviazione nel portale di Azure | Microsoft Azure"
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

Un account di archiviazione Azure offre uno spazio dei nomi univoco per archiviare e accedere agli oggetti di dati di archiviazione Azure. Tutti gli oggetti in un account di archiviazione vengono addebitati insieme a livello di gruppo. Per impostazione predefinita, i dati nel proprio account sono disponibili solo per l'utente, il proprietario dell'account.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Fatturazione di account di archiviazione

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Quando si crea una macchina virtuale Azure, un account di archiviazione viene creato automaticamente nel percorso di distribuzione se si dispone già un account di archiviazione in quella posizione. In modo che non è necessario seguire la procedura descritta di seguito per creare un account di archiviazione per i dischi macchina virtuale. Il nome dell'account di archiviazione verrà impostato sul nome del computer virtuale. Vedere la [documentazione macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) per altri dettagli.

## <a name="storage-account-endpoints"></a>Punti finali account di archiviazione

Tutti gli oggetti archiviati in archiviazione Azure ha un indirizzo URL univoco. Il nome dell'account di archiviazione dei moduli sottodominio dell'indirizzo. La combinazione di nome sottodominio e il dominio è specifico di ogni servizio, costituisce un *endpoint* per l'account di archiviazione.

Ad esempio, se l'account di archiviazione è denominata *mystorageaccount*, quindi endpoint predefiniti per il proprio account di archiviazione sono:

- BLOB servizio: http://*mystorageaccount*. blob.core.windows.net

- Tabella di servizio: http://*mystorageaccount*. table.core.windows.net

- Accodare servizio: http://*mystorageaccount*. queue.core.windows.net

- File servizio: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Un account di archiviazione Blob esposto solo l'endpoint di servizio Blob.

L'URL per l'accesso a un oggetto in un account di archiviazione integrato aggiungendo la posizione dell'oggetto nella finestra account di archiviazione all'endpoint. Ad esempio, un indirizzo blob potrebbe avere questo formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare un nome di dominio personalizzato da utilizzare con l'account di archiviazione. Per gli account classica dello spazio di archiviazione, vedere [configurare un dominio personalizzato nome per l'Endpoint di spazio di archiviazione Blob](storage-custom-domain-name.md) per informazioni dettagliate. Per gli account di archiviazione di Manager delle risorse, questa funzionalità non è stato aggiunto al [portale di Azure](https://portal.azure.com) ancora, ma è possibile configurarlo con PowerShell. Per ulteriori informazioni, vedere il cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Scegliere **Nuovo**dal menu Hub -> **dati + spazio di archiviazione** -> **account di archiviazione**.

3. Immettere un nome per il proprio account di archiviazione. Per informazioni dettagliate su come utilizzare il nome dell'account di archiviazione per risolvere gli oggetti in archiviazione Azure, vedere [i punti finali account di archiviazione](#storage-account-endpoints) .

    > [AZURE.NOTE] I nomi degli account di archiviazione deve essere compreso tra 3 e 24 caratteri e può contenere solo lettere minuscole e numeri.
    >  
    > Il nome dell'account di archiviazione deve essere univoco all'interno di Azure. Portale di Azure indica se si seleziona il nome dell'account lo spazio di archiviazione è già in uso.

4. Specificare il modello di distribuzione da utilizzare: **Gestione risorse** o **classica**. **Manager delle risorse** è il modello di distribuzione consigliata. Per ulteriori informazioni, vedere [distribuzione classica e gestione di informazioni sulle risorse](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Gli account di archiviazione BLOB possono essere creati solo usando il modello di distribuzione di Manager delle risorse.

5. Selezionare il tipo di account di archiviazione: **Generale** o **archiviazione Blob**. **Generale** è il valore predefinito.

    Se è stata selezionata l'opzione **Generale** , quindi specificare il livello di prestazioni: **Standard** o **Premium**. Il valore predefinito è **Standard**. Per altre informazioni sull'account di archiviazione standard e premium, vedere [Introduzione a Microsoft Azure archiviazione](storage-introduction.md) e [lo spazio di archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md).

    Se è stata selezionata l'opzione di **Archiviazione Blob** , quindi specificare il livello di accesso: **Hot** o **interessanti**. Il valore predefinito è **Hot**. Vedere [archiviazione Blob Azure: raffreddare e Hot livelli](storage-blob-storage-tiers.md) per altri dettagli.

6. Selezionare l'opzione di replica per l'account di archiviazione: **LRS**, **GRS**, **RA GRS**o **ZRS**. Il valore predefinito è **RA GRS**. Per ulteriori informazioni sulle opzioni di replica di spazio di archiviazione di Azure, vedere [replica di archiviazione Azure](storage-redundancy.md).

7. Selezionare l'abbonamento in cui si desidera creare il nuovo account di archiviazione.

8. Specificare un nuovo gruppo di risorse o selezionare un gruppo di risorse esistenti. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

9. Selezionare l'area geografica per l'account di archiviazione. Per ulteriori informazioni su quali servizi sono disponibili in quale area, vedere [Le aree di Azure](https://azure.microsoft.com/regions/#services) .

10. Fare clic su **Crea** per creare l'account di archiviazione.

## <a name="manage-your-storage-account"></a>Gestire il proprio account di archiviazione

### <a name="change-your-account-configuration"></a>Modificare la configurazione dell'account

Dopo aver creato l'account di archiviazione, è possibile modificare la configurazione, ad esempio la modifica dell'opzione di replica utilizzato per il conto o la modifica del livello di accesso per un account di archiviazione Blob. Nel [portale di Azure](https://portal.azure.com), passare al proprio account di archiviazione, fare clic su **tutte le impostazioni** e quindi fare clic su **configurazione** in modo da visualizzare e/o modificare la configurazione dell'account.

> [AZURE.NOTE] A seconda del livello di prestazioni che si è scelto durante la creazione di account di archiviazione, alcune opzioni di replica potrebbero non essere disponibile.

La modifica dell'opzione di replica modificare i prezzi. Per ulteriori informazioni, vedere [lo spazio di archiviazione Azure prezzi](https://azure.microsoft.com/pricing/details/storage/) pagina.

Modificare il livello di accesso per gli account di archiviazione Blob, possono causare in base alle tariffe per la modifica oltre a modificare i prezzi. Vedere [gli account di archiviazione Blob - prezzi e fatturazione](storage-blob-storage-tiers.md#pricing-and-billing) per altri dettagli.

### <a name="manage-your-storage-access-keys"></a>Gestire i tasti di scelta dello spazio di archiviazione

Quando si crea un account di archiviazione, Azure genera due lo spazio di archiviazione di 512 bit tasti, utilizzato per l'autenticazione quando si accede a account di archiviazione. Grazie a due lo spazio di archiviazione i tasti di scelta, Azure consente di rigenerare le chiavi con l'accesso a tale servizio o nessuna interruzione del servizio di archiviazione.

> [AZURE.NOTE] È consigliabile evitare di condividere i tasti di scelta dello spazio di archiviazione con altri utenti. Per consentire l'accesso alle risorse archiviazione senza concedere i tasti di scelta, è possibile utilizzare una *firma di accesso condiviso*. Una firma di accesso condiviso consente di accedere a una risorsa nell'account per un intervallo che si definisce e con le autorizzazioni specificate. Per ulteriori informazioni, vedere [Uso firme di Access condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Visualizzazione e copia lo spazio di archiviazione i tasti di scelta

Nel [portale di Azure](https://portal.azure.com), passare al proprio account di archiviazione, fare clic su **tutte le impostazioni** e quindi premere **i tasti di scelta** per visualizzare, copiare e rigenera i tasti di scelta account. E il **I tasti di scelta** include anche stringhe di connessione preconfigurato utilizzando le chiavi primarie e secondarie che è possibile copiare per l'utilizzo delle applicazioni.

#### <a name="regenerate-storage-access-keys"></a>Rigenerare tasti di scelta dello spazio di archiviazione

È consigliabile modificare le chiavi di accesso al proprio account di archiviazione periodicamente allo scopo di mantenere le connessioni di spazio di archiviazione sicura. Due tasti di scelta rapida assegnati in modo che è possibile gestire le connessioni per l'account di archiviazione utilizzando un tasto di scelta rapida mentre si rigenera altri tasto di scelta rapida.

> [AZURE.WARNING] Rigenerare i tasti di scelta possono influire sulla servizi Azure, nonché applicazioni che dipendono dall'account di archiviazione. Tutti i client che utilizzano il tasto di scelta per accedere all'account di archiviazione devono essere aggiornati per utilizzare la nuova chiave.

**Servizi multimediali** - se si dispongono di servizi di supporto che dipendono dal proprio account di archiviazione, è necessario ripetere la sincronizzazione i tasti di scelta con il servizio di supporto dopo si rigenera i tasti.

**Applicazioni** - se si dispone di applicazioni web o servizi cloud che utilizzano l'account di archiviazione, si perderanno le connessioni se si rigenerare tasti, a meno che non si ottengono le chiavi.

**Gli elenchi di cartelle di archiviazione** - se si utilizza tutte le [applicazioni di explorer lo spazio di archiviazione](storage-explorers.md), è probabile che sia necessario aggiornare la chiave di spazio di archiviazione utilizzata dalle applicazioni.

Ecco il processo per la rotazione i tasti di scelta dello spazio di archiviazione:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento il tasto di scelta secondario dell'account di archiviazione.

2. Rigenerare il tasto di scelta principale per l'account di archiviazione. Scegliere e il **I tasti di scelta** , fare clic su **Rigenera chiave1**e quindi fare clic su **Sì** per confermare che si desidera generare una nuova chiave.

3. Aggiornare le stringhe di connessione nel codice per fare riferimento alla nuova chiave primaria access.

4. Rigenerare il tasto di scelta secondario nello stesso modo.

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

Per rimuovere un account di archiviazione in uso non è più, passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com)e fare clic su **Elimina**. Se si elimina un account di archiviazione, l'intera account, inclusi tutti i dati nella finestra account.

> [AZURE.WARNING] Non è possibile ripristinare un account di archiviazione eliminate o recuperare il contenuto in essa contenuto prima dell'eliminazione. Assicurarsi di eseguire il backup tutto ciò che si vuole salvare prima di eliminare l'account. Questa operazione anche vale per tutte le risorse nella finestra account, dopo aver eliminato un blob, tabella, una coda o un file, viene eliminato definitivamente.

Per eliminare un account di archiviazione associato a una macchina virtuale Azure, è necessario assicurarsi che siano stati eliminati eventuali dischi macchina virtuale. Se non si elimina innanzitutto dischi macchina virtuale, quando si tenta di eliminare l'account di archiviazione, verrà visualizzato un messaggio di errore simile a:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Se l'account di archiviazione utilizza il modello di distribuzione classica, è possibile rimuovere il disco macchina virtuale attenendosi alla procedura seguente nel [portale di Azure](https://manage.windowsazure.com):

1. Passare al [portale di Azure classica](https://manage.windowsazure.com).
2. Passare alla scheda macchine virtuali.
3. Fare clic sulla scheda dischi.
4. Selezionare il disco di dati, quindi fare clic su Elimina disco.
5. Per eliminare immagini disco, passare alla scheda immagini ed eliminare tutte le immagini vengono memorizzati nella finestra account.

Per ulteriori informazioni, vedere la [documentazione di Azure Virtual Machine](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Passaggi successivi

- [Archiviazione Blob Azure: Livelli di interessante e Hot](storage-blob-storage-tiers.md)
- [Azure replica di spazio di archiviazione](storage-redundancy.md)
- [Configurare le stringhe di connessione di archiviazione Azure](storage-configure-connection-string.md)
- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
- Visitare il [Blog del Team di archiviazione Azure](http://blogs.msdn.com/b/windowsazurestorage/).
