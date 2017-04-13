<properties
 pageTitle="Guida introduttiva a utilità di pianificazione di Azure nel portale di Azure | Microsoft Azure"
 description="Guida introduttiva a utilità di pianificazione di Azure nel portale di Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Guida introduttiva a utilità di pianificazione di Azure nel portale di Azure

È semplice creare programmate in Azure utilità di pianificazione. In questa esercitazione verrà imparare a creare un processo. Verrà anche informazioni su funzionalità di gestione e il monitoraggio dell'utilità di pianificazione.

## <a name="create-a-job"></a>Creare un processo

1.  Accedere al [portale di Azure](https://portal.azure.com/).  

2.  Fare clic su **+ Nuovo** > digitare _utilità di pianificazione_ nella casella di ricerca > selezionare **utilità di pianificazione** nei risultati > fare clic su **Crea**.

     ![][marketplace-create]

3.  Creare un processo che accede semplicemente http://www.microsoft.com/ con una richiesta GET. Nella schermata del **Processo di pianificazione** , immettere le informazioni seguenti:

    1.  **Nome:**`getmicrosoft`  

    2.  **Abbonamento:** L'abbonamento Azure   

    3.  **Processo insieme:** Selezionare un insieme di processo esistente oppure fare clic su **Crea nuovo** > Immettere un nome.

4.  Quindi, in **Impostazioni azione**, è possibile definire i valori seguenti:

    1.  **Tipo azione:**` HTTP`  

    2.  **Metodo:**`GET`  

    3.  **URL:**` http://www.microsoft.com`  

      ![][action-settings]

5.  Infine, è possibile definire una pianificazione. Il processo può essere definito come un processo di copia unico, ma si seleziona una pianificazione di ricorrenza:

    1. **Ricorrenza**:`Recurring`

    2. **Avviare**: data odierna

    3. **Ricorrente ogni**:`12 Hours`

    4. **Fine entro**: due giorni che vanno dalla data odierna della data  

      ![][recurrence-schedule]

6.  Fare clic su **Crea**

## <a name="manage-and-monitor-jobs"></a>Gestire e monitorare i processi

Dopo aver creato un processo, viene visualizzato nel dashboard di Azure principale. Fare clic su processo e viene aperta una nuova finestra con le schede seguenti:

1.  Proprietà  

2.  Impostazioni di azione  

3.  Programmazione  

4.  Cronologia

5.  Utenti

    ![][job-overview]

### <a name="properties"></a>Proprietà

Queste proprietà di sola lettura descrivono i metadati di gestione del processo di pianificazione.

   ![][job-properties]


### <a name="action-settings"></a>Impostazioni di azione

Fare clic su un processo nella schermata di **processi** consente di configurare il processo. Consente di configurare le impostazioni avanzate, se non è stata configurata nella creazione guidata con una rapida.

Per tutti i tipi di azione, è possibile modificare il criterio Riprova e l'azione di errore.

Per i tipi di azione del processo HTTP e HTTPS, è possibile modificare il metodo a qualsiasi verbo HTTP consentito. È inoltre possibile aggiungere, eliminare o modificare le intestazioni e informazioni di autenticazione di base.

Per i tipi di azione coda lo spazio di archiviazione, è possibile modificare l'account di archiviazione, coda, token SA e corpo.

Per i tipi di azione bus di servizio, è possibile modificare dello spazio dei nomi, percorso argomento/coda, le impostazioni di autenticazione, tipo di trasporto, le proprietà dei messaggi e il corpo del messaggio.

   ![][job-action-settings]

### <a name="schedule"></a>Programmazione

Consente di riconfigurare la pianificazione, se si desidera modificare la pianificazione è stato creato nella creazione guidata con una rapida.

Si tratta di un'opportunità per creare [le pianificazioni complesse e avanzata ricorrenza nel processo](scheduler-advanced-complexity.md)

È possibile modificare la data di inizio e ora, programmazione ricorrenza e fine data e ora (se il processo è ricorrente.)

   ![][job-schedule]


### <a name="history"></a>Cronologia

La scheda **cronologia** consente di visualizzare metriche selezionate per ogni esecuzione del processo di sistema per il processo selezionato. Queste metriche forniscono i valori in tempo reale relative lo stato dell'utilità di pianificazione:

1.  Stato  

2.  Dettagli  

3.  Tentativi

4.  Occorrenza: 1 °, 2 °, 3 °, e così via.

5.  Ora di inizio dell'esecuzione  

6.  Ora di fine dell'esecuzione

   ![][job-history]

È possibile fare clic su Esegui per visualizzarne i **Dettagli della cronologia**, incluse la risposta intera per ogni esecuzione. Questa finestra di dialogo consente anche di copiare la risposta negli Appunti.

   ![][job-history-details]

### <a name="users"></a>Utenti

Azure basato sui ruoli accesso controllo (RBAC) consente la gestione degli accessi diffusamente per utilità di pianificazione di Azure. Per informazioni su come usare la scheda utenti, fare riferimento al [Controllo dell'accesso Azure Role-Based](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Vedere anche

 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Modalità di compilazione di pianificazioni complesse e ricorrenza avanzata con utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Guida di riferimento all'API REST utilità di pianificazione](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a utilità di pianificazione dei cmdlet di PowerShell](scheduler-powershell-reference.md)

 [Utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
