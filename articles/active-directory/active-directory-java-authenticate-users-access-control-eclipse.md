<properties
    pageTitle="Come usare il controllo di accesso (Java) | Microsoft Azure"
    description="Informazioni su come sviluppare e usare il controllo dell'accesso con Java in Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Modalità di autenticazione utenti Web con il servizio controllo di accesso Azure mediante Eclisse

Questa guida illustra come usare Azure Access Control Service (ACS) all'interno del Toolkit di Azure per Eclisse. Per ulteriori informazioni su ACS, vedere la sezione [passaggi successivi](#next_steps) .

> [AZURE.NOTE]
> Il filtro di controllo di Azure Access Services è un'anteprima di tecnologia della community. Come software in versione preliminare, non formale è supportato da Microsoft.

## <a name="what-is-acs"></a>Che cos'è ACS?

Maggior parte dei casi non si è esperti di identità e si desidera in genere dedicato meccanismi di autenticazione e autorizzazione sviluppo ora per le applicazioni e servizi. ACS è un servizio di Azure che offre un modo semplice di autenticazione degli utenti che hanno l'esigenza di accedere le applicazioni web e servizi senza dover logica di autenticazione complesse fattore nel codice.

Le caratteristiche seguenti sono disponibili in ACS:

-   Integrazione con Windows Identity Foundation (WIF).
-   Supporto per i provider di identità web popolari (IP), inclusi Windows Live ID, Google, Yahoo! e Facebook.
-   Supporto per Active Directory Federation Services (ADFS) 2.0.
-   Un Open Data Protocol (OData)-basato su servizio di gestione che fornisce l'accesso alle impostazioni ACS.
-   Portale di gestione che gli amministratori possono accedere alle impostazioni ACS.

Per ulteriori informazioni su ACS, vedere [2.0 Servizio controllo di accesso][].

## <a name="concepts"></a>Concetti

Azure ACS si basa sull'identità dell'identità basata sulle attestazioni - un approccio coerente alla creazione di meccanismi di autenticazione per le applicazioni in esecuzione in locale o nel cloud. Identità basata sulle attestazioni offre un modo comune per applicazioni e servizi acquisire le informazioni di identità che necessarie sugli utenti all'interno dell'azienda in altre organizzazioni e su Internet.

Per completare le attività in questa Guida, è necessario conoscere i concetti seguenti:

**Client** - nel contesto di questa Guida sulle procedure relative questo è un browser che sta tentando di accedere all'applicazione web.

**Applicazione di terze parti (RP) Relying** - applicazione di un RP è un sito Web o un servizio che concede in outsourcing autenticazione da un'autorità esterna. In terminologia di identità, dice che il RP trust quest'ultima. Questa guida viene descritto come configurare un'applicazione all'attendibilità ACS.

**Token** - token è una raccolta di dati di sicurezza che in genere inviati dopo l'autenticazione di un utente. Contiene una serie di *attestazioni*, gli attributi dell'utente autenticato. Una richiesta di rimborso può rappresentare un nome utente, un identificatore per un ruolo di un utente appartiene a un utente età e così via. Un token è in genere con firma digitale, ovvero è possibile sempre origine alla certificazione e il relativo contenuto non può essere manomesso. Un utente accede a un'applicazione RP presentando un token valido rilasciato da un'autorità considera attendibile l'applicazione RP.

**Il Provider di identità (indirizzi IP)** - IP è un'autorità che autentica identità utente e rilascia token di sicurezza. Il lavoro effettivo di token di emissione è implementato anche se un servizio speciale denominato protezione servizio (token). Tipico IP esempi di Windows Live ID, Facebook, archivi utente aziendale (ad esempio Active Directory) e così via.
Quando ACS è configurato per considerare attendibile un indirizzo IP, il sistema accetta e convalidare i token emessi da tale IP. ACS affidabili più indirizzi IP contemporaneamente, vale a dire che quando l'applicazione trust ACS, è possibile istantaneamente offrire l'applicazione per tutti gli utenti autenticati da tutti gli indirizzi IP che ACS trust per proprio conto.

**Il Provider di federazione (virgola mobile)** - IP ha una conoscenza diretta degli utenti, eseguire l'autenticazione mediante le proprie credenziali ed emettere attestazioni sul sanno relative ad esse. Un Provider di federazione (virgola mobile) è un altro tipo di autorità: invece di autenticazione degli utenti direttamente, funge da un'autenticazione intermediario e intermediari tra uno RP e uno o più indirizzi IP. Indirizzi IP e f/s emettere token di sicurezza, pertanto si avvalgono entrambi protezione servizi token di. ACS è una virgola mobile.

**Motore di regole ACS** - logica utilizzata da trasformare in arrivo token da IP attendibile per i token deve essere utilizzato dal RP viene codificato in forma di regole di trasformazione delle attestazioni semplice. ACS è dotato di un modulo di regola che si occupa dell'applicazione di qualsiasi logica di trasformazione specificato per il RP.

**Namespace ACS** - uno spazio dei nomi è una partizione di primo livello di ACS utilizzate per organizzare le impostazioni. Uno spazio dei nomi contiene un elenco di indirizzi IP si ritiene attendibile, le applicazioni di RP che si desidera utilizzare, engine per elaborare i token in arrivo con le regole che si prevede che la regola e così via. Uno spazio dei nomi espone vari endpoint che verrà utilizzato dall'applicazione e lo sviluppatore per ottenere ACS per eseguire la funzione.

La figura seguente mostra come funziona il autenticazione ACS con un'applicazione web:

![Diagramma di flusso ACS][acs_flow]

1.  Il client (in questo caso un browser) richiede una pagina di RP.
2.  Poiché la richiesta non è ancora autenticato, il RP reindirizza l'utente autorità attendibile, ovvero ACS. Servizio ACS presenta all'utente la scelta degli indirizzi IP specificata per questo RP. L'utente seleziona IP appropriato.
3.  Il client accede alla pagina di autenticazione del IP e richiede all'utente di eseguire l'accesso.
4.  Dopo il client viene autenticato (ad esempio, l'identità vengono immesse credenziali), pi invierà un token di sicurezza.
5.  Dopo l'esecuzione di un token di sicurezza, l'indirizzo IP reindirizza il client ACS e il client invia il token di sicurezza emesso dal provider di per ACS.
6.  ACS convalidato il token di sicurezza emesso dal provider di input sull'identità dichiara questo token nel motore di regole ACS, calcola le richieste di identità di output e problemi di un nuovo token di sicurezza che contiene le richieste di output.
7.  ACS reindirizza il client di RP. Il client invia il nuovo token di sicurezza rilasciato da ACS per la RP. Il RP convalida la firma di token di sicurezza rilasciato da ACS, convalida sulle attestazioni in questo token e restituisce la pagina richiesto originariamente.

## <a name="prerequisites"></a>Prerequisiti

Per completare le attività in questa Guida, sarà necessario le operazioni seguenti:

- Un linguaggio sviluppo Kit (JDK), v 1.6 o versioni successive.
- Eclipse IDE per sviluppatori Java Edition, il o versione successiva. Può essere scaricato dal <http://www.eclipse.org/downloads/>. 
- Distribuzione di un server web basate sul linguaggio o applicazione, ad esempio Apache Tomcat, GlassFish, server applicazioni JBoss o alla banchina.
- un abbonamento Azure, può essere ottenuto tramite <http://www.microsoft.com/windowsazure/offers/>.
- Il Toolkit di Azure per Eclisse, aprile 2014 rilasciare o versione successiva. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
- Un certificato x. 509 da utilizzare con l'applicazione. Sarà necessario questo certificato pubblico certificato (CER) e scambio di informazioni personali (. Formato PFX). (Opzioni per la creazione di questo certificato verranno descritto più avanti in questa esercitazione).
- Avere familiarità con la Azure calcolare emulatore e tecniche di distribuzione descritte nella [creazione di un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Creare un Namespace ACS

Per iniziare a utilizzare Servizio controllo di accesso (ACS) in Azure, è necessario creare uno spazio dei nomi ACS. Lo spazio fornisce un ambito univoco per indirizzare sulle risorse ACS dall'interno dell'applicazione.

1. Accedere al [portale di gestione Azure][].
2. Fare clic su **Active Directory**. 
3. Per creare un nuovo controllo di accesso di spazio dei nomi, fare clic su **Nuovo**, fare clic su **Servizi di App**, fare clic su **Controllo di accesso**e quindi fare clic su **Creazione rapida**. 
4. Immettere un nome per lo spazio dei nomi. Azure consente di verificare che il nome sia univoco.
5. Selezionare l'area in cui viene utilizzato lo spazio dei nomi. Per ottenere prestazioni ottimali, usare l'area geografica in cui si distribuisce l'applicazione.
6. Se si dispone di più di una sottoscrizione, selezionare l'abbonamento a cui si desidera utilizzare per lo spazio dei nomi ACS.
7. Fare clic su **Crea**.

Azure crea e attiva lo spazio dei nomi. Attendere fino a quando non è **attivo** lo stato del nuovo spazio dei nomi prima di continuare. 

## <a name="add-identity-providers"></a>Aggiungere i provider di identità

In questa attività, aggiungere gli indirizzi IP di utilizzare con l'applicazione RP per l'autenticazione. Dimostrativo, questa operazione viene illustrato come aggiungere Windows Live come un indirizzo IP, ma è possibile utilizzare uno degli indirizzi IP elencati nel portale di gestione di ACS.


1.  Nel [Portale di gestione Azure][], fare clic su **Active Directory**, selezionare uno spazio dei nomi di controllo di accesso e quindi fare clic su **Gestisci**. Portale di gestione di ACS apre.
2.  Nel riquadro di spostamento sinistro del portale di gestione di ACS, fare clic su **provider di identità**.
3.  Windows Live ID è attivata per impostazione predefinita e non potranno essere eliminate. Ai fini di questa esercitazione, viene utilizzato solo Windows Live ID. Questa schermata invece nel punto in cui è possibile aggiungere altri indirizzi IP, facendo clic sul pulsante **Aggiungi** .

Windows Live ID è abilitato come un indirizzo IP dello spazio dei nomi ACS. Specificare quindi linguaggio applicazione web (creato in un secondo momento) come un RP.

## <a name="add-a-relying-party-application"></a>Aggiungere una componente applicazione di terze parti

In questa attività è configurare ACS per riconoscere l'applicazione web Java come applicazione RP valida.

1.  Nel portale di gestione di ACS fare clic su **applicazioni di terze parti Relying**.
2.  Nella pagina **Che si basa applicazioni di terze parti** , fare clic su **Aggiungi**.
3.  Nella pagina **Aggiungi applicazione di terze parti che si basa** , eseguire le operazioni seguenti:
    1.  In **nome**digitare il nome della RP. Per motivi di questa esercitazione, digitare **Azure Web App**.
    2.  In **modalità**, selezionare **Immettere le impostazioni manualmente**.
    3.  Nell' **area di autenticazione**, digitare l'URI a cui si applica il token di sicurezza rilasciato da ACS. Per questa attività, digitare **http://localhost:8080 /**.
        ![Componente parte dell'area di autenticazione da usare nel calcolo emulatore][relying_party_realm_emulator]
    4.  **Restituire** URL digitare l'URL alla quale ACS restituisce il token di sicurezza. Per questa attività, digitare **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying festa restituire URL per l'utilizzo in emulatore di calcolo][relying_party_return_url_emulator]
    5.  Accettare i valori predefiniti il resto dei campi.

4.  Fare clic su **Salva**.

È stato correttamente configurato l'applicazione web Java quando viene eseguito in emulatore calcolo Azure (in http://localhost:8080 /) da un RP nello spazio dei nomi ACS. Creare regole che ACS utilizza per l'elaborazione di reclami per il RP.

## <a name="create-rules"></a>Creazione di regole

In questa attività, definire regole che determinano come attestazioni vengono passate da indirizzi IP per il RP. Ai fini della Guida, è sufficiente verranno configurate ACS per copiare i valori e i tipi di attestazione di input direttamente nel token di output, senza filtrare o modificarli.

1.  Nella pagina principale portale di gestione ACS, fare clic su **gruppi di regole**.
2.  Nella pagina **Gruppi di regole** fare clic su **Predefinito regola gruppo di Azure Web App**.
3.  Nella pagina **Modifica regola gruppo** fare clic su **Genera**.
4.  Nel **generare regole: predefinito regola gruppo di Azure Web App** , assicurarsi che sia selezionata Windows Live ID e quindi fare clic su **Genera**.    
5.  Nella pagina **Modifica regola gruppo** fare clic su **Salva**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Caricare un certificato allo spazio dei nomi ACS

In questa attività si carica un. Certificato PFX utilizzati per firmare le richieste di token create da uno spazio dei nomi ACS.

1.  Nella pagina principale portale di gestione ACS, fare clic su **certificati e chiavi**.
2.  Nella pagina **certificati e chiavi** fare clic su **Aggiungi** sopra **La firma di Token**.
3.  Nella pagina **aggiungere Token-certificato per la firma o chiave** :
    1. Nella sezione **utilizzato per** selezionare **Azure Web App** (che deve essere impostato in precedenza come il nome dell'applicazione di terze parti componente) **Che si basa applicazione di terze parti** .
    2. Nella sezione **tipo** selezionare **X. 509**.
    3. Nella sezione **certificato** , fare clic sul pulsante Sfoglia e individuare il file di certificato x. 509 che si desidera utilizzare. Questo sarà un. File PFX. Selezionare il file, fare clic su **Apri**e quindi immettere la password del certificato nella casella di testo **Password** . Si noti che scopo di verifica, è possibile utilizzare un automatico-signed-certificato. Per creare un certificato autofirmato, utilizzare il pulsante **Nuovo** nella finestra di dialogo **Raccolta filtro ACS** (descritto più avanti) o utilizzare l'utilità di **encutil.exe** dal [sito Web di progetto][] di Azure Starter Kit per Java.
    4. Verificare che sia selezionata **Rendi principale** . Pagina **certificato aggiungere Token di firma o la chiave** dovrebbe essere simile al seguente.
        ![Aggiungere il certificato di firma di token][add_token_signing_cert]
    5. Fare clic su **Salva** per salvare le impostazioni e chiudere la pagina **certificato aggiungere Token di firma o la chiave** .

Successivamente, esaminare le informazioni nella pagina di integrazione delle applicazioni e copiare l'URI che sarà necessario configurare l'applicazione web Java usare ACS.

## <a name="review-the-application-integration-page"></a>Rivedere la pagina di integrazione delle applicazioni

Per trovare tutte le informazioni e il codice necessarie per configurare l'applicazione web Java (applicazione RP) per l'uso con ACS nella pagina del portale di gestione di ACS integrazione delle applicazioni. Queste informazioni saranno necessarie quando si configura l'applicazione web Java per l'autenticazione federati.

1.  Nel portale di gestione di ACS fare clic su **integrazione delle applicazioni**.  
2.  Nella pagina **Integrazione delle applicazioni** fare clic su **Pagine di accesso**.
3.  Nella pagina di **Integrazione di pagina di accesso** , fare clic su **Azure Web App**.

Nella **l'integrazione di pagina di accesso: Azure Web App** pagina URL indicato nella **opzione 1: collegamento a una pagina di accesso ospitato ACS** verrà utilizzato nell'applicazione web Java. Quando si aggiunge la raccolta di filtro del servizio controllo di accesso Azure all'applicazione Java, sarà necessario questo valore.

## <a name="create-a-java-web-application"></a>Creare un'applicazione web Java
1. All'interno di Eclisse, nel menu fare clic su **File**, fare clic su **Nuovo**e quindi fare clic su **Project Web dinamico**. (Se non è visibile **Dinamico progetto Web** elencato come un progetto disponibile dopo facendo clic su **File**, **Nuovo**, quindi eseguire le operazioni seguenti: fare clic su **File**, fare clic su **Nuovo**, fare clic su **progetto**, espandere **Web**, fare clic su **Progetto Web dinamico**e fare clic su **Avanti**.) Per motivi di questa esercitazione, nome del progetto **MyACSHelloWorld**. (Assicurarsi che quando si utilizza il nome, i passaggi successivi in questa esercitazione prevede che il file WAR in chiamarsi MyACSHelloWorld). Lo schermo apparirà simile al seguente:

    ![Creare un progetto di Hello World per exampple ACS][create_acs_hello_world]

    Fare clic su **Fine**.
2. In visualizzazione di Esplora file di progetto del Eclisse, espandere **MyACSHelloWorld**. Pulsante destro del mouse **DatiWeb**, fare clic su **Nuovo**e quindi fare clic su **File JSP**.
3. Nella finestra di dialogo **Nuovo File JSP** , assegnare un nome di file **index.jsp**. Tenere presente nella cartella padre MyACSHelloWorld/DatiWeb, come illustrato di seguito:

    ![Aggiungere un file JSP, ad esempio ACS][add_jsp_file_acs]

    Fare clic su **Avanti**.

4. Nella finestra di dialogo **Seleziona modello JSP** , selezionare **Nuovo File JSP (html)** e fare clic su **Fine**.
5. Quando si apre il file index.jsp in Eclisse, aggiungere del testo da visualizzare **Hello ACS World!** all'interno di esistente `<body>` elemento. L'aggiornamento `<body>` contenuto deve essere visualizzato il seguente:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Salvare index.jsp.
  
## <a name="add-the-acs-filter-library-to-your-application"></a>Aggiungere la raccolta di filtro ACS all'applicazione

1. In Esplora progetti di Eclisse rapida **MyACSHelloWorld**e quindi fare clic su **Configura percorso compilare** **Compilazione percorso**.
2. Nella finestra di dialogo **Percorso di compilazione di linguaggio** , fare clic sulla scheda **raccolte** .
3. Fare clic su **Aggiungi una raccolta**.
4. Fare clic su **Filtro di Azure accesso controllo servizi (in base alla Tech Apri MS)** e quindi fare clic su **Avanti**. Verrà visualizzata la finestra di dialogo **Filtro di servizi di controllo accesso Azure** .  (Il campo **posizione** potrebbe avere un percorso diverso, a seconda di dove è stato installato Eclisse, e il numero di versione potrebbe essere diverso, a seconda degli aggiornamenti software).

    ![Aggiungere una raccolta di filtro ACS][add_acs_filter_lib]

5. Con un browser aperto per la pagina di **Accesso pagina integrazione** del portale di gestione copiare l'URL nella **opzione 1: collegamento a una pagina di accesso ospitato ACS** di campo e incollarla nel campo **ACS autenticazione Endpoint** della finestra di dialogo Eclisse.
6. Utilizzare un browser aperto la pagina **Modifica applicazione di terze parti che si basa** del portale di gestione, copiare l'URL indicato nel campo **dell'area di autenticazione** e incollare nel campo **dell'Area di terze parti che si basa** della finestra di dialogo Eclisse.
7. All'interno della sezione **sicurezza** della finestra di dialogo Eclisse, se si desidera utilizzare un certificato esistente, fare clic su **Sfoglia**, passare al certificato che si desidera utilizzare, selezionarla e fare clic su **Apri**. In alternativa, se si desidera creare un nuovo certificato, fare clic su **Nuovo** per visualizzare la finestra di dialogo **Nuovo certificato** , quindi specificare la password, nome del file CER e nome del file. pfx per il nuovo certificato.
8. Selezionare **incorpora il certificato nel file WAR**. Incorporare il certificato in questo modo include nella distribuzione senza che sia necessario aggiungere manualmente come componente. (Se è invece necessario archiviare il certificato esternamente dal file WAR, si può aggiungere il certificato di un componente di ruolo e deselezionare **incorpora il certificato nel file WAR**.)
9. [Facoltativo] Mantieni **le connessioni HTTPS richiedono** selezionata. Se si imposta questa opzione, è necessario accedere all'applicazione con il protocollo HTTPS. Se non si vuole richiedere connessioni HTTPS, deselezionare questa opzione.
10. Per una distribuzione all'emulatore di calcolo, le impostazioni di **Filtro ACS Azure** saranno simile al seguente.

    ![Impostazioni del filtro ACS Azure per una distribuzione all'emulatore di calcolo][add_acs_filter_lib_emulator]

11. Fare clic su **Fine**.
12. Fare clic su **Sì** quando verrà visualizzata con una finestra di dialogo che indica che verrà creato un file Web.
13. Fare clic su **OK** per chiudere la finestra di dialogo **Percorso di compilazione di linguaggio** .

## <a name="deploy-to-the-compute-emulator"></a>Distribuire all'emulatore di calcolo

1. In Esplora progetti di Eclisse rapida **MyACSHelloWorld**, fare clic su **Azure**e quindi fare clic su **pacchetto per Azure**.
2. Per **nome del progetto**, digitare **MyAzureACSProject** e fare clic su **Avanti**.
3. Selezionare un JDK e applicazione server. (La procedura seguente illustra sono dettagli nell'esercitazione [creazione di un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).
4. Fare clic su **Fine**.
5. Fare clic sul pulsante **Esegui nell'emulatore Azure** .
6. Dopo l'applicazione web Java avvio nell'emulatore di calcolo, chiudere tutte le istanze del browser (in modo che le sessioni del browser corrente non interferisca con il test di accesso ACS).
7. Eseguire l'applicazione aprendo <> http://localhost:8080/MyACSHelloWorld/nel proprio browser ( <> o/https://localhost:8080/MyACSHelloWorld/se è selezionata **connessioni richiedono HTTPS**). Verrà richiesto per un account di accesso di Windows Live ID, quindi è opportuno URL restituito specificato per l'applicazione di terze parti componente.
99.  Dopo aver visualizzano l'applicazione, fare clic sul pulsante **Reimposta emulatore Azure** .

## <a name="deploy-to-azure"></a>Distribuire Azure

Per distribuire in Azure, è necessario modificare l'area di autenticazione componente di terze parti e restituire URL dello spazio dei nomi ACS.

1. All'interno del portale di gestione di Azure, nella pagina **Modifica applicazione di terze parti che si basa** modificare **dell'area di autenticazione** per l'URL del sito distribuito. Sostituire **esempio** con il nome del DNS specificato per la distribuzione.

    ![Componente parte dell'area di autenticazione per l'utilizzo in produzione][relying_party_realm_production]

2. Modificare l' **URL restituito** per essere l'URL dell'applicazione. Sostituire **esempio** con il nome del DNS specificato per la distribuzione.

    ![Componente URL restituito di terze parti per l'utilizzo in produzione][relying_party_return_url_production]

3. Fare clic su **Salva** per salvare l'aggiornato replying parti dell'area di autenticazione e tornare URL modifiche.
4. Mantenere la pagina di **Accesso pagina integrazione** aperta nel browser, è necessario copiare in esso subito.
5. In Esplora progetti di Eclisse rapida **MyACSHelloWorld**e quindi fare clic su **Configura percorso compilare** **Compilazione percorso**.
6. Fare clic sulla scheda **raccolte** , fare clic su **Filtro di servizi di controllo accesso Azure**e quindi fare clic su **Modifica**.
7. Con un browser aperto per la pagina di **Accesso pagina integrazione** del portale di gestione copiare l'URL nella **opzione 1: collegamento a una pagina di accesso ospitato ACS** di campo e incollarla nel campo **ACS autenticazione Endpoint** della finestra di dialogo Eclisse.
8. Utilizzare un browser aperto la pagina **Modifica applicazione di terze parti che si basa** del portale di gestione, copiare l'URL indicato nel campo **dell'area di autenticazione** e incollare nel campo **dell'Area di terze parti che si basa** della finestra di dialogo Eclisse.
9. All'interno della sezione **sicurezza** della finestra di dialogo Eclisse, se si desidera utilizzare un certificato esistente, fare clic su **Sfoglia**, passare al certificato che si desidera utilizzare, selezionarla e fare clic su **Apri**. In alternativa, se si desidera creare un nuovo certificato, fare clic su **Nuovo** per visualizzare la finestra di dialogo **Nuovo certificato** , quindi specificare la password, nome del file CER e nome del file. pfx per il nuovo certificato.
10. Mantieni **incorpora il certificato nel file WAR** selezionata, presupponendo che si desidera incorporare il certificato nel file WAR.
11. [Facoltativo] Mantieni **le connessioni HTTPS richiedono** selezionata. Se si imposta questa opzione, è necessario accedere all'applicazione con il protocollo HTTPS. Se non si vuole richiedere connessioni HTTPS, deselezionare questa opzione.
12. La distribuzione di Azure, le impostazioni di filtro ACS Azure saranno simile al seguente.

    ![Impostazioni di filtro ACS Azure per una distribuzione di produzione][add_acs_filter_lib_production]

13. Fare clic su **Fine** per chiudere la finestra di dialogo **Modifica catalogo** .
14. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà per MyACSHelloWorld** .
15. In Eclisse, fare clic sul pulsante **pubblica Azure cloud** . Rispondere ai messaggi, simile come completato nella sezione **per distribuire l'applicazione in Azure** della creazione di [Un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Dopo l'applicazione web è stato distribuito, chiudere tutte le sessioni di browser aperta, eseguire l'applicazione web e verrà richiesto di accedere con credenziali Windows Live ID, seguite da inviato all'URL dell'applicazione di terze parti componente restituito.

Al termine tramite l'applicazione Hello World ACS, è necessario ricordare di eliminare la distribuzione (è possibile informazioni su come eliminare una distribuzione in [creazione di un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).


## <a name="next_steps"></a>Passaggi successivi

Per un esame della sicurezza asserzione SAML (Markup Language) restituiti da ACS all'applicazione, vedere [come visualizzare SAML restituiti dal servizio controllo di accesso Azure][]. Per ulteriori esplorare le funzionalità di ACS e per sperimentare scenari più sofisticati, vedere [2.0 Servizio controllo di accesso][].

Inoltre, in questo esempio viene utilizzata l'opzione **incorpora il certificato nel file WAR** . Questa opzione semplifica la distribuzione del certificato. Se invece si desidera mantenere separati da un file WAR certificato di firma, è possibile utilizzare la tecnica seguente:

1. All'interno della sezione di **protezione** della finestra di dialogo **Filtro di servizi di controllo accesso Azure** , digitare $ **{env. JAVA_HOME}/mycert.cer** e deselezionare **incorpora il certificato nel file WAR**. (Regolare mycert.cer se il nome del file di certificato è diverso). Fare clic su **Fine** per chiudere la finestra di dialogo.
2. Copiare il certificato come componente di distribuzione: gestione di progetti di Eclisse In, espandere **MyAzureACSProject**, rapida **WorkerRole1**, fare clic su **proprietà**, espandere **Ruolo Azure**e fare clic su **componenti**.
3. Fare clic su **Aggiungi**.
4. Nella finestra di dialogo **Aggiungi componente** :
    1. Nella sezione **importazione** :
        1. Utilizzare il pulsante **File** per passare al certificato che si desidera utilizzare. 
        2. **Metodo**, selezionare **Copia**.
    2. Per **Come nome**, fare clic sulla casella di testo e accettare il nome predefinito.
    3. Nella sezione **distribuzione** :
        1. **Metodo**, selezionare **Copia**.
        2. In **alla directory**, digitare **% JAVA_HOME %**.
    4. La finestra di dialogo **Aggiungi componente** dovrebbe essere simile al seguente.

        ![Componente di certificato][add_cert_component]

    5. Fare clic su **OK**.

A questo punto, il certificato verrà incluso nella distribuzione. Si noti che indipendentemente dal fatto che si incorpora il certificato nel file di GUERRA o aggiungere come componente per la distribuzione, è necessario caricare il certificato allo spazio dei nomi, come descritto nella sezione [caricare un certificato allo spazio dei nomi ACS][] .

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Caricare un certificato allo spazio dei nomi ACS]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[sito Web di progetto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Come visualizzare SAML restituiti dal servizio controllo di accesso Azure]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Servizio controllo di Access 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portale di gestione di Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
