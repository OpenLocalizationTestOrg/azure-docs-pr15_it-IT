<properties 
   pageTitle="Web matrice virtuale StorSimple amministrazione dell'interfaccia utente | Microsoft Azure"
   description="Viene descritto come eseguire attività amministrative di dispositivi di base tramite l'interfaccia web StorSimple matrice virtuale."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utilizzare l'interfaccia utente Web per amministrare la matrice virtuale StorSimple

![flusso del processo di configurazione](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Panoramica

Le esercitazioni in questo articolo si applicano a Microsoft Azure StorSimple virtuale matrice (noto anche come il StorSimple locale virtuale dispositivo) esegue una versione di marzo 2016 disponibilità generale (GA). In questo articolo vengono illustrati alcuni dei flussi di lavoro complessi e attività di gestione che può essere eseguita nella matrice virtuale StorSimple. È possibile gestire la matrice virtuale StorSimple gestione StorSimple service dell'interfaccia utente (denominato portale dell'interfaccia utente) e il web locale dell'interfaccia utente per il dispositivo. Questo articolo analizza le attività che è possibile eseguire tramite l'interfaccia web.

In questo articolo include le esercitazioni seguenti:

- Ottenere la chiave di crittografia di servizio dati
- Risolvere i problemi di configurazione dell'interfaccia utente web
- Generare un pacchetto di log
- Arrestare o riavviare il dispositivo

## <a name="get-the-service-data-encryption-key"></a>Ottenere la chiave di crittografia di servizio dati

Quando si registra il dispositivo primo con il servizio di gestione StorSimple, viene generata una chiave di crittografia di servizio dati. Questa chiave è quindi necessario con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio di gestione StorSimple.

Se è stato smarrito la chiave di crittografia di servizio dati ed è necessario recuperarla, eseguire le operazioni seguenti passaggi l'interfaccia del dispositivo web locale registrate con il servizio.

#### <a name="to-get-the-service-data-encryption-key"></a>Per ottenere la chiave di crittografia di servizio dati

1. Connettersi al web locale dell'interfaccia utente. Passare alla **configurazione** > **Impostazioni Cloud**.
  

2. Nella parte inferiore della pagina, fare clic su **chiave di crittografia di servizio dati**. Verrà visualizzata una chiave. Copiare e salvare questo tasto.
    
    ![ottenere chiave di crittografia di servizio dati 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Risolvere i problemi di configurazione dell'interfaccia utente web

In alcuni casi quando si configura il dispositivo tramite web locale dell'interfaccia utente, che possono verificarsi errori. Per individuare e risolvere questi errori, è possibile eseguire il test di diagnostica.

#### <a name="to-run-the-diagnostic-tests"></a>Per eseguire il test di diagnostica

1. Nell'interfaccia utente di web locale, passare alla **risoluzione dei problemi** > **test di diagnostica**.

    ![diagnostica fase 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Nella parte inferiore della pagina, fare clic su **Esegui test di diagnostica**. Verrà avviata test per diagnosticare eventuali possibili problemi con la rete, dispositivo, proxy web, ora o impostazioni cloud. L'utente verrà informato che il dispositivo sia in esecuzione di test.

3. Dopo avere completato i test, verranno visualizzati i risultati. Nell'esempio seguente mostra i risultati dei test di diagnostica. Si noti che le impostazioni del proxy web non sono state configurate nel dispositivo e quindi il test di proxy web non è stato eseguito. Tutti gli altri test per le impostazioni di rete, server DNS e le impostazioni dell'ora è stata eseguita correttamente.

    ![eseguire test di diagnostica 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generare un pacchetto di log

Un pacchetto di log è costituito da tutti i log pertinenti che consentono di supporto Microsoft sulla risoluzione dei problemi qualsiasi dispositivo. In questa versione, è possibile generare un pacchetto di log tramite l'interfaccia web locale.

#### <a name="to-generate-the-log-package"></a>Per generare il pacchetto di log

1. Nell'interfaccia utente di web locale, passare alla **risoluzione dei problemi** > **registri eventi di sistema**.

    ![generare log pacchetto 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Nella parte inferiore della pagina, fare clic su **Crea pacchetto di log**. Verrà creato un pacchetto di registri di sistema. Verrà un paio di minuti.

    ![generare il pacchetto log 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Verrà visualizzato dopo il pacchetto è stato creato correttamente e la pagina verrà aggiornata per indicare la data in cui è stato creato il pacchetto e l'ora.

    ![generare il pacchetto di registro 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Fare clic su **scaricare il pacchetto di log**. Un pacchetto zip verrà scaricato nel sistema.

    ![generare il pacchetto di log 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. È possibile decomprimere il pacchetto di log scaricati e visualizzare i file di registro di sistema.

## <a name="shut-down-and-restart-your-device"></a>Arrestare e riavviare il dispositivo

Per arrestare o riavviare il dispositivo virtuale tramite l'interfaccia web locale. È consigliabile che prima di riavviare, eseguire i volumi o condivisioni offline su host, quindi il dispositivo. Si ridurrà la possibilità di danneggiamento dei dati. 

#### <a name="to-shut-down-your-virtual-device"></a>Per arrestare il dispositivo virtuale

1. Nell'interfaccia utente di web locale, passare a **manutenzione** > **le impostazioni di alimentazione**.

2. Nella parte inferiore della pagina, scegliere **Arresta**.

    ![arresto dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Verrà visualizzato un avviso che indica che la chiusura del dispositivo interromperà qualsiasi IO in corso, causando un tempo di inattività. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-web-ui-admin/image3.png).

    ![avviso di arresto dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)

    L'utente verrà informato che è stata avviata l'arresto.

    ![chiusura di dispositivo avviato](./media/storsimple-ova-web-ui-admin/image38.png)

    Il dispositivo verrà chiuso. Se si desidera iniziare il dispositivo, sarà necessario eseguire tale operazione tramite la gestione di Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Per riavviare il dispositivo virtuale

1. Nell'interfaccia utente di web locale, passare a **manutenzione** > **le impostazioni di alimentazione**.

2. Nella parte inferiore della pagina, scegliere **Riavvia**.

    ![riavvio di dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)

3. Verrà visualizzato un avviso che indica che il riavvio del dispositivo interromperà qualsiasi IOs in corso, causando un tempo di inattività. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Riavviare avviso](./media/storsimple-ova-web-ui-admin/image37.png)

    L'utente verrà informato che il riavvio è stato avviato.

    ![riavvio avviato](./media/storsimple-ova-web-ui-admin/image39.png)

    Durante il riavvio dell'interfaccia utente andranno perse la connessione. È possibile controllare il riavvio aggiornando periodicamente l'interfaccia utente. In alternativa, è possibile monitorare lo stato di riavvio del dispositivo tramite la gestione di Hyper-V.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare il servizio di gestione di StorSimple per gestire il dispositivo](storsimple-manager-service-administration.md).
