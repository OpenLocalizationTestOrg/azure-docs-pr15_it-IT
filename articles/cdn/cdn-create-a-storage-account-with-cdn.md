<properties
    pageTitle="Integrare un Account di archiviazione CDN | Microsoft Azure"
    description="Informazioni su come utilizzare il Azure contenuto rete (CDN) per distribuire contenuto ad alta larghezza di banda per la memorizzazione nella cache BLOB dallo spazio di archiviazione Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Integrare un Account di archiviazione CDN

È possibile attivare CDN per il contenuto della cache di spazio di archiviazione Azure. Gli sviluppatori offre una soluzione globale per l'esecuzione di larghezza di banda elevata contenuto per la memorizzazione nella cache BLOB e in contenuto statico delle istanze di calcolo in nodi fisici negli Stati Uniti, Europa, Asia, Australia e Sud America.


## <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione

Utilizzare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione di Azure. Un account di archiviazione offre accesso ai servizi di archiviazione Azure. Account di archiviazione rappresenta il livello più alto dello spazio dei nomi per l'accesso a tutti i componenti del servizio di archiviazione Azure: Blob servizi, i servizi di coda e servizi di tabella. Per ulteriori informazioni, vedere [Introduzione a Microsoft Azure archiviazione](../storage/storage-introduction.md).

Per creare un account di archiviazione, è necessario essere l'amministratore del servizio o un amministratore condivisa per la sottoscrizione associata.

> [AZURE.NOTE] Sono disponibili diversi metodi per creare un account di archiviazione, inclusi il portale di Azure e Powershell.  Per questa esercitazione si inizierà a usare il portale di Azure.  

**Per creare un account di archiviazione alla sottoscrizione di Azure**

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Nell'angolo superiore sinistro, selezionare **Nuovo**. Nella finestra di dialogo **Nuovo** , selezionare **dati + spazio di archiviazione**, quindi fare clic su **account di archiviazione**.

    Verrà visualizzata e il **creare account di archiviazione** .

    ![Creare Account di archiviazione][create-new-storage-account]

4. Nel campo **nome** digitare un nome di sottodominio. Questa voce può contenere 3-24 lettere e numeri.

    Questo valore diventa il nome host all'interno di URI utilizzato per indirizzare le risorse Blob, coda o tabella per la sottoscrizione. Per rispondere a una risorsa contenitore nel servizio Blob, utilizzare un URI nel formato seguente, in cui * &lt;StorageAccountLabel&gt; * fa riferimento al valore immesso nella casella **Immettere un URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importanti:** Etichetta URL sottodominio dell'account di archiviazione URI dei moduli e deve essere univoco tra tutti i servizi ospitati in Azure.

    Questo valore viene usato anche come il nome dell'account lo spazio di archiviazione nel portale o quando si accede a livello di programmazione a questo account.

5. Lasciare le impostazioni predefinite per **modello di distribuzione**, **tipo di Account**, **le prestazioni**e **replica**. 

6. Selezionare l' **abbonamento** utilizzati con l'account di archiviazione.

7. Selezionare o creare un **Gruppo di risorse**.  Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione di risorse Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Selezionare un percorso per il proprio account di archiviazione.

8. Fare clic su **Crea**. Il processo di creazione di account di archiviazione può richiedere alcuni minuti.


## <a name="step-2-create-a-new-cdn-profile"></a>Passaggio 2: Creare un nuovo profilo di rete CDN

Un profilo CDN è un insieme di CDN.  Ogni profilo contiene uno o più endpoint CDN.  Si consiglia di utilizzare più profili per organizzare gli endpoint CDN dominio internet, applicazione web o ad altri criteri.

> [AZURE.TIP] Se si dispone già di un profilo di rete CDN che si desidera utilizzare per questa esercitazione, procedere al [passaggio 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Passaggio 3: Creare un nuovo endpoint CDN

**Per creare un nuovo endpoint CDN per l'account di archiviazione**

1. Nel [Portale di gestione Azure](https://portal.azure.com), passare al proprio profilo CDN.  Si può hanno bloccata dashboard nel passaggio precedente.  Se non si sarà facendo clic su **Sfoglia**, quindi **profili CDN**e facendo clic sul profilo di cui si intende aggiungere l'endpoint.

    Verrà visualizzata e il profilo CDN.

    ![Profilo CDN][cdn-profile-settings]

2. Fare clic sul pulsante **Aggiungi Endpoint** .

    ![Endpoint pulsante Aggiungi][cdn-new-endpoint-button]

    Verrà visualizzata e il **componente un endpoint** .

    ![Aggiungere blade endpoint][cdn-add-endpoint]

3. Immettere un **nome** per l'endpoint CDN.  Questo nome da utilizzare per accedere alle risorse di memorizzati nella cache del dominio `<endpointname>.azureedge.net`.

4. Nell'elenco a discesa **tipo di origine** , selezionare *lo spazio di archiviazione*.  

5. Nell'elenco a discesa **origine hostname** , selezionare il proprio account di archiviazione.

6. Lasciare le impostazioni predefinite per **il percorso di origine**, **intestazione host di origine**e **porta protocollo/origine**.  È necessario specificare almeno un protocollo HTTP (HTTPS).

    > [AZURE.NOTE] Questa configurazione consente a tutti i contenitori pubblicamente visibili nell'account di archiviazione per la memorizzazione nella cache nella rete CDN.  Se si desidera limitare l'ambito di un singolo contenitore, utilizzare **il percorso di origine**.  Nota che il contenitore deve avere la visibilità impostata su pubblico.

7. Fare clic sul pulsante **Aggiungi** per creare il nuovo endpoint.

8. Dopo aver creato il punto finale, viene visualizzato in un elenco di endpoint per il profilo. La visualizzazione elenco Mostra l'URL da usare per accedere al contenuto della cache, come il dominio di origine.

    ![Endpoint CDN][cdn-endpoint-success]

    > [AZURE.NOTE] L'endpoint non immediatamente saranno disponibile per l'utilizzo.  Può richiedere fino a 90 minuti per la registrazione per la propagazione attraverso la rete CDN. Gli utenti che tentano di utilizzare immediatamente il nome di dominio CDN che venga visualizzato il codice di stato 404 fino a quando il contenuto sia disponibile tramite la rete CDN.


## <a name="step-4-access-cdn-content"></a>Passaggio 4: Accesso CDN contenuto

Per accedere a contenuto memorizzati nella cache la rete CDN, utilizzare l'URL CDN fornite nel portale. L'indirizzo di un blob memorizzati nella cache sarà simile alla seguente:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Dopo aver abilitare l'accesso a un account di archiviazione CDN o servizio ospitato, tutti gli oggetti accessibili sono idonei per la memorizzazione nella cache di CDN bordo. Se si modifica un oggetto attualmente memorizzati nella cache la rete CDN, il nuovo contenuto non saranno disponibile tramite la rete CDN finché la rete CDN consente di aggiornare il relativo contenuto quando scade il periodo di time to live contenuto memorizzati nella cache.

## <a name="step-5-remove-content-from-the-cdn"></a>Passaggio 5: Rimuovere contenuto dalla rete CDN

Se non si desidera memorizzare nella cache un oggetto nella Azure contenuti rete (CDN), è possibile eseguire una delle operazioni seguenti:

-   È possibile rendere il contenitore privato anziché pubblico. Per ulteriori informazioni, vedere [gestire anonimo accesso in lettura contenitori e BLOB](../storage/storage-manage-access-to-resources.md) .
-   È possibile disattivare o eliminare l'endpoint CDN tramite il portale di gestione.
-   È possibile modificare il servizio di hosting per non rispondere alle richieste per l'oggetto.

Un oggetto già memorizzata nella cache la rete CDN rimarrà memorizzati nella cache fino a quando scade il periodo di time to live per l'oggetto o il punto finale viene eliminato. Quando scade il periodo di time to live, la rete CDN controllerà per verificare se l'endpoint CDN è ancora valido e l'oggetto ancora in modalità anonima accessibile. In caso contrario, l'oggetto non è più possibile cache.


## <a name="additional-resources"></a>Risorse aggiuntive

-   [Come eseguire il mapping CDN contenuto a un dominio personalizzato](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
