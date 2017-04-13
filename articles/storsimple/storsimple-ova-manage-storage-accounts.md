<properties 
   pageTitle="Gestire il proprio account di archiviazione StorSimple | Microsoft Azure"
   description="Spiega come è possibile utilizzare la pagina Configura StorSimple Manager per aggiungere, modificare, eliminare o ruotare i tasti di sicurezza per un account di archiviazione associato alla matrice virtuale StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Utilizzare il servizio di gestione di StorSimple per gestire gli account di archiviazione per matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Pagina **Configura** presenta i parametri del servizio globale che possono essere creati nel servizio di gestione StorSimple. Questi parametri possono essere applicati a tutti i dispositivi connessi al servizio e includono:

- Account di archiviazione 
- Record di controllo di accesso 

In questa esercitazione viene illustrato come è possibile utilizzare la pagina di **configurazione** per aggiungere, modificare o eliminare gli account di archiviazione per la matrice virtuale StorSimple. Le informazioni contenute in questa esercitazione si applicano solo alla matrice virtuale StorSimple in esecuzione software in versione 2016 marzo GA.

 ![Configurare la pagina](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Gli account di archiviazione contengono le credenziali utilizzate dal dispositivo per accedere all'account di archiviazione con il provider di servizi cloud. Per gli account di archiviazione di Microsoft Azure, si tratta delle credenziali, ad esempio il nome dell'account e la chiave primaria di access. 

Nella pagina **Configura** tutti gli account di archiviazione che vengono creati per la sottoscrizione fatturazione vengono visualizzati in un formato tabulare contenente le informazioni seguenti:

- **Nome** : il nome univoco assegnato all'account quando è stata creata.
- **SSL attivato** : se il SSL è attivata e la comunicazione dispositivo nel cloud tramite il canale sicuro.

Le attività più comuni relativi agli account di archiviazione che può essere eseguito nella pagina **Configura** sono:

- Aggiungere un account di archiviazione 
- Modificare un account di archiviazione 
- Eliminare un account di archiviazione 


## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

Esistono tre tipi di account di archiviazione che possono essere usate con il dispositivo StorSimple.

- **Gli account di archiviazione generato automaticamente** , come il nome suggerito, questo tipo di account di archiviazione viene generato automaticamente quando viene creato il servizio. Per ulteriori informazioni sulla modalità di creazione di account di archiviazione, vedere [creare un nuovo servizio](storsimple-ova-manage-service.md#create-a-service). 
- **Gli account di archiviazione nella sottoscrizione servizio** : questi sono gli account di archiviazione Azure associati alla stessa sottoscrizione a quello del servizio. Per maggiori informazioni su come questi archiviazione vengono creati account, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md). 
- **Gli account di archiviazione all'esterno della sottoscrizione di servizio** : questi sono gli account di archiviazione Azure non associati al servizio e probabilmente precedente è stato creato il servizio.

Ogni matrice virtuale StorSimple crea un contenitore (con hcs un prefisso) nella finestra account di archiviazione associato. In questo contenitore contenente tutti i dati cloud per il dispositivo. Non eliminare questo contenitore accedendovi tramite il servizio di archiviazione Azure con questa azione determinerà la perdita di dati.

## <a name="add-a-storage-account"></a>Aggiungere un account di archiviazione

È possibile aggiungere un account di archiviazione per la configurazione del servizio di gestione StorSimple fornendo un nome univoco e le credenziali di accesso collegato all'account di archiviazione. È inoltre la possibilità di abilitare la modalità di sicuro sockets layer (SSL) per creare un canale sicuro per la comunicazione di rete tra il dispositivo e nel cloud.

È possibile creare più account per un provider di servizi cloud specificato. Mentre l'account di archiviazione viene salvata, il servizio tenta di comunicare con il provider di servizi cloud. Le credenziali e il materiale di accesso che si è fornito verranno autenticati al momento. Solo se l'autenticazione ha avuto esito positivo, viene creato un account di archiviazione. Se l'autenticazione non riesce, verrà visualizzato un messaggio di errore appropriato.

Gli account di archiviazione di Manager delle risorse creati nel portale di Azure sono supportati anche con StorSimple. Gli account di archiviazione Manager delle risorse non verranno visualizzato nell'elenco a discesa per la selezione, solo l'archiviazione verranno visualizzati gli account creati nel portale di classica Azure. Gli account di archiviazione di Manager delle risorse dovranno essere aggiunti con la procedura per aggiungere un account di archiviazione, come descritto di seguito.

Di seguito è riportata la procedura per aggiungere un account di archiviazione Azure classica.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modificare un account di archiviazione

È possibile modificare un account di archiviazione usato dal dispositivo in uso. Se si modifica un account di archiviazione è attualmente in uso, i campi disponibili per modificare sono tasto di scelta rapida e la modalità di SSL per l'account di archiviazione. È possibile fornire la nuova chiave di accesso di spazio di archiviazione o modificare la selezione **modalità Enable SSL** e salvare le impostazioni aggiornate.

#### <a name="to-edit-a-storage-account"></a>Per modificare un account di archiviazione

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic su **Configura**.

2. Fare clic su **Aggiungi/Modifica account di archiviazione**.

3. Nella finestra di dialogo **Aggiungi/Modifica account di archiviazione** :

  1. Nell'elenco a discesa degli **Account di archiviazione**selezionare un account esistente che si desidera modificare. 
  2. Se necessario, è possibile modificare la selezione di **Attivare la modalità SSL** .
  3. È possibile scegliere di rigenerare le chiavi di accesso account lo spazio di archiviazione. Per ulteriori informazioni, vedere [rigenerare le chiavi di account di archiviazione](storage-create-storage-account.md#manage-your-storage-access-keys). Fornire la nuova chiave account lo spazio di archiviazione. Per un account di archiviazione Azure, questa è la chiave primaria access. 
  4. Fare clic sull'icona di controllo ![selezionare icona](./media/storsimple-ova-manage-storage-accounts/checkicon.png) per salvare le impostazioni. Le impostazioni verranno aggiornate nella pagina **Configura** . 
  5. Nella parte inferiore della pagina, fare clic su **Salva** per salvare le impostazioni appena aggiornate. 

     ![Modificare un account di archiviazione](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

> [AZURE.IMPORTANT] È possibile eliminare un account di archiviazione solo se non è in uso. Se un account di archiviazione è in uso, l'utente verrà informato.

#### <a name="to-delete-a-storage-account"></a>Per eliminare un account di archiviazione

1. Nella pagina di destinazione del servizio di gestione StorSimple, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic su **Configura**.

2. Nell'elenco tabulare di account di archiviazione, passare il mouse sopra l'account che si desidera eliminare.

3. Verrà visualizzata un'icona di eliminazione (**x**) nella colonna estrema destra per l'account di archiviazione. Fare clic sull'icona di **x** per eliminare le credenziali.

4. Quando viene richiesto di confermare l'operazione, fare clic su **Sì** per continuare con l'eliminazione. L'elenco tabulare verrà aggiornato per riflettere le modifiche.

5. Nella parte inferiore della pagina, fare clic su **Salva** per salvare le impostazioni appena aggiornate.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [amministrare la matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).
