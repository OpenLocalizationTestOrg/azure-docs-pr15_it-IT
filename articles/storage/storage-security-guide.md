<properties
    pageTitle="Guida di sicurezza di spazio di archiviazione Azure | Microsoft Azure"
    description="Dettagli molti metodi di protezione di archiviazione di Azure, compresi esemplificativo RBAC, la crittografia del servizio di archiviazione, la crittografia lato Client, piccole e medie imprese 3.0 e la crittografia del Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guida di sicurezza di spazio di archiviazione Azure

##<a name="overview"></a>Panoramica

Spazio di archiviazione Azure fornisce una serie completa di funzionalità di sicurezza che insieme consentono agli sviluppatori di creare applicazioni protette. È possibile proteggere l'account di archiviazione tramite controllo dell'accesso basato sui ruoli e Azure Active Directory. Proteggere i dati in transito tra un'applicazione e Azure utilizzando [La crittografia lato Client](storage-client-side-encryption.md), HTTPS o 3.0 piccole e medie imprese. Dati impostare automaticamente la crittografia quando scritta allo spazio di archiviazione di Azure tramite [Lo spazio di archiviazione servizio di crittografia (SSE)](storage-service-encryption.md). Per crittografare utilizzando [La crittografia del Azure](../security/azure-security-disk-encryption.md), è possono impostare dischi del sistema operativo e dati utilizzati da macchine virtuali. Uso delle [Firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md), è possibile concedere l'accesso delegato per gli oggetti di dati in archiviazione Azure.

In questo articolo viene fornita una panoramica di ognuna di queste funzionalità di sicurezza che possono essere utilizzate con lo spazio di archiviazione di Azure. I collegamenti vengono forniti ad articoli che otterranno i dettagli di ogni caratteristica scopo è possibile eseguire facilmente ulteriori indagini su ogni argomento.

Di seguito sono argomenti da trattare in questo articolo:

-   [Protezione di piano di gestione](#management-plane-security) : consente di proteggere l'Account di archiviazione

    Il piano di gestione include le risorse utilizzate per gestire il proprio account di archiviazione. In questa sezione verrà illustrato il modello di distribuzione di gestione di risorse Azure e su come utilizzare controllo dell'accesso basato sui ruoli (RBAC) per controllare l'accesso agli account di archiviazione. Anche soffermarsi sulla gestione delle chiavi account lo spazio di archiviazione e come rigenerare loro.

-   [Dati il piano di sicurezza](#data-plane-security) : protezione dell'accesso ai dati

    In questa sezione vengono illustrate che consente l'accesso agli oggetti dati effettivi nel proprio account di archiviazione, ad esempio BLOB, file, code e tabelle, utilizzando le firme di Access condiviso e archiviati i criteri di accesso. Verranno illustrate le caratteristiche associazioni di protezione a livello di servizio e associazioni di protezione a livello di account. Vedremo anche come limitare l'accesso a uno specifico indirizzo IP (o intervallo di indirizzi IP), come limitare il protocollo utilizzato per HTTPS e come revocare una firma di Access condiviso senza attendere che la scadenza.

-   [Durante il transito crittografia](#encryption-in-transit)

    In questa sezione viene descritto come proteggere i dati quando si trasferisce o all'esterno di archiviazione Azure. Verrà illustrato l'utilizzo consigliato di HTTPS e la crittografia utilizzata da piccole e medie imprese 3.0 per condivisioni File Azure. Verrà inoltre osservare la crittografia lato Client, che consente di crittografare i dati prima di essere trasferiti in archiviazione nelle applicazioni client e decrittografare i dati dopo il trasferimento fuori dello spazio di archiviazione.

-   [Crittografia inattivi](#encryption-at-rest)

    Si parla lo spazio di archiviazione servizio di crittografia (SSE) e come è possibile abilitarla per un account di archiviazione, causando il BLOB di blocco BLOB di pagine e aggiungere BLOB da crittografare automaticamente quando scritta allo spazio di archiviazione Azure. Esamineremo anche come è possibile utilizzare la crittografia del Azure e informazioni sulle differenze di base e casi di crittografia disco e SSE e la crittografia del client. Esamineremo brevemente la conformità FIPS per i computer governo degli Stati Uniti.

-   Utilizzo [Dello spazio di archiviazione Analitica](#storage-analytics) per controllare l'accesso di spazio di archiviazione di Azure

    In questa sezione viene descritto come trovare informazioni nei log analitica lo spazio di archiviazione per una richiesta. È necessario dare un'occhiata analitica reale dello spazio di archiviazione dati del registro e scoprire come distinguere se viene richiesto con la chiave account lo spazio di archiviazione, con una firma di Access condiviso o in modalità anonima e che l'ha avuto esito positivo o negativo.

-   [Client basati su Browser con CORS per consentire](#Cross-Origin-Resource-Sharing-CORS)

    In questa sezione parla come consentire a croce origine condivisore (CORS). Vedremo domini accesso e la modalità di gestione con le funzioni CORS incorporate in archiviazione Azure.

##<a name="management-plane-security"></a>Protezione di piano di gestione

Il piano di gestione è costituito da operazioni che interessano l'account di archiviazione. Ad esempio, è possibile creare o eliminare un account di archiviazione, un elenco degli account di archiviazione in una sottoscrizione, recuperare le chiavi di account di archiviazione o rigenerare le chiavi di account di archiviazione.

Quando si crea un nuovo account di archiviazione, selezionare un modello di distribuzione di Classic o Manager delle risorse. Il modello classico di creazione di risorse in Azure consente solo l'accesso radicale all'abbonamento e di conseguenza, l'account di archiviazione.

Questa guida è incentrata su modello di gestione risorse è il modo consigliato per la creazione di account di archiviazione. Con l'account di archiviazione di Manager delle risorse, anziché concedere l'accesso a nell'intera sottoscrizione, è possibile controllare l'accesso a livello di più limitato al piano di gestione con controllo dell'accesso basato sui ruoli (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Come proteggere l'account di archiviazione con controllo dell'accesso basato sui ruoli (RBAC)

Parlare novità RBAC e come è possibile utilizzare. Ogni abbonamento Azure ha un Azure Active Directory. Utenti, gruppi e le applicazioni dalla directory possono concedere l'accesso per gestire le risorse nella sottoscrizione Azure che utilizzano il modello di distribuzione di Manager delle risorse. Questo processo viene definito controllo dell'accesso basato sui ruoli (RBAC). Per gestire l'accesso, è possibile usare il [portale di Azure](https://portal.azure.com/), gli [Strumenti di Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o [Azure lo spazio di archiviazione delle risorse Provider resto API](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Con il modello di Manager delle risorse, inserire l'account di archiviazione in delle risorse gruppo e controllare l'accesso al piano di gestione del proprio account di archiviazione specifici tramite Azure Active Directory. Ad esempio, è possibile consentire a utenti specifici la possibilità di accedere i tasti di account di archiviazione, mentre gli altri utenti possono visualizzare informazioni sugli account di archiviazione, ma non possono accedere i tasti di account di archiviazione.

####<a name="granting-access"></a>Concessione dell'accesso

Accesso tramite l'assegnazione di ruolo RBAC appropriata per gli utenti, gruppi e applicazioni, nell'ambito di destra. Per concedere l'accesso all'abbonamento intera, assegnare un ruolo a livello di sottoscrizione. È possibile concedere l'accesso a tutte le risorse in un gruppo di risorse mediante la concessione di autorizzazioni per il gruppo di risorse. È inoltre possibile assegnare ruoli specifici per le risorse specifiche, ad esempio gli account di archiviazione.

Ecco i punti principali che è necessario sapere sull'utilizzo RBAC per accedere alle operazioni di gestione di un account di archiviazione Azure:

-   Quando si assegna l'accesso, in cui sono assegnare un ruolo per l'account che si vuole avere accesso. È possibile controllare l'accesso a operazioni che consentono di gestire l'account di archiviazione, ma non a oggetti dati nella finestra account. Ad esempio, è possibile concedere l'autorizzazione per recuperare le proprietà dell'account di archiviazione (ad esempio ridondanza), ma non a un contenitore o dati in un contenitore all'interno di archiviazione Blob.

-   Per conto di qualcun disporre dell'autorizzazione per accedere agli oggetti dati nell'account di archiviazione, è possibile concedere l'autorizzazione per leggere i tasti di account di archiviazione e l'utente quindi possibile utilizzare questi tasti per accedere BLOB, code, tabelle e file.

-   Per un account utente specifico, un gruppo di utenti o per un'applicazione specifica, è possono assegnare i ruoli.

-   Ogni ruolo dispone di un elenco di azioni e non azioni. Ad esempio, il ruolo di collaboratore macchina virtuale contiene un'azione di "listKeys" consente di creare i tasti di account di archiviazione per la lettura. Il collaboratore è "Non azioni", ad esempio l'aggiornamento di accesso per gli utenti in Active Directory.

-   Ruoli per lo spazio di archiviazione includono (ma non sono limitati a) le operazioni seguenti:

    -   Proprietario – possono gestire tutti gli elementi, tra cui l'accesso.

    -   Collaboratori, che può eseguire alcuna il proprietario può tranne assegna l'accesso. Un utente con questo ruolo può visualizzarlo e rigenerare le chiavi di account di archiviazione. Con i tasti di account di archiviazione, possono accedere gli oggetti di dati.

    -   Utilità per la lettura – possono visualizzare informazioni sull'account di archiviazione, ad eccezione di informazioni riservate. Ad esempio, se si assegna un ruolo con le autorizzazioni di lettura per l'account di archiviazione a un utente, possono visualizzare le proprietà dell'account di archiviazione, ma non possono apportare le modifiche alle proprietà o visualizzare i tasti di account di archiviazione.

    -   Spazio di archiviazione per i collaboratori Account-possono gestire l'account di archiviazione, leggere i gruppi di risorse dell'abbonamento e le risorse e creare e gestire distribuzioni di gruppo di risorse abbonamento. Possono accedere anche le chiavi account lo spazio di archiviazione, che a sua volta indica che possono accedere al piano di dati.

    -   Amministratore di accesso utente – possono gestire l'accesso degli utenti per l'account di archiviazione. Ad esempio, concedono accesso in lettura a un utente specifico.

    -   Collaboratori macchina virtuale – possono gestire macchine virtuali ma non l'account di archiviazione a cui sono connessi. Questo ruolo possa elencati i tasti di account di archiviazione, il che significa che l'utente a cui assegnare il ruolo può eseguire l'aggiornamento del piano dati.

        Affinché un utente creare una macchina virtuale, hanno la possibilità di creare il file disco rigido virtuale corrispondente in un account di archiviazione. A tale scopo, è necessario essere in grado di recuperare la chiave account lo spazio di archiviazione e passare all'API di creazione della macchina virtuale. Pertanto, disporre questa autorizzazione in modo che riportano i tasti di account di archiviazione.

- La possibilità di definire ruoli personalizzati è una caratteristica che consente di comporre un insieme di azioni da un elenco delle azioni disponibili che possono essere eseguite su Azure risorse.

- L'utente deve essere configurato in Azure Active Directory prima di assegnare un ruolo ad essi.

- È possibile creare un report che ha concesso/revocato il tipo di accesso a/da chi e con quali ambito tramite PowerShell o CLI Azure.

####<a name="resources"></a>Risorse

-   [Controllo dell'accesso basato sui ruoli Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    In questo articolo viene illustrato il controllo dell'accesso basato sui ruoli di Azure Active Directory e sul suo funzionamento.

-   [RBAC: Integrato in ruoli](../active-directory/role-based-access-built-in-roles.md)

    In questo articolo viene descritta tutti i ruoli predefiniti disponibili in RBAC.

-   [Informazioni sulla distribuzione classica e Manager delle risorse](../resource-manager-deployment-model.md)

    In questo articolo illustra la distribuzione di Manager delle risorse e modelli di distribuzione classica e illustrati i vantaggi dell'uso dei gruppi di Manager delle risorse e delle risorse

-   [Calcolo Azure, rete e il provider di archiviazione in Gestione risorse Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    Questo articolo viene spiegato come calcolare Azure, la rete e i provider di archiviazione funzionano in base al modello di Manager delle risorse.

-   [Gestione di controllo dell'accesso basato sui ruoli con l'API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    In questo articolo viene illustrato come utilizzare l'API REST per gestire RBAC.

-   [Riferimento API REST del Provider di archiviazione Azure delle risorse](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Si tratta di riferimento per le API è possibile utilizzare per gestire il proprio account di archiviazione a livello di programmazione.

-   [Guida per gli sviluppatori per auth con API di gestione risorse di Azure](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    In questo articolo viene illustrato come eseguire l'autenticazione tramite l'API di gestione delle risorse.

-   [Controllo dell'accesso basato sui ruoli per Microsoft Azure da Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Si tratta di un collegamento a un video su Channel 9 dalla conferenza Ignite MS 2015. In questa sessione si parla di accedere alle funzionalità di creazione di report in Azure e gestione ed esplorare consigliate per la protezione dell'accesso a Azure abbonamenti con Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Gestione delle chiavi Account lo spazio di archiviazione

Tasti di account di archiviazione sono stringhe di 512 bit create da Azure che insieme al nome di account di archiviazione, possono essere utilizzati per accedere agli oggetti dati archiviati in account di archiviazione, ad esempio BLOB, entità all'interno di una tabella, i messaggi e i file in una condivisione file di Azure. Controllare l'accesso per l'archiviazione account tasti controlla l'accesso al piano di dati per l'account di archiviazione.

Ogni account di archiviazione sono disponibili due chiavi definite "Chiave 1" e "2" nel [portale di Azure](http://portal.azure.com/) e chiave nei cmdlet di PowerShell. Questi possibile rigenerare manualmente utilizzando uno dei vari metodi, inclusi, ma non limitato a tramite il [portale di Azure](https://portal.azure.com/), PowerShell, CLI Azure o a livello di programmazione tramite la libreria di Client .NET lo spazio di archiviazione o l'API REST di Azure archiviazione servizi.

Esistono diversi motivi per rigenerare le chiavi di account di archiviazione.

-   È possibile rigenerare loro regolarmente per motivi di sicurezza.

-   È necessario rigenerare le chiavi di account di archiviazione se qualcuno gestito modificare in un'applicazione e recuperare la chiave hardcoded o salvato in un file di configurazione, in modo che possano accedere al proprio account di archiviazione.

-   Un altro caso per rigenerazione della chiave sia se il team Usa un'applicazione di Esplora lo spazio di archiviazione che conserva la chiave account di archiviazione e che uno dei membri del team cambi. L'applicazione continui a funzionare, in modo che possano accedere al proprio account di archiviazione dopo che è fuori sede. Si tratta effettivamente il motivo principale che sono create le firme a livello di account di accesso condiviso, è possibile usare un account a livello SA anziché memorizzare i tasti di scelta in un file di configurazione.

####<a name="key-regeneration-plan"></a>Piano di rigenerazione della chiave

Non si vuole semplicemente rigenerare la chiave che si utilizza senza una pianificazione. In questo caso, è possibile tagliare disattivare l'accesso all'account di archiviazione, che possono causare problemi principali. Questo è il motivo per cui esistono due metodi. È necessario rigenerare un tasto alla volta.

Prima di rigenerare le chiavi, assicurarsi di avere un elenco di tutte le applicazioni che dipendono dall'account di archiviazione, nonché gli eventuali altri servizi in uso in Azure. Ad esempio, se si utilizza servizi multimediali di Windows Azure che dipendono dal proprio account di archiviazione, è necessario ripetere la sincronizzazione i tasti di scelta con il servizio di supporto dopo si rigenerare la chiave. Se si utilizza tutte le applicazioni, ad esempio una finestra di esplorazione di spazio di archiviazione, sarà necessario fornire i tasti di nuovi per tali applicazioni. Si noti che se si dispone di macchine virtuali cui disco rigido virtuale vengono memorizzati in account di archiviazione, non sarà possibile interessate dal rigenerare i tasti di account di archiviazione.

È possibile rigenerare le chiavi nel portale di Azure. Una volta le chiavi verranno reimpostate vengano può richiedere fino a 10 minuti per la sincronizzazione tra servizi di archiviazione.

Quando si è pronti, ecco il processo generale dettaglio come modificare la chiave. In questo caso, il presupposto è che al momento si usano chiave 1 e si desidera modificare tutto il necessario per utilizzare invece tasto 2.

1.  Rigenerare 2 di chiave per assicurarsi che sia protetto. Nel portale di Azure, è possibile eseguire questa operazione.

2.  In tutte le applicazioni in cui è archiviato il tasto di spazio di archiviazione, modificare la chiave di archiviazione per l'utilizzo di nuovo valore chiave 2. Verificare e pubblicare l'applicazione.

3.  Al termine di tutte le applicazioni e servizi siano e in esecuzione correttamente, rigenerare chiave 1. In questo modo che non è più chiunque a cui non è stata concessa espressamente la nuova chiave avrà accesso all'account di archiviazione.

Se al momento si usano 2 di chiave, è possibile utilizzare la stessa procedura ma invertire i nomi delle chiavi.

È possibile eseguire la migrazione su un paio di giorni, ogni applicazione di utilizzare la nuova chiave di modifica e pubblicazione. Dopo aver terminati di tutti gli elementi, è necessario tornare e rigenerare la chiave precedente in modo che non funziona più.

È inoltre possibile inserire la chiave account di archiviazione in un [Archivio di chiave Azure](https://azure.microsoft.com/services/key-vault/) come segreto e le applicazioni di recuperare la chiave da tale posizione. Quindi quando si rigenerare la chiave e aggiorna l'archivio di chiave di Azure, le applicazioni non necessario ridistribuire perché sono preleverà la nuova chiave dall'archivio di chiave di Azure automaticamente. Si noti che è possibile creare l'applicazione di leggere la chiave ogni volta che è necessario, è possibile memorizzare nella cache in memoria e in caso di errore quando si usa, recuperare nuovamente la chiave dall'archivio di chiave di Azure.

Utilizzando Azure chiave archivio viene aggiunto anche un altro livello di sicurezza per le chiavi di spazio di archiviazione. Se si usa questo metodo, che non è da hardcoded chiave lo spazio di archiviazione in un file di configurazione, che consente di rimuovere tale via di qualcuno riesca ad accedere alla pressione dei tasti senza le autorizzazioni specifiche.

Un altro vantaggio derivante dall'uso Azure chiave archivio è che è anche possibile controllare l'accesso per le chiavi con Azure Active Directory. Di conseguenza, che è possibile concedere l'accesso per alcune applicazioni necessari per recuperare le chiavi dall'archivio chiave Azure e ha la certezza che altre applicazioni non sarà possibile accedere alle chiavi senza concedere loro l'autorizzazione in particolare.

Nota: è consigliabile utilizzare solo uno dei tasti in tutte le applicazioni nello stesso momento. Se si Usa tasti 1 in alcune posizioni e 2 di chiave in altri, non sarà possibile ruotare le chiavi senza un'applicazione perdere l'accesso.

####<a name="resources"></a>Risorse

-   [Informazioni sugli account di archiviazione Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    In questo articolo viene fornita una panoramica degli account di archiviazione e illustra la visualizzazione, copia e rigenerazione tasti di scelta dello spazio di archiviazione.

-   [Riferimento API REST del Provider di archiviazione Azure delle risorse](https://msdn.microsoft.com/library/mt163683.aspx)

    In questo articolo contiene collegamenti ad articoli specifici su il recupero delle chiavi di account di archiviazione e rigenerazione i tasti di account di archiviazione per un Account Azure tramite l'API REST. Nota: Si tratta per gli account di archiviazione di Manager delle risorse.

-   [Operazioni su account di archiviazione](https://msdn.microsoft.com/library/ee460790.aspx)

    In questo articolo in gestore del servizio di archiviazione resto API Reference contiene collegamenti ad articoli specifici recuperare e rigenerare i tasti di account di archiviazione tramite l'API REST. Nota: Si tratta per gli account di archiviazione classica.

-   [Possibilità di utilizzare per gestione delle chiavi: consente di gestire l'accesso ai dati di archiviazione Azure con Azure Active Directory](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    In questo articolo viene illustrato come utilizzare Active Directory per controllare l'accesso alle chiavi di archiviazione Azure in Azure chiave archivio. Viene illustrato come utilizzare un processo di automazione di Azure per rigenerare le chiavi oraria.

##<a name="data-plane-security"></a>Protezione dei dati piano

Protezione dei dati piano fa riferimento a metodi utilizzati per proteggere gli oggetti dati archiviati in archiviazione Azure – BLOB, code, tabelle e file. Abbiamo visto metodi per crittografare i dati e la sicurezza durante il transito dei dati, ma come procedere per informazioni su come consentire l'accesso agli oggetti?

Esistono due metodi per controllare l'accesso agli oggetti di dati stessi. Il primo è controllando l'accesso per i tasti di account di archiviazione e la seconda utilizza condiviso firme di accesso per concedere l'accesso agli oggetti dati specifici per un periodo di tempo specifico.

Un'eccezione nota è che è possibile consentire accesso pubblico per il BLOB impostando il livello di accesso per il contenitore contenente il BLOB di conseguenza. Se si imposta l'accesso per un contenitore Blob o contenitore, sarà possibile pubblico accesso in lettura per BLOB in tale contenitore. Di conseguenza, chiunque disponga di un URL che puntano alle blob in tale contenitore aprirlo in un browser senza utilizzare una firma di Access condiviso o con i tasti di account di archiviazione.

###<a name="storage-account-keys"></a>Tasti di Account di archiviazione

Tasti di account di archiviazione sono stringhe di 512 bit create da Azure che, insieme al nome di account di archiviazione, può essere utilizzato per accedere agli oggetti dati archiviati in account di archiviazione.

Ad esempio, è possibile leggere BLOB, scrivere code, creare tabelle e modificare i file. Molte di queste operazioni possono essere eseguite tramite il portale di Azure, oppure utilizzare uno dei molti applicazioni Esplora archivi. È inoltre possibile scrivere codice per usare l'API REST o una delle raccolte di Client lo spazio di archiviazione per eseguire queste operazioni.

Come illustrato nella sezione sulla [Sicurezza di piano di gestione](#management-plane-security), è possibile concedere l'accesso a chiavi lo spazio di archiviazione per un account di archiviazione classica assegnando accesso completo all'abbonamento Azure. Tramite controllo dell'accesso basato sui ruoli (RBAC), è possibile controllare l'accesso alle chiavi dello spazio di archiviazione per un account di archiviazione utilizzando il modello di gestione di risorse Azure.

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Per la delega accesso agli oggetti nel proprio account mediante condiviso firme di accesso e archiviati i criteri di accesso

Una firma di Access condiviso è una stringa contenente un token di sicurezza che può essere collegato a un URI che consente di delegare l'accesso a oggetti di archiviazione e specificare i vincoli, ad esempio le autorizzazioni e l'intervallo di data/ora di accesso.

È possibile concedere l'accesso al BLOB, contenitori, i messaggi, file e le tabelle. Le tabelle, è possibile effettivamente concedere autorizzazioni per accedere a un intervallo di entità nella tabella specificando gli intervalli di chiavi partizione e riga a cui si vuole che l'utente abbia accesso. Ad esempio, se i dati archiviati con una chiave di partizione dello stato geografica, è possibile fornire agli utenti l'accesso a solo i dati per California.

In un altro esempio, potrebbe fornire un token di sa che consente di inserire voci a una coda di un'applicazione web e assegnare un lavoro ruolo applicazione un token SA per recuperare i messaggi dalla coda e la loro elaborazione. Oppure si potrebbe assegnare un cliente un token di sa che consente di caricare le immagini in un contenitore di archiviazione Blob e concedere un'autorizzazione di applicazione web per leggere le immagini. In entrambi i casi, non esiste una separazione dei problemi: ogni applicazione può essere specificato solo l'accesso necessarie per eseguire le attività. Questo è possibile mediante l'utilizzo di firme accesso condiviso.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Perché si desidera utilizzare le firme di Access condiviso

Perché si desidera utilizzare un SA invece di concedere solo la chiave di account di archiviazione, soluzione più semplice? Concedere la chiave account lo spazio di archiviazione è simile a condividere i tasti dell'unito lo spazio di archiviazione. Concede l'accesso completo. Un utente può usare i tasti di e caricare la libreria di tutta la al proprio account di archiviazione. È Impossibile sostituire i file con le versioni contenenti virus e i o sottrarre i dati. Fornire accesso illimitato al proprio account di archiviazione è importante non prendere scorrere.

Per le firme accesso condiviso, è possibile assegnare un client solo le autorizzazioni necessarie per un periodo di tempo limitato. Ad esempio, se qualcuno sta caricando un blob al proprio account, è possibile concedere loro accesso in scrittura per appena abbastanza tempo caricare blob (a seconda della dimensione del blob, naturalmente). E se si cambia idea, è possibile revocare l'accesso.

Inoltre, è possibile specificare che richieste mediante un SA sono limitate a un determinato indirizzo IP o intervallo di indirizzi IP esterno in Azure. È anche possibile richiedere che richieste utilizzando un protocollo specifico (HTTPS o HTTP/HTTPS). Indica che se si desidera consentire il traffico HTTPS, è possibile impostare il protocollo necessario HTTPS solo e verrà bloccato il traffico HTTP.

####<a name="definition-of-a-shared-access-signature"></a>Definizione di una firma di accesso condiviso

Una firma di Access condiviso è un insieme di parametri query aggiunte all'URL che punta alla risorsa

che fornisce informazioni sull'accesso consentito e l'intervallo di tempo per il quale è consentito l'accesso. Ecco un esempio; Questo URI fornisce accesso in lettura un blob per cinque minuti. Nota che sa parametri della query deve essere con codifica URL, ad esempio % 3A per due punti (:) o % 20 per uno spazio.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>La modalità di autenticazione la firma di Access condiviso dal servizio di archiviazione Azure

Quando il servizio di archiviazione riceve la richiesta, accetta i parametri di input query e viene creata una firma utilizzando il metodo stesso come programma di chiamata. Quindi vengono confrontati due firme. Se si accettano, il servizio di archiviazione possibile controllare la versione del servizio di archiviazione per assicurarsi che è valido, verificare che la data e ora correnti siano all'interno della finestra specificata, verificare che l'accesso richiesto corrisponde al richiesta e così via.

Ad esempio, con l'URL precedente, se l'URL è stata punta a un file anziché un blob della richiesta avrà esito negativo poiché specifica che la firma di Access condiviso per un blob. Se il comando resto chiamato era per aggiornare un blob, avrà esito negativo poiché la firma di Access condiviso specifica che è consentito solo accesso in lettura.

####<a name="types-of-shared-access-signatures"></a>Tipi di firme accesso condiviso

-   SA un livello di servizio può essere utilizzato per accedere risorse specifiche in un account di archiviazione. Alcuni esempi di questa recupera un elenco di BLOB in un contenitore, scaricare un blob, aggiornamento di un'entità in una tabella, aggiunta dei messaggi a una coda o caricare un file in una condivisione file.

-   Per accedere a qualsiasi elemento che sa un livello di servizio può essere usata per è possibile utilizzare sa un livello di account. Inoltre, può fornire opzioni per le risorse che non sono consentite con SA un livello di servizio, ad esempio la possibilità di creare contenitori, tabelle, code e nelle condivisioni file. È inoltre possibile specificare l'accesso a più servizi contemporaneamente. Ad esempio, è possibile consentire a un utente l'accesso agli BLOB e file nel proprio account di archiviazione.

####<a name="creating-an-sas-uri"></a>Creazione di un URI SA

1.  È possibile creare un URI ad hoc su richiesta, la definizione di tutti i parametri della query ogni volta.

    Questo è molto flessibile, ma se si dispone di un set di logico dei parametri sono simili ogni volta, l'uso di un criterio di accesso memorizzate è un'idea.

2.  È possibile creare un criterio di accesso memorizzate per un intero contenitore, una condivisione file, una tabella o coda. Quindi è possibile usare questo come base per URI SA create. Possono facilmente revocare le autorizzazioni in base a criteri di accesso memorizzate. È possibile impostare un massimo di 5 criteri definiti in ogni contenitore, coda, una tabella o condivisione di file.

    Se si prevede di molte persone leggere BLOB in un contenitore specifico, ad esempio, è possibile creare un criterio di accesso memorizzate che indica che "Concedi accesso in lettura" e le altre impostazioni che saranno gli stessi ogni volta. È possibile creare un URI SAS utilizzando le impostazioni del criterio di accesso memorizzate e specificando la data/ora di scadenza. Il vantaggio di questo è non è necessario specificare tutti i parametri di query ogni volta.

####<a name="revocation"></a>Revoca

Si supponga che sia stata compromessa la SA o si desidera modificare a causa di protezione a livello aziendale o i requisiti di conformità alle normative. Come si revocare l'accesso a una risorsa utilizzando tale SA? Dipende creazione URI SAS.

Se si utilizza ad hoc URI, sono disponibili tre opzioni. È possibile rilasciare token sa con criteri di scadenza breve e attendere per SA scadenza. È possibile rinominare o eliminare la risorsa (presupponendo che il token di stato nell'ambito a un singolo oggetto). È possibile modificare i tasti di account di archiviazione. Quest'ultima opzione può avere un impatto grande, a seconda dei servizi quanti utilizzati account di archiviazione e probabilmente non che si desidera eseguire senza una pianificazione.

Se si utilizza un SA derivato da un criterio di accesso memorizzate, è possibile rimuovere access revoca il criterio di accesso memorizzate: è possibile modificare solo, pertanto non è già scaduto o rimuoverlo completamente. Ha effetto immediato e invalida ogni SA creato con il criterio di accesso memorizzate. L'aggiornamento o rimuovere il criterio di accesso memorizzate potrebbero impatto gli utenti di tale contenitore specifico, condivisione file, tabella o coda tramite SA, ma se i client vengono scritte in modo che richiedono un nuove SA quando il vecchio diventa non valido, si ottengono risultati ottimali.

Poiché utilizzando una SA derivato da un criterio di accesso memorizzate offre la possibilità di revocare che sa immediatamente, è consigliabile utilizzare sempre i criteri di accesso memorizzate laddove possibile.

####<a name="resources"></a>Risorse

Per informazioni più dettagliate sull'utilizzo di firme accesso condiviso e archiviati i criteri di accesso, completa di esempi, vedere gli articoli seguenti:

-   Questi sono gli articoli della Guida di riferimento.

    -   [Servizio SA](https://msdn.microsoft.com/library/dn140256.aspx)

        In questo articolo vengono forniti esempi di utilizzo sa un livello di servizio con BLOB, i messaggi, gli intervalli di tabelle e file.

    -   [La creazione di un servizio SA](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [La creazione di un account sa](https://msdn.microsoft.com/library/mt584140.aspx)

-   Si tratta di esercitazioni per utilizzare la libreria client .NET per creare firme di Access condiviso e archiviati i criteri di accesso.

    -   [Uso delle firme accesso condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Condividere le firme di Access, parte 2: Creare e utilizzare un sa con il servizio di Blob](storage-dotnet-shared-access-signature-part-2.md)

        In questo articolo include una spiegazione del modello di SA, esempi di firme di Access condiviso e consigli per la procedura consigliata utilizzano delle associazioni di protezione. Viene illustrato è revoca dell'autorizzazione abbia concesso o meno.

-   Limitazione dell'accesso mediante indirizzo IP (IP ACL)

    -   [Che cos'è un endpoint elenco di controllo di accesso (ACL)?](../virtual-network/virtual-networks-acl.md)

    -   [La creazione di un servizio SA](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Questo articolo è il riferimento per SA a livello di servizio; include un esempio di ACLing IP.

    -   [La creazione di un Account SA](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Questo articolo è il riferimento per livello account sa; include un esempio di ACLing IP.

-   Autenticazione

    -    [Autenticazione per i servizi di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Condiviso firme accesso introduzione esercitazione

    -   [Guida introduttiva esercitazioni SA](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Crittografia durante il transito

###<a name="transport-level-encryption--using-https"></a>Crittografia a livello di trasporto: tramite HTTPS

Un altro passaggio da adottare per garantire la sicurezza dei dati dello spazio di archiviazione di Azure consiste nel crittografare i dati tra il client e lo spazio di archiviazione di Azure. Nel primo suggerimento consiste nell'usare sempre il protocollo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , che assicura di comunicazione sicura tramite Internet.

Quando la chiamata API REST o accedere a oggetti di archiviazione, è necessario utilizzare sempre HTTPS. Inoltre, **Firme accesso condiviso**, che possono essere utilizzati per delegare l'accesso a oggetti di archiviazione di Azure, includere un'opzione per specificare che è possibile utilizzare solo il protocollo HTTPS quando si usa firme accesso condiviso, garantire che chiunque l'invio di collegamenti con i token SA utilizzerà il protocollo corretto.

####<a name="resources"></a>Risorse

-   [Abilita HTTPS per un'app nel servizio App Azure](../app-service-web/web-sites-configure-ssl-certificate.md)

    In questo articolo viene illustrato come abilitare HTTPS per un'App Web di Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Usa la crittografia durante il transito con condivisioni File Azure

Spazio di archiviazione File Azure supporta HTTPS quando si utilizza l'API REST, ma più comunemente usati come una condivisione file di piccole e medie imprese connesso a una macchina virtuale. 2.1 piccole e medie imprese non supporta la crittografia, in modo che le connessioni sono consentite solo all'interno dell'area stessa in Azure. Tuttavia, piccole e medie imprese 3.0 supporta la crittografia e possono essere usate con Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, si consente l'area geografica tra accesso e persino access sul desktop.

Si noti che mentre nelle condivisioni File Azure possono essere utilizzate con Unix, il client Linux SMB non ancora supporta la crittografia, per consentire l'accesso è consentito solo all'interno di un'area di Azure. Supporto per la crittografia per Linux sia la Guida di orientamento di sviluppatori Linux responsabili della funzionalità di piccole e medie imprese. Durante l'aggiunta di crittografia, si avrà la stessa possibilità per l'accesso a una condivisione File Azure su Linux come per Windows.

####<a name="resources"></a>Risorse

-   [Utilizzo di spazio di archiviazione File Azure con Linux](storage-how-to-use-files-linux.md)

    In questo articolo viene illustrato come installare una condivisione File Azure in un sistema Linux e upload/download file.

-   [Guida introduttiva a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md)

    In questo articolo viene fornita una panoramica di condivisioni File Azure e su come installare e usarle con PowerShell e .NET.

-   [Spazio di archiviazione File Azure interno](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    In questo articolo annuncia la disponibilità generale di spazio di archiviazione File Azure e vengono fornite informazioni tecniche sulla crittografia 3.0 piccole e medie imprese.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Usa la crittografia lato Client per proteggere i dati inviati allo spazio di archiviazione

Un'altra opzione che consente di garantire la sicuro dei dati durante la fase di trasferimento tra un'applicazione client e lo spazio di archiviazione è la crittografia lato Client. I dati vengono crittografati prima trasferito nell'archiviazione Azure. Quando si recuperano dati dallo spazio di archiviazione di Azure, i dati decrittografia dopo aver ricevuto sul lato client. Anche se i dati vengono crittografati passando attraverso la rete, è consigliabile utilizzare anche HTTPS, in quanto non dispone i controlli di integrità dei dati incorporati che per ridurre il rischio di errori di rete che interessano l'integrità dei dati.

La crittografia lato client è anche un metodo per la crittografia dei dati inattivi, i dati vengono archiviati nella forma crittografata. Verranno fornite informazioni in modo più dettagliato nella sezione sulla [crittografia inattivi](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Crittografia inattivi

Esistono tre caratteristiche Azure che forniscono crittografia inattivi. Crittografia disco Azure viene usata per crittografare i sistema operativo e dati dischi in macchine virtuali IaaS. Gli altri due – SSE- e la crittografia lato Client sono entrambe usato per crittografare i dati in archiviazione Azure. Di seguito esaminare ognuna di esse e quindi un confronto e vedere quando ognuno di essi può essere usata.

Sebbene sia possibile utilizzare la crittografia lato Client per crittografare i dati durante il transito (che viene archiviato in forma crittografata in archiviazione), è preferibile semplicemente utilizzare HTTPS durante il trasferimento e di avere un modo per i dati da crittografare automaticamente quando viene archiviato. Esistono due modi per eseguire questa operazione, ossia la crittografia del Azure e SSE. Viene utilizzato uno per crittografare direttamente i dati su dischi del sistema operativo e dati utilizzati da macchine virtuali e l'altra viene utilizzata per crittografare dati scritti all'archivio Blob Azure.

###<a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione (SSE)

SSE consente di richiedere che il servizio di archiviazione crittografare automaticamente i dati quando si scrive allo spazio di archiviazione Azure. Quando si leggono dati dallo spazio di archiviazione di Azure, verrà decrittografare dal servizio di archiviazione prima di essere restituito. In questo modo è possibile proteggere i dati senza dover modificare il codice o aggiungere codice a tutte le applicazioni.

Questa è un'impostazione che si applica a account di archiviazione intera. È possibile abilitare e disabilitare questa caratteristica modificando il valore dell'impostazione. A questo scopo è possibile utilizzare il portale di Azure, PowerShell, CLI Azure, API REST di Provider di risorse dello spazio di archiviazione o la raccolta di Client .NET lo spazio di archiviazione. Per impostazione predefinita, SSE è disattivata.

In questa fase, le chiavi utilizzate per la crittografia vengono gestite da Microsoft. Abbiamo generare le chiavi originariamente e gestire l'archiviazione sicura di chiavi, nonché la rotazione regolare come definito dall'interno dei criteri di Microsoft. In futuro, si otterrà la possibilità di gestire il proprio chiavi di crittografia e specificare un percorso di migrazione dalle chiavi gestite Microsoft per chiavi gestite cliente.

Questa caratteristica è disponibile per gli account Standard e Premium lo spazio di archiviazione creati usando il modello di distribuzione di Manager delle risorse. SSE si applica solo a bloccare BLOB, BLOB di pagine e accoda BLOB. Gli altri tipi di dati, inclusi tabelle, code e file, non verranno crittografati.

Dati vengono crittografati solo quando è abilitata SSE e i dati vengono scritte a archiviazione Blob. Attivazione o disattivazione di SSE non ha effetti dati esistenti. In altre parole, quando si abilita la crittografia, non si torna né crittografare i dati che è già presente. né per decrittografare i dati già esistente quando si disattiva SSE.

Se si desidera utilizzare questa funzione con un account di archiviazione classica, è possibile creare un nuovo account di archiviazione di Manager delle risorse e usare AzCopy per copiare i dati al nuovo account. 

###<a name="client-side-encryption"></a>Crittografia lato client

Menzionato crittografia lato client quando si parla la crittografia dei dati in transito. Questa caratteristica consente di a livello di programmazione crittografare i dati in un'applicazione client prima di inviarlo in rete per consentire la scrittura dello spazio di archiviazione di Azure e decrittografare a livello di programmazione i dati dopo averla dallo spazio di archiviazione Azure.

Questo fornire crittografia durante il transito, ma fornisce anche la funzionalità di crittografia inattivi. Si noti che anche se i dati vengono crittografati durante il transito, è comunque consigliabile utilizzare HTTPS in modo da sfruttare i controlli di integrità dei dati incorporati per ridurre il rischio di errori di rete che interessano l'integrità dei dati.

Esempio di nel punto in cui è possibile utilizzare tale è se si ha un'applicazione web per l'archiviazione BLOB e recupera BLOB e si desidera l'applicazione e i dati in quanto più sicure. In questo caso, utilizzare la crittografia lato client. Il traffico tra il client e il servizio di Blob Azure contiene la risorsa crittografata e nessuno interpretare i dati durante il transito e ricostituire nel BLOB privato.

La crittografia lato client è integrata nel linguaggio e le librerie client lo spazio di archiviazione di .NET, che a sua volta utilizza le API di archivio di chiave di Azure, eseguendo un'operazione semplice per l'implementazione di. Il processo di crittografia e decrittografia dei dati viene utilizzata la tecnica busta e archiviati metadati utilizzati per la crittografia in ogni oggetto di archiviazione. Ad esempio, per BLOB, viene memorizzato nei metadati blob per code, viene aggiunto a ogni messaggio coda.

Per la crittografia se stesso, è possibile generare e gestire le proprie chiavi di crittografia. È inoltre possibile utilizzare le chiavi generate dalla libreria dei Client di spazio di archiviazione di Azure oppure è possibile creare l'archivio di chiave Azure generare le chiavi. È possibile archiviare le chiavi di crittografia in archiviazione chiave in locale oppure è possibile archiviarli in un archivio di chiave Azure. Archivio chiave Azure consente di concedere l'accesso a informazioni riservate in Azure chiave archivio a utenti specifici utilizzando Azure Active Directory. Ciò significa che non solo chiunque può leggere l'archivio di chiave Azure e recuperare le chiavi che si usa per la crittografia lato client.

####<a name="resources"></a>Risorse

-   [Crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio](storage-encrypt-decrypt-blobs-key-vault.md)

    In questo articolo viene illustrato come utilizzare la crittografia lato client con Azure chiave archivio, ad esempio come creare il KEK e archiviarlo nell'archivio di utilizzo di PowerShell.

-   [La crittografia lato client e Azure archivio chiave per lo spazio di archiviazione di Microsoft Azure](storage-client-side-encryption.md)

    In questo articolo fornisce una spiegazione di crittografia lato client e vengono forniti esempi di utilizzo della libreria di client lo spazio di archiviazione per crittografare e decrittografare risorse dalla pagina Servizi di quattro lo spazio di archiviazione. Parla anche Azure chiave archivio.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Utilizzo della crittografia disco Azure per crittografare dischi utilizzati da macchine virtuali

Crittografia disco Azure è una funzionalità è attualmente in anteprima. Questa caratteristica consente di crittografare il dischi del sistema operativo e dischi dati utilizzati da una macchina virtuale IaaS. Per Windows, le unità vengono crittografate utilizzando la tecnologia di crittografia BitLocker standard di settore. Per Linux, i dischi vengono crittografati utilizzando la tecnologia di crittografia di data mining. Questo è integrato con Azure chiave archivio in modo da controllare e gestire le chiavi di crittografia disco.

La soluzione Azure disco crittografia supporta i seguenti tre crittografia scenari:

-   Abilitare la crittografia dal nuovo macchine virtuali di IaaS creati da file disco rigido virtuale crittografati cliente e chiavi di crittografia forniti dal cliente, che vengono archiviate in Azure chiave archivio.

-   Abilitare la crittografia dal nuovo macchine virtuali di IaaS creato da Azure Marketplace.

-   Attivare la crittografia in macchine virtuali di IaaS esistente già in esecuzione in Azure.

>[AZURE.NOTE] Per macchine virtuali Linux già in esecuzione in Azure o macchine virtuali Linux creati dalle immagini in Azure Marketplace, la crittografia del disco del sistema operativo non è attualmente supportata. La crittografia del Volume del sistema operativo per macchine virtuali Linux è supportata solo per macchine virtuali crittografati in locale e caricati in Azure. Questa limitazione si applica solo su disco del sistema operativo. la crittografia dei volumi di dati per un VM Linux è supportata.

La soluzione supporta le operazioni seguenti per macchine virtuali IaaS per versione public Preview quando in Microsoft Azure attivata:

-   Integrazione con archivio chiave Azure

-   Standard [, D ed macchine virtuali di IaaS serie G](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Abilitare la crittografia in macchine virtuali IaaS creato tramite il modello di [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md)

-   Pubblica tutti Azure [aree geografiche](https://azure.microsoft.com/regions/)

Questa caratteristica assicura che tutti i dati sui dischi macchina virtuale è crittografato inattivi in archiviazione Azure.

####<a name="resources"></a>Risorse

-   [Crittografia di Azure disco per Windows o Linux IaaS virtuali](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    In questo articolo illustra la versione di anteprima di Azure disco crittografia e fornisce un collegamento per scaricare white paper.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Confronto tra la crittografia del Azure, SSE e la crittografia lato Client

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS macchine virtuali e i propri file disco rigido virtuale

Per dischi utilizzati da macchine virtuali IaaS, è consigliabile utilizzare la crittografia del Azure. È possibile attivare SSE per crittografare i file disco rigido virtuale utilizzato per eseguire il backup tali dischi in archiviazione Azure, ma solo crittografa appena scritti. Questo errore indica se si crea una macchina virtuale e quindi attiva SSE account di archiviazione che contiene il file disco rigido virtuale, solo le modifiche verranno crittografate, non il file disco rigido virtuale originale.

Se si crea una macchina virtuale utilizzo di un'immagine da Azure Marketplace Azure esegue una [copia dei riferimenti](https://en.wikipedia.org/wiki/Object_copying) dell'immagine al proprio account di archiviazione in archiviazione Azure, e non è crittografato anche se SSE è abilitato. Dopo che crea la macchina virtuale e si avvia aggiornamento dell'immagine, SSE per avviare la crittografia dei dati. Per questo motivo, è consigliabile utilizzare la crittografia del Azure in macchine virtuali create dalle immagini in Azure Marketplace se lo si desidera completamente crittografato.

Se si sposta una macchina virtuale non crittografata in Azure locali, sarà possibile caricare le chiavi di crittografia all'archivio chiave Azure e continuare a utilizzare la crittografia di quella macchina virtuale che si utilizzava in locale. Crittografia disco Azure è abilitata per la gestione di questo scenario.

Se si dispone non crittografato disco rigido virtuale locali, è possibile caricare nella raccolta come un'immagine personalizzata ed effettuare il provisioning di una macchina virtuale del file. Se si esegue questa operazione utilizzando i modelli di Manager delle risorse, è possibile richiedere di attivare la crittografia del Azure avvio la macchina virtuale.

Quando si aggiunge un disco dati e installarlo nella macchina virtuale, è possibile attivare la crittografia del Azure del disco dati. Modo da crittografare il disco di dati in locale prima di tutto e quindi il livello di gestione dei servizi eseguire un'operazione di scrittura lenta contro lo spazio di archiviazione in modo che il contenuto di spazio di archiviazione è crittografato.

####<a name="client-side-encryption"></a>Crittografia lato client####

La crittografia lato client è il metodo più sicuro per la crittografia dei dati, perché crittografa prima di transito e crittografa i dati inattivi. È necessario aggiungere codice alle applicazioni mediante lo spazio di archiviazione, è consigliabile non effettuare. In questi casi, è possibile utilizzare HTTPs per i dati in transito e SSE per crittografare i dati inattivi.

Con la crittografia lato client, è possibile crittografare entità della tabella, i messaggi e BLOB. Con SSE, è possibile crittografare solo BLOB. Se è necessario crittografare i dati nella tabella e coda, è necessario utilizzare la crittografia lato client.

La crittografia lato client è gestita interamente dall'applicazione. L'approccio più sicura, ma è necessario apportare le modifiche a livello di programmazione all'applicazione e inserire i processi di gestione delle chiavi in posizione. Utilizzare questo quando si desidera sicurezza aggiuntivo durante il transito e si desidera i dati memorizzati crittografato.

La crittografia lato client è un carico maggiore sul client e disporre tenerne conto per i piani di scalabilità, soprattutto se si ha la crittografia e il trasferimento di una grande quantità di dati.

####<a name="storage-service-encryption-sse"></a>Crittografia del servizio di archiviazione (SSE)

SSE viene gestita da archiviazione Azure. Utilizza SSE non è disponibile per la protezione dei dati in transito, ma crittografare i dati come indicato di archiviazione Azure. Non ha alcun impatto sulle prestazioni quando si utilizza questa caratteristica.

È solo possibile crittografare blocco BLOB, accodare BLOB e BLOB utilizza SSE della pagina. Se è necessario crittografare dati della tabella o una coda i dati, è necessario ricorrere crittografia lato client.

Se si dispone di un archivio o una raccolta di file disco rigido virtuale utilizzate come base per la creazione di nuove macchine virtuali, è possibile creare un nuovo account di archiviazione, abilitare SSE e quindi caricare i file disco rigido virtuale a tale account. I file disco rigido virtuale verranno crittografati tramite lo spazio di archiviazione di Azure.

Se si dispone di Azure disco crittografia abilitata per i dischi in macchine Virtuali e SSE attivato per l'account di archiviazione che contiene i file disco rigido virtuale, il corretto funzionamento regolare con maggiore; verrà generato in tutti i dati appena scritto da crittografare due volte.

##<a name="storage-analytics"></a>Spazio di archiviazione Analitica

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>Utilizzo dello spazio di archiviazione Analitica per controllare il tipo di autorizzazione

Per ogni account di archiviazione, è possibile abilitare Azure Analitica lo spazio di archiviazione eseguire la registrazione e archiviare i dati di metrica. Questo è un ottimo strumento da utilizzare quando si desidera verificare le metriche di prestazioni di un account di archiviazione o necessario risolvere i problemi di un account di archiviazione perché si verificano problemi di prestazioni.

Un altro blocco di dati che è possibile visualizzare nei log analitica lo spazio di archiviazione è il metodo di autenticazione utilizzato da una persona quando accedono a spazio di archiviazione. Ad esempio, di archiviazione Blob, è possibile visualizzare se utilizzati una firma di Access condiviso o i tasti di account di archiviazione o se il blob accesso è pubblico.

Può essere molto utile se sono strettamente protegge accesso allo spazio di archiviazione. In archiviazione Blob, ad esempio, è possibile impostare tutti i contenitori su private e implementare l'utilizzo di un servizio SA all'interno delle applicazioni. È quindi possibile controllare i log regolarmente per verificare se il BLOB sono accessibili tramite i tasti di account di archiviazione, che possono indicare violazioni della protezione, o se i BLOB sono pubblici ma non devono essere.

####<a name="what-do-the-logs-look-like"></a>I registri di aspetto delle?

Dopo che si abilita le metriche di account di archiviazione e la registrazione tramite il portale di Azure, dati analitica inizierà accumulare rapidamente. La registrazione e la metrica per ogni servizio è distinto; la registrazione è scritta solo quando l'attività è in tale account di archiviazione, mentre le metriche verranno registrate ogni minuto, ogni ora o ogni giorno, a seconda della configurazione.

I log sono archiviati in BLOB blocco in un contenitore denominato $logs nell'account di archiviazione. In questo contenitore viene creato automaticamente quando si abilita archiviazione Analitica. Dopo aver creato il contenitore, è possibile eliminare, sebbene sia possibile eliminare il relativo contenuto.

In contenitore $logs esiste una cartella per ogni servizio e sono disponibili le sottocartelle per anno/mese/giorno/ora. In ora, i registri sono semplicemente numerati. Ecco come apparirà la struttura di directory:

![Visualizzazione dei file di log](./media/storage-security-guide/image1.png)

Ogni richiesta allo spazio di archiviazione di Azure ha eseguito l'accesso. Ecco uno snapshot di un file di log, con alcuni campi prima.

![Snapshot di un file di log](./media/storage-security-guide/image2.png)

È possibile vedere che è possibile utilizzare i registri per tenere traccia di qualsiasi tipo di chiamate a un account di archiviazione.

####<a name="what-are-all-of-those-fields-for"></a>Quali sono di tutti i campi per?

Esiste un articolo elencato nelle risorse sotto che contiene l'elenco dei campi molti i log e vengono utilizzati per. Ecco l'elenco dei campi in ordine:

![Snapshot dei campi in un file di log](./media/storage-security-guide/image3.png)

Ci interessa le voci per GetBlob e la loro autenticazione, in modo che occorre eseguire una ricerca per le voci con tipo di operazione "Get-Blob" e controllare lo stato di richiesta (4<sup>esima</sup> colonna) e il tipo di autorizzazione (8<sup>esima</sup> colonna).

Ad esempio di prime righe nell'elenco precedente, lo stato della richiesta è "Operazione riuscita" e il tipo di autorizzazione "autenticazione". Questo errore indica che la richiesta convalidata utilizzando la chiave account lo spazio di archiviazione.

####<a name="how-are-my-blobs-being-authenticated"></a>Come vengono autenticati personale BLOB?

Ci sono tre casi che si sono interessati.

1.  Il blob è pubblico e accedervi tramite un URL senza una firma di accesso condiviso. In questo caso, lo stato della richiesta è "AnonymousSuccess" e il tipo di autorizzazione "anonimo".

    1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**200 124; 37; **anonimo**; risorsa mystorage...

2.  Il blob è privato ed è stato utilizzato con una firma di Access condiviso. In questo caso, lo stato della richiesta è "SASSuccess" e il tipo di autorizzazione è "sa".

    1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**200 416; 64; **sa**; risorsa mystorage...

3.  Il blob è privato e la chiave di archiviazione è stata utilizzata per accedervi. In questo caso, lo stato della richiesta è "**operazione riuscita**" e il tipo di autorizzazione è "**autenticato**".

    1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Success**206 59; 22; **l'autenticazione**, la risorsa mystorage...

È possibile utilizzare Microsoft messaggio Analyzer per visualizzare e analizzare i registri. Include funzionalità di ricerca e filtro. È ad esempio cercare le istanze di GetBlob per vedere se l'utilizzo sia quello previsto, ad esempio, per assicurarsi che un utente non è accedere all'account di archiviazione impropriamente.

####<a name="resources"></a>Risorse

-   [Spazio di archiviazione Analitica](storage-analytics.md)

    In questo articolo è una panoramica di analitica lo spazio di archiviazione e su come attivarli.

-   [Formato di memorizzazione Analitica Log](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    In questo articolo illustra il formato di registro Analitica lo spazio di archiviazione e dei dettagli di campi disponibili al suo interno, tra cui tipo di autenticazione, che indica il tipo di autenticazione utilizzato per la richiesta.

-   [Monitor di un Account di archiviazione nel portale di Azure](storage-monitor-storage-account.md)

    In questo articolo viene illustrato come configurare la registrazione per un account di archiviazione e il monitoraggio di metriche.

-   [Risoluzione dei problemi-to-End tramite Azure metriche di archiviazione e la registrazione, AzCopy e Analyzer messaggio](storage-e2e-troubleshooting.md)

    In questo articolo parla sulla risoluzione dei problemi tramite Analitica lo spazio di archiviazione e viene illustrato come utilizzare Microsoft messaggio Analyzer.

-   [Messaggio di Microsoft Analyzer operativo Guida](https://technet.microsoft.com/library/jj649776.aspx)

    In questo articolo è il riferimento per Analizzatore di messaggio Microsoft e include collegamenti a un'esercitazione, Guida introduttiva e funzionalità di riepilogo.

##<a name="cross-origin-resource-sharing-cors"></a>Croce origine condivisore (CORS)

###<a name="cross-domain-access-of-resources"></a>Accesso domini delle risorse

Quando un web browser in esecuzione in un unico dominio effettua una richiesta HTTP per una risorsa da un altro dominio, si tratta di una richiesta HTTP tra origine. Ad esempio una pagina HTML servita da contoso.com richieda jpeg ospitate in fabrikam.blob.core.windows.net. Per motivi di sicurezza, browser limitano le richieste HTTP tra origine avviate da script, ad esempio JavaScript. Ciò significa che al codice JavaScript in una pagina web in contoso.com richiede tale jpeg in fabrikam.blob.core.windows.net, il browser non consentirà la richiesta.

Che cos'è il fare con Azure lo spazio di archiviazione? Se si archiviano beni statiche, ad esempio file di dati JSON o XML in archiviazione Blob usando un account di archiviazione denominata Fabrikam, il dominio per le risorse saranno fabrikam.blob.core.windows.net e l'applicazione web contoso.com non sarà possibile accedervi utilizzando JavaScript perché i domini sono diversi. Questo vale anche se si sta tentando di chiamare uno dei servizi di archiviazione Windows Azure, ad esempio archiviazione tabella-che restituiscono dati JSON possano essere elaborati dal client JavaScript.

####<a name="possible-solutions"></a>Possibili soluzioni

Un modo per risolvere questo problema è per assegnare un dominio personalizzato, ad esempio "storage.contoso.com" a fabrikam.blob.core.windows.net. Il problema è che è possibile assegnare tale dominio personalizzato solo per conto di uno spazio di archiviazione. Cosa fare se le risorse sono archiviate in più account di archiviazione?

Un altro modo per risolvere questo problema è che l'applicazione web come proxy per le chiamate di spazio di archiviazione. Indica che se si sta caricando un file a archiviazione Blob, l'applicazione web da scrivere in locale e quindi copiarlo in archiviazione Blob o da leggere interamente in memoria e quindi scrivere a archiviazione Blob. In alternativa, è possibile scrivere un'applicazione web dedicato (ad esempio un'API Web) che carica i file in locale e vengono scritte a archiviazione Blob. In entrambi i casi, è necessario considerare per la funzione quando è necessario determinare la scalabilità.

####<a name="how-can-cors-help"></a>Come può aiutare CORS?

Spazio di archiviazione Azure consente di attivare CORS – incrociato origine la condivisione delle risorse. Per ogni account di archiviazione, è possibile specificare i domini che possono accedere alle risorse di account di archiviazione. Ad esempio, in questo caso descritto in precedenza, è possibile attivare l'account di archiviazione fabrikam.blob.core.windows.net CORS e configurarlo per consentire l'accesso a contoso.com. Quindi contoso.com di applicazione web possono accedere direttamente alle risorse fabrikam.blob.core.windows.net.

Una nota consiste CORS consente l'accesso, ma non fornisce l'autenticazione, è necessario per tutte non pubblico access delle risorse di archiviazione. Di conseguenza, che è possibile accedere BLOB solo se sono pubblici o si includere una firma di Access condiviso fornisce le autorizzazioni appropriate. Tabelle, code e file accesso non sono pubblico e richiedono un SA.

Per impostazione predefinita, CORS sono disabilitati in tutti i servizi. È possibile abilitare CORS utilizzando l'API REST o alla raccolta di client lo spazio di archiviazione per chiamare uno dei metodi per impostare i criteri di servizio. Quando si esegue questa operazione, includere una regola di CORS è in formato XML. Ecco un esempio di una regola CORS che è stata impostata utilizzando l'operazione di impostare le proprietà del servizio per il servizio Blob per un account di archiviazione. È possibile eseguire tale operazione utilizzando la libreria di client lo spazio di archiviazione o le API REST per l'archiviazione di Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Ecco cosa significa ogni riga:

-   **AllowedOrigins** In questo modo quali domini non corrispondenti possono richiedere e ricevere dati dal servizio di archiviazione. Indica che contoso.com e fabrikam.com può richiedere dati dallo spazio di archiviazione Blob per un account di archiviazione specifico. È inoltre possibile impostare questo a un carattere jolly (\*) per consentire a tutti i domini a richieste di accesso.

-   **AllowedMethods** Questo è l'elenco dei metodi, verbi richiesta HTTP, che può essere utilizzato quando si effettua la richiesta. In questo esempio, sono consentiti solo inserire e recuperare. È possibile impostare un carattere jolly (\*) per consentire a tutti i metodi da utilizzare.

-   **AllowedHeaders** Si tratta le intestazioni di richiesta che il dominio di origine è possibile specificare quando si effettua la richiesta. In questo esempio, tutte le intestazioni di metadati a partire da x-ms-metadati, destinazione metatag di ms x e x-ms-meta-abc sono consentiti. Il carattere jolly (\*) indica che qualsiasi inizio intestazione con il prefisso specificato è consentita.

-   **ExposedHeaders** In questo modo le intestazioni di risposta devono essere esposto dal browser in autorità richiesta. In questo esempio, una delle intestazioni a partire da "x-ms - meta-" verrà esposta.

-   **MaxAgeInSeconds** Si tratta la maggior quantità di tempo che un browser inserirà la richiesta di opzioni verifica preliminare. (Per ulteriori informazioni sulla richiesta di verifica preliminare, selezionare il primo articolo riportata di seguito).

####<a name="resources"></a>Risorse

Per ulteriori informazioni sui CORS e come abilitarla, vedere queste risorse.

-   [Condivisione (CORS) supporto per i servizi di archiviazione Azure sul Azure.com delle risorse tra origine](storage-cors-support.md)

    In questo articolo viene fornita una panoramica di CORS e su come impostare le regole per i servizi di archiviazione diversa.

-   [Croce origine condivisore (CORS) supporto per i servizi di archiviazione Azure su MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Questa è la documentazione di riferimento per il supporto CORS per i servizi di archiviazione Azure. Questo è disponibili collegamenti ad articoli applicando a ogni servizio di archiviazione e viene illustrato un esempio e viene spiegato ogni elemento nel file CORS.

-   [Archiviazione di Microsoft Azure: Introduzione a CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Si tratta di un collegamento all'articolo blog iniziale annuncio CORS e che mostra come utilizzarlo.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Domande frequenti sulla sicurezza di archiviazione Azure

1.  **Come è possibile verificare l'integrità del blob che sta il trasferimento o all'esterno di archiviazione Azure se non è possibile usare il protocollo HTTPS?**

    Se per qualsiasi motivo, che è necessario utilizzare HTTP invece HTTPS e si lavora con BLOB blocco, è possibile utilizzare il controllo MD5 per verificare l'integrità del BLOB trasferiti. In questo modo la protezione da errori di livello/trasporto di rete, ma non necessariamente con violazioni.

    Se è possibile utilizzare HTTPS, che offre la protezione a livello di trasporto, quindi utilizzo del controllo MD5 è ridondante e non necessarie.
    
    Per ulteriori informazioni, vedere [Panoramica di MD5 Blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **Informazioni sulla conformità FIPS per governo degli Stati Uniti?**

    Negli Stati Uniti Federal Information Processing Standard (FIPS) definisce algoritmi approvati per l'utilizzo da sistemi di computer governo degli Stati Uniti Federal per la protezione dei dati riservati. Attivazione FIPS modalità su un server di Windows o desktop indica il sistema operativo che devono essere utilizzati solo convalidato FIPS algoritmi. Se un'applicazione utilizza algoritmi non compatibili, verranno interrotte le applicazioni. Versioni con.NET Framework 4.5.2 o versioni successive, l'applicazione passa automaticamente gli algoritmi di crittografia per usare algoritmi compatibili FIPS quando il computer è in modalità FIPS.

    Microsoft decide ogni cliente per decidere se attivare la modalità FIPS. Si ritiene che non esiste alcun motivo valido per i clienti che non sono soggetti a normative per attivare la modalità FIPS per impostazione predefinita.

    **Risorse**

-   [Perché è stiamo non è consigliato "Modalità FIPS" più](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    In questo articolo di blog viene fornita una panoramica di FIPS e spiega perché non consentono la modalità FIPS per impostazione predefinita.

-   [FIPS 140 convalida](https://technet.microsoft.com/library/cc750357.aspx)

    In questo articolo vengono fornite informazioni su come i prodotti Microsoft e moduli di crittografia conformi agli standard FIPS per US enti governativi.

-   ["Crittografia di sistema: usare FIPS algoritmi compatibili per crittografia, l'hashing e firma" effetti impostazioni di protezione in Windows XP e versioni successive di Windows](https://support.microsoft.com/kb/811833)

    In questo articolo illustra l'uso della modalità FIPS nel computer Windows meno recenti.
