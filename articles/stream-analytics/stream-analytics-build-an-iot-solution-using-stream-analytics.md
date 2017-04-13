<properties
    pageTitle="Creare una soluzione IoT utilizzando Stream Analitica | Microsoft Azure"
    description="Guida introduttiva esercitazione per la soluzione di flusso Analitica IoT di uno scenario di caselli"
    keywords="soluzione IOT, funzioni della finestra"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Creare una soluzione IoT utilizzando Stream Analitica

## <a name="introduction"></a>Introduzione

In questa esercitazione si imparerà a utilizzare Azure flusso Analitica per ottenere in tempo reale informazioni approfondite dai dati. Gli sviluppatori possono facilmente combinare flussi di dati, ad esempio fare clic su flussi e registri eventi generati dal dispositivo con record cronologici o dati di riferimento ricavare informazioni aziendali. Come servizio di calcolo flusso completamente gestita e in tempo reale ospitato in Microsoft Azure, Azure flusso Analitica offre flessibilità predefinite, latenza ridotta e scalabilità per diventare in esecuzione in minuti.

Al termine di questa esercitazione, sarà possibile per:

-   Acquisire familiarità con il portale di Azure flusso Analitica.
-   Configurare e distribuire un processo di flusso.
-   Esprimere problemi reali e risolverli utilizzando il linguaggio di query flusso Analitica.
-   Sviluppare streaming soluzioni per i clienti utilizzando Stream Analitica con fiducia.
-   Utilizzare il monitoraggio e la registrazione esperienza per la risoluzione dei problemi.

## <a name="prerequisites"></a>Prerequisiti di

È necessario i prerequisiti seguenti per completare l'esercitazione:

-   La versione più recente di [PowerShell di Azure](../powershell-install-configure.md)
-   Visual Studio 2015 o gratuita [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Una [sottoscrizione Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilegi di amministratore nel computer
-   Download di [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) dall'area Download Microsoft
-   Facoltativo: Codice sorgente per il generatore di evento TollApp in [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introduzione scenario: "Ciao, a pagamento!"


Un numero a pagamento è un fenomeno comune. Si verifichino loro molti limiti, ponti e tunnel in tutto il mondo. Ogni postazione a pagamento ha più stand a pagamento. In stand manuale, arresta per pagare il numero a pagamento per un operatore. In automatizzato stand, un sensore nella parte superiore di ogni stand analizza una scheda RFID apposta su parabrezza del veicolo di quello passato stand a pagamento. È facile visualizzare il passaggio di veicoli mediante le elettriche presenti questi a pagamento come un flusso di evento in cui è possibile eseguire operazioni interessanti.

![Immagine di auto in stand a pagamento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dati in arrivo

In questa esercitazione funziona con due flussi di dati. Sensori installati in entrata e di uscita di posti di a pagamento producono il primo flusso. Il secondo flusso è un set di dati di ricerca statica contenente dati di registrazione di veicolo.

### <a name="entry-data-stream"></a>Flusso di immissione dati

Il flusso di dati voce contiene informazioni auto che accedono le elettriche presenti a pagamento.


| TollID | EntryTime               | LicensePlate | Stato | Rendere   | Modello   | VehicleType | VehicleWeight | Numero verde | Tag       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 12:01:00.000 2014-09-10 | JNB 7001     | NY    | Rappresentato  | CRV     | 1           | 0             | 7    |           |
| 1      | 12:02:00.000 2014-09-10 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 12:02:00.000 2014-09-10 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 12:03:00.000 2014-09-10 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 12:03:00.000 2014-09-10 | BNJ 1007     | NY    | Rappresentato  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 12:05:00.000 2014-09-10 | CSI 1007     | NJ    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Ecco una breve descrizione delle colonne:

| Colonna | Descrizione |
|--------------|--------------------------------------------------------------------|
| TollID       | L'ID di stand a pagamento che identifica un stand a pagamento            |
| EntryTime    | La data e l'ora della voce di veicolo a stand a pagamento in formato UTC |
| LicensePlate | Il numero di licenze standard di veicolo                            |
| Stato        | Uno stato negli Stati Uniti                                           |
| Rendere         | Il produttore del che l'auto                                 |
| Modello        | Il numero di modello di che l'auto                                 |
| VehicleType  | 1 per autovetture e 2 per veicoli commerciali       |
| WeightType   | Peso veicolo in tonnellate; 0 per autovetture                   |
| Numero verde         | Il valore di numero a pagamento in dollari                                              |
| Tag          | Il contrassegno e su veicolo che consente di automatizzare pagamento; campo vuoto in cui è stato eseguito manualmente il pagamento |


### <a name="exit-data-stream"></a>Flusso di dati di uscita

Il flusso di dati di uscita contiene informazioni Auto lasciando espansione a pagamento.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CSI 1007         |

Ecco una breve descrizione delle colonne:


| Colonna | Descrizione |
|--------------|-----------------------------------------------------------------|
| TollID       | L'ID di stand a pagamento che identifica un stand a pagamento         |
| ExitTime     | La data e l'ora di uscita del veicolo dal stand a pagamento in formato UTC |
| LicensePlate | Il numero di licenze standard di veicolo                         |

### <a name="commercial-vehicle-registration-data"></a>Dati di registrazione veicolo commerciale

L'esercitazione utilizza uno snapshot statico di un database di registrazione veicolo commerciale.


| LicensePlate | RegistrationId | Scaduto |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| SFONDO 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Ecco una breve descrizione delle colonne:


| Colonna | Descrizione |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Il numero di licenze standard di veicolo                         |
| RegistrationId     | ID di registrazione del veicolo |
| Scaduto | Lo stato di registrazione del veicolo: 0 se la registrazione di veicolo è attiva, 1 se la registrazione è scaduta                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurare l'ambiente per Azure flusso Analitica

Per completare questa esercitazione, è necessario un abbonamento a Microsoft Azure. Microsoft offre versione di valutazione gratuita per i servizi di Microsoft Azure.

Se non si dispone di un account Azure, è possibile [richiesta una versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Per iscriversi a una versione di valutazione gratuita, è necessario un cellulare in grado di ricevere messaggi di testo e una carta di credito valido.

Assicurarsi di seguire i passaggi indicati nella sezione "Pulire account Azure" alla fine di questo articolo in modo che è possibile utilizzare al meglio il 200 $ gratuiti Azure carta di credito.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Effettuare il provisioning di Azure risorse necessarie per l'esercitazione

In questa esercitazione richiede due hub di evento per ricevere *voce* e *uscire da* flussi di dati. Database SQL Azure restituisce i risultati dei processi flusso Analitica. Spazio di archiviazione Azure archivia i dati di riferimento sulle registrazioni di veicolo.

È possibile utilizzare lo script Setup.ps1 nella cartella TollApp GitHub per creare tutte le risorse necessarie. Per motivi di tempo, è consigliabile che viene eseguito. Se si desidera ulteriori informazioni su come configurare queste risorse nel portale di Azure, consultare Appendice "Configurazione delle risorse esercitazioni nel portale di Azure".

Scaricare e salvare la cartella [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) supporto e file.

Aprire un **Microsoft Azure PowerShell** finestra _come amministratore_. Se ancora non si dispone di PowerShell di Azure, seguire le istruzioni di [installazione e configurazione di Azure PowerShell](../powershell-install-configure.md) per installarlo.

Poiché Windows blocca automaticamente ps1, dll e file .exe, è necessario impostare il criterio di esecuzione prima di eseguire lo script. Assicurarsi che la finestra di PowerShell Azure viene eseguito _come amministratore_. Eseguire **Set-ExecutionPolicy senza restrizioni**. Quando richiesto, digitare **Y**.

![Schermata di "Set-ExecutionPolicy illimitato" in esecuzione nella finestra di PowerShell di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Eseguire **Get-ExecutionPolicy** per assicurarsi che il comando sia stata eseguita.

![Schermata di "Get-ExecutionPolicy" in esecuzione nella finestra di PowerShell di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Passare alla cartella che contiene gli script e applicazione di generatore.

![Schermata di "cd .\TollApp\TollApp" in esecuzione nella finestra di PowerShell di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tipo **.\\ Setup.ps1** per configurare l'account Azure, creare e configurare tutte le risorse necessarie e iniziare a generare eventi. Lo script riprende in modo casuale un'area per creare le risorse. Per specificare in modo esplicito un'area, è possibile passare il **-percorso** parametro come illustrato nell'esempio seguente:

**. \\Setup.ps1-posizione "Centrale USA"**

![Schermata della pagina di accesso Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Lo script aperta la pagina di **Accesso** di Microsoft Azure. Immettere le credenziali dell'account.

> [AZURE.NOTE] Se l'account ha accesso a più abbonamenti, verrà chiesto di immettere il nome dell'abbonamento che si desidera utilizzare per l'esercitazione.

Lo script può richiedere alcuni minuti per l'esecuzione. Al termine, l'output dovrebbe risultare analoga alla schermata riportata di seguito.

![Schermata di output dello script nella finestra di PowerShell di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Verrà inoltre visualizzata un'altra finestra è simile alla schermata seguente. Questa applicazione sta inviando eventi a un hub di evento Azure, che è necessario eseguire l'esercitazione. Pertanto, non interrompere l'applicazione o chiudere la finestra finché non viene completata l'esercitazione.

![Schermata "Invio hub di dati degli eventi"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Dovrebbe essere possibile visualizzare tutte le risorse create nel portale di Azure ora. Passare a <https://manage.windowsazure.com>e accedere con le credenziali dell'account.

### <a name="azure-event-hubs"></a>Hub evento Azure

Fare clic su **Servizio BUS** sul lato sinistro del portale di Azure per visualizzare gli hub di evento creato lo script nella sezione precedente.

![Bus di servizio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Verranno visualizzati tutti gli spazi di nomi disponibili nell'abbonamento. Fare clic su quello che inizia con *tolldata* (tolldata4637388511 in questo esempio). Fare clic sulla scheda **Evento hub** .

![Scheda hub evento nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Verrà visualizzata due hub di evento denominato *entry* e *uscire da* creata in questo spazio dei nomi.

![Screenshot che mostra gli hub di evento "entrata" o "uscita" nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contenitore di spazio di archiviazione Azure

1. Fare clic su **archiviazione** sul lato sinistro del portale di Azure per visualizzare il contenitore di archiviazione Azure utilizzato nell'esercitazione.

    ![Voce di menu spazio di archiviazione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Fare clic su quello che iniziano con *tolldata* (tolldata4637388511 in questo esempio). Fare clic sulla scheda **contenitori** per visualizzare il contenitore creato.

    ![Scheda contenitori nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Fare clic su contenitore **tolldata** per visualizzare il file JSON caricato contenente dati di registrazione di veicolo.

    ![Schermata del file registration.json nel contenitore](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Database SQL Azure

1. Fare clic su **Database SQL** sul lato sinistro del portale di Azure per il database di SQL che verrà utilizzato nell'esercitazione.

    ![Database SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Fare clic su **tolldatadb**.

    ![Schermata del database di SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copiare il nome del server senza il numero di porta (*nomeserver*. database.windows.net, ad esempio).

## <a name="connect-to-the-database-from-visual-studio"></a>Connettersi al database da Visual Studio

Utilizzare Visual Studio per accedere ai risultati query nel database di output.

Connettersi al database SQL (destinazione) da Visual Studio:

1. Aprire Visual Studio e quindi fare clic su **Strumenti** > **Connetti a Database**.

2. Se viene richiesto, fare clic su **Microsoft SQL Server** come origine dati.

    ![Finestra di dialogo Cambia origine dati](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. Nella casella **nome del Server** , incollare il nome che sono stati copiati nella sezione precedente dal portale di Azure (vale a dire *nomeserver*. database.windows.net).

4. Fare clic su **autenticazione di SQL Server**.

5. Immettere **tolladmin** nel campo **nome utente** e **123toll!** Nella casella **Password** .

6. **Selezionare o immettere il nome del database**, scegliere **TollDataDB** del database.

    ![Aggiungere la finestra di dialogo connessione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Fare clic su **OK**.

8. Aprire Esplora Server.

    ![Esplora server](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Vedere quattro tabelle del database TollDataDB.

    ![Tabelle del database TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Generatore eventi: progetto di esempio TollApp

Avvia automaticamente lo script di PowerShell inviare gli eventi mediante l'applicazione di esempio TollApp. Non è necessario eseguire operazioni aggiuntive.

Tuttavia, se si è interessati in dettagli di implementazione, sono disponibili il codice sorgente dell'applicazione TollApp GitHub [Esempi/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Schermata del codice di esempio visualizzato in Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Creare un processo di flusso Analitica

1. Nel portale di Azure, aprire flusso Analitica e fare clic su **Nuovo** nell'angolo inferiore sinistro della pagina per creare un nuovo processo analitica.

    ![Pulsante nuovo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Fare clic su **Crea rapido**. Selezionare l'area stessa nel punto in cui vengono create le altre risorse per lo script.

3. Affinché l'impostazione di **ACCOUNT di archiviazione monitoraggio internazionali** , selezionare **Crea nuovo ACCOUNT di archiviazione** e assegnare un nome univoco. Azure flusso Analitica utilizzerà l'account per memorizzare le informazioni di monitoraggio per tutti i processi futuri.

4. Fare clic su **Crea processo di analisi di flusso** nella parte inferiore della pagina.

    ![Creare opzione flusso Analitica processo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definire le origini di input

1. Fare clic su processo analitica creata nel portale.

    ![Screenshot che mostra il processo di analitica nel portale](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Fare clic sulla scheda **input** per definire l'origine dati.

    ![La scheda input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Fare clic su **Aggiungi un INPUT**.

    ![Aggiungere un'opzione di Input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Fare clic su **flusso di dati** nella prima pagina.

    ![L'opzione di flusso di dati](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Fare clic su **Evento HUB** nella seconda pagina della procedura guidata.

    ![L'opzione Hub di evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Immettere **EntryStream** come **ALIAS di INPUT**.

7. Fare clic su elenco a discesa **Evento Hub** e selezionare quella che inizia con "TollData" (ad esempio TollData9518658221).

8. Selezionare **voce** come nome dell'evento hub e **tutti** come il nome del criterio hub evento.

    Le impostazioni di aspetto:

    ![Impostazioni di hub di eventi](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Nella pagina successiva, selezionare **JSON** per **Il formato di SERIALIZZAZIONE evento** e **UTF8** per **codifica**.

    ![Impostazioni della serializzazione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Fare clic su **OK** nella parte inferiore della pagina per completare la procedura guidata.

    ![Schermata di EntryStream input nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Una volta creato il flusso di voce, è necessario seguire la stessa procedura per creare il flusso di uscita. Nella terza pagina della procedura guidata, assicurarsi di immettere valori come nella figura seguente.

    ![Impostazioni per il flusso di uscita](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    È stata definita due flussi di input:

    ![Flussi di input definiti nel portale di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Aggiungere quindi input di dati di riferimento per il file di archivio blob contenente dati di registrazione di auto.

11. Fare clic su **Aggiungi INPUT**e quindi fare clic su **Dati di riferimento**.

    ![Opzioni di "Aggiungi un input" con i dati di riferimento selezionati](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Nella pagina successiva, selezionare l'account di archiviazione che inizia con **tolldata**. Il nome del contenitore deve essere **tolldata**e il nome blob nella casella **Percorso motivo** deve essere **registration.json**. Il nome del file tra maiuscole e minuscole e deve essere minuscola.

    ![Impostazioni di archiviazione di blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Selezionare i valori, come illustrato nella schermata seguente nella pagina successiva e quindi fare clic su **OK** per completare la procedura guidata.

    ![Selezione di JSON per "serializzazione pari formato" e UTF8 per "Codifica"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

A questo punto sono definiti tutti gli input.

![Screenshot che mostra tre ingressi definiti](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definizione di output

1. Fare clic sulla scheda **OUTPUT** e quindi fare clic su **Aggiungi un OUTPUT**.

    ![La scheda Output e l'opzione "Aggiungi un output"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Fare clic su **Database SQL**.

3. Selezionare il nome del server che è stato usato nella sezione "La connessione al Database da Visual Studio" dell'articolo. Il nome del database è **TollDataDB**.

4. Immettere **tolladmin** nel campo **nome utente** **123toll!** nel campo **PASSWORD** e **TollDataRefJoin** nel campo della **tabella** .

    ![Impostazioni di Database SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Query di analitica flusso Azure

Scheda **QUERY** contiene una query SQL che consente di trasformare i dati in arrivo.

![Una query aggiunta alla scheda della Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

In questa esercitazione tenta di rispondere a molte domande business relative alle query di Analitica flusso di dati e costrutti che può essere utilizzata in Azure flusso Analitica per fornire una risposta pertinenti a pagamento.

Prima di iniziare il processo di flusso Analitica prima, esaminiamo alcuni scenari e la sintassi della query.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduzione al linguaggio di query Analitica flusso Azure
-----------------------------------------------------

Supponiamo che è necessario contare il numero di veicoli che immettere un stand a pagamento. Poiché si tratta di un flusso di eventi continuo, è necessario definire un "periodo di tempo". Si modifica la domanda per essere "veicoli quanti immettere uno stand a pagamento ogni tre minuti?". Questo è comunemente come conteggio lo scambio.

Osservare la query di Azure flusso Analitica che vengono fornite le risposte a questa domanda:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Come si può notare, Azure flusso Analitica Usa un linguaggio di query SQL che aggiunge alcuni estensioni per specificare aspetti relativi al momento della query.

Per ulteriori informazioni, consultare [Gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [finestre](https://msdn.microsoft.com/library/azure/dn835019.aspx) costrutti utilizzati nella query da MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Test query Analitica flusso Azure

Ora che è stato scritto la prima query Azure flusso Analitica, è possibile testare utilizzando il file di dati di esempio trovano nella cartella TollApp nel percorso seguente:

**.. \\TollApp\\TollApp\\dati**

Questa cartella contiene i file seguenti:

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Domanda 1: Numero di veicoli l'immissione di una fiera a pagamento

1. Aprire il portale di Azure e passare al processo di Azure flusso Analitica creato. Fare clic sulla scheda **QUERY** e incollare query dalla sezione precedente.

    ![Query incollato nella scheda della Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Per convalidare la query sui dati di esempio, fare clic sul pulsante **Test** . Nella finestra di dialogo visualizzata, passare a Entry.json, un file contenente dati di esempio dal flusso di evento **EntryTime** .

    ![Schermata del file Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Verificare che l'output della query come previsto:

    ![Risultati del test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Domanda 2: Report durata totale per ogni Auto al attraversano lo stand a pagamento

Tempo necessario per un'auto a passano attraverso il numero a pagamento consente di valutare l'efficienza del processo e l'esperienza dei clienti.

Per trovare il tempo totale, è necessario partecipare a flusso EntryTime con il flusso di ExitTime. Si verranno partecipa flussi nelle colonne TollId e LicencePlate. L'operatore **JOIN** è necessario specificare libertà temporale che viene descritta la differenza di tempo accettabile tra gli eventi di join. Utilizzare la funzione **DATEDIFF** per specificare che gli eventi non più di 15 minuti tra loro. Verranno applicate anche la funzione **DATEDIFF** per uscire dalla e gli orari di voce per calcolare il tempo effettivo trascorso un'auto in espansione a pagamento. Si noti la differenza tra l'uso di **DATEDIFF** quando viene utilizzata in un'istruzione **SELECT** invece di una condizione di **JOIN** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Per testare la query, aggiornare la query nella scheda **QUERY** del processo di:

    ![Query aggiornata nella scheda della Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Fare clic su **prova** e specificare i file di input di esempio per EntryTime ed ExitTime.

    ![Screenshot che mostra i file selezionati di input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Selezionare la casella di controllo per testare la query e visualizzare l'output:

    ![Output del test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Domanda 3: Inviare una segnalazione tutti veicoli commerciali con registrazione scaduta

Azure flusso Analitica consente snapshot statici dei dati di partecipare con flussi di dati temporali. Per illustrare questa funzionalità, utilizzare la domanda di esempio seguente.

Se un veicolo commerciale è registrato con la società a pagamento, può passare attraverso lo stand a pagamento senza interruzione per la verifica. Si utilizzerà la tabella di ricerca registrazione veicolo commerciale per identificare tutti i veicoli commerciali scaduti le registrazioni.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Per verificare una query utilizzando i dati di riferimento, è necessario definire un'origine di input per i dati di riferimento, che già stato fatto.

1. Per testare la query, incollare la query nella scheda della **QUERY** , fare clic su **Test**e specificare le due origini di input:

    ![Screenshot che mostra i file selezionati di input](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Visualizzare l'output della query:

    ![Schermata di output della query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Avviare il processo di flusso Analitica


A questo punto è possibile completare la configurazione e avviare il processo. Salvare la query da domanda 3, generando output corrispondente allo schema della tabella di output **TollDataRefJoin** .

Passare al processo di **DASHBOARD**e fare clic su **START**.

![Schermata del pulsante Start nel dashboard di processo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Nella finestra di dialogo visualizzata, modificare l'ora di **Inizio OUTPUT** in **ora personalizzati**. Modificare l'ora in un'ora prima l'ora corrente. In questo modo che tutti gli eventi dall'hub evento vengono elaborati dopo l'avvio di generare gli eventi all'inizio dell'esercitazione. A questo punto, fare clic sul segno di spunta per avviare il processo.

![Selezione di tempo personalizzato](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Avviare il processo può richiedere alcuni minuti. È possibile visualizzare lo stato della pagina principale per flusso Analitica.

![Screenshot che mostra lo stato del processo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Risultati della verifica in Visual Studio

1. Aprire Visual Studio Server Explorer e pulsante destro del mouse nella tabella **TollDataRefJoin** .
2. Fare clic su **Mostra tabella dati** per visualizzare l'output del lavoro.

    ![Selezione di "Mostra tabella dati" in Esplora Server](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Scalabilità Azure flusso Analitica processi

Azure flusso Analitica è progettato per illimitate scala in modo che possa gestire una grande quantità di dati. La query di Azure flusso Analitica è possibile utilizzare una clausola **Partizione di base** per comunicare al sistema che questo passaggio verrà scalare. **PartitionId** è una colonna speciale che verrà aggiunto automaticamente in modo che corrisponda l'ID di partizione dell'input (hub evento).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Interrompere il lavoro corrente, aggiornare la query nella scheda della **QUERY** e aprire la scheda **scala** .

    **Unità STREAMING** definire la quantità di grande potenza in grado di ricevere il processo.

2. Spostare il dispositivo di scorrimento a 6.

    ![Schermata di selezione 6 unità di trasmissione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Fare clic sulla scheda **output** e modificare il nome della tabella di SQL in **TollDataTumblingCountPartitioned**.

Se si avvia il processo a questo punto, Azure flusso Analitica può distribuire il lavoro su altre risorse di elaborazione e ottenere migliore velocità di trasmissione. Si noti che l'applicazione TollApp anche invia eventi suddivisi da TollId.

## <a name="monitor"></a>Monitor

Scheda **MONITOR** contiene le statistiche relative al processo in esecuzione.

![Screenshot che mostra le statistiche sui processi in esecuzione](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

È possibile accedere **Registri operazioni** dalla scheda **DASHBOARD** .

![L'opzione "Operazione log"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Schermata dell'operazione log in cui è possibile visualizzare lo stato dei processi](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Per visualizzare ulteriori informazioni su un determinato evento, fare clic sull'evento e quindi fare clic sul pulsante **Dettagli** .

![Screenshot che mostra i dettagli di un evento selezionato](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusioni

In questa esercitazione è stato introdotto al servizio di Azure flusso Analitica. Illustrato come configurare ingressi e uscite per il processo di flusso Analitica. Utilizzo dello scenario a pagamento dati, l'esercitazione spiegazione dei problemi che si verificano nell'area dei dati in movimento e come possono essere risolti con la query di tipo SQL semplice in Azure flusso Analitica comuni. L'esercitazione descritti costrutti di estensione SQL per l'utilizzo di dati temporali. È stato illustrato come unire i flussi di dati, come arricchire il flusso di dati con i dati di riferimento statico e come ridimensionare una query per ottenere maggiore produttività.

Anche se in questa esercitazione fornisce un'ottima introduzione, non è completo con qualsiasi mezzo. È possibile trovare ulteriori modelli di query mediante il linguaggio SAQL alcuni [esempi di Query per criteri di utilizzo comune flusso Analitica](stream-analytics-stream-analytics-query-patterns.md).
Fare riferimento alla [documentazione online](https://azure.microsoft.com/documentation/services/stream-analytics/) per altre informazioni su Azure flusso Analitica.

## <a name="clean-up-your-azure-account"></a>Pulire account Azure

1. Interrompere il processo di flusso Analitica nel portale di Azure.

    Lo script Setup.ps1 crea due hub di eventi e un database SQL. Le istruzioni seguenti consentono di pulire le risorse alla fine dell'esercitazione.

2. Nella finestra di PowerShell, digitare **.\\ Cleanup.ps1** per avviare lo script che consente di eliminare le risorse utilizzate in questa esercitazione.

    > [AZURE.NOTE] Risorse identificate dal nome. Verificare che si esaminare attentamente ogni elemento prima di confermare la rimozione.

    ![Screenshot che mostra il processo di pulizia](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
