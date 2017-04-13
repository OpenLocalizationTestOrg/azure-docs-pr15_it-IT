<properties 
   pageTitle="Gestire il proprio account di archiviazione StorSimple | Microsoft Azure"
   description="Spiega come è possibile utilizzare la pagina Configura StorSimple Manager per aggiungere, modificare, eliminare o ruotare i tasti di sicurezza per un account di archiviazione."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Utilizzare il servizio di gestione di StorSimple per gestire il proprio account di archiviazione

## <a name="overview"></a>Panoramica

Pagina **Configura** presenta tutti i parametri di servizio globale che possono essere creati nel servizio di gestione StorSimple. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

- Account di archiviazione 
- Modelli di larghezza di banda 
- Record di controllo di accesso 

In questa esercitazione viene illustrato come è possibile utilizzare la pagina di **configurazione** per aggiungere, modificare o eliminare gli account di archiviazione o ruotare i tasti di sicurezza per un account di archiviazione.

 ![Configurare la pagina](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Gli account di archiviazione contengono le credenziali utilizzate dal dispositivo per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, si tratta delle credenziali, ad esempio il nome dell'account e la chiave primaria di access. 

Nella pagina **Configura** tutti gli account di archiviazione che vengono creati per la sottoscrizione fatturazione vengono visualizzati in un formato tabulare contenente le informazioni seguenti:

- **Nome** : il nome univoco assegnato all'account quando è stata creata.
- **SSL attivato** : se il SSL è attivata e la comunicazione dispositivo nel cloud tramite il canale sicuro.
- **Riferimento** : il numero di volumi utilizzando l'account di archiviazione.

Le attività più comuni relativi agli account di archiviazione che può essere eseguito nella pagina **Configura** sono:

- Aggiungere un account di archiviazione 
- Modificare un account di archiviazione 
- Eliminare un account di archiviazione 
- Rotazione delle chiavi degli account di archiviazione 

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Esistono tre tipi di account di archiviazione che possono essere usate con il dispositivo StorSimple.

- **Gli account di archiviazione generato automaticamente** , come il nome suggerito, questo tipo di account di archiviazione viene generato automaticamente quando viene creato il servizio. Per ulteriori informazioni sulla modalità di creazione di account di archiviazione, vedere [passaggio 1: creare un nuovo servizio](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) in [Distribuisci dispositivo StorSimple locale](storsimple-deployment-walkthrough.md). 
- **Gli account di archiviazione nella sottoscrizione servizio** : questi sono gli account di archiviazione Azure associati alla stessa sottoscrizione a quello del servizio. Per maggiori informazioni su come questi archiviazione vengono creati account, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). 
- **Gli account di archiviazione all'esterno della sottoscrizione di servizio** : questi sono gli account di archiviazione Azure non associati al servizio e probabilmente precedente è stato creato il servizio.

## <a name="add-a-storage-account"></a>Aggiungere un account di archiviazione

È possibile aggiungere un account di archiviazione per fornire un nome univoco e le credenziali di accesso che sono collegate all'account di archiviazione (con il provider di servizi cloud specificato). È inoltre la possibilità di abilitare la modalità di sicuro sockets layer (SSL) per creare un canale sicuro per la comunicazione di rete tra il dispositivo e nel cloud.

È possibile creare più account per un provider di servizi cloud specificato. Prestare particolare attenzione, tuttavia, dopo la creazione di un account di archiviazione, non è possibile modificare il provider di servizi cloud.

Mentre l'account di archiviazione viene salvata, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e il materiale di accesso che si è fornito verranno autenticati al momento. Solo se l'autenticazione ha avuto esito positivo, viene creato un account di archiviazione. Se l'autenticazione non riesce, verrà visualizzato un messaggio di errore appropriato.

Gli account di archiviazione di Manager delle risorse creati nel portale di Azure sono supportati anche con StorSimple. Gli account di archiviazione Manager delle risorse non apparirà nell'elenco a discesa per la selezione quando si tenta di creare un contenitore di volume, solo gli account di archiviazione creati nel portale di classica Azure verrà visualizzato. Gli account di archiviazione di Manager delle risorse dovranno essere aggiunti con la procedura per aggiungere un account di archiviazione descritto di seguito.

> [AZURE.NOTE] La procedura per aggiungere un account di archiviazione varia in base alla versione di software StorSimple in uso. Assicurarsi di seguire la procedura corretta per la versione StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modificare un account di archiviazione

È possibile modificare un account di archiviazione che viene utilizzato da un contenitore di volume. Se si modifica un account di archiviazione è attualmente in uso, il campo solo disponibile per la modifica è il tasto di scelta per l'account di archiviazione. È possibile fornire la nuova chiave di accesso di spazio di archiviazione e salvare le impostazioni aggiornate.

#### <a name="to-edit-a-storage-account"></a>Per modificare un account di archiviazione

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic su **Configura**.

2. Fare clic su **Aggiungi/Modifica account di archiviazione**.

3. Nella finestra di dialogo **Aggiungi/Modifica account di archiviazione** :

  1. Nell'elenco a discesa degli **Account di archiviazione**selezionare un account esistente che si desidera modificare. Ad esempio anche gli account di archiviazione generate automaticamente quando il servizio è stato creato.
  2. Se necessario, è possibile modificare la selezione di **Attivare la modalità SSL** .
  3. È possibile ruotare i tasti di scelta account lo spazio di archiviazione. Per ulteriori informazioni su come eseguire la rotazione chiave, vedere [rotazione chiave di account di archiviazione](#key-rotation-of-storage-accounts) .
  4. Fare clic sull'icona di controllo ![selezionare icona](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) per salvare le impostazioni. Le impostazioni verranno aggiornate nella pagina **Configura** . Fare clic su **Salva** per salvare le impostazioni appena aggiornate.

     ![Modificare un account di archiviazione](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

> [AZURE.IMPORTANT] È possibile eliminare un account di archiviazione solo se non è utilizzato da un contenitore di volume. Se un account di archiviazione è utilizzato da un contenitore di volume, eliminare prima il contenitore volume e quindi eliminare l'account di archiviazione associato.

#### <a name="to-delete-a-storage-account"></a>Per eliminare un account di archiviazione

1. Nella pagina di destinazione del servizio di gestione StorSimple, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic su **Configura**.

2. Nell'elenco tabulare di account di archiviazione, passare il mouse sopra l'account che si desidera eliminare.

3. Verrà visualizzata un'icona di eliminazione (**x**) nella colonna estrema destra per l'account di archiviazione. Fare clic sull'icona di **x** per eliminare le credenziali.

4. Quando viene richiesto di confermare l'operazione, fare clic su **Sì** per continuare con l'eliminazione. L'elenco tabulare verrà aggiornato per riflettere le modifiche.

## <a name="key-rotation-of-storage-accounts"></a>Rotazione delle chiavi degli account di archiviazione

Per motivi di sicurezza, rotazione delle chiavi è spesso necessario nei data center. 

> [AZURE.NOTE] Le seguenti informazioni chiave rotazione e la procedura di rotazione si applicano a solo account di Microsoft Azure lo spazio di archiviazione. Se si usa un altro provider di servizi cloud, è possibile gestire chiavi per l'account archiviazione tramite dashboard del provider.
 
Le sottoscrizioni di Microsoft Azure possono avere uno o più account di archiviazione associato. L'accesso a tali account verrà controllata tramite i tasti di abbonamento e accesso per ogni account di archiviazione. 

Quando si crea un account di archiviazione, Microsoft Azure genera due lo spazio di archiviazione di 512 bit tasti utilizzati per l'autenticazione quando si accede a account di archiviazione. Due lo spazio di archiviazione i tasti di scelta consente di rigenerare le chiavi con l'accesso a tale servizio o nessuna interruzione del servizio di archiviazione. La chiave è attualmente in uso è la chiave *primaria* e chiave backup viene definita la chiave *secondaria* . Una di queste due chiavi deve essere fornita quando il dispositivo di Microsoft Azure StorSimple accede al provider del servizio di archiviazione cloud.

## <a name="what-is-key-rotation"></a>Che cos'è la rotazione chiave?

In genere, le applicazioni di utilizzano solo uno dei tasti per accedere ai dati. Dopo un determinato periodo di tempo, è possibile impostare le applicazioni di trasferire al tramite la seconda chiave. Dopo il cambio di applicazioni per la chiave secondaria, è possibile ritirare il primo tasto e quindi generare una nuova chiave. Usando i tasti di due in questo modo consente alle applicazioni di accedere ai dati senza incorrere qualsiasi tempo di inattività.

I tasti di account di archiviazione vengono sempre archiviati nel servizio in un modulo crittografato. Tuttavia, è possibile reimpostare questi tramite il servizio di gestione StorSimple. Il servizio è possibile ottenere la chiave primaria e chiave secondaria per tutti gli account di archiviazione nello stesso abbonamento, inclusi account creato il servizio di archiviazione, nonché lo spazio di archiviazione predefinito account generati quando il servizio di gestione di StorSimple è stato inizialmente creato. Il servizio di gestione StorSimple sempre otterrà questi tasti dal portale di classica Azure e quindi archiviarli in modo crittografato.

## <a name="rotation-workflow"></a>Flusso di lavoro di rotazione

Un amministratore di Microsoft Azure può rigenerare o modificare la chiave primaria o secondaria accedendo direttamente all'account di archiviazione (tramite il servizio di archiviazione Microsoft Azure). Il servizio di gestione di StorSimple non vedrà automaticamente questa modifica.

Per informare il servizio di gestione StorSimple della modifica, si sarà necessaria per accedere al servizio di gestione StorSimple, accedere all'account di archiviazione e quindi sincronizzare la chiave primaria o secondaria (a seconda di quale è stato modificato). Il servizio quindi Ottiene la chiave più recente, crittografa i tasti e invia la chiave crittografata al dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Per sincronizzare le chiavi per gli account di archiviazione nello stesso abbonamento come servizio (solo Azure)

1. Nella pagina **servizi** , fare clic sulla scheda **Configura** .

2. Fare clic su **Aggiungi/Modifica account di archiviazione**.

3. Nella finestra di dialogo, eseguire le operazioni seguenti:

  1. Selezionare l'account di archiviazione con la chiave che si desidera sincronizzare. I tasti di account di archiviazione vengono crittografati quando vengono visualizzati.
  2. Nel servizio di gestione StorSimple, è necessario aggiornare la chiave modificata nel servizio di archiviazione Microsoft Azure. Se la chiave primaria access è stata modificata (rigenerazione), fare clic su **Sincronizza chiave primaria**. Se è stata modificata la chiave secondaria, fare clic su **Sincronizza chiave secondaria**.

    ![sincronizzare i tasti](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Per sincronizzare le chiavi per gli account di archiviazione all'esterno di sottoscrizione assistenza

1. Nella pagina **servizi** , fare clic sulla scheda **Configura** .

2. Fare clic su **Aggiungi/Modifica account di archiviazione**.

3. Nella finestra di dialogo, eseguire le operazioni seguenti:

  1. Selezionare l'account di archiviazione con la chiave di accesso che si desidera aggiornare.
  2. È necessario aggiornare il tasto di scelta di spazio di archiviazione nel servizio di gestione StorSimple. In questo caso, è possibile visualizzare il tasto di scelta di spazio di archiviazione. Immettere la nuova chiave nella casella **Tasto di scelta di spazio di archiviazione Account**y. 
  3. Salvare le modifiche. Il tasto di scelta account lo spazio di archiviazione deve essere aggiornato.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulla [protezione StorSimple](storsimple-security.md).
- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
