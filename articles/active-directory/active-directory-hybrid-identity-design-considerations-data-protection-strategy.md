<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - definire strategia di protezione dei dati | Microsoft Azure"
    description="È necessario definire strategia di protezione dei dati per la soluzione di identità ibrido soddisfare le esigenze aziendali definito."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definire una strategia di protezione dei dati per la soluzione di identità ibrido

In questa attività, è necessario definire strategia di protezione dei dati per la soluzione di identità ibrido soddisfare i requisiti di business definiti in:

- [Determinare i requisiti di protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Determinare i requisiti di gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Requisiti di controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Determinare i requisiti di risposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Impostare le opzioni di protezione dei dati
Come è stato illustrato in [requisiti di sincronizzazione directory determinazione](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure Active Directory possono essere sincronizzate con i servizi di dominio Active Directory (AD DS) disponibile in locale. Questa integrazione consente alle organizzazioni di sfruttare Azure Active Directory per verificare le credenziali dell'utente quando si tenta di accedere alle risorse aziendali. Questa operazione può essere eseguita per entrambi gli scenari: dati nel resto locale e nel cloud.  Accesso ai dati di Azure Active Directory richiede l'autenticazione utente tramite un servizio token di sicurezza (servizio token di sicurezza).

Una volta autenticato, il nome dell'entità utente (UPN) verrà letti da token di autenticazione e la partizione replicata e viene determinato contenitore corrispondente al dominio dell'utente. Informazioni sulla presenza dell'utente, stato attivato e ruolo viene utilizzate il sistema di autorizzazione per determinare se l'accesso richiesto nel tenant di destinazione è autorizzato per l'utente in questa sessione. Determinate azioni autorizzati (in particolare, creare gli utenti, reimpostare password) creare un audit trail che può essere utilizzato da un amministratore tenant per la gestione della conformità o indagini.

Spostamento di dati dal Data Center locale in archiviazione Azure tramite una connessione Internet potrebbe non essere sempre possibile a causa di volume di dati, la disponibilità della larghezza di banda o altre considerazioni. Il [Servizio di importazione/esportazione di archiviazione di Azure](../storage/storage-import-export-service.md) fornisce un'opzione per posizionare/il recupero di volumi elevati di dati in archiviazione blob basate sull'hardware. È possibile inviare [crittografati con BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) unità disco rigido direttamente a un Data Center Azure nel punto in cui gli operatori cloud saranno caricato il contenuto al proprio account di archiviazione o è possibile scaricare i dati di Azure alle unità per tornare all'utente. Per questo processo, mediante una chiave di BitLocker generata dal servizio durante l'installazione di processo, vengono accettati solo dischi crittografati. Il tasto BitLocker viene fornito in Azure separatamente, in modo da fornire fuori banda chiave condivisione.

Poiché dati durante il transito possono avvenire in diversi scenari, è anche importante conoscere che Microsoft Azure utilizza [la rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per isolare il traffico dei tenant rispetto a altro, utilizzo di misure, ad esempio firewall di livello host e guest, filtri dei pacchetti IP, blocco delle porte e i punti finali HTTPS. Tuttavia, la maggior parte delle comunicazioni interne di Azure, inclusi infrastruttura per l'infrastruttura e infrastruttura al cliente (locale) anche crittografata. Un altro scenario importante è le comunicazioni all'interno di Azure Data Center; Microsoft gestisce le reti per assicurare che nessun macchine Virtuali possono rappresentare o intercettare sull'indirizzo IP di un altro. SSL/TLS viene utilizzata quando si accede archiviazione Azure o SQL database o quando ci si connette ai servizi Cloud. In questo caso, l'amministratore di cliente è responsabile per ottenere un certificato SSL/TLS e distribuirlo infrastruttura tenant. È possibile proteggere il traffico dati lo spostamento tra macchine virtuali nella stessa distribuzione o tra tenant situati in un'unica distribuzione tramite virtuali di Microsoft Azure attraverso i protocolli di comunicazione crittografato, ad esempio HTTPS, SSL/TLS o gli altri utenti.

A seconda di come la risposta alle domande [Determinazione requisiti di protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), dovrebbe essere possibile determinare la modalità di protezione dei dati e come la soluzione di identità ibrido viene spiegato in che. Nella tabella sono le opzioni supportate da Azure sono disponibili per ogni scenario di protezione dei dati.


| Opzioni di protezione dei dati         | Inattivi nel cloud | Nella parte restante locale | Durante il transito |
|---------------------------------|----------------------|---------------------|------------|
| Crittografia unità BitLocker      | X                    | X                   |            |
| SQL Server per crittografare database | X                    | X                   |            |
| Crittografia macchine Virtuali in macchine Virtuali             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Leggere [conformità dalla caratteristica](https://azure.microsoft.com/support/trust-center/services/) in [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/) per ulteriori informazioni sul qualifiche ogni servizio Azure è compatibile con.
Poiché le opzioni per la protezione dei dati utilizzano un approccio a più livelli, confronto tra queste opzioni non sono applicabili per questa attività. Assicurarsi che siano sfruttare tutte le opzioni disponibili per ogni stato che i dati saranno.

## <a name="define-content-management-options"></a>Impostare le opzioni di gestione dei contenuti
Uno dei vantaggi dell'uso di Azure Active Directory per gestire un'infrastruttura di identità ibrida è che il processo è completamente trasparente dal punto di vista dell'utente finale. L'utente tenterà di accedere a una risorsa condivisa, la risorsa richiede l'autenticazione, l'utente avrà inviare una richiesta di autenticazione a Azure Active Directory per ottenere il token e accedere alla risorsa. L'intero processo viene eseguita in background, senza interazione dell'utente. È inoltre possibile concedere l'autorizzazione a un [gruppo](active-directory-manage-groups.md#getting-started-with-access-management) di utenti per consentire loro di eseguire alcune operazioni comuni.

Le organizzazioni che sono in genere dubbi riguardo la privacy dei dati richiedono classificazione di dati per la soluzione. Se la propria infrastruttura locale corrente è già in uso classificazione di dati, è possibile sfruttare Azure Active Directory come archivio principale per l'identità dell'utente. Uno strumento comune che si tratti di usati locale per la classificazione di dati è denominato [Dati classificazione Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) per Windows Server 2012 R2. Questo strumento consente di identificare, classificare e proteggere i dati nei file server del cloud privato. È anche possibile sfruttare la [Classificazione automatica di File](https://technet.microsoft.com/library/hh831672.aspx) in Windows Server 2012 per eseguire questa operazione.

Se l'organizzazione non disporre di classificazione dati ma è necessario proteggere i file riservati senza aggiungere nuovi server in locale, è possibile utilizzare Microsoft [Servizio di Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS viene usata la crittografia, l'identità e criteri di autorizzazione per proteggere i file e un messaggio di posta elettronica e funziona su più dispositivi, telefoni e Tablet PC. Poiché Azure RMS è un servizio cloud, è necessario configurare in modo esplicito trust con altre organizzazioni prima di poter condividere contenuto protetto con loro. Se si dispone già di una directory di Azure Active Directory o di Office 365, la collaborazione tra più organizzazioni automaticamente è supportata. È inoltre possibile sincronizzare solo gli attributi di directory Azure RMS deve supportare un'identità comune per gli account di Active Directory locale, tramite servizi di sincronizzazione Active Directory Azure (AAD Sync) o Azure AD Connect.

È fondamentale di gestione dei contenuti sapere chi accede a quale risorsa, pertanto la funzionalità di registrazione RTF è importante per la soluzione di gestione delle identità. Azure Active Directory fornisce log oltre 30 giorni inclusi:

- Modifiche all'appartenenza ai ruoli (ex: utente aggiunto al ruolo amministratore globale)
- Gli aggiornamenti delle credenziali (ex: modifica della password)
- Gestione dei domini (ex: verificare un dominio personalizzato, la rimozione di un dominio)
- Aggiunta o rimozione di applicazioni
- Gestione degli utenti (ex: aggiunta, rimozione, l'aggiornamento di un utente)
- Aggiunta o rimozione di licenze

>[AZURE.NOTE]
Leggere [Microsoft Azure sicurezza e la gestione dei registri di controllo](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) per ulteriori informazioni sulle funzionalità di registrazione in Azure.
A seconda di come la risposta alle domande [Determinazione](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)dei requisiti di gestione dei contenuti, dovrebbe essere possibile determinare la modalità di contenuto da gestire della soluzione di identità ibrida. Benché tutte le opzioni esposte nella tabella 6 siano in grado di integrazione con Azure Active Directory, è importante definire più adatto alle proprie esigenze aziendali.

| Opzioni di gestione dei contenuti                                                               | Vantaggi                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Svantaggi                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralizzata locale (Server Active Directory Rights Management)                      | Controllo completo dell'infrastruttura di server responsabile per la classificazione dei dati <br> Funzionalità incorporata in Windows Server non è più necessaria licenza aggiuntiva o l'abbonamento <br> Può essere integrato con Azure Active Directory in uno scenario ibrido <br> Supporta Rights management (IRM) di informazioni in Microsoft Online services, ad esempio Exchange Online e SharePoint Online, nonché Office 365 <br> Supporta i prodotti server Microsoft locale, ad esempio Exchange Server, SharePoint Server e file server che eseguono Windows Server e infrastruttura di classificazione File (cliente). | Superiore, manutenzione (continuare con gli aggiornamenti, configurazione e aggiornamenti potenziali), dall'IT proprietario del Server <br> Richiedere un'infrastruttura server in locale<br> Doesn'tleverage funzionalità Azure a livello nativo                                     |
| Centralizzata nel cloud (Azure RMS)                                                     | Più semplice da gestire rispetto alla soluzione locale <br> Può essere integrato con Active Directory in uno scenario ibrido <br>  Completamente integrato con Azure Active Directory <br> Non richiede un server locale per distribuire il servizio <br> Supporta locale prodotti server Microsoft, ad esempio Exchange Server, SharePoint, Server e file server che eseguono Windows Server e classificazione dei File, infrastruttura del cliente <br> IT, può avere il controllo completo chiave del loro tenant con la funzionalità di BYOK.                                                                                    | L'organizzazione deve avere un abbonamento cloud che supporta RMS <br> L'organizzazione deve essere una directory di Azure Active Directory per supportare l'autenticazione utente per RMS                                                                                  |
| Ibrido (integrato con Azure RMS, locale Active Directory Rights Management Server) | Questo scenario sono indicati alcuni vantaggi entrambi, centralizzata in locale e nel cloud.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | L'organizzazione deve avere un abbonamento cloud che supporta RMS <br> L'organizzazione deve essere una directory di Azure Active Directory per supportare l'autenticazione utente per RMS, <br> Richiede una connessione tra servizio cloud Azure e infrastruttura locale |

## <a name="define-access-control-options"></a>Impostare le opzioni di controllo di accesso
Sfruttando l'autenticazione, autorizzazione e controllo dell'accesso funzionalità disponibili in Azure Active Directory sarà possibile abilitare la società da utilizzare un repository centrale identità consentendo agli utenti e i partner per utilizzare single sign-on (SSO), come illustrato nella figura seguente:

![](./media/hybrid-id-design-considerations/centralized-management.png)

La gestione centralizzata e completamente l'integrazione con altre directory

Azure Active Directory offre accesso singolo migliaia di applicazioni SaaS e le applicazioni web locale. Leggere la [elenco di compatibilità di Azure Active Directory federation: provider di identità di terze parti che possono essere utilizzati per implementare il single sign-on](https://msdn.microsoft.com/library/azure/jj679342.aspx) articolo per altri dettagli sul Single Sign-on di terze che sono stati testati da Microsoft. Questa funzionalità consente di implementare diversi scenari B2B e mantenendo al contempo controllo di gestione delle identità e accessi. Tuttavia, durante la B2B progettazione processo è importante conoscere bene il metodo di autenticazione che verrà utilizzato dal partner e verificare se questo metodo è supportato da Azure. Attualmente sono metodi supportati da Azure Active Directory:

- Protezione asserzione Markup Language (SAML)
- OAuth
- Kerberos
- Token
- Certificati


>[AZURE.NOTE] leggere i [Protocolli di autenticazione di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) per conoscere maggiori dettagli sulle ogni protocollo e le relative funzionalità di Azure.

Utilizzando il supporto di Azure Active Directory, applicazioni aziendali per dispositivi mobili possono utilizzare la stessa esperienza di autenticazione servizi mobili facile per consentire ai dipendenti di accedere all'interno delle applicazioni per dispositivi mobili con le proprie credenziali di Active Directory aziendale. Con questa caratteristica è supportata Azure Active Directory come provider di identità in servizi mobili insieme a con l'altri provider di identità è già supportato (che includono Microsoft Accounts, ID Facebook, Google ID e ID Twitter). Se le app locale utilizza le credenziali dell'utente che si trova in Active Directory della società, l'accesso dal partner e i relativi utenti provenienti dal cloud deve essere trasparente. È possibile controllare l'accesso condizionale dell'utente per le applicazioni web (basato su cloud), dell'API web, servizi cloud Microsoft, 3 ° applicazioni SaaS di terze parti e applicazioni native client (cellulare) e sono i vantaggi della sicurezza per il controllo, creazione di report in un'unica posizione. Tuttavia, è consigliabile convalidare in un ambiente di produzione o con un numero limitato di utenti.


>[AZURE.TIP] è importante ricordare che Azure Active Directory non dispone di criteri di gruppo come Active Directory ha. Per applicare i criteri per i dispositivi sono necessari una soluzione di gestione di dispositivi mobili, ad esempio [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Dopo l'utente autenticato con Azure Active Directory, è importante valutare il livello di accesso che l'utente avrà il. Il livello di accesso che l'utente avrà su una risorsa può variare, mentre Azure Active Directory è possibile aggiungere un ulteriore livello di sicurezza controllando l'accesso ad alcune risorse, è necessario anche tenere presente che la risorsa stessa avere anche il proprio elenco di controllo accesso separatamente, ad esempio il controllo di accesso per i file memorizzati in un File Server. Nella figura seguente vengono riepilogati i livelli di controllo di accesso che è possibile inserire in uno scenario ibrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Ogni interazione nel diagramma ha mostrato nella figura X rappresenta uno scenario di controllo di accesso che può essere coperta dal Azure Active Directory. Di seguito sono disponibili una descrizione di ogni scenario:

1 condizionale accesso alle applicazioni che sono ospitati in locale: È possibile utilizzare i dispositivi registrati con i criteri di accesso per le applicazioni che configurate per l'utilizzo di ADFS con Windows Server 2012 R2. Per ulteriori informazioni sull'impostazione di accesso condizionato per locale, vedere [impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](active-directory-conditional-access-on-premises-setup.md).
2. accedere controllo al portale di gestione di Azure: Azure ha anche la possibilità di controllare l'accesso al portale di gestione utilizzando RBAC (in base a controllo dell'accesso ruoli). Questo metodo consente all'azienda di limitare la quantità di operazioni che un singolo utente può eseguire una volta ha accesso al portale di gestione Azure. Utilizzando RBAC per controllare l'accesso al portale di ca gli amministratori IT delega accesso tramite accesso gestione dei metodi seguenti:

 - Assegnazione di gruppo in base al ruolo: È possibile assegnare l'accesso a gruppi di Azure Active Directory che possono essere sincronizzati da Active Directory locale. In questo modo è possibile sfruttare investimenti produttrice utensili e processi per la gestione dei gruppi dell'organizzazione. È anche possibile utilizzare la funzionalità di gestione gruppo delegato di Azure Active Directory Premium.
 - Fattore integrato in ruoli di Azure: È possibile utilizzare tre ruoli, ovvero proprietari, collaboratori e Reader, per assicurarsi che gli utenti e gruppi sono autorizzati a eseguire solo le attività necessarie per svolgere il proprio lavoro.
 - Accesso granulare alle risorse: È possibile assegnare i ruoli a utenti e gruppi per un particolare abbonamento, gruppo di risorse o una singola risorsa Azure ad esempio un sito Web o un database. In questo modo, è possibile assicurarsi che gli utenti hanno accesso a tutte le risorse che necessarie e nessun accesso alle risorse che non è necessario gestire.

>[AZURE.NOTE] [controllo dell'accesso basato sui ruoli in Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) per conoscere maggiori dettagli su questa funzionalità di lettura. Per gli sviluppatori che la creazione di applicazioni e si desidera personalizzare il controllo di accesso per tali, è anche possibile utilizzare i ruoli di applicazione Azure Active Directory per l'autorizzazione. Esaminare in questo [esempio DotNet di RoleClaims Web App](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) su come creare l'app per utilizzare questa funzionalità.

3 accesso condizionato per le applicazioni di Office 365 con Microsoft Intune: gli amministratori IT possono effettuare il provisioning di criteri di dispositivo accesso condizionato per proteggere risorse aziendali, mentre nello stesso momento consentendo agli information worker nei dispositivi conformi di accedere ai servizi. Per ulteriori informazioni, vedere [Condizionale i criteri di dispositivo di accesso per i servizi di Office 365](active-directory-conditional-access-device-policies.md).

4 accesso condizionato per le applicazioni Saas: [questa caratteristica](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) consente di configurare le regole di accesso di autenticazione a più fattori per applicazione e la possibilità di bloccare l'accesso per gli utenti non in una rete attendibile. È possibile applicare le regole di autenticazione a più fattori a tutti gli utenti assegnati all'applicazione o solo per gli utenti all'interno di gruppi di sicurezza specificato. Gli utenti possono essere esclusi dal requisito autenticazione a più fattori se accedono l'applicazione da un indirizzo IP all'interno dell'organizzazione di rete.

Poiché le opzioni per il controllo dell'accesso utilizzano un approccio a più livelli, confronto tra queste opzioni non sono applicabili per questa attività. Assicurarsi che siano sfruttare tutte le opzioni disponibili per ogni scenario che richiede di controllare l'accesso alle risorse.

## <a name="define-incident-response-options"></a>Impostare le opzioni di risposta
Consentono di Azure Active Directory IT identità potenziali rischi nell'ambiente mediante il monitoraggio delle attività dell'utente, IT consente di sfruttare le Azure AD Access e report sull'utilizzo di funzionalità per ottenere la visibilità sull'integrità e la sicurezza della directory dell'organizzazione. Con queste informazioni, un amministratore IT può determinare meglio nel punto in cui può trovarsi possibili rischi di sicurezza in modo da potervi adeguatamente a ridurre i rischi.  [Sottoscrizione di Azure Active Directory Premium](active-directory-get-started-premium.md) è disponibile una serie di report di sicurezza che consentono IT per ottenere queste informazioni. [Report di Azure Active Directory](active-directory-view-access-usage-reports.md) vengono classificati come illustrato di seguito:

- **Report anomalia**: contengono gli eventi che sono stati rilevati da anomala di accesso. Il nostro obiettivo è informare l'utente di attività e consentono di essere in grado di un'operazione sulle sospetto un evento.
- **Report di applicazione integrata**: fornisce informazioni approfondite in uso delle applicazioni cloud all'interno dell'organizzazione. Azure Active Directory offre l'integrazione con migliaia di applicazioni cloud.
- **Report di errore**: indicare gli errori che possono verificarsi durante il provisioning di account in applicazioni esterne.
- **Report specifici dell'utente**: visualizzare dispositivo/accesso nei dati di attività per un utente specifico.
- **Registri delle attività**: contengono un record di tutti gli eventi controllati all'interno delle ultime 24 ore, ultimi 7 giorni, o ultimi 30 giorni, nonché modifiche dell'attività di gruppo e attività di registrazione e reimpostare la password.

>[AZURE.TIP]
Un altro report che anche aiutare il team di risposta incidente lavorando a un caso è [l'utente con credenziali persa](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Questo report aree le corrispondenze tra queste sono elencate le credenziali persa e il tenant.

Altri importanti integrato nei rapporti di Azure Active Directory che può essere utilizzato durante un'analisi di risposta e sono:

- **Attività di reimpostazione della password**: offrire l'amministrazione approfondite come attivamente la reimpostazione della password è in uso nell'organizzazione.
- **Attività di registrazione la reimpostazione della password**: fornisce informazioni approfondite in cui gli utenti hanno registrato i metodi per la reimpostazione della password, e metodi che hanno selezionato.
- **Attività di gruppo**: fornisce una cronologia delle modifiche al gruppo (ex: gli utenti aggiunti o rimossi) che sono state avviate nel Pannello di accesso.

Oltre alle funzionalità di creazione di report principali disponibili in Azure Active Directory Premium che possono essere utilizzati durante il processo di analisi di risposta incidente IT consente inoltre sfruttare Report di controllo per ottenere informazioni come:

- Modifiche all'appartenenza ai ruoli (ex: utente aggiunto al ruolo amministratore globale)
- Gli aggiornamenti delle credenziali (ex: modifica della password)
- Gestione dei domini (ex: verificare un dominio personalizzato, la rimozione di un dominio)
- Aggiunta o rimozione di applicazioni
- Gestione degli utenti (ex: aggiunta, rimozione, l'aggiornamento di un utente)
- Aggiunta o rimozione di licenze

Poiché le opzioni per intervento utilizzano un approccio a più livelli, confronto tra queste opzioni non sono applicabili per questa attività. Assicurarsi che siano sfruttare tutte le opzioni disponibili per ogni scenario che è necessario utilizzare la funzionalità di creazione di report di Azure Active Directory come parte del processo di risposta della società.

## <a name="next-steps"></a>Passaggi successivi
[Determinare l'attività di gestione delle identità ibrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
