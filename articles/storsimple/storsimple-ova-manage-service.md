<properties 
   pageTitle="Distribuire il servizio di gestione di StorSimple per matrice virtuale StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come creare ed eliminare il servizio di gestione StorSimple nel portale di classica Azure e viene illustrato come gestire la chiave di registrazione del servizio."
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
   ms.date="05/19/2016"
   ms.author="alkohli" />

# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>Distribuire il servizio di gestione di StorSimple per StorSimple matrice virtuale

## <a name="overview"></a>Panoramica

Il servizio di gestione StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Dopo aver creato il servizio, è possibile utilizzare per gestire i dispositivi dal portale classico di Microsoft Azure in esecuzione in un browser. In questo modo è possibile monitorare tutti i dispositivi connessi al servizio di gestione StorSimple da un'unica posizione centrale, riducendo carico amministrativo.

La pagina di destinazione StorSimple Manager sono elencati tutti i servizi di StorSimple Manager che è possibile utilizzare per gestire i dispositivi di archiviazione StorSimple. Per ogni servizio di gestione StorSimple, vengono presentate le informazioni seguenti nella pagina gestore StorSimple:

- **Nome** : il nome assegnato al servizio di gestione StorSimple quando è stata creata. Il nome del servizio non possa essere modificato dopo aver creato il servizio.

- **Stato** : lo stato del servizio, che può essere **attivo**, **creazione**o **Online**.

- **Posizione** : posizione geografica in cui verrà distribuito il dispositivo StorSimple.

- **Abbonamento** – la sottoscrizione di fatturazione associata al servizio.

Attività comuni che possono essere eseguite tramite la pagina StorSimple Manager sono:

- Creare un servizio
- Eliminare un servizio
- Ottenere la chiave di registrazione del servizio
- Rigenerare la chiave di registrazione del servizio

In questa esercitazione viene descritto come eseguire ognuna di queste attività. Le informazioni contenute in questo articolo sono applicabile solo ai StorSimple virtuale matrici. Per ulteriori informazioni sulla serie 8000 StorSimple, passare alla [distribuzione di un servizio di gestione StorSimple](storsimple-manage-service.md).

## <a name="create-a-service"></a>Creare un servizio

Utilizzare l'opzione **Creazione rapida** per creare un servizio di gestione StorSimple se si desidera distribuire il dispositivo StorSimple. Per creare un servizio, è necessario che:

- Un abbonamento con un contratto Enterprise
- Un account di archiviazione di Microsoft Azure attivo
- Le informazioni di fatturazione utilizzato per la gestione degli accessi

È anche possibile generare un account di archiviazione predefinito quando si crea il servizio.

Un singolo servizio può gestire più dispositivi. Tuttavia, un dispositivo non è possibile estesi a più servizi. Una grande azienda può avere più istanze di servizio per lavorare con diversi abbonamenti, organizzazioni o anche i percorsi di distribuzione.  

> [AZURE.NOTE] È necessario istanze separate del servizio di gestione StorSimple per gestire i dispositivi di serie 8000 StorSimple e StorSimple virtuale matrici.

Per creare un servizio, procedere come segue.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminare un servizio

Prima di eliminare un servizio, assicurarsi che nessuna dispositivi connessi in uso. Se il servizio in uso, disattivare i dispositivi connessi. L'operazione disattiva server la connessione tra il dispositivo e il servizio, ma mantenere i dati di dispositivo nel cloud. 

> [AZURE.IMPORTANT] Dopo l'eliminazione di un servizio, l'operazione non può essere annullato. 

Per eliminare un servizio, procedere come segue.

### <a name="to-delete-a-service"></a>Per eliminare un servizio

1. Nella pagina **servizio StorSimple Manager** selezionare il servizio che si desidera eliminare.

1. Fare clic su **Elimina** nella parte inferiore della pagina.

1. Fare clic su **Sì** nel messaggio di notifica di conferma. Possono richiedere alcuni minuti per il servizio da eliminare.

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio

Dopo la creazione di un servizio è stata completata, sarà necessario eseguire la registrazione del dispositivo StorSimple con il servizio. Per registrare il primo dispositivo StorSimple, sarà necessario la chiave di registrazione del servizio. Per registrare altri dispositivi con un servizio StorSimple esistente, è necessario il codice di registrazione e la chiave di crittografia di dati di servizio (che viene generata sul primo dispositivo durante la registrazione). Per ulteriori informazioni su della chiave di crittografia di servizio dati, vedere [visualizzare la chiave di crittografia del servizio dati dal web locale dell'interfaccia utente](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key). 

Per ottenere la chiave di registrazione del servizio, procedere come segue.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Mantenere la chiave di registrazione del servizio in un percorso sicuro. Sarà necessario questo tasto, come chiave di crittografia dati servizio, per registrare altri dispositivi con il servizio. Dopo aver ottenuto la chiave di registrazione del servizio, sarà necessario configurare il dispositivo tramite Windows PowerShell per l'interfaccia StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio

È necessario rigenerare una chiave di registrazione servizio se è necessario eseguire rotazione delle chiavi o se è stato modificato l'elenco di amministratori dei servizi. Quando si rigenerare la chiave, la nuova chiave verrà usata solo per la registrazione periferiche successivi. I dispositivi già registrati sono interessati da questo processo.

Eseguire la procedura seguente per rigenerare una chiave di registrazione di servizio.

### <a name="to-regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio

1. Nella pagina **servizio StorSimple Manager** , fare clic su **Chiave di registrazione**.

1. Nella finestra di dialogo **Chiave di registrazione del servizio** , fare clic su **Rigenera**.

1. Verrà visualizzato un messaggio di conferma. Fare clic su **OK** per continuare con la rigenerazione.

1. Verrà visualizzata una nuova chiave di registrazione di servizio.

1. Copiare questa chiave e salvarla per la registrazione di eventuali nuovi dispositivi con il servizio.

1. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-ova-manage-service/image7.png) per chiudere la finestra di dialogo.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [iniziare](storsimple-ova-deploy1-portal-prep.md) a una matrice virtuale StorSimple.
    
- Informazioni su come [amministrare il dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

 
