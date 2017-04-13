<properties
   pageTitle="Esercitazione: Elaborare fatture EDIFACT tramite servizi BizTalk Azure | Servizi di BizTalk Microsoft Azure"
   description="Come creare e configurare l'app connettore casella o API e usarla in un'app di logica nel servizio App Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Esercitazione: Processo EDIFACT fatture tramite servizi BizTalk di Windows Azure
È possibile usare il portale di servizi BizTalk come configurare e distribuire accordi EDIFACT e X12. In questa esercitazione si osserva come creare un contratto EDIFACT per lo scambio di fatture tra partner. In questa esercitazione è scritto intorno a una soluzione business-to-end associate ai due partner, Northwind e Contoso che scambiare messaggi EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Esempio in base a questa esercitazione
In questa esercitazione è scritto all'interno di un campione, **INVIO EDIFACT fatture Using BizTalk Services**, che è disponibile per il download [Riutilizzabili](http://go.microsoft.com/fwlink/?LinkId=401005). È possibile utilizzare il campione e verranno illustrate in questa esercitazione per comprendere la modalità di compilazione di esempio. In alternativa, è possibile utilizzare questa esercitazione per creare il proprio soluzione terra-up. In questa esercitazione è rivolto il secondo approccio, in modo che si comprendere la modalità di compilazione di questa soluzione. Inoltre, il più possibile l'esercitazione coerente con il campione e utilizza gli stessi nomi per gli elementi (ad esempio schemi, trasformazioni) come usati nel campione.  

>[AZURE.NOTE] Poiché questa soluzione comporta l'invio di un messaggio da un bridge EAI a un bridge EDI, Riutilizza esempio [BizTalk Services Bridge concatenazione campione](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>Qual è la soluzione?

In questa soluzione Northwind riceve le fatture EDIFACT da Contoso. Queste fatture non sono in formato EDI standard. Pertanto, prima di inviare la fattura per Northwind, è necessario convertito in un documento di fattura (denominato anche INVOIC) EDIFACT. Dopo aver ricevuto, Northwind deve elaborare fattura EDIFACT e restituire un messaggio di controllo (denominato anche CONTRL) a Contoso.

![][1]  

Per ottenere in questo scenario business, Contoso utilizza le funzionalità offerte da Microsoft Azure BizTalk Services.

*   Contoso utilizza ponti EAI per trasformare la fattura originale EDIFACT INVOIC.

*   Bridge EAI invia il messaggio a un bridge di trasmissione EDI distribuito come parte di un contratto configurato nel portale servizi BizTalk.

*   Il bridge invia EDI elabora INVOIC EDIFACT e indirizza a Northwind.

*   Dopo aver ricevuto la fattura, Northwind restituisce un CONTRL messaggio per il EDI riceveranno bridge distribuito come parte del contratto.  

> [AZURE.NOTE] Facoltativamente, questa soluzione viene inoltre illustrato come utilizzare il batch per inviare le fatture in batch, invece di inviare una fattura separatamente.  

Per completare lo scenario, vengono utilizzati code Bus di servizio per inviare Northwind fattura da Contoso o ricevere messaggi di conferma da Northwind. Queste code possono essere create utilizzando un'applicazione client, che è disponibile per il download e incluso nel pacchetto di esempio disponibile come parte di questa esercitazione.  

## <a name="prerequisites"></a>Prerequisiti

*   È necessario avere uno spazio dei nomi Bus di servizio. Per istruzioni sulla creazione di uno spazio dei nomi, vedere [How To: creare o modificare un Namespace di servizio servizio Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supponiamo che si dispone già di uno spazio dei nomi di servizio Bus viene completato il provisioning, denominata **edifactbts**.

*   È necessario disporre di un abbonamento a servizi BizTalk. Per ulteriori informazioni, vedere [creare un servizio BizTalk portale Azure classica](http://go.microsoft.com/fwlink/?LinkID=302280). Per questa esercitazione, si supponga di che avere un abbonamento servizi BizTalk chiamato **contosowabs**.

*   Registrare il proprio abbonamento servizi BizTalk nel portale di servizi di BizTalk. Per ulteriori informazioni, vedere [registrazione di una distribuzione di servizio BizTalk nel portale di servizi di BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   È installato Visual Studio.

*   È necessario avere installato BizTalk Services SDK. È possibile scaricare SDK da [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Passaggio 1: Creare code Bus di servizio  
Questa soluzione utilizza code Bus di servizio per scambiare messaggi tra partner. Contoso e Northwind inviare messaggi a code da dove ponti EAI e/o EDI utilizzano. Per questa soluzione, è necessario tre code Bus di servizio:

*   **northwindreceive** – Northwind riceve la fattura da Contoso tramite questa coda.

*   **contosoreceive** – Contoso riceve la conferma da Northwind tramite questa coda.

*   **sospesa** : sospesa tutti i messaggi vengono inviati alla coda. I messaggi vengono sospesi in caso di errore durante l'elaborazione.

È possibile creare tali code Bus del servizio mediante un'applicazione client inclusa nel pacchetto di esempio.  

1.  Dalla posizione in cui è stato scaricato l'esempio, aprire **Esercitazione l'invio di fatture con BizTalk servizi EDI Bridges.sln**.

2.  Premere **F5** per compilare e avviare l'applicazione **Client esercitazione** .

3.  Nella schermata, immettere il servizio Bus ACS dello spazio dei nomi, nome autorità che emette e chiave.

    ![][2]  
4.  Un messaggio che richiede tre code verranno create nello spazio dei nomi Bus di servizio. Fare clic su **OK**.

5.  Lasciare il Client di esercitazione in esecuzione. Aperto, fare clic su **Servizio Bus** > **_spazio dei nomi del servizio Bus_** > **code**e verificare che vengano create tre code.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Passaggio 2: Creare e distribuire contratto per i partner commerciali
Creare un contratto partner commerciali tra Contoso e Northwind. Un contratto per i partner commerciali definisce un contratto trade tra le due business partner, ad esempio gli schemi di messaggio da utilizzare, il protocollo di messaggistica da usare e così via. Un contratto per i partner commerciali include due ponti EDI, uno per inviare messaggi a partner (denominato **EDI inviare bridge**) e uno per ricevere i messaggi da partner (denominato **EDI ricevere bridge**).

Nel contesto di questa soluzione, il bridge invia EDI corrisponde al lato invio del contratto e viene utilizzato per inviare la fattura EDIFACT da Contoso Northwind. Allo stesso modo, il bridge di ricezione EDI corrisponde al lato ricezione del contratto e viene utilizzato per ricezione riconoscimenti da Northwind.  

### <a name="create-the-trading-partners"></a>Creare il partner

Per iniziare, creare partner per Contoso e Northwind.  

1.  Nel portale servizi BizTalk, nella scheda **partner** , fare clic su **Aggiungi**.

2.  Nella pagina nuovo partner, immettere **Contoso** come nome del partner e quindi fare clic su **Salva**.

3.  Ripetere il passaggio per creare il partner secondo **Northwind**.  

### <a name="create-the-agreement"></a>Creare il contratto
Contratti di partner commerciali vengono creati tra i profili di business dei partner commerciali. Questa soluzione utilizza i profili di partner predefiniti che vengono creati automaticamente quando è stato creato il partner.  

1.  Nel portale di servizi BizTalk, fare clic su **accordi** > **Aggiungi**.

2.  Nella pagina **Impostazioni generali** del contratto nuovo specificare i valori, come illustrato nell'immagine seguente e quindi fare clic su **Continua**.

    ![][3]  

    Dopo aver fatto clic **Continua**, due schede, **Le impostazioni di ricezione** e **Inviare** diventano disponibili.

3.  Creare il contratto di invio tra Contoso e Northwind. Il presente contratto determina il modo in cui Contoso invia la fattura EDIFACT a Northwind.

    1.  Fare clic su **impostazioni di invio**.

    2.  Mantenere i valori predefiniti delle schede **URL in ingresso**, **la trasformazione**e **batch dei** .

    3.  Nella scheda **protocollo** , nella sezione **schemi** caricare lo schema **EFACT_D93A_INVOIC.xsd** . Questo schema non è disponibile con il pacchetto di esempio.

        ![][4]  
    4.  Nella scheda **trasporto** specificare i dettagli per le code Bus di servizio. Per il contratto lato invia serve coda **northwindreceive** per inviare la fattura EDIFACT Northwind e coda **in sospeso** per instradare tutti i messaggi che esito negativo durante l'elaborazione e vengono sospesi. È stato creato in queste code **passaggio 1: creare code Service Bus** (in questo argomento).

        ![][5]  

        In **le impostazioni di trasporto > tipo di trasporto** e **messaggio sospensione Impostazioni > tipo di trasporto**, selezionare Bus di servizio Azure e fornire i valori, come illustrato nell'immagine.

4.  Creare il contratto di ricezione tra Contoso e Northwind. Il presente contratto determina come Contoso riceve la conferma di Northwind.

    1.  Fare clic su **impostazioni di ricezione**.

    2.  Mantenere i valori predefiniti delle schede di **trasporto** e **trasformare** .

    3.  Nella scheda **protocollo** , nella sezione **schemi** caricare lo schema **EFACT_4.1_CONTRL.xsd** . Questo schema non è disponibile con il pacchetto di esempio.

    4.  Nella scheda **Route** creare un filtro per assicurarsi che solo riconoscimenti da Northwind sono indirizzati a Contoso. In **Impostazioni**fare clic su **Aggiungi** per creare il filtro di routing.

        ![][6]  
        1.  Fornire i valori per **Il nome della regola**, **regola la distribuzione**e **Route destinazione** , come illustrato nell'immagine.

        2.  Fare clic su **Salva**.

    5.  Nella scheda **Route** specificare di nuovo in sospesi riconoscimenti (riconoscimenti esito negativo durante l'elaborazione) vengono instradati alle. Impostare il tipo di trasporto su Bus di servizio Azure, che tipo di destinazione alla **coda**, il tipo di autenticazione alla **Firma di Access condiviso** (SA), fornire la stringa di connessione SA per lo spazio dei nomi Bus di servizio e quindi immettere il nome della coda come **sospesa**.

5.  Infine, fare clic su **Distribuisci** per distribuire il contratto. Nota i punti finali nel punto in cui l'invio e la ricezione contratti vengono distribuiti.

    *   Nella scheda **Impostazioni invio** , in **URL in ingresso**, prendere nota dell'endpoint. Per inviare un messaggio da Contoso a Northwind utilizzando il bridge invia EDI, è necessario inviare un messaggio all'endpoint.

    *   Nella scheda **Impostazioni di ricezione** in **trasporto**nota il punto finale. Per inviare un messaggio da Northwind a Contoso tramite il EDI ricevere bridge, è necessario inviare un messaggio all'endpoint.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Passaggio 3: Creare e distribuire il progetto BizTalk Services

Nel passaggio precedente distribuiti trasmissione EDI e ricevere accordi per elaborare le fatture EDIFACT e riconoscimenti. Questi contratti possono elaborare solo i messaggi conformi allo schema di messaggio EDIFACT standard. Tuttavia, per lo scenario per questa soluzione, Contoso invia una fattura a Northwind in uno schema proprietario interno. Pertanto, prima che il messaggio viene inviato al bridge invia EDI, è necessario organizzarli dallo schema interno allo schema di fattura EDIFACT standard. Il progetto BizTalk Services EAI in modo che.

Il progetto di servizi BizTalk, **InvoiceProcessingBridge**, che consente di trasformare il messaggio è incluso come parte del campione che è stato scaricato. Il progetto include gli elementi seguenti:

*   **INHOUSEINVOICE. XSD** -Schema della fattura interno che viene inviato a Northwind.

*   EFACT_D93A_INVOIC **. XSD** -Schema di fattura EDIFACT standard.

*   EFACT_4.1_CONTRL **. XSD** -Schema della conferma EDIFACT che Northwind Invia a Contoso.

*   INHOUSEINVOICE_to_D93AINVOIC **. TRFM** – la trasformazione che mappa lo schema di fattura interne allo schema di fattura EDIFACT standard.  

### <a name="create-the-biztalk-services-project"></a>Creare il progetto BizTalk Services
1.  Soluzione Visual Studio espandere il progetto InvoiceProcessingBridge e quindi aprire il file **MessageFlowItinerary.bcs** .

2.  Fare clic in un punto qualsiasi dell'area di lavoro e impostare l' **URL del servizio di BizTalk** nella casella della proprietà per specificare il nome dell'abbonamento BizTalk Services. Ad esempio `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  Dalla casella degli strumenti, trascinare un **Bridge unidirezionale Xml** all'area di lavoro. Impostare le proprietà di **Entità nome** e **Indirizzo relativo** del bridge per **ProcessInvoiceBridge**. Fare doppio clic su **ProcessInvoiceBridge** per aprire l'area di configurazione bridge.

4.  All'interno della casella **Tipi di messaggi** , fare clic sul segno più (**+**) per specificare lo schema del messaggio di posta in arrivo. Poiché il messaggio di posta in arrivo per il bridge EAI è sempre la fattura sessioni, impostare **INHOUSEINVOICE**.

    ![][8]  
5.  Fare clic sulla forma di **Trasformazione Xml** nella casella della proprietà, per la proprietà di **mappe** , fare clic sui puntini di sospensione (**.**). Nella finestra di dialogo **Selezione mappe** , selezionare il file di trasformazione **INHOUSEINVOICE_to_D93AINVOIC** e quindi fare clic su **OK**.

    ![][9]  
6.  Tornare a **MessageFlowItinerary.bcs**e dalla casella degli strumenti, trascinare un **Endpoint del servizio esterno bidirezionale** a destra del **ProcessInvoiceBridge**. Impostare la proprietà **Name entità** **EDIBridge**.

7.  In Esplora soluzioni espandere **MessageFlowItinerary.bcs** e fare doppio clic sul file **EDIBridge.config** . Sostituire il contenuto di **EDIBridge.config** con le operazioni seguenti.

    > [AZURE.NOTE] Perché è necessario modificare il file config? Endpoint del servizio esterni aggiunti all'area di progettazione di bridge rappresenta i bridge EDI che è distribuiti in precedenza. Ponti EDI bidirezionali ponti, con un'Invia e ricevano lato. Tuttavia, il bridge EAI aggiunti alla finestra di progettazione bridge è un bridge unidirezionale. Pertanto, per gestire modelli di exchange di messaggio diverso di due ponti, serve un comportamento bridge personalizzato, inclusa la configurazione nel file config. Inoltre, il comportamento personalizzato gestisce anche l'autenticazione all'endpoint bridge invia EDI. Questo comportamento personalizzato è disponibile come esempio separato in [BizTalk Services Bridge concatenazione sample - EAI a EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Questa soluzione riutilizza il campione.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Aggiornare il file EDIBridge.config per includere i dettagli di configurazione

    *   In _<behaviors>_, fornire la ACS dello spazio dei nomi e un tasto associato l'abbonamento a servizi BizTalk.

    *   In _<client>_, fornire l'endpoint in cui viene distribuito il contratto di trasmissione EDI.

    Salvare le modifiche e chiudere il file di configurazione.

9.  Dalla casella degli strumenti, fare clic sul **connettore** e unire i componenti **ProcessInvoiceBridge** ed **EDIBridge** . Selezionare il connettore e nella finestra Proprietà impostare **Condizione di filtro** su **Tutto maiuscole**. In questo modo che tutti i messaggi elaborati da bridge EAI vengono instradati alle bridge EDI.

    ![][10]  
10.  Salvare le modifiche alla soluzione.  

### <a name="deploy-the-project"></a>Distribuire il progetto

1.  Nel computer in cui è stato creato il progetto BizTalk Services, scaricare e installare il certificato SSL per l'abbonamento a servizi BizTalk. In BizTalk Services, fare clic su **Dashboard**e quindi fare clic su **Scarica certificato SSL**. Fare doppio clic sul certificato e seguire la richiesta per completare l'installazione. Assicurarsi di installare il certificato nell'archivio certificati **Autorità di certificazione radice attendibili** .

2.  In Esplora soluzioni Visual Studio, fare clic sul progetto **InvoiceProcessingBridge** e quindi fare clic su **Distribuisci**.

3.  Fornire i valori, come illustrato nell'immagine e quindi fare clic su **Distribuisci**. È possibile ottenere le credenziali ACS per i servizi BizTalk facendo clic su **Informazioni di connessione** nel dashboard di BizTalk Services.

    ![][11]  

    Dal riquadro di output, copiare l'endpoint in bridge EAI viene distribuito, ad esempio `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Questo URL endpoint sarà necessario in un secondo momento.  

## <a name="step-4-test-the-solution"></a>Passaggio 4: Verificare la soluzione


In questo argomento si osserva come verificare la soluzione utilizzando l'applicazione **Client esercitazione** fornito come parte del campione.  

1.  In Visual Studio, premere F5 per avviare il **Client esercitazione**.

2.  La schermata deve avere i valori prepopolati ottenuto nel passaggio cui è stato creato code Bus di servizio. Fare clic su **Avanti**.

3.  Nella finestra successiva, specificare credenziali ACS per l'abbonamento a servizi BizTalk e i punti finali in indirizzi EAI ed EDI (ricezione) ponti sono distribuiti.

    Il punto finale bridge EAI copiata nel passaggio precedente. Per EDI receive endpoint bridge, nel portale servizi BizTalk, per visualizzare il contratto > Impostazioni di ricezione > trasporto > Endpoint.

    ![][12]  
4.  Nella finestra successiva in Contoso, fare clic sul pulsante **Invia fattura interni** . Nel File aprire la finestra di dialogo, aprire il file INHOUSEINVOICE.txt. Esaminare il contenuto del file e quindi fare clic su **OK** per inviare la fattura.

    ![][13]  
5.  In pochi secondi si riceve la fattura Northwind. Fare clic sul collegamento **Visualizza** per visualizzare la fattura ricevuta da Northwind. Si noti come della fattura ricevuta dal Northwind nello schema EDIFACT standard mentre quello inviato dal Contoso era uno schema interno.

    ![][14]  
6.  Selezionare la fattura e quindi fare clic su **Invia conferma**. Nella finestra di dialogo che appare, si noti che l'ID di interscambio stesso nella fattura ricevuta e la citazione inviato. Fare clic su OK nella finestra di dialogo **Invia conferma** .

    ![][15]  
7.  In pochi secondi, il riconoscimento è stato ricevuto contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Passaggio 5 (facoltativo): fattura EDIFACT inviare in batch 
Ponti BizTalk servizi EDI supporta anche il batch dei messaggi in uscita. Questa funzionalità è utile per la ricezione di partner che preferiscono ricevere un batch di messaggi (riunione determinato criterio) invece di singoli messaggi.

L'aspetto più importante quando si lavora con batch è la versione effettiva del batch, detto anche i criteri di rilascio. I criteri di rilascio possono essere basati sulle come partner di destinazione desidera ricevere messaggi. Se il batch è attivato, il bridge EDI inviare il messaggio in uscita per i partner di destinazione finché non è soddisfatta i criteri di rilascio. Ad esempio, un criterio batch in base a messaggio dimensioni invia un batch solo quando "n" si esegue il batch di messaggi. Un criterio di batch può anche essere basate sul tempo, in modo che viene inviato un batch di un'ora fissa ogni giorno. In questa soluzione si tenta di base a criteri la dimensione dei messaggi.

1.  Nel portale di servizi BizTalk, fare clic su contratto creata in precedenza. Fare clic su Invia Impostazioni > batch > Aggiungi Batch.

2.  Per nome batch, immettere **InvoiceBatch**, fornire una descrizione e quindi fare clic su **Avanti**.

3.  Specificare un criterio di batch, che consente di definire quali messaggi devono essere batch. In questa soluzione è batch tutti i messaggi. Selezionare Usa definizioni opzione avanzata, quindi immettere **1 = 1**. Si tratta di una condizione che sia sempre true e pertanto verranno batch tutti i messaggi. Fare clic su **Avanti**.

    ![][17]  
4.  Specificare un criterio di rilascio batch. Dalla casella di riepilogo, selezionare **MessageCountBased**e per **contare**, specificare **3**. Questo errore indica che un batch di tre messaggi che verrà inviato a Northwind. Fare clic su **Avanti**.

    ![][18]  
5.  Controllare il riepilogo e quindi fare clic su **Salva**. Fare clic su **Distribuisci** per ridistribuire il contratto.

6.  Tornare al **Client esercitazione**, fare clic su **Invia fattura interni**e seguire le istruzioni per inviare la fattura. Si noterà che non si riceve alcuna fattura Northwind perché le dimensioni del batch non è soddisfatta. Ripetere questo passaggio due volte, in modo da avere tre fattura i messaggi inviati a Northwind. Questo soddisfa i criteri di rilascio batch dei 3 messaggi e verrà visualizzata una fattura Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

