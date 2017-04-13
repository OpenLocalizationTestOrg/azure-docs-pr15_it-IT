<properties 
   pageTitle="Cambiare la password di amministratore di dispositivo virtuale StorSimple | Microsoft Azure"
   description="Viene descritto come utilizzare il portale classico Azure o web matrice virtuale StorSimple dell'interfaccia utente per cambiare la password di amministratore del dispositivo."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Modificare la password di amministratore del dispositivo matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale StorSimple, è necessario immettere una password di amministratore del dispositivo. Quando il dispositivo StorSimple è prima di tutto il provisioning e avviato, la password predefinita è *Password1*. Per la protezione dei dati, la password predefinita scade la prima volta che si accede e viene richiesto di modificare la password.

Per modificare la password di amministratore del dispositivo in qualsiasi momento dopo il dispositivo sia distribuito nell'ambiente di produzione, è possibile utilizzare l'interfaccia web locale o classica nel portale di Azure. Ognuna di queste procedure viene descritta in questo articolo.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Utilizzare il portale classico Azure per cambiare la password

Per cambiare la password di amministratore del dispositivo tramite il portale classica Azure, procedere come segue.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Per modificare la password di amministratore del dispositivo tramite il portale classica Azure

1. Nel portale, fare clic su **dispositivi** > **configurazione** del dispositivo.

2. Scorrere fino alla sezione **Password dell'amministratore del dispositivo** . Fornire una password di amministratore che contiene da 8 a 15 caratteri. La password deve essere una combinazione di caratteri maiuscoli, minuscoli, numerici e speciali.

3. Confermare la password.

4. Fare clic su **Salva** nella parte inferiore della pagina.

La password di amministratore del dispositivo deve essere aggiornata. È possibile utilizzare questa password modificata per accedere al dispositivo in locale.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Utilizzare il web matrice virtuale StorSimple dell'interfaccia utente per cambiare la password

Per cambiare la password di amministratore del dispositivo tramite l'interfaccia web locale, procedere come segue.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Per modificare la password di amministratore del dispositivo tramite l'interfaccia web locale

1. Nell'interfaccia utente di web locale, fare clic su **manutenzione** > **Modifica della Password** per il dispositivo.

    ![modifica della password1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Immettere la **password corrente**.

3. Fornire una **Nuova Password**. La password deve contenere almeno 8 caratteri. Deve contenere 3 di 4 delle operazioni seguenti: caratteri maiuscoli, minuscoli, numerici e speciali.

    Si noti che la password non può essere uguale ultime 24 password.

3. Immettere nuovamente la password per confermarla.

    ![modificare password2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Nella parte inferiore della pagina, fare clic su **Applica**. Verrà quindi applicata la nuova password. Se la modifica della password non viene eseguito correttamente, si verrà visualizzato l'errore seguente.

    ![Errore relativo alla password](./media/storsimple-ova-change-device-admin-password/image42.png)

    Dopo aver completato l'aggiornamento della password, verrà visualizzato. È quindi possibile utilizzare questa password modificata per accedere al dispositivo in locale.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'amministrazione della matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).
