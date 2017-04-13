<properties
    pageTitle="Introduzione a soluzioni preconfigurate | Microsoft Azure"
    description="Seguire questa esercitazione per informazioni su come distribuire una soluzione di Azure IoT Suite preconfigurato."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Esercitazione: Introduzione a soluzioni preconfigurate

## <a name="introduction"></a>Introduzione

Azure IoT Suite [soluzioni preconfigurate] [ lnk-preconfigured-solutions] combinare più servizi Azure IoT per offrire soluzioni-to-end che implementano scenari aziendali IoT comuni. Soluzione *monitoraggio remoto* preconfigurato si connette a e monitora i dispositivi. È possibile utilizzare la soluzione per analizzare il flusso di dati dai dispositivi e per migliorare i risultati aziendali eseguendo processi rispondere automaticamente a tale flusso di dati.

In questa esercitazione viene illustrato come effettuare il provisioning della soluzione di preconfigurato monitoraggio remota. Inoltre illustra le funzionalità di base della soluzione di monitoraggio remoto. È possibile accedere a molte di queste funzionalità tramite il dashboard di soluzione che distribuisce insieme alla soluzione preconfigurata:

![Monitoraggio remoto preconfigurato dashboard soluzione][img-dashboard]

Per completare questa esercitazione, è necessario un abbonamento attivo a Azure.

> [AZURE.NOTE]  Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Visualizzare il dashboard di soluzione

Dashboard di soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare telemetria, aggiungere dispositivi e configurare le regole.

1.  Quando è stato completato il provisioning e il riquadro per la soluzione preconfigurato indica **pronti**, fare clic su **barra di avvio** per aprire il portale soluzione monitoraggio remoto in una nuova scheda.

    ![Avviare la soluzione preconfigurata][img-launch-solution]

2.  Per impostazione predefinita, il portale soluzione Mostra *dashboard soluzione*. È possibile selezionare le altre visualizzazioni usando il menu a sinistra.

    ![Monitoraggio remoto preconfigurato dashboard soluzione][img-dashboard]

Il dashboard vengono visualizzate le informazioni seguenti:

- La mappa consente di visualizzare il percorso di ogni dispositivo connesso alla soluzione. Quando si esegue prima di tutto la soluzione, sono disponibili quattro dispositivi simulati. I dispositivi simulati sono implementati come WebJobs Azure e la soluzione utilizza API Bing Maps per tracciare le informazioni sulla mappa.
- Riquadro **Cronologia telemetria** vengono tracciati telemetria umidità e temperatura da un dispositivo selezionato in quasi in tempo reale e consente di visualizzare dati di aggregazione, ad esempio umidità massima, minima e Media.
- Il pannello **Cronologia allarmi** Mostra recenti eventi di avviso quando un valore di telemetria ha superato la soglia. È possibile definire i proprio Sveglie oltre a esempi creati la soluzione preconfigurata.

## <a name="view-the-device-list"></a>Visualizzare l'elenco dei dispositivi

L'elenco dei dispositivi Mostra tutti i dispositivi registrati nella soluzione. Visualizzare e modificare i metadati di dispositivo, aggiungere o rimuovere i dispositivi e inviare i comandi per i dispositivi.

1.  Fare clic su **dispositivi** nel menu a sinistro per visualizzare l' *elenco dei dispositivi* per questa soluzione.

    ![Nell'elenco dei dispositivi dashboard][img-devicelist]

2.  L'elenco dei dispositivi Mostra vi sono quattro dispositivi simulati creati dal processo di provisioning.

3.  Fare clic su un dispositivo nell'elenco dei dispositivi per visualizzare i dettagli del dispositivo.

    ![Dettagli dispositivo nel dashboard][img-devicedetails]

Riquadro **Dei dettagli dispositivo** contiene tre sezioni:

- La sezione **Azioni** Elenca le azioni eseguite sul dispositivo. Se si disabilita il dispositivo, non è consentita di telemetria di inviare o ricevere i comandi. Se si disabilita un dispositivo, è possibile quindi riattivarlo. È possibile aggiungere una regola associata con il dispositivo che attiva un avviso quando un valore di telemetria supera una determinata soglia. È anche possibile inviare un comando a un dispositivo. Quando un dispositivo si connette prima di tutto, indica la soluzione i comandi che è possibile rispondere.
- La sezione **Proprietà dei dispositivi** sono elencati i metadati di dispositivo. Alcuni di questi metadati provengono da dispositivo (ad esempio, il produttore) e alcune viene generato dalla soluzione (ad esempio l'ora di creazione). È possibile modificare i metadati di dispositivo da qui.
- La sezione **Autenticazione chiavi** elencate le chiavi che il dispositivo consente di eseguire l'autenticazione con la soluzione.

## <a name="send-a-command-to-a-device"></a>Inviare un comando a un dispositivo

Riquadro dei dettagli dispositivo Mostra tutti i comandi che supporta un dispositivo specifico e consente di inviare comandi a un dispositivo. Al primo avvio di un dispositivo invia informazioni sui comandi supportati per la soluzione.

1.  Fare clic su **comandi** nel riquadro dei dettagli dispositivo per il dispositivo selezionato.

    ![Comandi di dispositivo nel dashboard][img-devicecommands]

2.  Selezionare **PingDevice** dall'elenco dei comandi.

3.  Fare clic su **comando Invia**.

4.  È possibile visualizzare lo stato del comando nella cronologia dei comandi.

    ![Stato del comando nel dashboard][img-pingcommand]

La soluzione consente di tenere traccia dello stato di ogni comando che invia. Inizialmente il risultato è **in sospeso**. Quando il dispositivo segnala che è eseguito il comando, il risultato è impostato su **esito positivo**.

## <a name="add-a-new-simulated-device"></a>Aggiungere un nuovo dispositivo simulato

Quando si distribuisce la soluzione preconfigurata, viene automaticamente effettuato il provisioning di quattro dispositivi di esempio che è possibile visualizzare nell'elenco dei dispositivi Questi dispositivi sono *dispositivi simulati* in esecuzione in un WebJob Azure. Dispositivi simulati semplificano acquisire familiarità con la soluzione preconfigurata senza la necessità di distribuire dispositivi reali, fisici. Se si desidera collegare un dispositivo reale alla soluzione, vedere [connettere il dispositivo per il computer remoto monitoraggio soluzione preconfigurato] [ lnk-connect-rm] esercitazione.

La procedura seguente viene illustrato come aggiungere un dispositivo simulato alla soluzione:

1.  Passare all'elenco dispositivo.

2.  Fare clic su **+ Aggiungi dispositivi** nell'angolo inferiore sinistro per aggiungere un dispositivo.

    ![Aggiunta di un dispositivo per la soluzione preconfigurata][img-adddevice]

3.  Fare clic su **Aggiungi nuovo** sul riquadro **Simulata dispositivo** .

    ![Impostare nuovi dettagli dispositivo nel dashboard][img-addnew]
    
    Oltre a creare un nuovo dispositivo simulato, è possibile aggiungere un dispositivo fisico se si sceglie di creare un **Dispositivo personalizzati**. Per ulteriori informazioni sulla connessione di dispositivi fisici alla soluzione, vedere [connettere il dispositivo per la famiglia IoT remoto preconfigurato soluzione di monitoraggio][lnk-connect-rm].

4.  Selezionare **Consenti definire il proprio ID dispositivo**e immettere un nome di ID univoco del dispositivo, ad esempio **mydevice_01**.

5.  Fare clic su **Crea**.

    ![Salvare un nuovo dispositivo][img-definedevice]

6. Nel passaggio 3 di **un dispositivo simulato**, fare clic su **fatto** per tornare all'elenco dispositivo.

7. È possibile visualizzare il dispositivo **in esecuzione** nell'elenco dei dispositivi.

    ![Nuovi dispositivi di visualizzazione nell'elenco dei dispositivi][img-runningnew]

8. È inoltre possibile visualizzare telemetria simulata dal dispositivo nuovo nel dashboard:

    ![Visualizzazione di telemetria da nuovi dispositivi][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Modificare i metadati di dispositivo

Quando un dispositivo si connette prima di tutto alla soluzione, invia i metadati alla soluzione. Quando si modificano i metadati di dispositivo tramite dashboard soluzione, invia i nuovi valori dei metadati al dispositivo e archiviati i nuovi valori nel database DocumentDB soluzione. Per ulteriori informazioni, vedere [il Registro di sistema di dispositivo identità e DocumentDB][lnk-devicemetadata].

1.  Passare all'elenco dispositivo.

2.  Selezionare nuovi dispositivi nell' **Elenco dei dispositivi**e quindi fare clic su **Modifica** per modificare le **Proprietà dei dispositivi**:

    ![Modificare i metadati di dispositivo][img-editdevice]

3. Scorrere verso il basso e apportare modifiche a vales latitudine e longitudine. Fare clic su **Salva le modifiche apportate del Registro di sistema di dispositivo**.

    ![Modificare i metadati di dispositivo][img-editdevice2]

4. Tornare al dashboard, il percorso del dispositivo è cambiato sulla mappa:

    ![Modificare i metadati di dispositivo][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Aggiungere una regola per il nuovo dispositivo

Non esistono regole per la nuova periferica che appena aggiunto. In questa sezione, aggiungere una regola che attiva un avviso quando la temperatura segnalata dal nuovo device supera 47 gradi. Prima di iniziare, si noterà che la cronologia di telemetria per la nuova periferica nel dashboard di mostra che la temperatura dispositivo non superano mai il 45 gradi.

1.  Passare all'elenco dispositivo.

2.  Selezionare il dispositivo nuovo nell' **Elenco dei dispositivi**e quindi fare clic su **Aggiungi regola** per aggiungere una regola per il dispositivo.

3. Creare una regola che utilizza **temperatura** del campo dati e **AlarmTemp** come output quando la temperatura supera 47 gradi:

    ![Aggiungere una regola di dispositivo][img-adddevicerule]

4. Fare clic su **Salva e visualizzare le regole** per salvare le modifiche.

5.  Fare clic su **comandi** nel riquadro dei dettagli dispositivo per il nuovo dispositivo.

    ![Aggiungere una regola di dispositivo][img-adddevicerule2]

6.  Selezionare **ChangeSetPointTemp** dall'elenco di comandi e impostare **SetPointTemp** su 45. Quindi fare clic su **Invia un comando**:

    ![Aggiungere una regola di dispositivo][img-adddevicerule3]

7.  Passare al dashboard di soluzione. Dopo alcuni secondi, una nuova voce nel riquadro **Cronologia avviso** verrà visualizzato quando la temperatura svolto nuovi dispositivi supera la soglia 47 gradi:

    ![Aggiungere una regola di dispositivo][img-adddevicerule4]

8. È possibile esaminare e modificare tutte le regole nella pagina **regole** del dashboard:

    ![Regole di dispositivi di elenco][img-rules]

9. È possibile esaminare e modificare tutte le azioni che è possibile eseguire in risposta a una regola nella scheda **Azioni** del dashboard:

    ![Azioni elenco dispositivo][img-actions]

> [AZURE.NOTE] È possibile definire le azioni che è possono inviare un messaggio di posta elettronica o SMS in risposta a una regola o integrazione con un sistema line-of-business tramite un' [App di logica][lnk-logic-apps]. Per ulteriori informazioni, vedere [connessione logica App monitoraggio remoto il Azure IoT Suite preconfigurato soluzione][lnk-logicapptutorial].

## <a name="other-features"></a>Altre caratteristiche

Tramite il portale di soluzione che è possibile eseguire ricerche per i dispositivi con caratteristiche specifiche, ad esempio un numero di modello:

![Ricerca di un dispositivo][img-search]

È possibile disattivare un dispositivo e dopo la disattivazione è possibile rimuoverlo:

![Disattivare e rimuovere un dispositivo][img-disable]

## <a name="behind-the-scenes"></a>Dietro le quinte

Quando si distribuisce una soluzione preconfigurata, il processo di distribuzione crea più risorse nella sottoscrizione Azure selezionato. È possibile visualizzare queste risorse nel [portale]di Azure[lnk-portal]. Il processo di distribuzione crea un **gruppo di risorse** con un nome in base al nome che scelto per la soluzione preconfigurata:

![Soluzione preconfigurato nel portale di Azure][img-portal]

È possibile visualizzare le impostazioni di ogni risorsa selezionandolo nell'elenco delle risorse nel gruppo di risorse.

È anche possibile visualizzare il codice sorgente per la soluzione preconfigurato. Il codice di origine soluzione preconfigurato monitoraggio remoto non è in [azure iot-remota monitoraggio] [ lnk-rmgithub] GitHub repository:

- La cartella **DeviceAdministration** contiene il codice sorgente per il dashboard.
- La cartella **simulatore** contiene il codice sorgente per il dispositivo simulato.
- La cartella **EventProcessor** contiene il codice sorgente per il processo di back-end che gestisce la posta in arrivo telemetria.

Al termine, è possibile eliminare la soluzione preconfigurata dall'abbonamento Azure in [azureiotsuite.com] [ lnk-azureiotsuite] sito. Questo sito è possibile eliminarlo facilmente tutte le risorse che è sono effettuato il provisioning al momento della creazione della soluzione preconfigurata.

> [AZURE.NOTE] Per garantire eliminare tutti gli elementi correlati alla soluzione preconfigurata, eliminarla in [azureiotsuite.com] [ lnk-azureiotsuite] del sito e non è sufficiente eliminare il gruppo di risorse nel portale.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata distribuita una soluzione di lavoro preconfigurato, è possibile continuare a Introduzione a IoT famiglia di prodotti, leggere gli articoli seguenti:

- [Monitoraggio remoto preconfigurato soluzione dettagliata][lnk-rm-walkthrough]
- [Connettere il dispositivo per la soluzione preconfigurata monitoraggio remota][lnk-connect-rm]
- [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
