<properties
    pageTitle="Eseguire il monitoraggio a superficie hub con registro Analitica | Microsoft Azure"
    description="Utilizzare la soluzione superficie Hub per rilevare lo stato dell'hub di superficie e capire come in uso."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="monitor-surface-hubs-with-log-analytics"></a>Hub superficie monitor con registro Analitica

Questo articolo descrive come è possibile utilizzare la soluzione superficie Hub di Log Analitica per monitorare Microsoft Surface Hub dispositivi con Microsoft operazioni di gestione famiglia di prodotti (OMS). Registro Analitica consente di rilevare lo stato dell'hub di superficie, oltre a capire come in uso.

Ogni Hub Surface con installato l'agente di monitoraggio Microsoft. L'agente che è possibile inviare dati dall'Hub di superficie a OMS. File di log vengono letti dal hub superficie e sono quindi inviati al servizio Outlook Mobile. Problemi come server sia in linea, il calendario non sincronizzato, oppure se l'account di dispositivo non è in grado di accedere a Skype vengono visualizzate in OMS nel dashboard superficie Hub. Utilizzando i dati nel dashboard, è possibile identificare i dispositivi che non eseguono o che si verificano altri problemi e potenzialmente applicare correzioni per i problemi rilevati.


## <a name="installing-and-configuring-the-solution"></a>Installare e configurare la soluzione

Utilizzare le informazioni seguenti per installare e configurare la soluzione. Per gestire l'hub di superficie da Microsoft operazioni di gestione famiglia di prodotti (OMS), è necessario disporre le operazioni seguenti:

- Abbonamento valido a [OMS](http://www.microsoft.com/oms).
- Un livello di [abbonamento OMS](https://azure.microsoft.com/pricing/details/log-analytics/) in grado di supportare il numero di dispositivi che si desidera eseguire il monitoraggio. Prezzi OMS variabile a seconda quanti dispositivi sono iscritti e la quantità di dati è processi. È consigliabile da prendere in considerazione per la pianificazione dell'implementazione superficie Hub.

Successivamente, si verrà aggiunta di un abbonamento a OMS all'attuale abbonamento Microsoft Azure o creare una nuova area di lavoro tramite il portale OMS. Istruzioni dettagliate per l'uso dei metodi descritti si trova nella [Guida introduttiva a Log Analitica](log-analytics-get-started.md). Dopo aver configurata la sottoscrizione OMS, esistono due modi per registrare i dispositivi Surface Hub:

- Automaticamente tramite InTune
- Manualmente tramite **le impostazioni** nel dispositivo Hub superficie.

## <a name="set-up-monitoring"></a>Configurare il monitoraggio

È possibile monitorare l'integrità e attività dell'Hub di superficie utilizzando Analitica Log in OMS. È possibile registrare Hub superficie in OMS mediante la funzione InTune o localmente utilizzando **le impostazioni** dell'hub superficie.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Connettersi a superficie hub OMS tramite InTune

È necessario l'ID dell'area di lavoro e la chiave dell'area di lavoro per l'area di lavoro OMS che gestirà il hub superficie. È possibile accedere a queste dal portale di OMS.

InTune è un prodotto Microsoft che consente di gestire centralmente le impostazioni di configurazione OMS applicati a uno o più dispositivi. Seguire questa procedura per configurare i dispositivi tramite InTune:

1. Accedere a InTune.
2. Passare a **Impostazioni** > **connessi origini**.
3. Creare o modificare un criterio basato sul modello di superficie Hub.
4. Passare alla sezione OMS (approfondimenti operativi Azure) dei criteri e aggiungere *l'ID dell'area di lavoro* e *Come chiave dell'area di lavoro* per il criterio.
5. Salvare il criterio.
6. Associare i criteri di gruppo di dispositivi appropriato.

  ![Criteri InTune](./media/log-analytics-surface-hubs/intune.png)

InTune quindi Sincronizza le impostazioni di OMS con i dispositivi nel gruppo di destinatari, la registrazione l'area di lavoro OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Connettersi superficie hub OMS mediante l'app di impostazioni

È necessario l'ID dell'area di lavoro e la chiave dell'area di lavoro per l'area di lavoro OMS che gestirà il hub superficie. È possibile accedere a queste dal portale di OMS.

Se non si usa InTune per gestire l'ambiente, è possibile registrare il dispositivo manualmente tramite **le impostazioni** su ogni Hub superficie:

1. Il tuo fulcro superficie, aprire **Impostazioni**.
2. Immettere le credenziali di amministratore di dispositivo quando richiesto.
3. Fare clic su **questo dispositivo**e in **monitoraggio**, fare clic su **Configura impostazioni OMS**.
4. Selezionare **Abilita il monitoraggio**.
6. Nella finestra di dialogo Impostazioni OMS digitare l' **ID dell'area di lavoro** e la **Chiave dell'area di lavoro**.  
  ![Impostazioni](./media/log-analytics-surface-hubs/settings.png)
7. Fare clic su **OK** per completare la configurazione.

Un messaggio di conferma verrà visualizzato il o meno la configurazione OMS sia stata applicata al dispositivo. Se è stato visualizzato un messaggio che informa che l'agente connesso al servizio Outlook Mobile. Il dispositivo inizia quindi a inviare dati a OMS nel punto in cui è possibile visualizzare e agire su di esso.

## <a name="monitor-surface-hubs"></a>Hub superficie monitor

Monitorare l'hub di superficie utilizzando OMS è molto simile monitoraggio altri dispositivi iscritti.

1. Accedere al portale di OMS.
2. Passare a dashboard superficie Hub soluzione pack.
3. Viene visualizzata l'integrità del dispositivo.

  ![Dashboard Hub a superficie](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

È possibile creare [avvisi](log-analytics-alerts.md) in base a ricerche log esistente o personalizzato. Uso dei dati che OMS raccoglie dall'hub di superficie, è possibile cercare problemi e avviso per le condizioni definite dall'utente per i dispositivi.


## <a name="next-steps"></a>Passaggi successivi

- Utilizzare [le ricerche Log in Analitica Log](log-analytics-log-searches.md) per visualizzare dati superficie Hub dettagliati.
- Consente di creare [avvisi](log-analytics-alerts.md) per ricevere una notifica quando si verificano problemi con l'hub di superficie.
