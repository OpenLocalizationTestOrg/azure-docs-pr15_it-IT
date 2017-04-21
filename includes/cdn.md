# <a name="using-cdn-for-azure"></a>Utilizzo di rete CDN per Azure

Il Azure contenuto rete (CDN) offre una soluzione globale per l'esecuzione di larghezza di banda elevata contenuto per la memorizzazione nella cache BLOB e in contenuto statico delle istanze di calcolo in nodi fisici negli Stati Uniti, Europa, Asia, Australia e Sud America. Per un elenco corrente dei percorsi di rete CDN nodo, vedere [Azure CDN nodo posizioni].

Questa operazione prevede i passaggi seguenti:

* [Passaggio 1: Creare un account di archiviazione](#Step1)
* [Passaggio 2: Creare un nuovo endpoint CDN per l'account di archiviazione](#Step2)
* [Passaggio 3: Accedere al contenuto CDN](#Step3)
* [Passaggio 4: Rimuovere il contenuto CDN](#Step4)

Vantaggi dell'utilizzo CDN per memorizzare nella cache dati Azure:

-   Esperienza utente e prestazioni migliori per gli utenti finali che non sono un'origine di contenuto e utilizzano applicazioni in molti 'trip internet' necessari per caricare il contenuto
-   Su larga scala distribuita per gestire meglio istantaneo condizioni di carico elevato, ad esempio, all'inizio di un evento, ad esempio il lancio del prodotto

I clienti esistenti di CDN ora possono utilizzare la rete CDN di Azure nel [portale classica Azure]. La rete CDN è una funzionalità aggiuntiva per l'abbonamento e ha un [piano di fatturazione]separate.

<a id="Step1"> </a>
<h2>Passaggio 1: Creare un account di archiviazione</h2>

Utilizzare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione di Azure. Un account di archiviazione offre accesso ai servizi di archiviazione Azure. Account di archiviazione rappresenta il livello più alto dello spazio dei nomi per l'accesso a tutti i componenti del servizio di archiviazione Azure: Blob servizi, i servizi di coda e servizi di tabella. Per ulteriori informazioni sui servizi di archiviazione Azure, vedere [uso dei servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Per creare un account di archiviazione, è necessario essere l'amministratore del servizio o un amministratore condivisa per la sottoscrizione associata.

> [AZURE.NOTE] Per informazioni su questa operazione utilizzando l'API di gestione del servizio di Azure, vedere l'argomento di riferimento [Creare Account di archiviazione](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Per creare un account di archiviazione alla sottoscrizione di Azure**

1.  Accedere al [portale classica Azure].
2.  Nell'angolo inferiore sinistro fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** , selezionare **I servizi di dati**, quindi scegliere **lo spazio di archiviazione**, quindi **Creazione rapida**.

    Verrà visualizzata la finestra di dialogo **Crea Account lo spazio di archiviazione** .

    ![Creare Account di archiviazione][create-new-storage-account]

4. Nel campo **URL** digitare un nome di sottodominio. Questa voce può contenere da 3-24 lettere e numeri.

    Questo valore diventa il nome host all'interno di URI utilizzato per indirizzare le risorse Blob, coda o tabella per la sottoscrizione. Per rispondere a una risorsa contenitore nel servizio Blob, utilizzare un URI nel formato seguente, in cui * &lt;StorageAccountLabel&gt; * fa riferimento al valore immesso nella casella **Immettere un URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importanti:** Etichetta URL sottodominio dell'account di archiviazione URI dei moduli e deve essere univoco tra tutti i servizi ospitati in Azure.

    Questo valore viene usato anche come il nome dell'account lo spazio di archiviazione nel portale o quando si accede a livello di programmazione a questo account.

5.  Dall'elenco a discesa **Gruppo regione/affinità** selezionare un'area o un gruppo di affinità per l'account di archiviazione. Selezionare un gruppo di affinità invece di un'area Se si desidera che i servizi di archiviazione stiano nello stesso data center con altri servizi di Windows Azure che si siano utilizzando. Questo può migliorare le prestazioni e non in base alle tariffe addebitate per uscita.  

    **Nota:** Per creare un gruppo di affinità, aprire l'area **Impostazioni** del portale di gestione, fare clic su **gruppi di affinità**e quindi fare clic su **Aggiungi un gruppo di affinità** o **Aggiungi**. È anche possibile creare e gestire gruppi di affinità tramite l'API di gestione del servizio di Windows Azure. Per ulteriori informazioni, vedere [operazioni sui gruppi di affinità].

6. Dall'elenco a discesa **abbonamento** selezionare l'abbonamento utilizzati con l'account di archiviazione.
7.  Fare clic su **Crea Account di archiviazione**. Il processo di creazione di account di archiviazione può richiedere alcuni minuti.
8.  Per verificare che l'account di archiviazione è stato creato correttamente, verificare che l'account viene visualizzato in elementi elencati per **lo spazio di archiviazione** con lo stato **Online**.

<a id="Step2"> </a>
<h2>Passaggio 2: Creare un nuovo endpoint CDN per l'account di archiviazione</h2>

Dopo aver abilitare l'accesso a un account di archiviazione CDN o servizio ospitato, tutti gli oggetti accessibili sono idonei per la memorizzazione nella cache di CDN bordo. Se si modifica un oggetto attualmente memorizzati nella cache la rete CDN, il nuovo contenuto non saranno disponibile tramite la rete CDN finché la rete CDN consente di aggiornare il relativo contenuto quando scade il periodo di time to live contenuto memorizzati nella cache.

**Per creare un nuovo endpoint CDN per l'account di archiviazione**

1. Nel [portale classica Azure], nel riquadro di spostamento, fare clic su **CDN**.

2. Sulla barra multifunzione fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **App servizi** **CDN**e quindi **Creare rapido**.

3. Nell'elenco a discesa **Origine dominio** , selezionare l'account di archiviazione creata nella sezione precedente nell'elenco degli account di archiviazione disponibile. 

4. Fare clic sul pulsante **Crea** per creare il nuovo endpoint.

5. Dopo aver creato il punto finale, viene visualizzato in un elenco di endpoint per la sottoscrizione. La visualizzazione elenco Mostra l'URL da usare per accedere al contenuto della cache, come il dominio di origine. 

    Il dominio di origine è la posizione da cui la rete CDN memorizza contenuto. Dominio di origine può essere un account di archiviazione o un servizio cloud; Per quanto riguarda in questo esempio viene utilizzato un account di archiviazione. Contenuto di spazio di archiviazione è memorizzata nella cache per i server perimetrali in base a un'impostazione di controllo della cache specificate o per requisiti tipici dell'impostazione predefinita della rete memorizzazione nella cache. 


    > [AZURE.NOTE] La configurazione creata per l'endpoint non immediatamente sarà disponibile. può richiedere fino a 60 minuti per la registrazione per la propagazione attraverso la rete CDN. Gli utenti che tentano di utilizzare immediatamente il nome di dominio CDN che venga visualizzato il codice di stato 400 (richiesta non valida) fino a quando il contenuto sia disponibile tramite la rete CDN.

<a id="Step3"> </a>
<h2>Passaggio 3: Accesso CDN contenuto</h2> 

Per accedere a contenuto memorizzati nella cache la rete CDN, utilizzare l'URL CDN fornite nel portale. L'indirizzo di un blob memorizzati nella cache sarà simile alla seguente:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Passaggio 4: Rimuovere contenuto dalla rete CDN</h2>

Se non si desidera memorizzare nella cache un oggetto nella Azure contenuti rete (CDN), è possibile eseguire una delle operazioni seguenti:

-   Per un blob Azure, è possibile eliminare il blob dal contenitore pubblico.
-   È possibile rendere il contenitore privato anziché pubblico. Per ulteriori informazioni, vedere [Limitare l'accesso a contenitori ed BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   È possibile disattivare o eliminare l'endpoint CDN tramite il portale di gestione.
-   È possibile modificare il servizio di hosting per non rispondere alle richieste per l'oggetto.

Un oggetto già memorizzata nella cache la rete CDN rimangono memorizzati nella cache fino alla scadenza del periodo time to live per l'oggetto. Quando scade il periodo di time to live, la rete CDN controllerà per verificare se l'endpoint CDN è ancora valido e l'oggetto ancora in modalità anonima accessibile. In caso contrario, l'oggetto non è più possibile cache.

La possibilità di eliminare immediatamente il contenuto non è supportata nel portale di gestione Azure. Contattare il [supporto di Azure](https://azure.microsoft.com/support/options/) se è necessario eliminare immediatamente il contenuto. 

## <a name="additional-resources"></a>Risorse aggiuntive

-   [Come creare un gruppo di affinità in Azure]
-   [Come: gestire gli account di archiviazione per una sottoscrizione Azure]
-   [Gestione servizio API]
-   [Come eseguire il mapping CDN contenuto a un dominio personalizzato]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Percorsi nodo CDN Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portale classica Azure]: https://manage.windowsazure.com/
  [piano di fatturazione]: /pricing/calculator/?scenario=full
  [Come creare un gruppo di affinità in Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Gestione servizio API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Come eseguire il mapping CDN contenuto a un dominio personalizzato]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
