<properties
    pageTitle="Introduzione a Esplora archivi (Preview) | Microsoft Azure"
    description="Gestire le risorse di archiviazione Azure con Esplora risorse lo spazio di archiviazione (Preview)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Introduzione a Esplora archivi (Preview)

## <a name="overview"></a>Panoramica 

Esplora archivi di Microsoft Azure (Preview) è un'applicazione autonoma che consente di utilizzare facilmente i dati di archiviazione Azure in Windows, OS X e Linux. In questo articolo verranno fornite informazioni diverse modalità di connettersi e gestire gli account di archiviazione Azure.

![Esplora archivi di Microsoft Azure (Preview)][15]

## <a name="prerequisites"></a>Prerequisiti

- [Scaricare e installare Esplora archivi (preview)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account di archiviazione o il servizio

Esplora archivi (Preview) è possibile connettersi all'account di archiviazione modi miriade. Sono inclusi connessione gli account di archiviazione associati le sottoscrizioni Azure, la connessione per gli account di archiviazione e servizi condivisi da altri abbonamenti Azure e anche il collegamento a e gestione di archiviazione locale utilizzando l'emulatore di spazio di archiviazione di Azure:

- [Connettersi a un abbonamento a Azure](#connect-to-an-azure-subscription) - gestire le risorse di archiviazione che appartengono all'abbonamento Azure.
- [Rilevare lo spazio di archiviazione di sviluppo locale](#work-with-local-development-storage) - gestire l'archiviazione locale utilizzando l'emulatore di spazio di archiviazione Azure. 
- [Connetti a un'archiviazione esterna](#attach-or-detach-an-external-storage-account) - gestire le risorse di archiviazione che appartengono a un'altra sottoscrizione Azure utilizzando l'account di archiviazione nome dell'account e chiave.
- [Account di archiviazione Connetti utilizzando SA](#attach-storage-account-using-sas) - gestire le risorse di archiviazione che appartengono a un'altra sottoscrizione Azure utilizzando una SA.
- [Servizio Connetti utilizzando SA](#attach-service-using-sas) - gestire il servizio di archiviazione specifici (contenitore blob, coda o tabella) che appartengono a un'altra sottoscrizione Azure utilizzando una SA.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a un abbonamento a Azure

> [AZURE.NOTE] Se non si dispone di un account Azure, è possibile [iscriversi per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [attivare i vantaggi della propria sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. In Esplora archivi (Preview), selezionare **Impostazioni Account Azure**. 

    ![Impostazioni dell'account Azure][0]

1. Riquadro a sinistra verrà quindi visualizzati tutti gli account di Microsoft che è stato eseguito l'accesso. Per connettersi a un altro account, selezionare **Aggiungi un account**e seguire le finestre di dialogo di accedere con un account Microsoft associato almeno un abbonamento attivo a Azure.

    ![Aggiungere un account][1]

1. Una volta completata l'accesso con un account Microsoft, gli abbonamenti Azure associati a quell'account verrà inseriti riquadro a sinistra. Selezionare le sottoscrizioni Azure con cui si desidera utilizzare e quindi selezionare **Applica**. (Selezionare Attiva o disattiva **tutte le sottoscrizioni** selezionando tutti o nessuno delle sottoscrizioni di Azure elencate).

    ![Selezionare sottoscrizioni Azure][3]

1. Riquadro a sinistra visualizzerà gli account di archiviazione associati le sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

## <a name="work-with-local-development-storage"></a>Lavorare con lo spazio di archiviazione di sviluppo locale

Esplora archivi (Preview) consente di utilizzare archivio locale utilizzando l'emulatore di spazio di archiviazione Azure. In questo modo è possibile scrivere codice in base a e verificare lo spazio di archiviazione senza necessariamente un account di archiviazione distribuito su Azure (dall'account di archiviazione è da emulare dall'emulatore lo spazio di archiviazione Azure).

>[AZURE.NOTE] Emulatore di spazio di archiviazione di Azure è attualmente supportato solo per Windows. 

1. Nel riquadro sinistro di Esplora archivi (Preview), espandere la **(locali e allegate** > **Gli account di archiviazione** > nodo**(sviluppo)** .

    ![Nodo sviluppo locale][21]

1. Se non è ancora stato installato l'emulatore di spazio di archiviazione di Azure, verrà richiesto di eseguire questa operazione tramite una barra informazioni. Se viene visualizzata la barra delle informazioni, selezionare **la versione più recente di scaricare**e installare emulatore. 

    ![Scaricare prompt dei comandi emulatore lo spazio di archiviazione di Azure][22]

1. Dopo aver installato l'emulatore, sarà necessario la possibilità di creare e usare le tabelle, code e BLOB locale. Per informazioni su come lavorare con ogni tipo di account di archiviazione, selezionare sul collegamento appropriato di seguito:

    - [Gestire le risorse di archiviazione blob Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gestire le risorse di archiviazione condivisione file Azure - *disponibile a breve*
    - Gestire le risorse di archiviazione Azure coda - *disponibile a breve*
    - Gestire le risorse di archiviazione tabelle Azure - *disponibile a breve*

## <a name="attach-or-detach-an-external-storage-account"></a>Collegare o scollegare un account di archiviazione esterna

Esplora archivi (Preview) fornisce in grado di connettersi agli account di archiviazione esterna in modo che gli account di archiviazione possono essere condiviso con facilità. In questa sezione viene descritto come allegare a (e disconnettersi da) gli account di archiviazione esterni.

### <a name="get-the-storage-account-credentials"></a>Ottenere le credenziali dell'account di archiviazione

Per condividere un account di archiviazione esterno, il proprietario di tale account deve prima ottenere le credenziali - nome dell'account e chiave - per l'account e quindi condividere le informazioni con la persona che desiderano allegare a quell'account (esterna). Ottenere le credenziali dell'account di archiviazione può essere eseguita tramite il portale Azure procedendo come segue: 

1.  Accedere al [portale di Azure](https://portal.azure.com).
1.  Selezionare **Sfoglia**.
1.  Selezionare **gli account di archiviazione**.
1.  In e il **Account di archiviazione** , selezionare l'account di archiviazione desiderato.
1.  Nella finestra pala di **Impostazioni** per l'account di archiviazione selezionato, selezionare **i tasti di scelta**.

    ![Opzione di tasti di accesso][5]
    
1.  In e il **i tasti di scelta** , copiare i valori **chiave 1** e **Nome dell'ACCOUNT di archiviazione** per l'utilizzo quando ci si connette all'account di archiviazione. 

    ![Tasti di scelta][6]

### <a name="attach-to-an-external-storage-account"></a>Connettersi a un account di archiviazione esterna
Per connettersi a un account di archiviazione esterna, è necessario nome dell'account e la chiave. La sezione *ottenere le credenziali dell'account di archiviazione* spiega come ottenere questi valori dal portale di Azure. Tuttavia, si noti che nel portale di chiave account è chiamata "chiave 1" in modo in cui Esplora aree di spazio di archiviazione (Preview) richiede una chiave di account, si verrà immettere (o incollare) il valore "chiave 1". 
 
1.  In Esplora archivi (Preview), selezionare **Connetti allo spazio di archiviazione Azure**.

    ![Connettersi a archiviazione Azure][23]

1.  Nella finestra di dialogo **connessione allo spazio di archiviazione di Azure** specificare la chiave account (valore "1 di chiave" dal portale di Azure) e quindi selezionare **Avanti**.

    ![Connettersi alla finestra di dialogo di archiviazione Azure][24] 

1.  Nella finestra di dialogo **Allegare esterni lo spazio di archiviazione** , immettere il nome dell'account di archiviazione nella casella **nome dell'Account** , specificare altre impostazioni desiderate, quindi scegliere **Avanti** al termine. 

    ![Allegare finestra di dialogo archiviazione esterna][8]

1.  Nella finestra di dialogo **Riepilogo connessioni** , verificare le informazioni. Se si desidera apportare modifiche, selezionare **Nuovo** e immettere nuovamente le impostazioni desiderate. Al termine, selezionare **Connetti**.

1.  Una volta connessa, l'account di archiviazione esterna verrà visualizzato con il testo **(esterna)** aggiunto al nome dell'account di archiviazione. 

    ![Risultato della connessione a un account di archiviazione esterna][9]

### <a name="detach-from-an-external-storage-account"></a>Disconnettersi da un account di archiviazione esterna

1.  Pulsante destro del mouse l'account di archiviazione esterno che si desidera disconnettere e - scegliere **Disconnetti**dal menu di scelta rapida.

    ![Disconnettersi da opzione di archiviazione][10]

1.  Quando viene visualizzata la finestra di messaggio di conferma, selezionare **Sì** per confermare la disconnessione dall'account di archiviazione esterni.

## <a name="attach-storage-account-using-sas"></a>Allegare account lo spazio di archiviazione tramite SA

Una [sa (condiviso accesso firma)](storage/storage-dotnet-shared-access-signature-part-1.md) offre l'amministratore di un abbonamento Azure la possibilità di concedere l'accesso a un account di archiviazione temporanea senza che sia necessario fornire le proprie credenziali di Azure abbonamento. 

A questo scopo, si supponga utente è un amministratore di un abbonamento Azure e l'utente desidera che potrebbero verificarsi accedere a un account di archiviazione per un periodo di tempo limitato con determinate autorizzazioni:

1. Successivamente genera una SA (costituito la stringa di connessione per l'account di archiviazione) per un periodo di tempo e con le autorizzazioni desiderate.
1. L'utente a condivide le SA con la persona che desiderano accesso all'account di archiviazione - utente b, in questo esempio.  
1. Potrebbero verificarsi utilizza Esplora archivi (Preview) per collegare all'account che appartengono a successivamente utilizzando le SA specificate. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Ottenere un SA per l'account che si desidera condividere

1.  In Esplora archivi (Preview) destro l'account di archiviazione da condividere e - dal menu di scelta rapida, selezionare **Ottenere firma accesso condiviso**.

    ![Ottenere l'opzione di menu di scelta rapida SA][13]

1. Nella finestra di dialogo **Firma accesso condiviso** , specificare il periodo di tempo e le autorizzazioni che si desidera per l'account e selezionare **Crea**.

    ![Finestra di dialogo SA ottenere][14]
 
1. Una seconda finestra di dialogo di **Firma di Access condiviso** verrà visualizzata visualizzate le associazioni di protezione. Selezionare **Copia** accanto la **Stringa di connessione** per copiarlo negli Appunti. Selezionare **Chiudi** per chiudere la finestra di dialogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Connettersi all'account condiviso utilizzando le associazioni di protezione

1.  In Esplora archivi (Preview), selezionare **Connetti allo spazio di archiviazione Azure**.

    ![Connettersi a archiviazione Azure][23]

1.  Nella finestra di dialogo **connessione allo spazio di archiviazione di Azure** , specificare la stringa di connessione e quindi selezionare **Avanti**.

    ![Connettersi alla finestra di dialogo di archiviazione Azure][24] 

1.  Nella finestra di dialogo **Riepilogo connessioni** , verificare le informazioni. Se si desidera apportare modifiche, selezionare **Nuovo** e immettere nuovamente le impostazioni desiderate. Al termine, selezionare **Connetti**.

1.  Una volta allegato, l'account di archiviazione verrà visualizzato con il testo (SA) aggiunto al nome dell'account che è fornito.

    ![Il risultato della associati a un account tramite SA][17]

## <a name="attach-service-using-sas"></a>Allegare service usando SA

Sezione [account di archiviazione Connetti tramite SA](#attach-storage-account-using-sas) viene illustrato come amministratore abbonamento Azure può concedere temporaneamente l'accesso a un account di archiviazione generando (e condivisione) SA per l'account di archiviazione. Analogamente, è possibile generare un SA per uno specifico servizio (contenitore blob, coda o tabella) all'interno di un account di archiviazione.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Generare un SA per il servizio che si desidera condividere

In questo contesto, un servizio può essere un contenitore di blob, coda o tabella. Nelle sezioni seguenti viene illustrato come generare le associazioni di protezione per il servizio nell'elenco:

- [Ottenere le associazioni di protezione per un contenitore di blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Ottenere le associazioni di protezione per una condivisione file - *disponibile a breve*
- Ottenere le associazioni di protezione per una coda - *disponibile a breve*
- Ottenere le associazioni di protezione per una tabella - *disponibile a breve*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Allegare al servizio account condiviso mediante le associazioni di protezione

1.  In Esplora archivi (Preview), selezionare **Connetti allo spazio di archiviazione Azure**.

    ![Connettersi a archiviazione Azure][23]

1.  Nella finestra di dialogo **connessione allo spazio di archiviazione di Azure** specificare URI SAS e quindi selezionare **Avanti**.

    ![Connettersi alla finestra di dialogo di archiviazione Azure][24] 

1.  Nella finestra di dialogo **Riepilogo connessioni** , verificare le informazioni. Se si desidera apportare modifiche, selezionare **Nuovo** e immettere nuovamente le impostazioni desiderate. Al termine, selezionare **Connetti**.

1.  Una volta connesso, il servizio appena applicato verrà visualizzato sotto il nodo **(Servizio SA)** .

    ![Risultato della connessione a un servizio condiviso con SA][20]

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Se si dispone di un lungo elenco di account di archiviazione, un modo rapido per individuare un account di archiviazione specifico consiste nell'utilizzare la casella di ricerca nella parte superiore del riquadro sinistro. 

Durante la digitazione nella casella di ricerca, nel riquadro sinistro verrà visualizzata solo l'account di archiviazione che corrispondono al valore di ricerca a che è stata immessa in fino che puntano. Lo screenshot seguente è illustrato un esempio in cui stati cercato tutti gli account di archiviazione in cui il nome dell'account di archiviazione contiene il testo "tarcher".

![Ricerca di account di archiviazione][11]
    
Per annullare la ricerca, selezionare il pulsante **x** nella casella di ricerca.

## <a name="next-steps"></a>Passaggi successivi
- [Gestire le risorse di archiviazione blob Azure con Esplora risorse lo spazio di archiviazione (Preview)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
