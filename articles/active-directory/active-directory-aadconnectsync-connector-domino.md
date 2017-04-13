<properties
   pageTitle="Connettore di Lotus Domino | Microsoft Azure"
   description="In questo articolo viene descritto come configurare Lotus Domino connettore Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Documentazione tecnica di Lotus Domino connettore
Questo articolo descrive il connettore di Lotus Domino. L'articolo si applica ai seguenti prodotti:

- Gestione identità Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario utilizzare correzione 4.1.3671.0 o versioni successive [KB3092178](https://support.microsoft.com/kb/3092178).

Per MIM2016 e FIM2010R2, il connettore è disponibile per il download dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Panoramica del connettore Lotus Domino
Il connettore di Lotus Domino consente di integrare il servizio di sincronizzazione con IBM Lotus Domino server.

Dal punto di vista di alto livello, le caratteristiche seguenti sono supportate per la versione corrente del connettore:

Caratteristica | Supporto tecnico
--- | ---
Origine dati connessa | Server: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9</li>Client:<li>Lotus Notes 9</li>
Scenari | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione dei gruppi</li><li>Gestione delle password</li>
Operazioni | <li>Completo e di importazione Delta</li><li>Esportazione</li><li>Impostare e modificare la password nella password HTTP</li>
Schema | <li>Persona (Roaming utente, contatto (persone con alcun certificato))</li><li>Gruppo</li><li>Risorsa (riunione Online sala, risorsa)</li><li>Database di posta elettronica</li><li>Individuazione automatica degli attributi di oggetti supportati</li>

Il connettore Lotus Domino utilizza il client Lotus Notes per comunicare con Lotus Domino Server. Causa la relazione, è necessario installare un Client di Lotus Notes supportati nel server di sincronizzazione. Le comunicazioni tra il client e server implementata tramite l'interfaccia di Lotus Notes .NET interoperabilità (Interop.domino.dll). Questa interfaccia facilita la comunicazione tra il client di Lotus Notes e la piattaforma Microsoft.NET e supporta l'accesso ai documenti di Lotus Domino e visualizzazioni. Per l'importazione delta, è inoltre possibile che l'interfaccia nativa C++ viene utilizzato (a seconda del metodo di importazione delta selezionato).

### <a name="prerequisites"></a>Prerequisiti
Prima di utilizzare il connettore, accertarsi di avere le operazioni seguenti per il server di sincronizzazione:

- 4.5.2 Microsoft .NET Framework o versioni successive
- Il client Lotus Notes deve essere installato il server di sincronizzazione
- Il connettore di Lotus Domino richiede il database predefinito Lotus Domino LDAP schema (schema.nsf) deve essere presente nel server Domino Directory. Se non è presenta, è possibile installare Office eseguendo o riavviare il servizio LDAP sul server di Domino.

### <a name="connected-data-source-permissions"></a>Autorizzazioni di origine dati connessione
Per eseguire una delle operazioni supportate connettore Lotus Domino, è necessario essere membri dei gruppi seguenti:

- Amministratori di accesso completi
- Amministratori
- Amministratori di database

Nella tabella seguente sono elencate le autorizzazioni necessarie per ogni operazione:

Operazione | Diritti di accesso
--- | ---
Importazione | <li>Leggere documenti pubblici</li><li> Amministratore di accesso completo (quando si è membri del gruppo administrators accesso completo, si dispone automaticamente l'accesso efficace all'ACL.)</li>
Esportare e impostare Password | Accesso effettivo: <li>Creazione di documenti</li><li>Eliminare documenti</li><li>Leggere documenti pubblici</li><li>Scrivere documenti pubblici</li><li>Documenti di replica o copia</li>In caso di esportazione, sarà necessario anche i ruoli seguenti: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Le operazioni dirette e AdminP
Operazioni che uno passare direttamente alla directory Domino o attraverso il processo di AdminP. Le seguenti tabelle oggetti tutti di elenco supportati, operazioni e, se applicabile, il metodo di implementazione correlate:

**Rubrica principale**

Oggetto | Creare | Aggiornamento | Elimina
--- | --- | --- | ---
Persona | AdminP | Accesso diretto | AdminP
Gruppo | AdminP | Accesso diretto | AdminP
MailInDB | Accesso diretto | Accesso diretto | Accesso diretto
Risorsa | AdminP | Accesso diretto | AdminP

**Rubrica secondario**

Oggetto | Creare | Aggiornamento | Elimina
--- | --- | --- | ---
Persona | N/D | Accesso diretto | Accesso diretto
Gruppo | Accesso diretto | Accesso diretto | Accesso diretto
MailInDB | Accesso diretto | Accesso diretto | Accesso diretto
Risorsa | N/D | N/D | N/D

Quando viene creata una risorsa, viene creato un documento di note. Analogamente, quando si elimina una risorsa, il documento di note viene eliminato.

### <a name="ports-and-protocols"></a>Porte e protocolli
IBM Lotus Notes client e server Domino comunicare utilizzando note Remote Procedure chiamare (NRPC) in cui NRPC necessario utilizzare TCP/IP. Il numero di porta predefinita è 1352, ma può essere modificato dall'amministratore del Domino.

### <a name="not-supported"></a>Non è supportata
Le operazioni seguenti non sono supportate per la versione corrente del connettore Lotus Domino:

- Spostare cassette postali tra server.

## <a name="create-a-new-connector"></a>Creare un nuovo connettore

### <a name="client-software-installation-and-configuration"></a>Configurazione e installazione del Software client
Installare Lotus Notes deve essere il server **prima** che è installato il connettore.

Durante l'installazione, assicurarsi di eseguire un' **Installazione utente singolo**. Il valore predefinito **Multiutente installare** non funziona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Nella pagina caratteristiche installare solo le funzionalità di Lotus Notes necessarie e **Accesso singolo Client**. Accesso singolo è necessario per il connettore in grado di accedere al server di Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** Avviare una volta Lotus Notes con un utente che si trova nello stesso server l'account usato come account di servizio del connettore. Assicurarsi di chiudere il client di Lotus Notes nel server. Non possono essere in esecuzione nello stesso momento che il connettore tenta di connettersi al server di Domino.

### <a name="create-connector"></a>Creare connettore
Per creare un connettore Lotus Domino, selezionare **Agente di gestione** e **creare**nel **Servizio di sincronizzazione** . Selezionare il connettore **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se la versione del servizio di sincronizzazione offre la possibilità di configurare **architettura**, assicurarsi che il connettore è impostato sul valore predefinito per l'esecuzione nel **processo**.

### <a name="connectivity"></a>Connettività
Nella pagina connettività è necessario specificare il nome del server Lotus Domino e immettere le credenziali di accesso.  
![Connettività](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Proprietà Server Domino supporta due formati per il nome del server:

- NomeServer
- NomeServer/DirectoryName

Il formato **Nomeserver/DirectoryName** è il formato desiderato per l'attributo perché offre risposta più rapida quando il connettore contatta il domino.

File di ID utente fornito è archiviato nel database di configurazione del servizio di sincronizzazione.

Per **l'Importazione Delta** si dispone di queste opzioni:

- **Nessuno**. Il connettore eseguire qualsiasi importazione delta.
- **Aggiungere o aggiornare**. L'importazione delta sono gli effetti di connettore aggiungere e aggiornare le operazioni. Per l'eliminazione, è richiesta un'operazione di **Importazione completa** . Questa operazione utilizza interoperabilità .net.
- **Aggiunta, aggiornamento/eliminazione**. L'importazione di delta connettore sono gli effetti di aggiungere, aggiornare ed eliminare operazioni. Questa operazione utilizza interfacce C++ native.

Nelle **Opzioni di Schema** sono disponibili le opzioni seguenti:

- **Schema predefinito**. Il connettore individuato lo schema dal server Domino. Questo è l'opzione predefinita.
- **DSML Schema**. Utilizzato solo se il server Domino non esporre lo schema. È quindi possibile creare un file DSML con lo schema e importarlo invece. Per ulteriori informazioni sul DSML, vedere [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando si fa clic su Avanti, vengono verificati i parametri di configurazione ID utente e password.

### <a name="global-parameters"></a>Parametri globali
Nella pagina parametri globali configurare il fuso orario e l'importazione e opzione operazione di esportazione.  
![Parametri globali](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Il parametro **Domino Server fuso orario** definisce il percorso del Server Domino.

Questa opzione di configurazione è necessaria per supportare **l'importazione delta** perché consente la sincronizzazione servizio determinare le modifiche tra le ultime due importazioni.

#### <a name="import-settings-method"></a>Impostazioni di importazione, metodo
La **Eseguire importazione completa,** sono disponibili le opzioni:

- Ricerca
- Visualizzazione (scelta consigliata)

**Ricerca** utilizza l'indicizzazione nella Domino ma si tratta in genere che gli indici non vengono aggiornati in tempo reale e i dati restituiti dal server non sono sempre corretti. Per un sistema con molte modifiche, questa opzione in genere non funziona correttamente e fornisce FALSO vengono eliminate in alcune situazioni. Tuttavia, **la ricerca** è più rapido rispetto alla **visualizzazione**.

**Visualizzazione** è consigliabile poiché fornisce lo stato corretto dei dati. È leggermente più ricerca lenta.

#### <a name="creation-of-virtual-contact-objects"></a>Creazione di oggetti contatti virtuale
Il **abilitare la creazione di \_oggetto contatto** dispone di queste opzioni:

- Nessuno
- Valori non di riferimento
- Valori di riferimento e Non di riferimento

In Domino, gli attributi di riferimento possono contenere diversi formati per fare riferimento ad altri oggetti. Per essere in grado di rappresentare diverse variazioni, implements connettore \_contattare oggetti, noto anche come **Contatti virtuale** (VC). Questi oggetti vengono creati in modo che possono partecipare agli oggetti MV esistenti o previsto come nuovi oggetti. In questo modo, i riferimenti attributo possono essere salvati.

Attivare questa impostazione e se il contenuto di un attributo di riferimento non è un formato nome distinto un \_oggetto contatto viene creato. Ad esempio, un attributo membro di un gruppo può contenere gli indirizzi SMTP. È anche possibile avere shortName e altri attributi presenti negli attributi di riferimento. Per questo scenario, selezionare i **Valori Non di riferimento**. Questa configurazione è l'impostazione più comune per implementazioni Domino.

Quando Lotus Domino è configurato affinché rubriche distinta con diversi nomi distinti che rappresenta lo stesso oggetto, è possibile anche creare \_gli oggetti di contatto per tutti i valori di riferimento che si trovano in una Rubrica. Per questo scenario, selezionare l'opzione **riferimento e i valori Non di riferimento** .

Se si dispone di più valori nell'attributo **FullName** in Domino, quindi è consentire la creazione dei contatti virtuale in modo che i riferimenti possono essere risolti. Ad esempio, l'attributo può avere più valori dopo un matrimonio o divorzio. Selezionare la casella di controllo Abilita **... FullName dispone di più valori** per questo scenario.

Mediante l'unione degli attributi corretti, il \_oggetti contatto si aggiungono all'oggetto MV.

Questi oggetti sono VC =\_contatto aggiunto alla loro nome distinto.

#### <a name="import-settings-conflict-object"></a>Importare le impostazioni, sono in conflitto oggetto
**Escludere un conflitto oggetto**

In un'implementazione Domino di grandi dimensioni, è possibile che più oggetti hanno lo stesso nome distinto a causa di problemi di replica. In questi casi, il connettore vedrà due oggetti con diversi UniversalIDs ma stesso nome distinto. Il conflitto può provocare un oggetto temporaneo creato nell'area connettore. Il connettore è possibile ignorare gli oggetti selezionati in Domino come vittima di replica. Il suggerimento è mantenere questa casella di controllo selezionata.

#### <a name="export-settings"></a>Esportare le impostazioni
Se è selezionata l'opzione **Usa AdminP dell'aggiornamento dei riferimenti** , esportazione degli attributi di riferimento, ad esempio membro, è una chiamata diretta e non si utilizza il processo di AdminP. Utilizzare questa opzione solo quando AdminP non è stato configurato per mantenere l'integrità referenziale.

#### <a name="routing-information"></a>Informazioni di routing
In Domino, è possibile che un riferimento presenta informazioni sul routing incorporati come suffisso nome distinto. Ad esempio, potrebbe contenere l'attributo di membro di un gruppo **CN=example/organization@ABC**. Il suffisso @ABC informazioni routing. Informazioni sul routing usati Domino per inviare messaggi di posta elettronica per il sistema di Domino corretto, che può essere un sistema in un'altra organizzazione. Nella casella informazioni di Routing, è possibile specificare il routing dei suffissi utilizzato all'interno dell'organizzazione nell'ambito del connettore. Se uno dei seguenti valori viene rilevato come suffisso in un attributo di riferimento, le informazioni di routing viene rimosso dal riferimento. Se non è possibile associare il routing suffisso su un valore di riferimento a uno di questi valori specificati, un \_oggetto contatto viene creato. Questi \_contatto vengono creati con ** RO=@ ** inserito il nome distinto. Per questi \_oggetti contatto gli attributi seguenti possono essere aggiunti anche per consentire a partecipare a un oggetto reale, se necessario: \_routingName, \_contatto, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Altre rubriche
Se non si dispone di **Assistente scrittura in directory** installato, che fornisce il nome di rubriche secondario, è possibile immettere manualmente le liste di distribuzione.

#### <a name="multivalued-transformation"></a>Trasformazione multivalore
Molti attributi Lotus Domino sono multivalore. Gli attributi metaverse corrispondenti vengono in genere singolo valore. Se si configura l'importazione e l'opzione di operazione di esportazione, attivare il connettore agevolare la conversione richiesto degli attributi in questione.

**Esportazione**  
L'opzione di operazione di esportazione supporta due modalità:

- Aggiungere l'elemento
- Sostituire l'elemento

**Sostituisci elemento** : quando si seleziona questa opzione, il connettore sempre rimuovere i valori correnti dell'attributo in Domino e sostituirli con i valori specificati. L'oggetto fornito con valori possono essere include uno o più valori.

Esempio: L'attributo Assistente di un oggetto persona è i seguenti valori:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se un nuovo Assistente denominato **David Alessandro** viene assegnato a tale oggetto utente, il risultato è:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Aggiungere elementi** : quando si seleziona questa opzione, il connettore conserva i valori esistenti sull'attributo in Domino e inserire nuovi valori nella parte superiore dell'elenco dei dati.

Esempio: L'attributo Assistente di un oggetto persona è i seguenti valori:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se un nuovo Assistente denominato **David Alessandro** viene assegnato a tale oggetto utente, il risultato è:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importazione**  
L'opzione di operazione di importazione supporta due modalità:

- Impostazione predefinita
- Multivalore a valore singolo

**Predefinito** : quando si seleziona l'opzione predefinita, tutti i valori di tutti gli attributi vengono importati.

**Multivalued a valore singolo** : quando si seleziona questa opzione, un attributo multivalore viene convertito in un attributo multivalore. Se è presente più di un valore, viene utilizzato il valore nella parte superiore (questo valore è in genere anche le ultime).

Esempio: L'attributo Assistente di un oggetto persona è i seguenti valori:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Gli aggiornamenti più recenti per l'attributo sono **David Alessandro**. Perché l'opzione di operazione di importazione è impostato su Multivalued singolo valore, connettore Importa solo **David Alessandro** nello spazio connettore.

La logica per convertire gli attributi multivalore in attributi a valore singolo non è valida per l'attributo di membro del gruppo e per l'attributo fullname persona.

È inoltre possibile configurare importazione ed esportazione di regole di trasformazione per gli attributi multivalore per ogni attributo, come un'eccezione per la regola globale. Per configurare questa opzione, immettere [tipooggetto]. [attributename] nelle caselle di testo **elenco attributi esclusione di importare** ed **esportare elenco attributi di esclusione** . Ad esempio, se si immette Person.Assistant e il flag globale è impostato su importare tutti i valori, viene importato solo il primo valore dell'Assistente.

#### <a name="certifiers"></a>Rilascio degli attestati
Tutte le unità di organizzazione/organizzativo sono elencate per il connettore. Per esportare gli oggetti di persona alla rubrica principale, è necessario un certificatore con la relativa password.

Se tutti rilascio degli attestati abbia la stessa password, è possibile utilizzare la **Password per Certifers tutti** . È possibile immettere la password e specificare solo il file certificatore.

Se si importa solo, non è necessario specificare qualsiasi rilascio degli attestati.

### <a name="configure-provisioning-hierarchy"></a>Configurare gerarchia di Provisioning
Quando si configura il connettore Lotus Domino, ignorare questa pagina della finestra di dialogo. Il connettore Lotus Domino non supporta il provisioning di gerarchia.  
![Gerarchia di provisioning](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurare le partizioni e gerarchie
Quando si configura partizioni e gerarchie, è necessario selezionare la rubrica principale denominata NAB=names.nsf. Oltre la rubrica principale, è possibile selezionare rubriche secondario se presenti.  
![Partizioni](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selezionare gli attributi
Quando si configurano gli attributi, è necessario selezionare tutti gli attributi con prefisso ** \_MMS\_**. Questi attributi necessari durante il provisioning di nuovi oggetti a Lotus Domino

![Attributi](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestione del ciclo di vita degli oggetti
In questa sezione viene fornita una panoramica dei vari oggetti Domino.

### <a name="person-objects"></a>Oggetti utente
L'oggetto persona rappresenta gli utenti dell'organizzazione e le unità di organizzazione. Oltre agli attributi predefiniti, l'amministratore di Domino può aggiungere attributi personalizzati a un oggetto di persona. Almeno un oggetto persona deve includere tutti gli attributi obbligatori. Per un elenco completo degli attributi obbligatori, vedere [Lotus Notes proprietà](#lotus-notes-properties). Per registrare un oggetto utente, è necessario soddisfare i prerequisiti seguenti:

- La Rubrica (nsf) sono stata configurata e dovrebbe essere la rubrica principale.
- È necessario che il certificatore O/OU Id e la password per registrare un determinato utente nell'organizzazione / unità organizzativa.
- È necessario impostare un determinato set di proprietà di Lotus Notes per un oggetto di persona. Queste proprietà vengono utilizzate per il provisioning l'oggetto di persona. Per ulteriori informazioni, vedere la sezione denominata [Lotus Notes proprietà](#lotus-notes-properties) più avanti in questo documento.
- Password HTTP iniziale per una persona è un attributo e un set durante il provisioning.
- L'oggetto utente deve essere uno dei seguenti tre tipi supportati:
    1. Utente normale che include un file di posta elettronica e un file di id utente
    2. Un utente (un utente normale che include tutti i file di database comune) comune
    3. Contatti (utente con alcun file id)

Persone (ad eccezione dei contatti) possono essere raggruppate ulteriormente negli utenti di IT e internazionale come definito dal valore della \_MMS\_IDRegType proprietà. Questi utenti usare il Client di note per accedere ai server Lotus Domino, disporre di un Id di note e un documento di persona. Se utilizzano stampa di note, quindi dispongono anche un file di stampa. L'utente deve essere registrata per diventare attivo. Per ulteriori informazioni, vedere:

- [Configurare gli utenti di note](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Registrazione utente](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gestione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Ridenominazione di utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Tutte queste operazioni vengono eseguite in Lotus Domino e quindi importarli il servizio di sincronizzazione.

### <a name="resources-and-rooms"></a>Sale e risorse
Una risorsa è un altro tipo di un database di Lotus Domino. Risorse possono essere delle sale riunioni con diversi tipi di apparecchiature, ad esempio proiettori. Sono disponibili i sottotipi di risorse è supportate da Lotus Domino connettore definiti per l'attributo tipo risorsa:

Tipo di risorsa | Attributo tipo risorsa
--- | ---
Chat room | 1
Risorsa (altro) | 2
Riunione online | 3

Per il tipo di oggetto delle risorse per l'uso, è necessario:

- Database di prenotazione delle risorse dovrà già esistenti nel server Domino connesso
- Il sito è già definito per la risorsa

Il database di prenotazione risorse contiene tre tipi di documenti:

- Profilo sito
- Risorsa
- Caratteristica di prenotazione

Per ulteriori informazioni sull'impostazione del database di prenotazione delle risorse, vedere [impostazione di database prenotazioni di risorse](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Creazione, aggiornamento ed eliminazione di risorse**  
Le operazioni di creazione, aggiornamento ed eliminazione vengono eseguite tramite il connettore Lotus Domino nel database di prenotazione delle risorse. Risorse vengono create come documenti nel NSF (vale a dire il principale Rubrica). Per ulteriori informazioni sulla modifica e l'eliminazione di risorse, vedere [Modifica ed eliminazione documenti della risorsa](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importazione / esportazione per le risorse**  
Le risorse possono essere importate a ed esportate dal servizio di sincronizzazione, come qualsiasi altro tipo di oggetto. Selezionare il tipo di oggetto come risorsa durante la configurazione. Per operazione di esportazione ha esito positivo, è necessario disporre di dettagli del tipo di risorsa, Database conferenza e il nome del sito.

### <a name="mail-in-databases"></a>Database della posta In arrivo
Un Database di posta elettronica è un database che consente di ricevere messaggi. Si tratta di una cassetta postale di Lotus Domino che non è associata a qualsiasi account utente di Lotus Domino specifico (ovvero, non avere propri file ID e la password). Un database di posta elettronica ha un univoco ID utente ("nome breve") associato con il proprio indirizzo di posta elettronica.

Se è necessaria una cassetta postale distinta con il proprio indirizzo di posta elettronica che può essere condivisi tra più utenti (ad esempio group@contoso.com), viene creato un database di posta elettronica. Accedere alla cassetta postale verrà controllate tramite il controllo elenco di accesso, che contiene i nomi degli utenti note consentiti per aprire la cassetta postale.

Per un elenco degli attributi necessari, vedere la sezione intitolata [Attributi obbligatori](#mandatory-attributes) più avanti in questo articolo.

Quando un database è progettato per ricevere un messaggio di posta, viene creato un documento di Database di posta elettronica in Lotus Domino. In questo documento deve trovarsi nella Directory di Domino di tutti i server che memorizza una copia del database. Per informazioni dettagliate sulla creazione di un documento di database di posta elettronica, vedere [creazione di un documento di Database di posta elettronica](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Prima di creare un Database di posta elettronica, il database deve esistere (dovrebbe creati dall'amministratore di Lotus) nel server Domino.

### <a name="group-management"></a>Gestione dei gruppi
È possibile ottenere una panoramica dettagliata della gestione di gruppo Lotus Domino dalle risorse seguenti:

- [Uso dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Creazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Creazione e la modifica di gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gestione dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Ridenominazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestione delle password
Per un utente di Lotus Domino registrato, esistono due tipi di password:

1. Password dell'utente (archiviato nel file User.id)
2. Internet / password HTTP

Il connettore Lotus Domino supporta solo operazioni con password HTTP.

Per eseguire la gestione delle password, è necessario attivare la gestione delle password per il connettore nella finestra di progettazione di agente di gestione. Per attivare la gestione delle password, selezionare **Attiva la gestione delle password** nella pagina **Configura estensioni del** finestra di dialogo.  
![Configurare le estensioni](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Supporto di connettore Lotus Domino seguendo operazioni su password Internet:

- Imposta Password: Impostare password consente di impostare una nuova password HTTP/Internet all'utente di Domino. Per impostazione predefinita è sbloccata anche l'account. Il contrassegno Sblocca viene esposta nell'interfaccia WMI del motore di sincronizzazione.
- Modifica della Password: In questo scenario, un utente desidera cambiare la password o verrà chiesto di cambiare la password dopo un periodo di tempo specificato. Per eseguire l'operazione posizionare, sia la vecchia e la nuova password sono obbligatori. Una volta modificata, la nuova password viene aggiornata in Lotus Domino.

Per ulteriori informazioni, vedere:

- [Tramite la funzionalità di blocco di Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gestione delle password Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informazioni di riferimento
In questa sezione sono elencati, ad esempio attributo requisiti per il connettore Lotus Domino e le descrizioni degli attributi.

### <a name="lotus-notes-properties"></a>Proprietà Lotus Notes
Quando viene effettuato il provisioning di oggetti utente nella directory di Lotus Domino, gli oggetti devono avere un determinato set di proprietà con valori specifici popolati. Questi valori sono necessarie solo per creare le operazioni.

Nella tabella seguente vengono elencate le proprietà e viene fornita una descrizione di esse.

Proprietà | Descrizione
--- | ---
\_MMS_AltFullName | Nome completo alternativo dell'utente.
\_MMS_AltFullNameLanguage | La lingua da utilizzare per specificare il nome completo alternativo dell'utente.
\_MMS_CertDaysToExpire | Il numero di giorni che vanno dalla data corrente del certificato scade. Se non viene specificato, la data predefinita è due anni dalla data corrente.
\_MMS_Certifier | Proprietà che contiene il nome della gerarchia dell'organizzazione del certificatore. Ad esempio: OU = OrganizationUnit, O = Org, C = paese.
\_MMS_IDPath | Se la proprietà è vuota, viene creato in locale alcun file di identificazione utente nel Server di sincronizzazione. Se la proprietà contiene il nome del file, viene creato un file di ID utente nella cartella madata. La proprietà può inoltre contenere un percorso completo.
\_MMS_IDRegType | Le persone possono essere suddivise in contatti, Aiutaci a utenti e gli utenti internazionali. Nella tabella seguente elenca i valori possibili: <li>0 - contatto</li><li>1 - utente US</li><li>2 - utente internazionale</li>
\_MMS_IDStoreType | Proprietà obbligatoria per gli Stati Uniti e gli utenti internazionali. La proprietà contiene un valore integer che specifica se l'ID utente viene archiviata come allegato nella Rubrica note o nel file di posta elettronica della persona. Se il file ID utente è un allegato nella Rubrica, è possibile facoltativamente creato come file con \_MMS_IDPath. <li>Svuotare - file di archivio ID nell'archivio di ID, senza file di identificazione (utilizzato per i contatti).</li><li> 1 - allegato nella Rubrica note. Il \_MMS_Password deve essere impostata per i file di identificazione utente degli allegati</li><li>2 - memorizzare ID nel File di posta elettronica della persona. Il \_MMS_UseAdminP deve essere impostata su false per consentire il file di posta elettronica creato durante la registrazione di persona. Il \_MMS_Password deve essere impostata per i file di identificazione utente.</li>
\_MMS_MailQuotaSizeLimit | Il numero di megabyte consentiti per il database di file di posta elettronica.
\_MMS_MailQuotaWarningThreshold | Il numero di megabyte consentiti per il database di file di posta elettronica prima di un avviso.
\_MMS_MailTemplateName | Il file di modello di posta elettronica che viene utilizzato per creare file di posta elettronica dell'utente. Se viene specificato un modello, viene creato il file di stampa utilizzando il modello specificato. Se non viene specificato alcun modello, il file del modello predefinito viene utilizzato per creare il file.
\_MMS_OU | Proprietà facoltativa che rappresenta il nome dell'unità Organizzativa sotto il certificatore. Questa proprietà deve essere vuota per i contatti.
\_MMS_Password | Proprietà obbligatoria per gli utenti. La proprietà contiene la password per il file di identificazione dell'oggetto.
\_MMS_UseAdminP | Proprietà deve essere impostata su true se il file di posta elettronica deve essere creato dal processo di AdminP sul server Domino (asincrono per il processo di esportazione). Se è impostata su false, il file di posta elettronica viene creato con l'utente di Domino (icona nel processo di esportazione).

Per un utente con un file di identificazione associato, il \_MMS_Password proprietà deve contenere un valore. Per l'accesso a posta elettronica tramite il client di Lotus Notes, le proprietà serverposta e corso di un utente devono contenere un valore.

Per accedere alla posta elettronica tramite un Web browser, le proprietà seguenti devono contenere valori:

- Corso - proprietà richiesta che contiene il percorso sul server Lotus Domino in cui è archiviato il file della posta.
- Serverposta - proprietà richiesta che contiene il nome del server Lotus Domino. Questo valore è il nome da utilizzare durante la creazione del file di Lotus posta nel server di Domino.
- HTTPPassword - proprietà facoltativa che contiene la password di accesso Web per l'oggetto.

Per accedere al Server di Domino senza la funzionalità di posta elettronica, la proprietà HTTPPassword deve contenere un valore. Proprietà corso e serverposta può essere vuoti.

Con \_MMS_ IDStoreType = 2 (id store nel file di posta elettronica), la proprietà MailSystem di NotesRegistrationclass è impostata su REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Attributi obbligatori
Il connettore Lotus Domino supporta principalmente questi tipi di oggetti (tipi di documento):

- Gruppo
- Database di posta elettronica
- Persona
- Contatto con alcun certificatore
- Risorsa

In questa sezione sono elencati gli attributi che sono obbligatori per ogni oggetto supportati esportare in un server Domino.

Tipo di oggetto | Attributi obbligatori
--- | ---
Gruppo | <li>Nomeelenco</li>
Database di principali | <li>FullName</li><li>Corso</li><li>Serverposta</li><li>MailDomain</li>
Persona | <li>Cognome</li><li>Corso</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contatto con alcun certificatore | <li>\_MMS_IDRegType</li>
Risorsa | <li>FullName</li><li>Tipo di risorsa</li><li>ConfDB</li><li>ResourceCapacity</li><li>Sito</li><li>DisplayName</li><li>Corso</li><li>Serverposta</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Problemi e domande comuni

### <a name="schema-detection-does-not-work"></a>Rilevamento di schema non funziona
Per essere in grado di rilevare lo schema, è necessario che il file schema.nsf sia presenta nel server di Domino. Il file viene visualizzata solo se LDAP è installato nel server. Se lo schema non è rilevabile, verificare quanto segue:

- Schema.nsf file è presente nella cartella radice del domino
- L'utente è autorizzato a visualizzare il file schema.nsf.
- Forzare il riavvio del server LDAP. Aprire **Lotus Domino Console** e scegliere **Informare ReloadSchema LDAP** per ricaricare lo schema.

### <a name="not-all-secondary-address-books-are-visible"></a>Non tutte le rubriche secondario sono visibili
Il connettore Domino si basa su funzionalità **Assistente scrittura in Directory** in grado di trovare le rubriche secondario. Se le rubriche secondario non è presente, verificare se [Assistenza Directory](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) è stato abilitato e configurato nel Server di Domino.

### <a name="custom-attributes-in-domino"></a>Attributi personalizzati nel Domino
Esistono diversi modi in Domino estendere lo schema viene visualizzato come un attributo personalizzato consumo dal connettore.

**Metodo 1: Estendere lo schema di Lotus Domino**

1. Creare una copia del modello di Directory Domino {PUBNAMES. NTF} da seguente [procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (non personalizzare la directory di IBM Lotus Domino predefinita modello):
2. Aprire il modello di directory copia del Domino {CONTOSO. Modello NTF} creato nella finestra di progettazione di Domino e segue la procedura seguente:
    - Fare clic su elementi condivisi ed espandere sottomaschere
    - Fare doppio clic su sottomaschera InheritableSchema ${nome oggetto} (dove {nome oggetto} è il nome della classe oggetto strutturale predefinito, ad esempio: persona).
    - Nome attributo che si desidera aggiungere in schema {MyPersonAtrribute} e corrispondente a tale attributo. Creare un campo per selezionare il Menu **Crea** e quindi selezionare **campi** dal menu.
    - Nel campo aggiunto impostarne le proprietà selezionando il tipo, stile, dimensioni, tipo di carattere e gli altri parametri correlati nella finestra Proprietà campo.
    - Mantenere l'attributo stesso valore predefinito come il nome specificato per l'attributo (ad esempio, se nome attributo MyPersonAttribute, mantenere il valore predefinito con lo stesso nome).
    - Salvare la sottomaschera InheritableSchema ${nome oggetto} con valori aggiornati.
3. Sostituire il modello di Directory Domino {PUBNAMES. NTF} con il nuovo modello personalizzato {CONTOSO. NTF} dai seguenti [la procedura seguente](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Chiudere Domino amministratore e aprire Console Domino a riavviare il servizio LDAP e per ricaricare lo Schema LDAP:
    - Nella Console Domino, inserire il comando sotto il testo del **Comando Domino** archiviato per riavviare il servizio LDAP - [Riavviare LDAP attività]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Per ricaricare LDAP schema comando informare LDAP - stabilire ReloadSchema LDAP
5. Amministratore di Domino aperto e scheda Seleziona utenti e gruppi per visualizzare l'attributo aggiunto viene visualizzata nel domino aggiungere persona.
6. Aprire Schema.nsf dalla scheda **file** e sarà possibile attributo aggiunto in classe di oggetti LDAP dominoPerson verificare.

**Metodo 2: Creare un auxClass con attributi personalizzati e associare la classe di oggetti**

1. Creare una copia del modello di Directory Domino {PUBNAMES. NTF} da seguente [procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (non personalizzare mai nella directory di IBM Lotus Domino modello predefinito):
2. Aprire il modello di directory copia del Domino {CONTOSO. Modello NTF} creato, nella finestra di progettazione di Domino.
3. Nel riquadro sinistro selezionare codice condiviso e quindi sottomaschere.
4. Fare clic su nuova sottomaschera
5. Eseguire le operazioni seguenti per specificare le proprietà per la nuova sottomaschera:
    - Con la nuova sottomaschera aprire, scegliere Struttura - proprietà sottomaschera
    - Accanto a proprietà Name, immettere un nome per la classe oggetto supplementare, ad esempio TestSubform.
    - Mantenere la proprietà Opzioni "Includere nella sottomaschera Inserisci … finestra di dialogo" selezionato
    - Deselezionare le opzioni proprietà "passaggio di rendering tramite HTML nelle note."
    - Lasciare la stessa altre proprietà e chiudere la finestra di dialogo Proprietà sottomaschera.
    - Salvare e chiudere la sottomaschera nuova.
6. Eseguire le operazioni seguenti per aggiungere un campo per definire la classe oggetto supplementare:
    - Aprire la sottomaschera che è stato creato.
    - Scegliere Crea - campo.
    - Accanto al nome nella scheda Generale della finestra di dialogo campo, specificare un nome, ad esempio: {MyPersonTestAttribute}.
    - Nel campo aggiunto impostarne le proprietà selezionando tipo, stile, dimensioni, tipo di carattere e le proprietà correlate.
    - Mantenere l'attributo stesso valore predefinito come il nome specificato per l'attributo (ad esempio, se nome attributo MyPersonTestAttribute, mantenere il valore predefinito con lo stesso nome).
    - Salvare la sottomaschera con valori aggiornati ed eseguire le operazioni seguenti:
        - Nel riquadro sinistro selezionare codice condiviso e quindi sottomaschere
        - Selezionare nuova sottomaschera e scegliere Struttura, le proprietà della struttura.
        - Fare clic sulla scheda terza da sinistra e selezionare **Modifica propagare tale divieto di progettazione**.
7. Aprire ${nome oggetto} ExtensibleSchema sottomaschera, (dove {nome oggetto} è il nome della classe oggetto strutturale predefinita, ad esempio – persona).
8. Inserire una risorsa e selezionare la sottomaschera (che è stato creato, ad esempio – TestSubform) e salvare la sottomaschera ExtensibleSchema ${nome oggetto}.
9. Sostituire il modello di Directory Domino {PUBNAMES. NTF} con il nuovo modello personalizzato {CONTOSO. NTF} dai seguenti [la procedura seguente](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Chiudere Domino amministratore e aprire Console Domino a riavviare il servizio LDAP e per ricaricare lo Schema LDAP:
    - Nella Console Domino, inserire il comando sotto il testo del **Comando Domino** archiviato per riavviare il servizio LDAP - [Riavviare LDAP attività](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Per ricaricare LDAP schema comando LDAP indicare a **Indicare ReloadSchema LDAP**.
11. Aprire Domino amministratore e selezionare la scheda utenti e gruppi per visualizzare l'attributo aggiunto viene visualizzata nel domino Aggiungi utente (in altre schede).
12. Aprire Schema.nsf dalla scheda **file** e sarà possibile attributo aggiunto in classe oggetto TestSubform LDAP supplementare verificare.

**Metodo 3: Aggiungere l'attributo personalizzato per la classe ExtensibleObject**

1. Apri il file {Schema.nsf} posizionato nella directory principale
2. Selezionare le classi di oggetti LDAP dal menu a sinistra in **Tutti i documenti di Schema** e fare clic sul pulsante **Aggiungi oggetto classe** :
3. Specificare il nome LDAP sotto forma di {zzzExtensibleSchema} (dove zzz è il nome della classe oggetto strutturale predefinita, ad esempio persona). Ad esempio, per estendere lo schema per persona oggetto classe, fornire nome LDAP {PersonExtensibleSchema}.
4. Specificare il nome di classe dell'oggetto superiore, per cui si vuole estendere lo schema. Ad esempio, per estendere lo schema di classe oggetto utente, specificare il nome di classe di livello superiore dell'oggetto {dominoPerson}:
5. Fornire un OID valido corrispondente alla classe di oggetti.
6. Selezionare gli attributi estese/personalizzati in campi obbligatori o tipi di attributo facoltativo le esigenze:
7. Dopo aver aggiunto gli attributi necessari per la ExtensibleObjectClass, fare clic su **Salva e Chiudi**.
8. Viene creato un ExtensibleObjectClass per la classe di oggetti predefiniti rispettivi con attributi estesi.

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per informazioni su come attivare la registrazione risolvere i problemi di connessione, informazioni su [come abilitare ETW Tracing per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).
