<properties
   pageTitle="Creare App logica EAI utilizzando VETR nelle app di logica di servizio App Azure | Microsoft Azure"
   description="Convalidare, codificare e trasformare caratteristiche dei servizi BizTalk XML"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Creare App logica EAI utilizzando VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

La maggior parte dei scenari di integrazione applicazione Enterprise (EAI) interporre dati tra un'origine e destinazione. Scenari di questo presentano un insieme comune di requisiti:

- Assicurarsi che i dati da sistemi diversi vengono formattati correttamente.
- Eseguire "ricerca" sui dati in ingresso per prendere decisioni.
- Convertire i dati da un formato a un altro. Ad esempio, convertire dati dal formato di dati del sistema CRM in formato dati di un sistema ERP.
- La distribuzione che si desidera sistema o applicazione desiderata.

In questo articolo illustra un modello di integrazione comune: "mediation messaggio unidirezionale" o VETR (convalida, Enrich, trasformazione, Route). Motivo VETR Media dati tra entità di origine e un'entità di destinazione. In genere di origine e destinazione sono origini dati.

Valutare la possibilità di un sito Web che accetta ordini. Gli utenti pubblicano post ordini per il sistema tramite HTTP. Dietro le quinte, il sistema convalida i dati in arrivo per la correttezza, Normalizza essa e viene mantenuto in una coda Bus di servizio per ulteriori elaborazioni. Il sistema accetta ordini dalla coda previsto in un formato specifico. In questo modo, il flusso di-to-end è:

**HTTP** → **convalidare** → **Trasforma** → **Bus di servizio**

![Flusso di base VETR][1]

Le app di API BizTalk seguenti consentono di creare questo modello:

* **HTTP Trigger** - origine a messaggi di evento
* **Convalida** - convalida la correttezza dei dati in arrivo
* **Trasformare** - trasformazioni dati da posta in arrivo in formato richiesto dal sistema downstream
* **Connettore Bus servizio** - entità di destinazione in cui vengono inviati i dati


## <a name="constructing-the-basic-vetr-pattern"></a>Costruire il modello di VETR base
### <a name="the-basics"></a>Nozioni fondamentali

Nel portale di Azure, selezionare **+ Nuovo**, selezionare **Web + Mobile**e quindi selezionare **App logica**. Scegliere un nome, percorso, abbonamento, gruppo di risorse e un percorso che funziona. Gruppi di risorse utilizzati come contenitori per le app; tutte le risorse per un'app passare allo stesso gruppo di risorse.

Successivamente, aggiungere trigger e azioni.


## <a name="add-http-trigger"></a>Aggiungere Trigger HTTP
1. Nei **Modelli di App logica**, selezionare **Crea da zero**.
1. Selezionare **Comunicare ascoltatore HTTP** dalla raccolta per creare un nuovo listener. Chiamare **HTTP1**.
2. Impostare il **Invia risposta automatica?** impostando su false. Configurare l'azione di trigger per _Metodo HTTP_ _post_ e impostazione _URL relativo_ alla _/OneWayPipeline_:  
    ![Trigger HTTP][2]
3. Selezionare il segno di spunta verde per completare il trigger.

## <a name="add-validate-action"></a>Aggiungere la convalida di azione

A questo punto, non resta che specificare le azioni eseguite ogni volta che il trigger viene attivato, vale a dire ogni volta che si riceve una chiamata sull'endpoint HTTP.

1. Aggiungere **Convalida XML BizTalk** della raccolta e denominarla _(Validate1)_ per creare un'istanza.
2. Configurare uno schema XSD per convalidare i messaggi in arrivo XML. Selezionare l'operazione di _convalida_ e quindi _triggers('httplistener').outputs. Contenuto_ come valore per il parametro _inputXml_ .

A questo punto, l'operazione di convalida è la prima azione dopo comunicare ascoltatore HTTP: 

![Convalida BizTalk XML][3]

Analogamente, è possibile aggiungere il resto delle azioni. 

## <a name="add-transform-action"></a>Aggiungere azioni di trasformazione
Configurare le trasformazioni per normalizzare i dati in arrivo di seguito.

1. Aggiungere **Il servizio di trasformazione BizTalk** dalla raccolta.
2. Per configurare una trasformazione per trasformare i messaggi in arrivo XML, selezionare l'azione **trasformare** come azione da eseguire quando si chiama questa API. Selezionare ```triggers(‘httplistener’).outputs.Content``` come valore per _inputXml_. *Mappa* è un parametro facoltativo dal momento che i dati in arrivo vengono trovata una corrispondenza con tutte le trasformazioni configurate e verranno applicate solo quelle che corrispondono allo schema.
3. Infine, la trasformazione viene eseguita solo se ha avuto esito positivo di convalida. Per configurare questa condizione, selezionare l'icona a forma di ingranaggio nella parte superiore destra e selezionare _Aggiungi che venga rispettata una condizione_. Impostare la condizione su ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![Trasformazioni BizTalk][4]


## <a name="add-service-bus-connector"></a>Aggiungere connettore Bus del servizio
Successivamente, aggiungere la destinazione, ovvero una coda di Bus del servizio, in cui scrivere dati.

1. Aggiungere un **Connettore Bus servizio** dalla raccolta. Impostare il **nome** _Servicebus1_, impostare **Stringa di connessione** per la stringa di connessione per l'istanza di bus del servizio, impostare **Nome entità** _coda_e ignorare **nome dell'abbonamento**.
2. Selezionare l'azione **Invia messaggio** e impostare il campo del **contenuto** per l'azione _actions('transformservice').outputs. OutputXml_.
3. Impostare il **Tipo di contenuto del** campo *applicazione/xml*:  

![Bus di servizio][5]


## <a name="send-http-response"></a>Invia risposta HTTP
Una volta completata l'elaborazione della pipeline, inviare nuovamente una risposta HTTP per esito positivo o negativo con la procedura seguente:

1. Aggiungere un **Comunicare ascoltatore HTTP** dalla raccolta e selezionare l'azione **Invia risposta HTTP** .
2. Impostare **ID risposta** al *messaggio*.
2. Impostare **Risposta contenuto** *elaborazione Pipeline completata*.
3. **Codice di stato risposta** *200* per indicare HTTP 200 OK.
4. Selezionare il menu nell'angolo superiore destro a discesa e selezionare **Aggiungi che venga rispettata una condizione**.  Impostare la condizione nell'espressione seguente:  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Ripetere questi passaggi per inviare una risposta HTTP in caso di errore anche. Modificare **condizione** sull'espressione seguente:  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Selezionare **OK** e quindi **creare**.



## <a name="completion"></a>Completamento
Ogni volta che si riceve un messaggio all'endpoint HTTP, attiva l'app ed esegue le azioni che appena creato. Per gestire tali applicazioni di logica creare, selezionare **Sfoglia** nel portale di Azure e selezionare **App logica**. Selezionare l'app per visualizzare altre informazioni.

Alcuni argomenti utili:

[Gestire e monitorare le app API e i connettori](app-service-logic-monitor-your-connectors.md)  <br/>
[Monitorare le app logica](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
