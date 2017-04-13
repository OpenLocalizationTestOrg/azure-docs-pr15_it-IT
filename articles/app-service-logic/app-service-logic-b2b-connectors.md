<properties 
    pageTitle="Connettori Business-to-Business e le API App nelle App logica | Microsoft Azure" 
    description="Informazioni su come creare e configurare i connettori EDI, EDIFACT, AS2 e TPM; architettura microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Connettori Business-to-Business e le API App

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Logica App include molte API App BizTalk importanti per gli ambienti di integrazione. Queste applicazioni API basate sui concetti e strumenti che BizTalk Server, ma sono ora disponibili come parte della logica di App. 

Una categoria di queste applicazioni API sono le app di API Business-to-Business (B2B). Usa queste App API B2B, è possibile facilmente aggiungere partner, creare contratti ed eseguire tutti gli elementi come si preferisce locale mediante EDI, AS2 ed EDIFACT.  

Queste applicazioni API B2B offrono funzionalità "Trigger" e "Azioni". Un Trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un X12 messaggio da un partner. Un'azione è il risultato, ad esempio dopo aver ricevuto un X12 del messaggio, quindi inviare il messaggio utilizzando AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>Che cos'è un connettore Business-to-Business o API App
La funzionalità di Business-to-Business (B2B) include App API esistente, predefinite che consentono di società diverse, divisioni, applicazioni e così via a comunicare utilizzando AS2, EDI ed EDIFACT. 

Le app di API B2B includono: 

Connettore o API App | Descrizione
--- | ---
Gestione Partner commerciali BizTalk | Un'App API che consente di creare le relazioni business-to-business (B2B) mediante partner e contratti. Queste relazioni di utilizzano il AS2, EDIFACT e X12 protocollo.<br/><br/>L'App API TPM, la necessità di base del connettore AS2 e il X12 o EDIFACT API app. 
Connettore AS2 | Connettore che riceve e invia messaggi che utilizzano il trasporto AS2. Il connettore trasporti dati in modo sicuro e affidabile tramite Internet.
BizTalk EDIFACT | Un'App API che riceve e invia messaggi che utilizzano EDIFACT. EDIFACT viene chiamati anche come UN/EDIFACT (United Nations/elettronica interscambio dati per amministrazione, e-Commerce e trasporto) e usato tra settore.
BizTalk X12 | Un'App API che riceve e invia messaggi che utilizzano il X12 protocollo. X12 è anche più comunemente ASC X 12 (accreditati standard denominato X12) e usato tra settore. 


Usa queste App API, è possibile completare diversi EDI messaggistica attività. Ad esempio, utilizzando il connettore AS2, è possibile in modo sicuro ricevere e inviare tipi diversi di messaggi (EDI, XML, in File Flat, e così via) a un cliente, ad esempio una divisione all'interno della società risorse umane o qualsiasi utente che utilizza AS2. 

È possibile creare tanti API App che si desidera e crearli facilmente. È inoltre possibile riutilizzare un singolo API App più scenari o flussi di lavoro.

È possibile eseguire questa operazione senza scrivere codice. Iniziamo. 


## <a name="requirements-to-get-started"></a>Requisiti per iniziare
Quando si crea B2B API App, esistono alcune risorse necessarie. Questi elementi devono essere creati dall'utente prima che possa essere utilizzate all'interno di altre App API. Tali requisiti includono: 

Requisito | Descrizione
--- | ---
Database SQL Azure | Archivia elementi B2B inclusi partner, schemi, i certificati e agreeements. Ognuna delle applicazioni di API B2B richiede un Database di SQL Azure. <br/><br/>**Nota** Copiare la stringa di connessione al database.<br/><br/>[Creare un Database SQL Azure](../sql-database/sql-database-get-started.md)
Contenitore di archiviazione Blob Azure | Proprietà del messaggio, archivi quando AS2 archiviazione è abilitata. Se non è necessario AS2 messaggio archiviazione, un contenitore di archiviazione non è necessario. <br/><br/>**Nota** Se si desidera consentire l'archiviazione, copiare la stringa di connessione a questa archiviazione Blob.<br/><br/>[Informazioni sugli account di archiviazione Azure](../storage/storage-create-storage-account.md)
Servizio Bus Namespace e i relativi valori di chiave | Archivia X12 ed EDIFACT il batch di dati. Se non è necessario il batch, uno spazio dei nomi Bus di servizio non è necessario.<br/><br/>**Nota** Se si attiva la divisione in batch, copiare questi valori.<br/><br/>[Creare un Namespace Bus di servizio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Istanza TPM | Per creare un connettore AS2 e X12 o EDIFACT API App è necessaria un'istanza di BizTalk Trading Partner (TPM Management). Quando si crea l'App API TPM, si sta creando l'istanza di TPM. <br/><br/>**Nota** Conoscere il nome dell'App API TPM. 


## <a name="create-the-api-apps"></a>Creare App API
Tramite il portale di Azure o utilizzando le API REST, è possibile creare B2B API app. 


### <a name="create-the-api-apps-using-rest-apis"></a>Creare App API utilizzando le API REST
[Vedere la documentazione su come utilizzare le API REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Creare le app di API B2B nel portale di Azure
Nel portale di Azure, è possibile creare B2B API App durante la creazione di logica Apps, App Web o App Mobile. In alternativa, è possibile crearne uno utilizzando il proprio blade. Entrambe le opzioni sono semplici, in modo che varia a seconda del esigenze o preferenze. Alcuni utenti preferiscono prima creare tutte le applicazioni di API B2B con le relative proprietà specifiche. Quindi, creare logica App/Web App/Mobile Apps e aggiungere le app di API B2B è stato creato.  

La procedura seguente crea le app di API B2B utilizzando e il API app.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Creare BizTalk Trading Partner Management (TPM) API App

> [AZURE.NOTE] Per creare un connettore AS2 e X12 o EDIFACT API App è necessaria un'istanza di BizTalk Trading Partner (TPM Management). Quando si crea l'App API TPM, si sta creando l'istanza di TPM.

La procedura seguente crea l'istanza TPM:

1. Nel portale di Azure Startboard (alla Home page), selezionare **Marketplace**. **API App** Elenca tutte le applicazioni API esistenti e connettori. È inoltre possibile **ricerca** per le app di API B2B specifico.
2. Selezionare **BizTalk gestione dei Partner commerciali**. Nella nuova pala selezionare **Crea**. 
3. Immettere le proprietà: 

    Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per l'istanza TPM. Ad esempio, è possibile denominarlo *AccountsPayableTPM*.
Impostazioni del pacchetto | Immettere la ADO.NET **Stringa di connessione di Database** al Database SQL Azure è stato creato. <br/><br/>Quando si copia la stringa di connessione, la password non viene aggiunto alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione.
Piano di servizio di App | Elenca il piano di pagamento. È possibile modificarlo se è necessario maggiore o minore di risorse.
Prezzi livello | Proprietà di sola lettura in cui sono elencati i Prezzo categoria all'interno di abbonamento Azure. 
Gruppo di risorse | Creare un nuovo PIN o utilizzare un gruppo esistente. Tutte le app API e connettori per App logica, Web App e App Mobile devono essere dello stesso gruppo di risorse. <br/><br/>[Utilizzo di gruppi di risorse](../azure-resource-manager/resource-group-overview.md) descrive questa proprietà. 
Abbonamento | Proprietà di sola lettura in cui sono elencati l'abbonamento corrente.
Posizione | L'area geografica che ospita il servizio Azure. 
Aggiungere a Startboard | Selezionare questa opzione per aggiungere l'App API B2B il Starboard (la pagina home).

4. Selezionare **Crea**. 

Dopo aver creato l'APP API TPM (TPM istanza), è possibile creare quindi il connettore AS2 e/o il X12 o EDIFACT API app. 


#### <a name="create-the-as2-connector"></a>Creare il connettore AS2

1. Nel portale di Azure Startboard (alla Home page), selezionare **Marketplace**. **API App** Elenca tutte le applicazioni API esistenti e connettori. È inoltre possibile **ricerca** per le app di API B2B specifico.
2. Selezionare **il connettore AS2**. Nella nuova pala selezionare **Crea**. 
3. Immettere le proprietà: 

    Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per il connettore AS2. Ad esempio, è possibile denominarlo *AS2Connector*.
Impostazioni del pacchetto | Immettere le impostazioni specifiche per tale App API, ad esempio il nome dell'istanza TPM. <br/><br/>In questo argomento per le proprietà specifiche, vedere [Aggiungere le impostazioni dei pacchetti AS2](#AddAS2Conn) . 
Piano di servizio di App | Elenca il piano di pagamento. È possibile modificarlo se è necessario maggiore o minore di risorse.
Prezzi livello | Proprietà di sola lettura in cui sono elencati i Prezzo categoria all'interno di abbonamento Azure. 
Gruppo di risorse | Creare un nuovo PIN o utilizzare un gruppo esistente. [Utilizzo di gruppi di risorse](../azure-resource-manager/resource-group-overview.md) descrive questa proprietà. 
Abbonamento | Proprietà di sola lettura in cui sono elencati l'abbonamento corrente.
Posizione | L'area geografica che ospita il servizio Azure. 
Aggiungere a Startboard | Selezionare questa opzione per aggiungere l'App API B2B il Starboard (la pagina home).

    **<a name="AddAS2Conn"></a>Connettore AS2 impostazioni pacchetto**

    Proprietà | Descrizione
--- | --- 
Stringa di connessione di database | Immettere la stringa di connessione ADO.NET al Database SQL Azure è stato creato. Quando si copia la stringa di connessione, la password non viene aggiunto alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Abilitare l'archiviazione dei messaggi in arrivo | Facoltativo. Abilitare questa proprietà archiviare le proprietà dei messaggi dei messaggi in arrivo AS2 ricevuto da un partner. 
Stringa di connessione di spazio di archiviazione Blob Azure  | Immettere la stringa di connessione al contenitore di archiviazione Blob Azure che è stato creato. Quando si abilita archiviazione, messaggi codificati e codificati vengono memorizzati in questo contenitore di spazio di archiviazione.
Nome dell'istanza TPM | Immettere il nome della **Gestione Partner commerciali BizTalk** App API creata in precedenza. Quando si crea il connettore AS2, il connettore viene eseguita solo gli accordi AS2 all'interno di questa specifica istanza TPM.

4. Selezionare **Crea**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Creare le app di API X12 o EDIFACT

1. Nel portale di Azure Startboard (alla Home page), selezionare **Marketplace**. **API App** Elenca tutte le applicazioni API esistenti e connettori. È inoltre possibile **ricerca** per le app di API B2B specifico.
2. Selezionare **BizTalk X 12** o **EDIFACT BizTalk**. Nella nuova pala selezionare **Crea**. 
3. Immettere le proprietà: 

    Proprietà | Descrizione
--- | ---
Nome | Immettere un nome per l'App API B2B. Ad esempio, è possibile denominarlo *EDI850APIApp*.
Impostazioni del pacchetto | Immettere le impostazioni specifiche per tale App API, ad esempio il nome dell'istanza TPM. <br/><br/>In questo argomento per le proprietà specifiche, vedere [X12 o EDIFACT pacchetto impostazioni](#AddX12) . 
Piano di servizio di App | Elenca il piano di pagamento. È possibile modificarlo se è necessario maggiore o minore di risorse.
Prezzi livello | Proprietà di sola lettura in cui sono elencati i Prezzo categoria all'interno di abbonamento Azure. 
Gruppo di risorse | Creare un nuovo PIN o utilizzare un gruppo esistente. [Utilizzo di gruppi di risorse](../azure-resource-manager/resource-group-overview.md) descrive questa proprietà. 
Abbonamento | Proprietà di sola lettura in cui sono elencati l'abbonamento corrente.
Posizione | L'area geografica che ospita il servizio Azure. 
Aggiungere a Startboard | Selezionare questa opzione per aggiungere l'App API B2B il Starboard (la pagina home).

    **<a name="AddX12"></a>Impostazioni di pacchetto App API X12 ed EDIFACT**  

    Proprietà | Descrizione
--- | --- 
Stringa di connessione di database | Immettere la stringa di connessione ADO.NET al Database SQL Azure è stato creato. Quando si copia la stringa di connessione, la password non viene aggiunto alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Servizio Bus Namespace | Immettere lo spazio dei nomi Bus di servizio che è stato creato. È necessario solo quando il batch è attivato. 
Nome del servizio Bus Namespace condiviso tasto di scelta | Immettere lo spazio dei nomi del servizio Bus tasto di scelta rapida è stato creato. È necessario solo quando il batch è attivato. 
Valore servizio Bus Namespace condiviso tasto di scelta | Immettere lo spazio dei nomi del servizio Bus valore tasto di scelta rapida è stato creato. È necessario solo quando il batch è attivato. 
Nome dell'istanza TPM | Immettere il nome della **Gestione Partner commerciali BizTalk** App API creata in precedenza. Quando si crea la X12 o EDIFACT API App, l'API App viene eseguita solo gli accordi X12/EDFIACT all'interno di questa specifica istanza TPM.

4. Selezionare **Crea**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Aggiungere il proprio partner, contratti, certificati e gli schemi 
Nel portale di Azure, aprire l'App di API TPM. Nella sezione **componenti** aggiungere partner, contratti, certificati e schemi. 

È anche possibile aggiungere accordi ai connettori AS2, X12 API App e App API EDIFACT. 


## <a name="monitor-your-api-apps"></a>Monitorare le app API
Nel portale di Azure, aprire l'App di API TPM. Nella sezione **operazioni** è possibile visualizzare le operazioni di gestione diverso. Ad esempio, è possibile:

- Eventi di errore e visualizzazione informativo
- Visualizza conteggio di thread e l'utilizzo della memoria del processo di lavoro (w3wp)
- Visualizzare i registri di server web e applicazioni

Più, a [monitorare le app logica](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Aggiungere le app API all'applicazione 
Servizio di Microsoft Azure App presenta diversi tipi di applicazioni che è possono utilizzare queste App API B2B. Creare un nuovo o aggiungere le app di API B2B esistente logica Apps, App Mobile o Web Apps. 

Entro l'App, semplicemente selezionando automaticamente le app di API B2B dalla raccolta viene aggiunto all'App.  

> [AZURE.IMPORTANT] Per aggiungere connettori e le API App creata in precedenza, creare la logica Apps, App Mobile o Web Apps nello stesso gruppo di risorse. 

La procedura seguente aggiunta le app di API B2B logica Apps, App Mobile o Web App: 

1. Nel portale di Azure Startboard (pagina home), visitare il **Marketplace**e cercare la logica, Mobile o Web Apps. 

    Se si sta creando una nuova App cercare logica Apps, App Mobile o Web Apps. Selezionare l'App e nella nuova pala selezionare **Crea**. [Creare un'App logica](app-service-logic-create-a-logic-app.md) Elenca i passaggi. 

2. Aprire l'App e selezionare **trigger e azioni**. 

3. Nella **raccolta**, selezionare l'App API B2B, che viene automaticamente aggiunto all'App. È anche possibile creare una nuova App API B2B.

    > [AZURE.IMPORTANT] Il connettore AS2 e X12, EDIFACT API App richiedere un'istanza di TPM. In modo che se si creano nuove app API B2B, creare innanzitutto l'App API TPM e quindi creare il connettore AS2, X12 API App o EDIFACT API App. 

4. Fare clic su **OK** per salvare le modifiche. 

>[AZURE.NOTE] Per iniziare a usare le applicazioni di logica di Azure prima di iscriversi a un account Azure, [Provare a App logica](https://tryappservice.azure.com/?appservice=logic). Creare un'app di logica starter breve immediatamente. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="more-b2b-resources"></a>Altre risorse B2B

[Creazione di un processo B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Creazione di un contratto per i Partner commerciali](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[Cosa sono i connettori e BizTalk API App](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>Informazioni sulle App logica e Web Apps
[Quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md)<br/>
[Siti Web e Web Apps nel servizio App Azure](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Ulteriori connettori

[Elenco di App API e connettori](app-service-logic-connectors-list.md)<br/><br/>
[Cosa sono i connettori e BizTalk API App](app-service-logic-what-are-biztalk-api-apps.md) 
