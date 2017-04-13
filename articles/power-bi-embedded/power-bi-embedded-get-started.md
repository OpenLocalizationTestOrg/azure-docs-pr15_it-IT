<properties
   pageTitle="Introduzione a Microsoft Power BI incorporata"
   description="Power BI incorporato, aggiungere report di Power BI interattivi in un'applicazione di business intelligence"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introduzione a Microsoft Power BI incorporata

**Power BI incorporato** è un servizio di Azure che consente agli sviluppatori di applicazioni aggiungere report di Power BI interattivi nelle applicazioni. **Power BI incorporato** funziona con le applicazioni esistenti senza la necessità di ristrutturazione o modifica delle modalità di accesso.

Risorse per **Microsoft Power BI incorporata** vengono effettuato il provisioning tramite l' [API ARM Azure](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso, la risorsa che viene effettuato il provisioning è una **Raccolta di area di lavoro di Power BI**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Creare una raccolta di un'area di lavoro
Un **Insieme di area di lavoro** è la risorsa Azure principale e un contenitore per il contenuto verrà incorporato nell'applicazione. Un **Insieme di area di lavoro** possono essere create in due modi:

   -    Manualmente tramite il portale di Azure
   -    Livello di programmazione utilizzando le API Manager(ARM) risorse di Azure

Di seguito ripetere i passaggi per creare un **Insieme di area di lavoro** tramite il portale di Azure.

   1.   Aprire e accedere al **Portale di Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Fare clic su **+ Nuovo** nel riquadro superiore.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   In **dati + Analitica** fare clic su **Power BI incorporato**.
   4.   Nella **Creazione Blade**, immettere le informazioni richieste. Per **prezzi**, vedere [Power BI incorporata prezzi](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Fare clic su **Crea**.

**Area di lavoro Raccolta** richiederà qualche minuto a disposizione. Al termine, verrà accettata per l' **Area di lavoro Raccolta Blade**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

**Creazione Blade** contiene le informazioni necessarie chiamare API che creano aree di lavoro e distribuire contenuto.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Visualizzazione Power BI API tasti

Una delle parti più importanti delle informazioni necessarie per chiamare le API REST di Power BI sono i **Tasti di scelta**. Questi sono utilizzati per generare i **token app** che vengono utilizzati per autenticare le richieste di API. Per visualizzare i **Tasti di scelta**, fare clic su **I tasti di scelta** della **Blade impostazioni**. Per ulteriori informazioni sui **token di app**, vedere [Authenticating e l'autorizzazione con Power BI incorporato](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Sarà necessario ' avviso ha due chiavi.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copiare questi tasti e archiviare in modo sicuro all'interno dell'applicazione. È importante considerare questi tasti come si farebbe con una password, perché verrà forniscono l'accesso al contenuto nella **Raccolta di area di lavoro**.

Mentre si due trovano ad esempio, è necessario solo una chiave in un determinato momento. La seconda chiave viene fornita in modo che è possibile rigenerare periodicamente le chiavi senza interrompere l'accesso al servizio.

Dopo aver creato un'istanza di Power BI per l'applicazione e **I tasti di scelta**, è possibile importare un report nell'applicazione. Prima si imparerà a importare un report, la sezione seguente vengono illustrati la creazione set di dati di Power BI e report per incorporare un'app.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Creare report per incorporare un'app e set di dati di Power BI

Ora che è stata creata un'istanza di Power BI per l'applicazione e presentano **Tasti di scelta**, è necessario creare il set di dati di Power BI e un report che si desidera incorporare. Set di dati e report possono essere create usando **Power BI Desktop**. È possibile scaricare [Power BI Desktop per rendere disponibile](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). In alternativa, per iniziare rapidamente, è possibile scaricare il [Retail analisi esempio PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Per ulteriori informazioni su come usare **Power BI Desktop**, vedere [Introduzione a Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Con **Power BI Desktop**, connettersi all'origine dati per l'importazione di una copia dei dati in **Power BI Desktop** o la connessione direttamente alla fonte dati utilizzando **DirectQuery**.

Ecco le differenze nell'utilizzo di **importazione** / **DirectQuery**.

|Importazione | DirectQuery
|---|---
|Tabelle, colonne *e i dati* vengono importate o copiate in **Power BI Desktop**. Mentre si lavora con le visualizzazioni, **Power BI Desktop** query una copia dei dati. Per visualizzare le modifiche apportate ai dati sottostanti, aggiornare o importare un completa, corrente set di dati nuovamente.|Solo *le tabelle e colonne* vengono importati o copiata in **Power BI Desktop**. Mentre si lavora con le visualizzazioni, **Power BI Desktop** query origine dati sottostante, che indica che si sta visualizzando sempre i dati correnti.

Per ulteriori informazioni sulla connessione a un'origine dati, vedere [connettersi a un'origine dati](power-bi-embedded-connect-datasource.md).

Dopo aver salvato il lavoro in **Power BI Desktop**, viene creato un file PBIX. Il file contiene il rapporto. Inoltre, se si importano dati la PBIX contiene il set completo di dati o se si utilizza **DirectQuery**, la PBIX contiene solo uno schema di set di dati. Distribuire a livello di programmazione di PBIX nell'area di lavoro tramite l' [API di importazione di Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI incorporato** sono API aggiuntive per modificare il server database che fa riferimento il set di dati e impostare le credenziali di account di servizio che verrà usato il set di dati per connettersi al database di. Vedere [SetAllConnections Post](https://msdn.microsoft.com/library/mt711505.aspx) e [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Passaggi successivi
I passaggi precedenti per creare in una raccolta di un'area di lavoro e il primo report e set di dati. A questo punto è possibile imparare a scrivere codice per **Power BI incorporato**. Per informazioni su come iniziare, è stata creata un'app web di esempio: [Guida introduttiva a campione](power-bi-embedded-get-started-sample.md). Nell'esempio viene illustrato come per:

  - Contenuto di provisioning
      - Creare un'area di lavoro
      - Importare un file PBIX
      - Aggiornare le stringhe di connessione e impostare le credenziali per il set di dati.

  - In modo sicuro incorporare un report

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva di esempio](power-bi-embedded-get-started-sample.md)
- [Autenticazione e l'autorizzazione con Power BI incorporata](power-bi-embedded-app-token-flow.md)
- [Desktop di Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
