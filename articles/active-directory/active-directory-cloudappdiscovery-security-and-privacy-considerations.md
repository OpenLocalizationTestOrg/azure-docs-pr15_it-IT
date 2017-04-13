<properties
    pageTitle="Considerazioni sulla Privacy e sicurezza App individuazione cloud | Microsoft Azure"
    description="Questo argomento illustra gli aspetti di sicurezza e privacy correlati al Cloud App rilevamento."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considerazioni sulla Privacy e sicurezza App individuazione cloud

Microsoft si impegna a proteggere la privacy e proteggere i dati, durante l'esecuzione del software e servizi che consentono di gestire la sicurezza dell'organizzazione. <br>
È riconosciuto che quando si affidare i dati ad altri utenti, che protezione richiede investimenti ingegneristica sicurezza rigido ed esperienze di backup del database.
Microsoft aderisce alle linee guida di sicurezza e conformità strict da procedure di ciclo di vita di sviluppo di software sicura a operare un servizio. <br>
Impostazione della sicurezza e protezione dei dati è una priorità presso Microsoft.

Questo argomento viene illustrato come dati sono raccolte, elaborati e protetti all'interno di rilevamento di Azure Active Directory Cloud App




##<a name="overview"></a>Panoramica

Individuazione di App cloud è una funzionalità di Azure Active Directory ed è ospitato in Microsoft Azure. <br>
L'agente di endpoint Cloud App individuazione viene utilizzato per raccogliere dati di individuazione dell'applicazione dal computer IT gestiti. <br>
I dati raccolti viene inviati in modo sicuro su un canale crittografato al servizio di individuazione di Azure Active Directory Cloud App. <br>
I dati di individuazione di App Cloud per un'organizzazione siano visibili nel portale di Azure. <br>


<center>![Come funziona il Cloud App individuazione](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


Nelle sezioni seguenti vengono seguire il flusso di informazioni e descrivono come è protetto durante il passaggio dall'organizzazione del servizio di individuazione di App Cloud e infine al portale di individuazione di App Cloud.



## <a name="collecting-data-from-your-organization"></a>La raccolta di dati dell'organizzazione

Per utilizzare la funzionalità di rilevamento App Cloud di Azure Active Directory per ottenere informazioni approfondite nelle applicazioni utilizzate da dipendenti all'interno dell'organizzazione, è necessario innanzitutto distribuire l'agente di endpoint di Azure Active Directory Cloud App rilevamento ai computer dell'organizzazione.

Gli amministratori del tenant di Azure Active Directory (o delegati) è possono scaricare il pacchetto di installazione dell'agente dal portale di Azure. L'agente può essere manualmente installato o installato in più computer dell'organizzazione tramite SCCM o criteri di gruppo.

Per ulteriori informazioni sulle opzioni di distribuzione, vedere [Guida alla distribuzione di criteri di gruppo di Cloud App individuazione](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Dati raccolti mediante l'agente

Le informazioni indicate nell'elenco seguente vengono raccolte dall'agente quando viene effettuata una connessione a un'applicazione Web. Le informazioni vengono raccolti solo per le applicazioni che l'amministratore ha configurato per l'individuazione. <br>
È possibile modificare l'elenco di applicazioni basate su cloud che esegue il monitoraggio dell'agente di a e il Cloud App individuazione in Microsoft [Azure portale](https://portal.azure.com/), in **Impostazioni**->**Raccolta di dati**->**elenco insiemi di App**. Per ulteriori informazioni, vedere [Guida introduttiva con Cloud App individuazione](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Categoria di informazioni**: le informazioni utente <br>
**Descrizione**: <br>
Il nome utente di Windows del processo che sono stati resi una richiesta di applicazione Web di destinazione (ad esempio: DOMINIO\nome utente) e Windows protezione identificatore (SID) dell'utente.


**Categoria di informazioni**: informazioni sul processo <br>
**Descrizione**: <br>
Il nome del processo che ha effettuato la richiesta di applicazione Web di destinazione (ad esempio: "iexplore.exe")

**Categoria di informazioni**: le informazioni di computer <br>
**Descrizione**: <br>
Il nome NetBIOS del computer in cui è installato l'agente.

**Categoria di informazioni**: informazioni sul traffico App <br>
**Descrizione**: <br>

Le seguenti informazioni di connessione:

- L'origine (computer locale) e indirizzi IP di destinazione e i numeri di porta

- L'indirizzo IP pubblico dell'organizzazione attraverso il quale la richiesta in uscita.

- Ora della richiesta

- Il volume del traffico inviati e ricevuti

- La versione IP (4 o 6)

- Per le connessioni TLS solo: il nome di estensione indicazione di nome Server o il certificato server host di destinazione.

Le seguenti informazioni HTTP:

- Metodo (GET, POST e così via)

- Protocollo (HTTP/1.1, ecc.)

- Stringa agente utente

- Nome host

- Destinazione URI (esclusi stringa di query)

- Informazioni sul tipo di contenuto

- Informazioni sull'URL di provenienza (esclusi stringa di query)



> [AZURE.NOTE] Raccolte le informazioni HTTP sopra per tutte le connessioni non crittografato.
Per le connessioni TLS, queste informazioni vengono acquisite solo quando l'impostazione di 'Analisi approfondita' è attivato nel portale. L'impostazione è "Sì" per impostazione predefinita.
Per ulteriori informazioni dettagliate, vedere di seguito e [Guida introduttiva con Cloud App individuazione](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Oltre ai dati raccolti l'agente sull'impegno di rete, inoltre raccoglie informazioni anonime il software e configurazione hardware, segnalazioni errori e informazioni sull'utilizzo dell'agente.

<br><br>
### <a name="how-the-agent-works"></a>Funzionamento dell'agente

L'installazione dell'agente include due componenti:

- Un componente in modalità utente

- Un componente di driver modalità kernel (driver di piattaforma filtro Windows)



Quando l'agente prima di tutto è installato memorizza un certificato attendibile specifiche del computer nel computer che utilizza quindi per stabilire una connessione protetta con il servizio di individuazione di App Cloud. <br>
L'agente recupera periodicamente configurazione dei criteri dal servizio di individuazione di App Cloud questa connessione sicura. <br>
Il criterio include informazioni su quali applicazioni cloud monitor e se l'aggiornamento automatico deve essere abilitato, tra le altre cose.

Mentre il traffico Web viene inviato e ricevuto nel computer da Internet Explorer e Chrome, l'agente di rilevamento App Cloud analizza il traffico ed estrae i metadati pertinenti (vedere la sezione **dati raccolti dall'agente** precedente). <br>
Minuto, l'agente di carica i metadati raccolti al servizio di individuazione di App Cloud su un canale crittografato.

Il componente driver intercetta il traffico crittografato e stesso inserisce nel flusso crittografato. Ulteriori dettagli nella sezione **dati Intercepting da connessioni crittografate (analisi approfondita)** .


### <a name="respecting-user-privacy"></a>Rispettare la tutela della privacy

Il nostro obiettivo è fornire agli amministratori gli strumenti per impostare l'equilibrio tra dettagliata ottica in informativa sulla privacy di utilizzo e utente dell'applicazione in base alle esigenze per l'organizzazione. A tale scopo, sono disponibili i comandi seguenti nella pagina Impostazioni nel portale:

- **Raccolta di dati**: gli amministratori possono scegliere per specificare quali applicazioni o categorie di applicazioni che desiderano recuperare i dati di individuazione.

- **Analisi approfondita**: gli amministratori possono scelto di specificare se l'agente raccoglie il traffico HTTP per le connessioni SSL/TLS (o **' analisi approfondita '**). Ulteriori informazioni su questo nella sezione successiva.

- **Opzioni di consenso**: gli amministratori possono utilizzare il portale Cloud App individuazione di scegliere se informare gli utenti della raccolta di dati da parte dell'agente e se richiedere utente consenso prima dell'avvio di agente la raccolta di dati utente.

L'agente di endpoint Cloud App individuazione raccoglie le informazioni descritte nella sezione **dati raccolti dall'agente** precedente.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Intercettazione dei dati da connessioni crittografate (analisi approfondita)
Come indicato in precedenza, gli amministratori possono configurare l'agente per monitorare i dati da connessioni crittografate ('analisi approfondita'). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) corrisponde a uno dei protocolli più comuni in uso su Internet oggi. Per la crittografia delle comunicazioni con TLS, un client può stabilire un canale di comunicazione protette con un server web. TLS contiene essenziali di protezione per il passaggio delle credenziali di autenticazione ed evitare la divulgazione delle informazioni riservate.

Mentre il canale crittografato protetto-to-end fornito da TLS consente importante sicurezza e protezione della privacy, il protocollo è spesso oggetto di abusi scopi dannoso o nefandi. Molto così infatti, tale TLS viene spesso come "firewall-bypass universale protocollo". La radice del problema è che la maggior parte dei firewall non è possibile esaminare la comunicazione TLS poiché i dati a livello di applicazioni vengono crittografati con SSL. Pertanto, gli utenti malintenzionati sfruttano spesso TLS per recapitare payload dannoso a un utente certo che anche il firewall a livello di applicazione più intelligente completamente non vedenti a TLS e deve semplicemente inoltrare la comunicazione TLS tra host. Gli utenti finali sfruttare spesso TLS per evitare di accedere ai controlli stabilite dal loro firewall aziendali e di un server proxy, utilizzo per connettersi al proxy pubblico e per protocolli non TLS attraverso il firewall che in caso contrario potrebbe essere bloccato da criteri di tunneling.

Analisi approfondita consente l'agente di rilevamento App Cloud come un attendibile uomo-in-diretti. Quando viene effettuata una richiesta di client per accedere a una risorsa HTTPS protetta, il driver Endpoint agente intercetta la connessione e stabilisce che una nuova connessione al server di destinazione per recupera il certificato SSL per conto del cliente. Agente di verifica che il certificato può essere considerati attendibili (per il controllo non è stato revocato ed eseguire altre verifiche certificato) e, se questi passaggi, quindi l'agente di Endpoint consente di copiare le informazioni dal certificato server crea il proprio certificato server, ossia noto come un certificato di intercettazione - utilizzo di tali informazioni. Il certificato di intercettazione è firmato il volo dall'agente di endpoint con un certificato, che è installato nell'archivio certificati attendibili di Windows. Il certificato autofirmato è contrassegnato come non esportabile e ACL sia per gli amministratori. Si intende mai lasciare il computer in cui è stato creato. Quando l'applicazione client dell'utente finale riceve il certificato di intercettazione, consideri attendibili perché convalidare correttamente la catena di certificati verso il certificato di autenticazione. Questo processo è principalmente trasparente dal punto di vista dell'utente finale con alcune considerazioni come descritto di seguito.

Abilitando analisi approfondita Cloud App Discovery Endpoint Agent possibile decrittografare e controllarne comunicazioni TLS crittografata, consentendo il servizio per ridurre il rumore e fornire informazioni approfondite sull'utilizzo delle App cloud crittografato.

#### <a name="a-word-of-caution"></a>Una parola di attenzione
Prima di attivare analisi approfondita, si consiglia di comunicare le intenzioni a legali e reparti delle risorse Umane e ottenere il proprio consenso. Il controllo delle comunicazioni crittografate privato dell'utente finale può essere soggetto riservato, per motivi di più evidenti. Prima di una produzione introduzione di analisi approfondita, verificare che la sicurezza aziendale e criteri di utilizzo sono stati aggiornati per indicare che verrà controllate alla ricerca di comunicazione crittografato. Notifica all'utente ed esenzione dei siti considerate riservate (ad esempio bancari e medici) potrebbe anche essere necessarie se si configura l'individuazione di App Cloud per eseguire il monitoraggio delle loro. Come detto in precedenza, gli amministratori possono utilizzare il portale Cloud App individuazione scegliere se si desidera informare gli utenti della raccolta di dati da parte dell'agente e richiedere il consenso dell'utente prima dell'avvio di agente di raccolta dati utente.

### <a name="known-issues-and-drawbacks"></a>Problemi noti e svantaggi
Esistono alcuni casi in cui intercettazione TLS potrebbe influire negativamente l'esperienza utente finale:

- Certificati di convalida estesa "Extended" il rendering di barra degli indirizzi del browser web verde funga da una traccia visiva che si sta visitando un sito web attendibile. Controllo TLS non è possibile duplicare EV certificato che emette al client, in modo che i siti web che utilizzano i certificati EV funzionerà normalmente ma verde non sono visualizzati la barra degli indirizzi.  

- Pubblica chiave blocco (noto anche come certificato blocco) sono progettati per aiutare a proteggere gli utenti da attacchi uomo nel secondo e malintenzionati autorità di certificazione. Quando il certificato per un sito bloccato non corrisponde a uno dei nota CA valida, il browser rifiuta la connessione con un errore. Poiché intercettazione TLS, infatti, un uomo-in-diretti, le connessioni avrà esito negativo.

- Se l'utente fa clic sull'icona del lucchetto nel browser barra indirizzo del browser per esaminare le informazioni sul sito, non vedranno una catena che terminano in Autorità di certificazione usato per firmare il certificato di sito Web, ma invece una catena di certificati che terminano con le finestre attendibile archivio certificati.

Per ridurre le occorrenze di questi problemi, è tenere traccia di servizi cloud e le applicazioni client note usare convalida estesa o blocco chiave pubblica e indicare l'agente di Endpoint per evitare di intercettazione connessioni interessate. Anche in questi casi, si riceverà comunque report con l'uso di queste applicazioni basate su cloud e il volume di dati da trasferire, tuttavia, dal momento che non sono complete controllate alla ricerca, dettagli utilizzo delle App sarà disponibili.


## <a name="sending-data-to-cloud-app-discovery"></a>Inviare dati a Cloud App individuazione

Una volta metadati sono stati raccolti dall'agente, è cache del computer per un minuto o fino a quando i dati memorizzati nella cache raggiunge le dimensioni di 5MB. È quindi compressi e inviato tramite una connessione protetta del servizio di individuazione di App Cloud.

Se l'agente non riesce a comunicare con il servizio di individuazione di App Cloud per qualsiasi motivo, i metadati raccolti vengono memorizzati nella cache del file locale accessibili solo tramite gli utenti del computer (ad esempio gruppo Administrators). <br>
L'agente automaticamente tenta di inviare di nuovo i metadati memorizzati nella cache fino a quando non è stato ricevuto correttamente dal servizio Cloud App individuazione.



## <a name="receiving-the-data-at-the-service-end"></a>Ricezione di dati alla fine del servizio

Eseguire l'autenticazione gli agenti per l'individuazione di App Cloud servizio utilizzando il certificato di autenticazione di computer client specifico a cui fa riferimento di sopra e inoltra dati su un canale crittografato. <br>
Pipeline analitica del servizio Cloud App individuazione elabora i metadati per ogni cliente separatamente, in modo logico partizioni in tutte le fasi della pipeline analitica.
I metadati analizzati unità vari report nel portale.

I metadati non elaborati e i metadati analizzati vengono archiviate per 180 giorni. Inoltre, i clienti possono scegliere acquisire i metadati analizzati in un account di archiviazione blob Azure specifiche.
Questo è utile per analisi non in linea dei metadati, nonché più criteri di conservazione dei dati.

## <a name="accessing-the-data-using-the-azure-portal"></a>Accesso ai dati tramite il portale di Azure

Nel tentativo di proteggere i metadati raccolti, per impostazione predefinita solo gli amministratori globali del tenant hanno accesso alle funzionalità di rilevamento App Cloud nel portale di Azure. <br>
Tuttavia, gli amministratori possono scegliere delegare questo accesso ad altri utenti o gruppi.



> [AZURE.NOTE] Per ulteriori informazioni, vedere [Guida introduttiva con Cloud App individuazione](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Tutti gli utenti di accedere ai dati nel portale, deve essere autorizzato con una licenza di Azure Active Directory Premium.



##<a name="additional-resources"></a>Risorse aggiuntive


* [Come è possibile a individuare App cloud unsanctioned utilizzati nell'organizzazione](active-directory-cloudappdiscovery-whatis.md)
* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
