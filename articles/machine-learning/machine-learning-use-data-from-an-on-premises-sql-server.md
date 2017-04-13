<properties
pageTitle="Usare i dati da un database di SQL Server locale in apprendimento | Azure"
description="Utilizzare i dati da un database di SQL Server locale eseguire analitica avanzate con Azure apprendimento."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Eseguire analitica avanzate con Azure apprendimento utilizzando i dati da un database di SQL Server locale


Spesso aziende che funzionano con dati locali che vogliono sfruttare la scala e flessibilità del cloud per i carichi di lavoro di apprendimento. Ma non si desidera interrompere i flussi di lavoro e processi aziendali corrente spostando i dati locali al cloud. Azure apprendimento supporta ora la lettura dei dati da un database di SQL Server locale e quindi formazione e il punteggio di un modello con questi dati. Non è possibile copiare e sincronizzare i dati tra nel cloud e il server locale manualmente. Se, tuttavia, il modulo di **Importare dati** in Azure Machine Learning Studio ora leggere direttamente dal database di SQL Server locale per la formazione e il punteggio processi. 

Questo articolo offre una panoramica sull'ingresso locale dati di SQL server in Azure apprendimento. Si presuppone che si ha familiarità con i concetti di apprendimento Azure come aree di lavoro, moduli, set di dati, esperimenti e *così via*.

> [AZURE.NOTE] Questa caratteristica non è disponibile per le aree di lavoro gratuiti. Per ulteriori informazioni sui prezzi apprendimento e livelli, vedere [Azure Machine Learning prezzi](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Installare il Gateway di gestione dati Microsoft

Per accedere a un database di SQL Server locale in apprendimento Azure è necessario scaricare e installare il Gateway di gestione dati Microsoft. Quando si configura la connessione di gateway in computer risorse Studio sarà necessario la possibilità di scaricare e installare il gateway mediante la finestra di dialogo **Download e registro dati gateway** descritto di seguito.

È anche possibile installare il Gateway di gestione dati anticipo il download e l'esecuzione del pacchetto di installazione MSI dall' [Area Download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Scegliere la versione più recente, selezionare versione a 32 bit o 64 bit in base alle esigenze del computer. Il file MSI è utilizzabile anche per aggiornare un Gateway di gestione dati esistenti per la versione più recente, con tutte le impostazioni mantenute.

Il gateway sono previsti i prerequisiti seguenti:

- Le versioni di sistemi operativi Windows supportate sono Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
- Configurazione consigliata per il computer gateway è almeno 2 GHz, 4 core, 8 GB di RAM e disco 80 GB.
- Se il computer host sospende, il gateway non sarà possibile a rispondere a richieste di dati. Pertanto, è possibile configurare un piano di presa di corrente nel computer prima di installare il gateway. L'installazione di gateway visualizzato un messaggio se il computer è configurato per sospensione.
- Poiché Copia attività si verifica una frequenza specifica, l'utilizzo delle risorse (CPU, memoria) nel computer segue anche lo stesso modello con punta e di inattività. Utilizzo delle risorse anche dipende molto la quantità di dati da spostare. Quando sono più processi di copia in corso si sarà osservare Uso risorse salire di orari di punta. Durante la configurazione minima elencata sopra è sufficiente tecnicamente, è consigliabile avere una configurazione con altre risorse rispetto alla configurazione minima a seconda del carico specifico per lo spostamento dei dati.

È necessario considerare le operazioni seguenti durante l'installazione e utilizzo di un Gateway di gestione dati:

- È possibile installare solo un'istanza di Gateway di gestione dati in un unico computer.
- È possibile utilizzare un singolo gateway per più origini dati locali.
- È possibile connettersi più gateway in un computer diverso alla stessa origine dati locale.
- Configurare un gateway per l'area di lavoro solo una alla volta. Gateway non possono essere condivisi tra le aree di lavoro al momento.
- È possibile configurare più gateway per un'area di lavoro. Ad esempio, può essere necessario usare un gateway che sia connesso a origini dati di test in fase di sviluppo e un gateway di produzione quando si è pronti a rendono operativi.
- Il gateway non è necessario essere nello stesso computer come origine dati, ma rimanere vicina all'origine dati riduce il tempo per il gateway per connettersi all'origine dati. È consigliabile installare il gateway su un computer diverso da quello contenente l'origine dati locale in modo che il gateway e un'origine dati non sono in conflitto per le risorse.
- Se si dispone già di un gateway installato nel computer che servono scenari di Power BI o Azure Data Factory, installare un gateway separato per l'apprendimento Azure in un altro computer. 

    > [AZURE.NOTE] È possibile eseguire nello stesso computer Gateway di gestione dati e Gateway di Power BI.

- È necessario utilizzare il Gateway di gestione dati per l'apprendimento Azure anche se si usa Azure ExpressRoute per altri dati. È necessario considerare l'origine dati come origine dati locale, ovvero un firewall, anche quando si usa ExpressRoute e il Gateway di gestione dati per stabilire la connessione tra l'apprendimento e l'origine dati. 

È possibile trovare informazioni dettagliate sui prerequisiti di installazione, procedura di installazione e suggerimenti sulla risoluzione dei problemi nell'articolo, [spostare i dati tra origini locali e cloud con Gateway di gestione dati](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), che iniziano con la sezione [Considerazioni per l'utilizzo di Gateway di gestione dati](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dati di ingresso provenienti da database di SQL Server locale in apprendimento Azure


In questa procedura dettagliata, si verrà configurato un Gateway di gestione dati in un'area di lavoro di apprendimento Azure, configurarlo e leggere i dati da un database di SQL Server locale.

> [AZURE.TIP] Prima di iniziare, disattivare popup del browser per `studio.azureml.net`. Se si usa il browser Google Chrome, scaricare e installare uno dei diversi plug-in disponibili Store sul Web di Google Chrome, [Fare clic su una sola volta App estensione](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Passaggio 1: Creare un gateway

Il primo passaggio consiste nel creare e configurare il gateway per l'accesso al database SQL in locale.

1. Accesso a [Azure Machine Learning Studio](https://studio.azureml.net/Home/) e selezionare l'area di lavoro che si desidera utilizzare.

2. Fare clic su e **l'Impostazioni** sul lato sinistro e quindi fare clic sulla scheda **Gateway di dati** nella parte superiore.

3. Fare clic su **Nuovo GATEWAY dati** nella parte inferiore dello schermo.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Nella finestra di dialogo **Nuovo gateway di dati** , immettere il **Nome del Gateway** e aggiungere facoltativamente una **Descrizione**. Fare clic sulla freccia nell'angolo inferiore destro per passare alla sezione successiva della configurazione.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. Nella finestra di gateway dati Download e registro, copiare il codice di registrazione di GATEWAY negli Appunti.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Se si dispone non ancora scaricato e installato il Gateway di gestione dati Microsoft, fare clic su **Scarica gateway di gestione dati**. Verrà visualizzato il sito Microsoft Download Center in cui è possibile selezionare la versione del gateway che è necessario scaricarlo e installarlo. È possibile trovare informazioni dettagliate sui prerequisiti di installazione, procedura di installazione e suggerimenti sulla risoluzione dei problemi nella sezione inizio dell'articolo [spostare dati tra origini locali e cloud con Gateway di gestione dati](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Dopo l'installazione di gateway di Gestione configurazione di Gateway di gestione dati verrà aperto e verrà visualizzata la finestra di dialogo **registrare gateway** . Incollare il **Codice di registrazione Gateway** copiato negli Appunti e fare clic su **Registra**.

8. Se si dispone già di un gateway installato, eseguire Gestione configurazione di Gateway di gestione dati, fare clic su **Cambia key**, incollare il  **Codice di registrazione Gateway** copiato negli Appunti e fare clic su **OK**.

9. Una volta completato l'installazione, viene visualizzata la finestra di dialogo per Microsoft Gateway configurazione di gestione dati **registrare gateway** . Incollare il codice di registrazione di GATEWAY copiato negli Appunti sopra e fare clic su **Registra**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. La configurazione di gateway viene completata quando i valori seguenti sono impostati nella scheda **Home** in Microsoft Gateway configurazione di gestione dati:

    - **Nome del gateway** e **nome dell'istanza** sono impostate per il nome del gateway.

    - **Registrazione** è impostato su **registrata**.

    - **Stato** è impostato su **avviato**.

    - Barra di stato nella parte inferiore Visualizza **connesso al servizio Cloud Gateway di gestione dati** con un segno di spunta verde.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure Machine Learning Studio viene aggiornato anche quando la registrazione viene eseguita correttamente.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. Nella finestra di dialogo **Download e registrare il gateway di dati** , fare clic sul segno di spunta per completare la configurazione. La pagina **Impostazioni** Visualizza lo stato del gateway come "Online". Nel riquadro di destra sono disponibili informazioni sullo stato e altre informazioni utili.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. Nella finestra di gestione di configurazione di Gateway di gestione di dati di Microsoft passa alla scheda **certificati** . Il certificato specificato in questa scheda viene utilizzato per le credenziali per l'archivio di dati in locale che specificano nel portale di crittografia/decrittografia. Questo è il certificato predefinito generato. Si consiglia di questa modifica per il proprio certificato che si esegue il backup nel sistema di gestione dei certificati. Fare clic su **Modifica** per utilizzare invece il proprio certificato.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (facoltativo) Se si desidera attivare la registrazione dettagliata per risolvere i problemi relativi al gateway, in Microsoft Gateway configurazione di gestione dati passare alla scheda **diagnostica** e selezionare l'opzione **Abilita registrazione dettagliata per la risoluzione dei problemi** . Sono disponibili le informazioni di registrazione in Visualizzatore eventi di Windows in **registri applicazioni e servizi**  - &gt; nodo **Gateway di gestione dati** . È anche possibile usare la scheda **diagnostica** per verificare la connessione a un'origine dati locale mediante il gateway.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Il gateway impostato processo di apprendimento Azure è stata completata.
A questo punto si è pronti per l'uso dei dati in locale.

È possibile creare e configurare più gateway in Studio per ogni area di lavoro. Ad esempio, potrebbe essere un gateway che si desidera connettersi a origini dati di test in fase di sviluppo e un gateway diversi per le origini dati di produzione. Apprendimento Azure offre flessibilità per configurare più gateway a seconda della rete aziendale. Attualmente non è possibile condividere un gateway tra le aree di lavoro e solo un gateway può essere installato in un unico computer. Per altre considerazioni durante l'installazione di gateway, vedere [Considerazioni per l'utilizzo di Gateway di gestione dati](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) vedere l'articolo, [spostare i dati tra origini locali e cloud con Gateway di gestione dati](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passaggio 2: Usare il gateway per leggere i dati da un'origine dati locale

Dopo aver configurato il gateway, è possibile aggiungere un modulo di **Importare dati** di una prova che inserisce i dati da database di SQL Server locale.

1.  In computer risorse Studio, selezionare la scheda **ESPERIMENTI** , fare clic su **+ Nuovo** nell'angolo in basso a sinistra e selezionare **Prova vuoto** (o selezionare uno dei diversi esperimenti di esempio disponibili).

2.  Trovare e trascinare il modulo **Importa dati** all'area di lavoro di prova.

3.  Fare clic su **Salva con nome** sotto l'area. Immettere "Azure Machine Learning locale SQL Server esercitazione" per il nome di prova, selezionare l'area di lavoro e fare clic sul segno di spunta **OK** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Fare clic sul modulo **Importa dati** per selezionarlo, quindi nel riquadro **proprietà** a destra dell'area di disegno, selezionare "Database SQL locale" nell'elenco a discesa **origine dati** .

5.  Selezionare il **gateway di dati** è stato installato e registrato. È possibile impostare un altro gateway selezionando "(Aggiungi nuovo Gateway di dati...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Immettere il **nome del server Database** SQL Server e il **nome del Database**, insieme SQL **una query di Database** che si desidera eseguire.

7.  Fare clic su **valori di invio** in **nome utente e password** e immettere le credenziali di database. È possibile utilizzare l'autenticazione di Windows o SQL Server a seconda della configurazione SQL Server locale.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    Il messaggio "valori necessari" verrà modificato in "set di valori" con un segno di spunta verde. È sufficiente immettere le credenziali una sola volta a meno che non cambia la password o informazioni sul database. Apprendimento Azure utilizza il certificato che è specificato quando è stato installato il gateway per crittografare le credenziali nel cloud. Azure mai memorizza le credenziali locali senza crittografia.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Fare clic su **Esegui** per eseguire la prova.

Al termine di prova in esecuzione è possibile visualizzare i dati importati dal database scegliendo **Visualizza**la porta di output del modulo **Importa dati** .

Dopo aver sviluppo la prova, è possibile distribuire e rendono operativi il modello. Usa il servizio di esecuzione Batch, dati del database di SQL Server locale configurato nel modulo **Importa dati** verranno letti e utilizzati per il punteggio. Sebbene sia possibile utilizzare il servizio di risposta richiesta per il punteggio dati locali, si consiglia di utilizzare invece il [componente aggiuntivo di Excel](machine-learning-excel-add-in-for-web-services.md) . Attualmente, scrivere un locale di SQL Server database tramite **Esporta dati** non è supportato nell'esperimenti o servizi web pubblicato.

