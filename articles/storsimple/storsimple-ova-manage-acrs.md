<properties 
   pageTitle="Gestire i record di controllo di accesso per la matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive come gestire i record di controllo di accesso (ACRs) per determinare quali host è possibile connettersi a un volume nella matrice virtuale StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Utilizzare il servizio di gestione StorSimple per gestire i record di controllo di accesso per la matrice virtuale StorSimple 

## <a name="overview"></a>Panoramica

Record di controllo di accesso (ACRs) consentono di specificare quali host possono connettersi a un volume nella matrice virtuale StorSimple (noto anche come il StorSimple locale virtuale dispositivo). ACRs impostata su un volume specifico e contengono i nomi completi iSCSI (nomi) degli host. Quando un host tenta di connettersi a un volume, il dispositivo controlla ACR associato a tale volume corrispondente al nome IQN e, se viene trovata una corrispondenza, quindi la connessione. La sezione **record di controllo di accesso** nella pagina **Configura** Visualizza tutti i record di controllo di access con i nomi degli host corrispondente.

In questa esercitazione illustra le attività di correlati ACR comuni seguenti:

- Ottenere il IQN
- Aggiungere un record di controllo di accesso 
- Modificare un record di controllo di accesso 
- Eliminare un record di controllo di accesso 

> [AZURE.IMPORTANT] 
> 
> - Quando si assegna una ACR a un volume, prestare attenzione che il volume non è accessibile contemporaneamente da più host non cluster poiché questo potrebbe danneggiare il volume. 
> - Quando si elimina un ACR da un volume, assicurarsi che l'host corrispondente non accede il volume perché potrebbe comportare l'eliminazione di un'interruzione di lettura e scrittura.

## <a name="get-the-iqn"></a>Ottenere il IQN

Per ottenere IQN di un host di Windows che esegue Windows Server 2012, procedere come segue.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Aggiungere un ACR

Utilizzare la pagina di **configurazione** del servizio di gestione StorSimple aggiungere ACRs. In genere, associare una ACR con un volume.

Per informazioni sull'associazione di un ACR con un volume, vedere aggiungere [un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Quando si assegna una ACR a un volume, prestare attenzione che il volume non è accessibile contemporaneamente da più host non cluster poiché questo potrebbe danneggiare il volume.
 
Per aggiungere un ACR, procedere come segue.

#### <a name="to-add-an-acr"></a>Per aggiungere un ACR

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic sulla scheda **configurazione** .

    ![scheda Configurazione](./media/storsimple-ova-manage-acrs/acr1.png)

2. Nell'elenco tabulare in **record di controllo di accesso**, specificare un **nome** per il ACR.

3. In **nome iniziatore iSCSI**, immettere il nome IQN dell'host del Windows. 

4. Fare clic su **Salva** nella parte inferiore della pagina per salvare ACR appena creato. Verrà visualizzato il seguente messaggio di conferma.

    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr2.png)

5. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per riflettere questa aggiunta.

## <a name="edit-an-acr"></a>Modificare un ACR

Utilizzare la pagina di **configurazione** nel portale di classica Azure modificare ACRs. 

> [AZURE.NOTE] È necessario modificare solo tali ACRs che non sono attualmente in uso. Per modificare un ACR associato a un volume è attualmente in uso, è necessario prima di tutto disconnettere il volume.

Per modificare un ACR, procedere come segue.

#### <a name="to-edit-an-acr"></a>Per modificare un ACR

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic sulla scheda **configurazione** .

2. Nell'elenco tabulare i record di controllo di accesso, passare il mouse su ACR che si desidera modificare.

3. Fornire un nuovo nome e/o IQN per il ACR.

4. Fare clic su **Salva** nella parte inferiore della pagina per salvare ACR modificate. Verrà visualizzato un messaggio di conferma. 

5. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornato per rendere effettiva la modifica.

## <a name="delete-an-access-control-record"></a>Eliminare un record di controllo di accesso

Utilizzare la pagina di **configurazione** nel portale di classica Azure eliminare ACRs. 

> [AZURE.NOTE] 
> 
> - È necessario eliminare solo tali ACRs che non sono attualmente in uso. Per eliminare un ACR associato a un volume è attualmente in uso, è necessario prima di tutto disconnettere il volume.
> - Quando si elimina un ACR da un volume, assicurarsi che l'host corrispondente non accede il volume perché potrebbe comportare l'eliminazione di un'interruzione di lettura e scrittura.

Per eliminare un record di controllo di accesso, procedere come segue.

#### <a name="to-delete-an-access-control-record"></a>Per eliminare un record di controllo di accesso

1. Nella pagina di destinazione del servizio, selezionare il servizio, fare doppio clic sul nome di servizio e quindi fare clic sulla scheda **configurazione** .

2. Nell'elenco tabulare dei record di controllo di accesso (ACRs), passare il mouse su ACR che si desidera eliminare.

3. Verrà visualizzata un'icona di eliminazione (**x**) nella colonna a destra estrema per ACR selezionato. Fare clic sull'icona di **x** per eliminare il ACR. Verrà visualizzato il seguente messaggio di conferma.

    ![messaggio di conferma](./media/storsimple-ova-manage-acrs/acr3.png)

5. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-manage-acrs/check-icon.png). L'elenco tabulare verrà aggiornata per riflettere l'eliminazione.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'aggiunta di volumi e la configurazione ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
