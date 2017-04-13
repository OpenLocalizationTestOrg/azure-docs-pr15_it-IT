<properties
   pageTitle="Connettore LDAP generico | Microsoft Azure"
   description="In questo articolo viene descritto come configurare generico LDAP connettore Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Riferimento tecnico connettore LDAP generico
Questo articolo descrive il connettore LDAP generico. L'articolo si applica ai seguenti prodotti:

- Gestione identità Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario utilizzare correzione 4.1.3671.0 o versioni successive [KB3092178](https://support.microsoft.com/kb/3092178).

Per MIM2016 e FIM2010R2, il connettore è disponibile per il download dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Quando si fa riferimento a RFC IETF, il documento è utilizzando il formato (RFC [numero RFC] / [sezione nel documento]), ad esempio (RFC 4512/4.3).
Ulteriori informazioni in http://tools.ietf.org/html/rfc4500 (è necessario sostituire 4500 con corretto).

## <a name="overview-of-the-generic-ldap-connector"></a>Panoramica del connettore LDAP generico
Il connettore LDAP generico consente di integrare il servizio di sincronizzazione con server LDAP v3.

Alcune operazioni e gli elementi dello schema, ad esempio quelli necessari per eseguire l'importazione delta, non vengono specificati in RFC IETF. Per queste operazioni, sono supportate sola directory LDAP specificata in modo esplicito.

Dal punto di vista di alto livello, le caratteristiche seguenti sono supportate per la versione corrente del connettore:

Caratteristica | Supporto tecnico
--- | --- |
Origine dati connessa | Il connettore è supportato con tutti i server di v3 LDAP (conforme RFC 4510). Sono stati testato con le operazioni seguenti: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Microsoft Active Directory globale catalogo Active Directory</li><li>Server directory 389</li><li>Server di Directory Apache</li><li>IBM Tivoli DS</li><li>Directory Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Dispositivo aperto</li><li>Apri DS</li><li>Apri LDAP (openldap.org)</li><li>Oracle (in precedenza DOM) Directory Server Enterprise Edition</li><li>Server di Directory virtuale RadiantOne (VDS)</li><li>Server di DOM una Directory</li>**Directory degni di nota non supportate:** <li>Microsoft Active Directory servizi di dominio (AD DS) [utilizzare invece Active Directory Connector predefiniti]</li><li>Directory Internet Oracle (OID)</li>
Scenari   | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione dei gruppi</li><li>Gestione delle password</li>
Operazioni |Per tutte le directory LDAP sono supportate le operazioni seguenti: <li>Importazione completa</li><li>Esportazione</li>Nella directory specificate sono supportate solo le operazioni seguenti:<li>Importazione delta</li><li>Impostare la Password, cambiare la Password</li>
Schema | <li>Schema viene rilevato dallo schema LDAP (RFC3673 e RFC4512/4.2)</li><li>Supporta classi strutturale, aux e classe di oggetti extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Supporto di gestione di importazione e la password delta
Directory supportate per l'importazione Delta e la gestione delle Password:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta imposta Password
- Microsoft Active Directory globale catalogo Active Directory
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta imposta Password
- Server directory 389
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Server di Directory Apache
    - Non supporta l'importazione delta perché questo directory non dispone di un file di log di modifica permanente
    - Supporta imposta Password
- IBM Tivoli DS
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Directory Isode
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Novell eDirectory e NetIQ eDirectory
    - Supporta le operazioni di aggiunta, aggiornamento ed Rinomina per l'importazione delta
    - Non supporta le operazioni di eliminazione per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Dispositivo aperto
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Apri DS
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Apri LDAP (openldap.org)
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta imposta Password
    - Non supporta Cambia password
- Oracle (in precedenza DOM) Directory Server Enterprise Edition
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
- Server di Directory virtuale RadiantOne (VDS)
    - È necessario utilizzare versione 7.1.1 o versioni successive
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password
-  Server di DOM una Directory
    - Supporta tutte le operazioni per l'importazione delta
    - Supporto di impostazione e modifica la Password

### <a name="prerequisites"></a>Prerequisiti
Prima di utilizzare il connettore, accertarsi di avere le operazioni seguenti per il server di sincronizzazione:

- 4.5.2 Microsoft .NET Framework o versioni successive

### <a name="detecting-the-ldap-server"></a>Rilevare il server LDAP
Il connettore si basa sulle varie tecniche per rilevare e identificare il server LDAP. Il connettore utilizza la radice DSE, fornitore nome/versione e esamina lo schema per trovare univoci oggetti e attributi noti presenti in alcuni server LDAP. Questi dati, se disponibile, viene utilizzato per popolare le opzioni di configurazione del connettore.

### <a name="connected-data-source-permissions"></a>Autorizzazioni di origine dati connessione
Per importare ed esportare le operazioni sugli oggetti directory connessa, l'account di connettore deve disporre di autorizzazioni sufficienti. Il connettore deve scrivere le autorizzazioni per essere in grado di esportare e autorizzazioni di lettura per poter importare. Configurazione di autorizzazione viene eseguita all'interno di esperienze gestione della directory di destinazione se stesso.

### <a name="ports-and-protocols"></a>Porte e protocolli
Il connettore utilizza il numero di porta specificato nella configurazione, che per impostazione predefinita è 389 per LDAP e 636 per LDAPS.

Per LDAPS, è necessario utilizzare SSL 3.0 o TLS. SSL 2.0 non supportati e non può essere attivato.

### <a name="required-controls-and-features"></a>Caratteristiche e i controlli obbligatori
I seguenti controlli LDAP/funzionalità devono essere disponibili sul server LDAP per il connettore per il corretto funzionamento:  
`1.3.6.1.4.1.4203.1.5.3`Filtri vero/falso

Il filtro vero/falso spesso non viene segnalato come supportato da directory LDAP e potrebbe essere incluso nella **Pagina globale** in **Obbligatorio caratteristiche non trovato**. Viene utilizzato per creare **o** filtri nelle query LDAP, ad esempio quando si importano più tipi di oggetto. Se è possibile importare più di un tipo di oggetto, il server LDAP supporta questa funzionalità.

Se si utilizza una directory in cui un identificatore univoco è quello di ancoraggio seguenti devono essere disponibili (vedere la sezione [Configurare gli ancoraggi](#configure-anchors) più avanti in questo articolo per ulteriori informazioni):  
`1.3.6.1.4.1.4203.1.5.1`Tutti gli attributi operativi

Se la directory ha più oggetti quelli che possono essere visualizzati in una chiamata alla directory, è consigliabile usare suddivisione in pagine. Per lo spostamento per l'uso, è necessario una delle opzioni seguenti:

**Opzione 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Opzione 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Se entrambe le opzioni sono attivate nella configurazione del connettore, viene utilizzato pagedResultsControl.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl viene utilizzato solo con la modalità di importazione delta USNChanged in grado di visualizzare gli oggetti eliminati.

Il connettore tenta di rilevare le opzioni presente nel server. Se non è possibile rilevare le opzioni, un messaggio di avviso è presenta nella pagina globale proprietà del connettore. Non tutti i server LDAP presenta tutti i controlli/caratteristiche che supportano e anche se il problema è presenta, il connettore potrebbe funzionare senza problemi.

### <a name="delta-import"></a>Importazione delta
Importazione delta è disponibile solo quando è stata rilevata una directory di supporto. Attualmente vengono utilizzati i metodi seguenti:

- Accesslog LDAP. Vedere [http://www.openldap.org/doc/admin24/overlays.html#Access registrazione](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Changelog LDAP. Vedere [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- TimeStamp. Per eDirectory Novell/NetIQ, il connettore utilizza la data/ora ultima per ottenere creato e aggiornato oggetti. Novell/NetIQ eDirectory non fornisce un equivalente indica che recuperare gli oggetti eliminati. Questa opzione può essere utilizzata anche se nessun altro metodo di importazione delta è attivo il server LDAP. Questa opzione non è possibile importare eliminati oggetti.
- USNChanged. Vedere: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Non è supportata
Le caratteristiche LDAP seguenti non sono supportate:

- Riferimenti LDAP tra server (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
Per creare un connettore LDAP generico, selezionare **Agente di gestione** e **creare**nel **Servizio di sincronizzazione** . Selezionare il connettore **generico LDAP (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Connettività
Nella pagina connettività è necessario specificare le informazioni Host, porta e associazione. A seconda che associazione è selezionata, altre informazioni potrebbero essere fornite nelle sezioni seguenti.

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- L'impostazione di timeout della connessione viene usato solo per la prima connessione al server per rilevare lo schema.
- Se associazione è anonimo, quindi nessuno nomeutente / password né certificato utilizzati.
- Altre associazioni immettere informazioni per uno dei due in nomeutente / password o selezionare un certificato.
- Se si usa Kerberos per eseguire l'autenticazione, è necessario fornire l'area di autenticazione/dominio dell'utente.

Casella di testo **alias attributo** viene usata per gli attributi definiti nello schema di sintassi RFC4522. Questi attributi non vengono rilevati durante il rilevamento di schema e il connettore deve avere informazioni per identificare gli attributi. Ad esempio per immettere nella casella Alias attributo per identificare correttamente l'attributo userCertificate come attributo binario sono necessario le operazioni seguenti:

`userCertificate;binary`

Di seguito è illustrato un esempio di come questa configurazione potrebbe aspetto:

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Selezionare la casella di controllo **includere attributi operativi nello schema** di includere anche gli attributi creati dal server. Sono inclusi gli attributi, ad esempio quando l'oggetto è stato creato e ultimo aggiornamento.

Selezionare **Includi extensible attributi dello schema** per extensible (RFC4512/4.3) vengono utilizzati e abilitare questa opzione consente di tutti gli attributi da utilizzare per tutti gli oggetti. Questa opzione, lo schema di dimensioni molto grandi in modo a meno che la directory connessa questa caratteristica è il suggerimento per mantenere l'opzione deselezionata.

### <a name="global-parameters"></a>Parametri globali
Nella pagina parametri globali si configura il nome distinto al log delle modifiche delta e caratteristiche aggiuntive di LDAP. La pagina è già presenti le informazioni fornite dal server LDAP.

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

Nella sezione superiore Visualizza le informazioni fornite dal server, ad esempio il nome del server. Il connettore verifica inoltre che partecipano a principale DSE i controlli obbligatori. Se non sono elencati questi controlli, verrà visualizzata un messaggio di avviso. Alcune directory LDAP non includere nell'elenco tutte le caratteristiche in principale DSE ed è possibile che il connettore funziona senza problemi, anche se è presenta un messaggio di avviso.

Le caselle di controllo **supportati controlli** controllare il comportamento di alcune operazioni:

- Con il comando Elimina albero selezionata, viene eliminata una gerarchia con una chiamata LDAP. Con il comando Elimina albero deselezionato, il connettore non archivio se necessario.
- Con i risultati per pagina selezionati, il connettore non un'importazione per pagina con la dimensione specificata nella procedura Esegui.
- La VLVControl e SortControl è un'alternativa alla pagedResultsControl per leggere i dati dalla directory LDAP.
- Se le tre opzioni (pagedResultsControl, VLVControl e SortControl) siano deselezionate il connettore Importa tutti gli oggetti in un'unica operazione potrebbe non riuscire se si tratta di una directory di grandi dimensioni.
- ShowDeletedControl viene utilizzato solo quando il metodo di importazione Delta è USNChanged.

Il log modifiche nome distinto è il contesto di denominazione utilizzato per il log delle modifiche delta, ad esempio **cn = changelog**. Per poter importare dati delta, è necessario specificare il valore.

Di seguito è un elenco di log delle modifiche predefinito DNs:

Directory | Log delle modifiche delta
--- | ---
Catalogo globale Microsoft AD LDS e Active Directory | Rilevati automaticamente. USNChanged.
Server di Directory Apache | Non è disponibile.
Directory 389 | Log delle modifiche. Valore da utilizzare predefinito: **cn = changelog**
IBM Tivoli DS | Log delle modifiche. Valore da utilizzare predefinito: **cn = changelog**
Directory Isode | Log delle modifiche. Valore da utilizzare predefinito: **cn = changelog**
Novell/NetIQ eDirectory | Non è disponibile. TimeStamp. Viene utilizzato il connettore ultimo aggiornamento data/ora per ottenere aggiunto e aggiornare i record.
Apri dispositivo/DS | Log delle modifiche.  Valore da utilizzare predefinito: **cn = changelog**
Apri LDAP | Log di accesso. Valore da utilizzare predefinito: **cn = accesslog**
Oracle DSEE | Log delle modifiche. Valore da utilizzare predefinito: **cn = changelog**
RadiantOne VDS | Directory virtuale. Dipende dalla directory connessi VDS.
Server di DOM una Directory | Log delle modifiche. Valore da utilizzare predefinito: **cn = changelog**

L'attributo password corrisponde al nome dell'attributo che il connettore deve seguire per impostare la password di modifica della password e le operazioni di impostazione di password.
Questo valore è impostato per **userPassword** per impostazione predefinita, ma può essere modificato quando necessario per un determinato sistema LDAP.

Nell'elenco delle partizioni aggiuntive, è possibile aggiungere ulteriori spazi dei nomi non automaticamente rilevato. Ad esempio, questa impostazione può essere utilizzata se diversi server costituiscono un cluster logico che deve essere importato nello stesso momento. Come in un insieme di strutture Active Directory possono essere presenti più domini ma tutti i domini condividono uno schema, è possibile simulare la stessa immettendo ulteriori spazi dei nomi in questa casella. Ogni spazio dei nomi può importare da server diversi e ulteriormente configurata nella pagina Configura partizioni e gerarchie. Usare Ctrl + Invio per iniziare una nuova riga.

### <a name="configure-provisioning-hierarchy"></a>Configurare gerarchia di Provisioning
Questa pagina viene utilizzata per eseguire il mapping al componente di nome distinto, ad esempio OU per il tipo di oggetto che deve essere completato il provisioning, ad esempio unità organizzativa.

![Gerarchia di provisioning](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Mediante la configurazione di provisioning gerarchia, è possibile configurare il connettore per creare automaticamente una struttura quando necessario. Ad esempio, se è presente un controller di dominio dello spazio dei nomi = contoso, Cc = com e un nuovo oggetto cn = Fausto, ou = Seattle, c = US, controller di dominio = contoso, controller di dominio = viene completato il provisioning com, quindi il connettore è possibile creare un oggetto del paese di tipo per Stati Uniti e un'unità organizzativa per Seattle se non sono già presenti nella directory.

### <a name="configure-partitions-and-hierarchies"></a>Configurare le partizioni e gerarchie
Nella pagina partizioni e gerarchie, selezionare tutti gli spazi dei nomi degli oggetti che si prevede di importare ed esportare.

![Partizioni](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Per ogni spazio dei nomi, è anche possibile configurare le impostazioni di connettività che si sostituiscono i valori specificati nella schermata di integrazione applicativa. Se questi valori rimangono al valore vuoto predefinito, le informazioni nella schermata di integrazione applicativa vengono usate.

È anche possibile selezionare le unità organizzative il connettore deve importare ed esportare in e contenitori.

### <a name="configure-anchors"></a>Configurare gli ancoraggi
Questa pagina avere sempre un valore preconfigurato e non può essere modificata. Se è stato individuato il fornitore di server, l'ancoraggio potrebbe essere popolato con un attributo non modificabile, ad esempio il GUID per un oggetto. Se non è stato rilevato o noti non ha un attributo non modificabile, il connettore utilizza nome distinto (nome distinto) come ancoraggio.

![ancoraggi](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Di seguito è un elenco di server LDAP e ancoraggio utilizzato:

Directory | Attributo di ancoraggio
--- | ---
Catalogo globale Microsoft AD LDS e Active Directory | objectGUID
Server directory 389 | nome distinto
Directory Apache | nome distinto
IBM Tivoli DS | nome distinto
Directory Isode | nome distinto
Novell/NetIQ eDirectory | GUID
Apri dispositivo/DS | nome distinto
Apri LDAP | nome distinto
Oracle ODSEE | nome distinto
RadiantOne VDS | nome distinto
Server di DOM una Directory | nome distinto

## <a name="other-notes"></a>Altre note
In questa sezione vengono fornite informazioni di aspetti specifici per il connettore o per altri motivi è importante conoscere.

### <a name="delta-import"></a>Importazione delta
La filigrana delta in Apri LDAP è data/ora UTC. Per questo motivo, è necessario sincronizzare clock tra il servizio di sincronizzazione FIM e LDAP aperto. In caso contrario, alcune voci nel log delle modifiche delta potrebbero essere stati omessi.

Per Novell eDirectory, l'importazione delta è non rilevate le eliminazioni di oggetto. Per questo motivo, è necessario eseguire un'importazione completa periodicamente per trovare tutti gli oggetti eliminati.

Per le directory con un log delle modifiche delta basato su data/ora, altamente è consigliabile eseguire un'importazione completa momenti periodici. In questo modo il motore di sincronizzazione per trovare e dissimilarities tra il server LDAP e novità nello spazio connettore.

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per informazioni su come attivare la registrazione risolvere i problemi di connessione, informazioni su [come abilitare ETW Tracing per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).
