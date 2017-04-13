<properties
    pageTitle="Gestire le risorse di archiviazione Blob Azure con Esplora risorse lo spazio di archiviazione (Preview) | Microsoft Azure"
    description="Gestire i contenitori Blob Azure e BLOB con Esplora risorse lo spazio di archiviazione (Preview)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gestire le risorse di archiviazione Blob Azure con Esplora risorse lo spazio di archiviazione (Preview)

## <a name="overview"></a>Panoramica

[Archiviazione Blob Azure](./storage/storage-dotnet-how-to-use-blobs.md) è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio testo o dati binari, che è possibile accedervi da in un punto qualsiasi nel mondo tramite HTTP o HTTPS.
È possibile utilizzare lo spazio di archiviazione Blob per esporre i dati pubblicamente al mondo o per l'archiviazione dei dati dell'applicazione in privato. In questo articolo verrà come usare Esplora archivi (Preview) per l'uso con contenitori blob e BLOB.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario disporre le operazioni seguenti:

- [Scaricare e installare Esplora archivi (preview)](http://www.storageexplorer.com)
- [Connettersi a un account di archiviazione Azure o servizi](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Creare un contenitore di blob

Tutti i BLOB devono trovarsi in un contenitore di blob, è sufficiente un raggruppamento logico di BLOB. Un account può contenere un numero illimitato di contenitori e ogni contenitore consentono di memorizzare un numero illimitato di BLOB.

La procedura seguente viene illustrato come creare un contenitore di blob all'interno dello spazio di archiviazione Explorer (Preview).

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione entro il quale si desidera creare il contenitore blob.
1.  Pulsante destro del mouse **Contenitori Blob**e - dal menu di scelta rapida - selezionare **Creare Blob contenitore**.

    ![Creare menu di scelta rapida contenitori blob][0]

1.  Verrà visualizzata una casella di testo sotto la cartella di **Contenitori Blob** . Immettere il nome per il contenitore di blob. Vedere la sezione di [regole di denominazione contenitore](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) per un elenco di regole e le restrizioni su blob contenitori di denominazione.

    ![Creare la casella di testo contenitori Blob][1]

1.  Premere **INVIO** al termine per creare il contenitore blob o **Esc** per annullare. Una volta creato il contenitore blob correttamente, verrà visualizzato nella cartella dei **Contenitori Blob** per l'account di archiviazione selezionato.

    ![Contenitore di BLOB creato][2]

## <a name="view-a-blob-containers-contents"></a>Visualizzare il contenuto del contenitore blob

I contenitori BLOB contenere BLOB e cartelle (che possono contenere BLOB).

La procedura seguente viene illustrato come visualizzare il contenuto di un contenitore di blob all'interno dello spazio di archiviazione Explorer (Preview):

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore blob che si desidera visualizzare.
1.  Espandere dell'account di archiviazione **Contenitori Blob**.
1.  Pulsante destro del mouse contenitore blob che si desidera visualizzare e - dal menu di scelta rapida, selezionare **Apri Blob contenitore Editor**.
È anche possibile fare doppio clic sul contenitore blob che si desidera visualizzare.

    ![Menu di scelta rapida dell'editor Open blob contenitore][19]

1.  Riquadro principale verrà visualizzato il contenuto del contenitore blob.

    ![Editor contenitore BLOB][3]

## <a name="delete-a-blob-container"></a>Eliminare un contenitore di blob

Contenitori BLOB possono essere facilmente creati o eliminati in base alle esigenze. (Per informazioni su come eliminare BLOB singoli, fare riferimento alla sezione [Gestione BLOB in un contenitore di blob](./#managing-blobs-in-a-blob-container))

La procedura seguente viene illustrato come eliminare un contenitore di blob all'interno dello spazio di archiviazione Explorer (Preview):

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore blob che si desidera visualizzare.
1.  Espandere dell'account di archiviazione **Blob contenitori**.
1.  Pulsante destro del mouse contenitore blob che si desidera eliminare e - dal menu di scelta rapida, selezionare **Elimina**.
È inoltre possibile premere **Elimina** per eliminare il contenitore blob attualmente selezionato.

    ![Eliminare il menu di scelta rapida contenitore blob][4]

1.  Selezionare **Sì** per la finestra di dialogo di conferma.

    ![Eliminare blob conferma contenitore][5]

## <a name="copy-a-blob-container"></a>Copiare un contenitore di blob

Esplora archivi (Preview) consente di copiare un contenitore di blob negli Appunti e quindi incollare tale contenitore blob in un altro account di archiviazione. (Per informazioni su come copiare BLOB singoli, fare riferimento alla sezione [Gestione BLOB in un contenitore di blob](./#managing-blobs-in-a-blob-container))

La procedura seguente viene illustrato come copiare un contenitore di blob dall'account di uno spazio di archiviazione.

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore blob che si desidera copiare.
1.  Espandere dell'account di archiviazione **Contenitori Blob**.
1.  Pulsante destro del mouse contenitore blob che si desidera copiare e - dal menu di scelta rapida, selezionare **Copia Blob contenitore**.

    ![Menu di scelta rapida di copia blob contenitore][6]

1.  Account di archiviazione desiderato "destinazione" in cui si desidera incollare il contenitore blob destro e - dal menu di scelta rapida, selezionare **Incolla Blob contenitore**.

    ![Menu di scelta rapida contenitore blob Incolla][7]

## <a name="get-the-sas-for-a-blob-container"></a>Ottenere le associazioni di protezione per un contenitore di blob

Una [firma di accesso condiviso (SA)](./storage/storage-dotnet-shared-access-signature-part-1.md) consente di delega per l'accesso alle risorse nell'account di archiviazione.
Questo errore indica che è possibile concedere che autorizzazioni agli oggetti nel proprio account di archiviazione un client limitate per un determinato periodo di tempo e con un determinato set di autorizzazioni, senza che sia necessario condividere i tasti di scelta account.

La procedura seguente viene illustrato come creare un SA per un contenitore di blob:

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore blob per il quale si desidera ottenere un SA.
1.  Espandere dell'account di archiviazione **Blob contenitori**.
1.  Rapida contenitore blob desiderato e - dal menu di scelta rapida, selezionare **Ottenere firma accesso condiviso**.

    ![Ottenere il menu di scelta rapida SA][8]

1.  Nella finestra di dialogo **Firma accesso condiviso** , specificare i criteri, date di inizio e scadenza, fuso orario e accedere livelli desiderati per la risorsa.

    ![Ottenere SA opzioni][9]

1.  Dopo aver specificare le opzioni associazioni di protezione, selezionare **Crea**.

1.  Una seconda finestra di dialogo di **Firma di accesso condiviso** , verrà visualizzato in cui sono elencati il contenitore blob oltre l'URL e QueryStrings è possibile usare per accedere alla risorsa di archiviazione.
Selezionare **Copia** accanto all'URL che si desidera copiare negli Appunti.

    ![Copiare l'URL SA][10]

1.  Al termine, selezionare **Chiudi**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gestire i criteri di accesso per un contenitore di blob

La procedura seguente viene illustrato come gestire (aggiungere e rimuovere) le regole per un contenitore di blob di accesso:

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore di blob cui si desidera gestire i criteri di accesso.
1.  Espandere dell'account di archiviazione **Blob contenitori**.
1.  Selezionare il contenitore blob desiderato e - dal menu di scelta rapida, selezionare **Gestire i criteri di accesso**.

    ![Gestire i menu di scelta rapida dei criteri di accesso][11]

1.  Finestra di dialogo **Criteri di accesso** verrà elencati i criteri di accesso già stati creati per il contenitore selezionato blob.

    ![Opzioni criterio di accesso][12]        

1.  Seguire questi passaggi a seconda dell'operazione di gestione dei criteri di accesso:

    - **Aggiungere un nuovo criterio di accesso** , selezionare **Aggiungi**. Una volta generato, la finestra di dialogo **Criteri di accesso** verrà visualizzato il criterio di accesso appena aggiunto (con le impostazioni predefinite).
    - **Modificare un criterio di accesso** - apportare tutte le modifiche desiderate e selezionare **Salva**.
    - **Rimozione di un criterio di accesso** , selezionare **Rimuovi** accanto al criterio di accesso che si desidera rimuovere.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Impostare il livello di accesso pubblico per un contenitore di blob

Per impostazione predefinita, ogni contenitore blob è impostata su "Nessun accesso pubblico".

La procedura seguente viene illustrato come specificare un livello di accesso pubblico per un contenitore di blob.

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore di blob cui si desidera gestire i criteri di accesso.
1.  Espandere dell'account di archiviazione **Blob contenitori**.
1.  Selezionare il contenitore blob desiderato e - dal menu di scelta rapida, selezionare **Imposta livello di accesso pubblico**.

    ![Impostare il menu di scelta rapida livello di accesso pubblico][13]

1.  Nella finestra di dialogo **Imposta livello di accesso Public contenitore** , specificare il livello di accesso desiderato.

    ![Impostare le opzioni livello di accesso pubblico][14]

1.  Selezionare **Applica**.

## <a name="managing-blobs-in-a-blob-container"></a>Gestione BLOB in un contenitore di blob

Dopo aver creato un contenitore di blob, è possibile caricare un blob in tale contenitore blob, scaricare un blob nel computer locale, aprire un blob dal computer locale e molto altro ancora.

La procedura seguente viene illustrato come gestire il BLOB e cartelle, in un contenitore di blob.

1.  Aprire Esplora archivi (Preview).
1.  Nel riquadro a sinistra espandere l'account di archiviazione che contiene il contenitore blob che si desidera gestire.
1.  Espandere dell'account di archiviazione **Blob contenitori**.
1.  Fare doppio clic sul contenitore blob che si desidera visualizzare.
1.  Riquadro principale verrà visualizzato il contenuto del contenitore blob.

    ![Contenitore di blob di visualizzazione][3]

1.  Riquadro principale verrà visualizzato il contenuto del contenitore blob.

1.  Seguire questi passaggi a seconda dell'attività da eseguire:

    - **Caricare file in un contenitore di blob**

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Carica**, quindi scegliere **Carica file** dal menu a discesa.

            ![Caricare dal menu file][15]

        1.  Nella finestra di dialogo **Carica file** , selezionare il pulsante di puntini di sospensione (**.**) sul lato destro della casella di testo **file** di selezionare i file che si desidera caricare.

            ![Carica file, opzioni][16]

        1.  Specificare il tipo di **tipo Blob**. L'articolo [Introduzione a archiviazione Blob Azure con .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) vengono illustrate le differenze tra i vari tipi di blob.

        1.  Facoltativamente, specificare una cartella di destinazione in cui vengono caricati i file selezionati. Se la cartella di destinazione non esiste, verrà creato.

        1.  Selezionare **Carica**.

    - **Caricare una cartella in un contenitore di blob**

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Carica**e quindi **Caricare cartella** dal menu a discesa.

            ![Cartella menu Carica][17]

        1.  Nella finestra di dialogo **cartella di caricamento** , selezionare il pulsante di puntini di sospensione (**.**) sul lato destro della casella di testo **cartella** per selezionare la cartella in cui si desidera caricare il contenuto.

            ![Caricare Opzioni cartella][18]

        1.  Specificare il tipo di **tipo Blob**. L'articolo [Introduzione a archiviazione Blob Azure con .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) vengono illustrate le differenze tra i vari tipi di blob.

        1.  Facoltativamente, specificare una cartella di destinazione in cui verrà caricato il contenuto della cartella selezionata. Se la cartella di destinazione non esiste, verrà creato.

        1.  Selezionare **Carica**.

    - **Scaricare un blob nel computer locale**

        1.  Selezionare il blob che si desidera scaricare.

        1.  Sulla barra degli strumenti del riquadro principale, fare clic **su Download**.

        1.  Nella finestra di dialogo **specificare dove salvare il blob scaricato** , specificare la posizione in cui si desidera blob scaricati e il nome che si desidera assegnare.  

        1.  Selezionare **Salva**.

    - **Aprire un blob nel computer locale**

        1.  Selezionare il blob che si desidera aprire.

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Apri**.

        1.  Il blob verrà scaricato e aperti mediante l'applicazione associata al tipo di file sottostanti del blob.

    - **Copiare un blob negli Appunti**

        1.  Selezionare il blob che si desidera copiare.

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Copia**.

        1.  Nel riquadro sinistro, passare a un altro contenitore blob e fare doppio clic su esso per visualizzarlo nel riquadro principale.

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Incolla** per creare una copia del blob.

    - **Eliminare un blob**

        1.  Selezionare il blob che si desidera eliminare.

        1.  Sulla barra degli strumenti del riquadro principale, selezionare **Elimina**.

        1.  Selezionare **Sì** per la finestra di dialogo di conferma.

## <a name="next-steps"></a>Passaggi successivi

- Visualizzare la [versione più recente dello spazio di archiviazione Explorer (Preview) note sulla versione e video](http://www.storageexplorer.com).
- Informazioni su come [creare applicazioni che utilizzano i file, tabelle, code e BLOB Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png