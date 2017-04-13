<properties
   pageTitle="Informazioni sulle e creare un'app BizTalk regole API nell'App logica | Microsoft Azure"
   description="In questo argomento vengono illustrate le funzionalità del connettore BizTalk regole e istruzioni sull'utilizzo dell'"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Regole di BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Regole di business include i criteri e decisioni che consentono di controllare processi aziendali. Questi criteri possono essere definiti formale in manuali procedura, i contratti o accordi o possono esistere come conoscenze o competenze incorporati in dipendenti. Questi criteri sono dinamici e soggetti a modifiche in tempo a causa di modifiche in piani aziendali, normative o altri motivi.

Implementazione di questi criteri in linguaggi richiede coordinazione e tempo e non viene attivato non programmatori a partecipare nella creazione e gestione dei criteri aziendali. Regole di Business BizTalk offre un modo per implementare tali criteri e separare il resto del processo aziendale rapidamente. In questo modo per rendere le modifiche necessarie ai criteri di business senza influenzare il resto del processo aziendale.

##<a name="why-rules"></a>Perché le regole

Sono disponibili 3 motivi principali per utilizzare le regole aziendali BizTalk nel processo aziendale:

* Separare logica Business dal codice dell'applicazione
- Consentire a Business Analyst per un maggiore controllo sulla gestione di logica di business
+ Apportare modifiche alla logica business Vai a produzione più velocemente

##<a name="rules-concepts"></a>Concetti di regole

##<a name="vocabulary"></a>Terminologia

Un _terminologia_ è un insieme di definizioni di nomi degli oggetti elaborazione usati nelle condizioni e azioni. Definizioni terminologia semplificano la lettura, informazioni e condividere dagli utenti in un dominio specifico business le regole.

Vocabolari sono progettati per collegarle tra semantica aziendale e implementazione. Ad esempio un'associazione dati per uno stato di approvazione potrebbe fare riferimento a una colonna di in una riga in un determinato database, rappresentato da una query SQL. Anziché l'inserimento di questo tipo di rappresentazione complessi in una regola, ma è possibile creare una definizione terminologia associata a che l'associazione dati, con un nome descrittivo "Stato". Successivamente è possibile includere "Stato" in qualsiasi numero di regole e il motore di regola possibile recuperare i dati corrispondenti dalla tabella.

##<a name="rule"></a>Regola

Regole aziendali sono istruzioni dichiarative che regolano l'esecuzione dei processi aziendali. Una regola è costituita da una condizione e azioni. La condizione viene valutata e se il risultato è vero, il motore regole avvia una o più azioni.
Le regole di Business Rules Framework sono definite nel formato seguente:

_IF_ _condizione_ _Quindi_ _azione_

Tenere presente quanto segue:

*Importo se risulta maggiore o uguale a e i fondi disponibili*  
*Effettuare transazioni e conferma di stampa*

Questa regola determina se una transazione sarà effettuata tramite l'applicazione di regole di business, sotto forma di un confronto di due valori monetari, sotto forma di importo di una transazione e i fondi disponibili.
È possibile utilizzare le regole aziendali per creare, modificare e distribuire regole aziendali. In alternativa, è possibile eseguire le operazioni precedenti a livello di programmazione.

###<a name="conditions"></a>Condizioni

Una condizione è vero/falso (booleane) espressione tramite cui è costituito da uno o più predicati.
In questo esempio predicato minore o uguale a verrà applicato all'importo e i fondi disponibili. Questa condizione restituirà sempre true o false.
Predicati possono essere combinati con gli operatori logici AND, OR e non in modo da formare un'espressione logica è potenzialmente molto grande, ma sono sempre uguale a true o false.

###<a name="actions"></a>Azioni

Azioni sono le conseguenze funzionale della valutazione condizione. Se viene soddisfatta una condizione, vengono avviate una o più azioni corrispondente.
In questo esempio, "presentare transazione" e "stampare ricevuta" sono azioni che vengono eseguite quando e solo quando la condizione (in questo caso, "Se quantità è minore o uguale a e i fondi disponibili") è vera.
Azioni sono rappresentate in Business Rules Framework esecuzione di operazioni sui documenti XML.

##<a name="policy"></a>Criteri

Un criterio è un raggruppamento logico di regole. Si comporre un criterio, salvarla, verificarla e, quando si è soddisfatti del risultato usarla in un ambiente di produzione.

###<a name="policy-composition"></a>Composizione dei criteri

È possibile comporre i criteri nel portale di regole. Un criterio può contenere un insieme di qualsiasi dimensione di regole, ma in genere si scrive un criterio di regole che riguardano un dominio specifico business all'interno del contesto dell'applicazione che verrà utilizzato il criterio.

###<a name="policy-testing"></a>Verifica di criteri
È possibile eseguire in modo efficace esecuzione di un test del criterio prima che venga utilizzato in un ambiente di produzione. Portale di regole, è possibile fornire input a un criterio, eseguire i criteri e visualizzarne l'output. L'output include log, esecuzione della regola, valutazione condizione e output risultante.

##<a name="sample-scenario---insurance-claims"></a>Esempio di Scenario - indennizzo
Facciamo uno scenario di esempio e scorrere quando si scrive la regola Business per lo stesso.

![Testo alternativo][1]

In uno scenario di indennizzo molto semplice, il richiedente invia la sua indennizzo (tramite un client come sito Web, telefono App e così via). Questo Richiedi alla convocazione viene inviato all'unità di elaborazione Richiedi dell'azienda e basata sul risultato dell'elaborazione, la richiesta può essere uno dei due approvato rifiutato oppure inviate lungo per l'ulteriore elaborazione manuale.
Richiedi Processing Unit in questo scenario è quello che include la regola Business per il sistema. Eseguendo questa unità in dettaglio, è possibile visualizzare le operazioni seguenti:

![Testo alternativo][2]

Possiamo utilizzare regole aziendali per implementare la logica business.


##<a name="creation-of-rules-api-app"></a>Creazione di regole Api App


1. Accesso al portale di Azure
2. Selezionare -> Nuovo Marketplace quindi cercare *BizTalk regole*
3. Selezionare le regole di BizTalk dall'elenco dei risultati. Verrà visualizzata e il BizTalk regole
4. Selezionare il pulsante *Crea* ![testo alternativo][3]
1. Nella nuova pala visualizzata immettere le informazioni seguenti:  
    1. Nome: assegnare un nome per l'App API di regole
    1. Piano di servizio App: selezionare o creare un piano di servizio nuova App
    1. Prezzi livello-scegliere il livello prezzo desiderato questa App per cui si trovano in
    1. Gruppo di risorse, selezionare o creare il gruppo di risorse in cui l'App deve trovarsi in
    2. Abbonamento - selezionare l'abbonamento che si desidera utilizzare
    1. Posizione, scegliere l'area geografica in cui si desidera l'App per la distribuzione.
4.  Selezionare *Crea*. Pochi minuti dopo l'applicazione API regole BizTalk verrebbero creato.

##<a name="vocabulary-creation"></a>Creazione di terminologia
Dopo la creazione di un'App di API regole BizTalk, il passaggio successivo, è possibile creare vocabolari. La previsione è che lo sviluppatore sia l'utente di tipo più comune per eseguire le operazioni questo esercizio. Ecco come ottenere l'operazione viene eseguita:


1. Barra di avvio il BizTalk regole API App dal portale di facendo clic su Sfoglia -> API App: ><Your Rules API App>. Consente di accedere al dashboard di App API regole simile alla seguente:

   ![Testo alternativo][4]

2. selezionare "Terminologia definizioni". Vengono visualizzati gli 3 la terminologia Authoring schermo selezionare "Aggiungi" per iniziare ad aggiungere nuove definizioni di terminologia.
Esistono 2 tipi di definizioni di terminologia attualmente supportati: valore letterale e XML.

##<a name="literal-definition"></a>Definizione letterale
1.  Dopo aver fatto clic su "Aggiungere", una nuova pala "Aggiungi definizione" aperta. Immettere i seguenti valori
  1.    Nome-solo caratteri alfanumerici sono previste senza caratteri speciali. Anche deve essere univoco all'elenco di definizione terminologia esistente.
  2.    Descrizione: campo facoltativo.
  3.    Tipo di definizione: Esistono 2 tipi supportati. Scegliere letterali in questo esempio
  4.    Tipo di dati: in questo modo agli utenti di selezionare il tipo di dati della definizione. Attualmente sono supportati i tipi di 4 dati: si.  Stringa – questi valori devono essere immesse tra virgolette doppie ("esempio stringa")  
    II. Booleano, può essere vero o FALSO  
    III.    Numero, può essere un numero decimale  
    IV. DateTime: ciò significa che la definizione del tipo di tipo Data. Dati devono essere immesse utilizzando questo formato: gg/mm/aaaa hh AM\PM  
  5. Input – si tratta in cui si immette il valore della definizione. I dati immessi devono essere conforme al tipo di dati scelta. È possibile immettere un singolo valore, una serie di valori separati da virgole o un intervallo di valori utilizzando la parola chiave *a*. Ad esempio, è possibile immettere un valore univoco 1; un set di 1, 2, 3; o un intervallo da 1 a 5. Si noti che intervallo è supportato solo per i numeri.
  6. Fare clic su *OK*.

![Testo alternativo][5]
##<a name="xml-definition"></a>Definizione XML
Se si sceglie il tipo di terminologia in formato XML, è necessario specificare input seguenti  
  un.    Schema: facendo clic sulla seguente verrà aperto un nuovo utente che consente di blade per scegliere da un elenco di schemi già caricati o che consente di caricare uno nuovo.
b.    XPATH: questo input Ottiene sbloccati solo dopo aver scelto uno schema nel passaggio precedente. Fare clic su questo verrà visualizzato lo schema che è stato selezionato e consente di selezionare il nodo per il quale è necessario creare una definizione di terminologia.  
  c.    FATTORIALE – questo input identifica quale oggetto dati da notte al motore di regole per l'elaborazione. Questa è una proprietà avanzata e per impostazione predefinita è impostata per l'elemento padre di XPATH selezionato. FATTORIALE diventa particolarmente importante per gli scenari di concatenazione e insieme.

![Testo alternativo][6]

### <a name="add-bulk"></a>Aggiungere in blocco
La procedura sopra indicata acquisito l'esperienza per la creazione di definizioni di terminologia. Una volta creato, le definizioni di terminologia creato verranno visualizzato in forma di elenco. Esistono requisiti per poter generare più definizioni dallo stesso schema anziché inserire la procedura sopra indicata ogni singola volta. Verrà visualizzata nel punto in cui può essere utile la funzionalità Aggiungi in blocco.
Selezionare "Aggiungi in blocco" verrà visualizzata una nuova pala. Il primo passaggio consiste nel selezionare lo schema per cui devono essere create più definizioni. La selezione di questa verrà aperta una nuova pala consentendo di scegliere da un elenco degli schemi già caricati o per caricare uno nuovo.
A questo punto la proprietà XPath Ottiene sbloccata. La selezione di questa verrà aperto il Visualizzatore di Schema in cui è possano selezionare più nodi.
I nomi di più definizioni create verranno automaticamente il nome del nodo selezionato. Questi possono essere modificati sempre dopo la creazione.

![Testo alternativo][7]

##<a name="policy-creation"></a>Creazione dei criteri
Una volta lo sviluppatore ha creato vocabolari necessari, la previsione è che il Business Analyst sarebbero i criteri aziendali creazione uno tramite il portale di Azure.  
    1. all'applicazione di regole create, non c'è un obiettivo criteri facendo clic su cui l'utente verrà inserito la pagina di creazione di criteri.  
    2. questa pagina verrà visualizzato l'elenco dei criteri che con questa applicazione di regole specifico. Il analyst è possibile aggiungere un nuovo criterio semplicemente digitando un nome di criteri e raggiungere scheda due volte. Più criteri possono trovarsi in un'unica App API regole.  
    3. selezionare il criterio creato avrà l'utente per la pagina dei dettagli dei criteri in cui si possono osservare le regole nei criteri.  
    ![Testo alternativo][8]
 4.  Selezionare "Aggiungi" per aggiungere una nuova regola. Verrà visualizzata una nuova pala.

##<a name="rule-creation"></a>Creazione di regole
Una regola è insieme di istruzioni condizione e azione. Se la condizione restituisce true, vengono eseguite le azioni. In e il Crea regola, assegnare un nome univoco (per il criterio) e una descrizione (facoltativa).
La casella condizione (IF) può essere usata per creare istruzioni condizionali complesse. Ecco le parole chiave supportate:  
1.  E – operatore condizionale  
2.  Oppure -operatore condizionale  
3.  indica\_non\_esistenti  
4.  esiste  
5.  FALSO  
6.  è\_uguale\_a  
7.  è\_maggiore\_più  
8.  è\_maggiore\_più\_uguale\_a  
9.  è\_in  
10. è\_meno\_più  
11. è\_meno\_più\_uguale\_a  
12. è\_non\_in  
13. è\_non\_uguale\_a  
14. resto  
15. vero  

La casella Action(THEN) può contenere più istruzioni, uno per ogni riga, per creare le azioni da eseguire. Ecco le parole chiave supportate:  
1.  è uguale a  
2.  FALSO  
3.  vero  
4.  interrompere  
5.  resto  
6.  null  
7.  aggiornamento  

Le caselle condizione e azione forniscono Intellisense per creare rapidamente una regola. Può essere attivato premendo ctrl + barra spaziatrice oppure semplicemente iniziare a digitare. Parole chiave corrispondenti caratteri digitati verranno automaticamente filtrate verso il basso e visualizzate. Nella finestra di intellisense illustra tutte le parole chiave e le definizioni di terminologia.
![Testo alternativo][9]

##<a name="explicit-forward-chaining"></a>Espliciti Forward concatenazione
Supporta le regole di BizTalk espliciti forward concatenazione, se gli utenti che vogliono valutare nuovamente le regole in risposta a determinate azioni, possono attivare questa utilizzando determinate parole chiave. Di seguito sono le parole chiave supportate:  
   1.   aggiornare <vocabulary definition> – la parola chiave viene rivalutata tutte le regole che utilizzano la definizione di terminologia specificato nella relativa condizione.  
   2.   Interruzione: tutte le esecuzioni regola si interrompe la parola chiave

##<a name="enabledisable-rules"></a>Abilita/disabilita regole
Ogni regola del criterio può essere abilitata o disabilitata. Per impostazione predefinita tutte le regole sono abilitate. Le regole disabilitate mancate essere eseguita durante l'esecuzione dei criteri. Abilita/disabilita le regole possono essere eseguite da e il regola direttamente, i comandi disponibili nella barra dei comandi nella parte superiore e il o dai criteri, il menu di scelta rapida (pulsante destro del mouse su una regola) con l'opzione Abilita/Disabilita.

##<a name="rule-priority"></a>Priorità regole
Tutte le regole di un criterio vengono eseguite in ordine. La priorità di esecuzione è l'ordine in cui si sono verificati nei criteri. Questo livello di priorità può essere modificata dagli semplicemente trascinando la regola.

##<a name="test-policy"></a>Criterio di prova
È possibile verificare i criteri utilizzando il comando "Test dei criteri" in e il Test dei criteri. In questo blade è possibile visualizzare un elenco di definizioni terminologia utilizzate nei criteri che richiedono un input utente. Gli utenti possono aggiungere manualmente i valori per i dati di input per loro scenario di test. In alternativa, gli utenti possono anche scegliere di importare test XMLs per input. Una volta tutti gli input, eseguire il test e gli output per ogni definizione di terminologia verranno visualizzati nella colonna output per il confronto semplice. Per visualizzare registri descrittivi Business Analyst, fare clic su "Visualizza i registri" per visualizzare i registri di esecuzione. Per salvare i log, l'opzione "Salva Output" è disponibile per archiviare tutti i dati correlati per l'analisi indipendenti di test.

## <a name="using-rules-in-logic-apps"></a>Usando le regole in App logica
Dopo il criterio è stato creato e verificato, è ora pronto per l'utilizzo. È possibile creare una nuova App logica selezionando logica App relativi al lato sinistro della pagina home del portale. Dopo aver creato l'App logica, avviarlo, quindi selezionare *trigger e azioni*. È quindi possibile selezionare il modello di *creare da zero* . Seguire i passaggi per aggiungere l'App di API regole BizTalk all'App logica. Al termine, si verificherà un'opzione per scegliere quali App API regole (azione) di destinazione. Azioni includono l'elenco dei criteri che devono essere eseguita. Scegliere un determinato criterio trascorso il quale gli input necessari per il criterio deve essere specificato. Gli utenti possono utilizzare l'output dall'App API regole valle per ulteriormente processi decisionali.

## <a name="using-rules-via-apis"></a>Utilizzo delle regole tramite API
L'App API regole può anche essere richiamato utilizzando una vasta gamma di API. Gli utenti in questo modo non limitata usando logica App e possono usare le regole in qualsiasi applicazione eseguendo chiamate resto. Le API REST esatta disponibili possono essere visualizzate facendo clic sull'obiettivo "API definizione" nel dashboard di regole.

![Testo alternativo][10]

Di seguito è riportato un esempio di come potrebbe usare questa API c#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Si noti che l'App API regole precedenti ha le impostazioni di protezione impostate su "Anon pubblico". Questo può essere impostato dall'interno dell'App API utilizzando - tutte le impostazioni -> Impostazioni applicazione -> livello di accesso.

![Testo alternativo][11]

## <a name="editing-vocabulary-and-policy"></a>Modifica criteri e terminologia
Uno dei vantaggi principali di utilizzo di regole Business è che modifiche logica business possono essere inserite in produzione molto più veloce. Qualsiasi modifica apportata alla terminologia e i criteri viene applicata immediatamente nell'ambiente di produzione. È sufficiente cercare la definizione di terminologia rispettivi o criteri e apportare le modifiche in modo che possano essere applicati.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
