<properties
    pageTitle="La protezione dell'identità di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come la protezione dell'identità di Azure Active Directory consente di limitare la possibilità degli utenti malintenzionati per sfruttare un'identità compromesso o dispositivo e proteggere un'identità o un dispositivo che in precedenza è stato sospetto o noto per essere compromessa."
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
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
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Protezione dell'identità di Azure Active Directory 

Azure protezione di Active Directory identità è una funzionalità dell'edizione P2 Azure Active Directory Premium che offre una panoramica di eventi di rischio e potenziali vulnerabilità influisce negativamente sulla identità dell'organizzazione. Microsoft è stato protezione basato sul cloud delle identità per su dieci anni e con la protezione dell'identità di Azure Active Directory, Microsoft sta questi stessi sistemi di protezione disponibili per i clienti aziendali. Protezione dell'identità utilizza funzionalità di rilevamento anomalia esistente Azure Active Directory (disponibile tramite il report attività anomala di Azure Active Directory) e sono disponibili nuovi tipi di evento di rischio in grado di rilevare alterazioni in tempo reale.



##<a name="getting-started"></a>Guida introduttiva

La maggior parte delle violazioni della protezione eseguita quando gli utenti malintenzionati l'accesso a un ambiente furto di identità dell'utente. Gli utenti malintenzionati hanno diventano sempre più efficaci sfruttando violazioni di terze parti e l'utilizzo di attacchi di phishing sofisticate. Una volta un pirata informatico riesce ad accedere a anche un account utente con privilegi limitati, è semplice per poter accedere alle risorse aziendali importanti tramite movimento laterale. È pertanto essenziale proteggere tutte le identità e, quando un'identità è compromessa, tempestiva impedisce che l'identità compromesso abusati. 

Individuazione delle identità compromesse non è alcuna operazione semplice. Per fortuna consigliabile eseguirla per la protezione dell'identità: protezione dell'identità utilizza algoritmi adattata apprendimento ed euristica per rilevare alterazioni e gli eventi che possono indicare che è stata compromessa un'identità di rischio.
 
Usare questi dati, la protezione dell'identità genera report e avvisi che consente di analizzare questi eventi di rischio e non viene accettato rimedio appropriate o attenuazione.
 
Ma la protezione di Azure Active Directory identità è maggiore di uno strumento di monitoraggio e creazione di report. In base a eventi di rischio, la protezione dell'identità calcola un livello di rischio utente per ogni utente, che consente di configurare i criteri basati sui rischi per proteggere automaticamente l'identità dell'organizzazione.  Oltre ad altri controlli di accesso condizionato forniti da Azure Active Directory e questa funzionalità, questi criteri basati sui rischi automaticamente bloccare o offrono le azioni di monitoraggio adattata che includono la reimpostazione della password e l'applicazione di autenticazione a più fattori.  

####<a name="explore-identity-protections-capabilities"></a>Esplorare le funzionalità di protezione dell'identità 

**Rilevamento degli eventi di rischio e rischioso account:**  

- Rilevamento 6 tipi di evento di rischio utilizzando regole euristiche e apprendimento 

- Il calcolo dei livelli di rischio dell'utente

- Consigli personalizzati per migliorare la sicurezza complessiva del sistema evidenziando vulnerabilità



**In analisi eventi di rischio:**

- Invio di notifiche per eventi di rischio

- In analisi eventi di rischio utilizzando le informazioni pertinenti e contestuali

- Fornire flussi di lavoro di base per tenere traccia delle indagini

- Che consente di accedere facilmente alle azioni di correzione, ad esempio la reimpostazione della password



**Criteri basati sui rischi condizionale access:**

- Criteri per ridurre i componenti aggiuntivi di accesso rischiosi da bloccare gli accessi o la richiesta di problemi di autenticazione a più fattori.

- Criteri per blocco o gli account utente rischiosa sicura

- Criteri per richiedere agli utenti di registrarsi per l'autenticazione a più fattori


## <a name="detection-and-risk"></a>Rilevamento e i rischi

### <a name="risk-events"></a>Eventi di rischio

Eventi di rischio sono eventi contrassegnati come sospetto protezione dell'identità e indicano che un'identità è compromessa. Per un elenco completo degli eventi di rischio, vedere [tipi di eventi di rischio rilevati da Azure Active Directory identità protezione](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Livello di rischio

Il livello di rischio per un evento di rischio è un'indicazione di gravità dell'evento di rischio (elevato, medio o basso). Il livello di rischio consente agli utenti di protezione dell'identità di definire le azioni da seguire per ridurre il rischio all'organizzazione. Gravità dell'evento di rischio rappresenta l'intensità del segnale previsione di violazione di identità, combinati con la quantità di rumore introduce in genere. 

- **Altezza**: confidenza elevata ed evento di rischio gravità elevato. Questi eventi sono indicatori sicuri che l'identità dell'utente sia stata compromessa e tutti gli account utente interessati devono essere risolte immediatamente.

- **Supporto**: gravità elevata, ma evento di rischio confidenza inferiore, o viceversa. Questi eventi sono potenzialmente pericolosi e tutti gli account utente interessati devono essere risolte.

- **Bassa**: bassa confidenza ed evento di rischio gravità bassa. Questo evento potrebbe non richiede un'azione immediata, ma in combinazione con altri eventi di rischio, può fornire un'indicazione sicura che l'identità è compromessa. 


![Livello di rischio] (./media/active-directory-identityprotection/01.png "Livello di rischio")

 

Eventi di rischio identificati sia **tempo reale**, o post-elaborazione dopo l'evento di rischio già state inserite (offline). Attualmente maggior parte degli eventi di rischio in protezione dell'identità vengono calcolata offline e visualizzare protezione dell'identità all'interno di 2 a 4 ore. Durante la valutazione in tempo reale, gli eventi di rischio in tempo reale appariranno nel Console di protezione di identità all'interno di 5-10 minuti.

Diversi client legacy non supportano attualmente prevenzione e rilevamento di eventi di rischio in tempo reale. Di conseguenza, gli accessi da questi client non rilevati o impediti in tempo reale.


## <a name="investigation"></a>Indagini
Il viaggio tramite la protezione dell'identità in genere inizia con il dashboard di protezione dell'identità. 

![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection/1000.png "Monitoraggio e aggiornamento")

Il dashboard consente di accedere a:
 
- Report, ad esempio **utenti contrassegno per il rischio**, **rischi** e **vulnerabilità**
- Impostazioni, ad esempio la configurazione dei **Criteri di sicurezza**, **le notifiche** e **la registrazione di autenticazione a più fattori**
 

In genere è il punto di partenza per indagini, ossia il processo di revisione alle attività, i registri e altre informazioni pertinenti relative a un evento di rischio per decidere se sono necessarie operazioni di ripristino o attenuazione, e come è stata compromessa, l'identità e comprendere l'utilizzo dell'identità compromesso.

È possibile collegare le attività di analisi per le [notifiche](active-directory-identityprotection-notifications.md) di che Azure protezione Directory Active Invia per posta elettronica.

Nelle sezioni seguenti offrono ulteriori dettagli e procedure relative alle indagini.  



## <a name="what-is-a-user-risk-level"></a>Che cos'è un livello di rischio utente?

Un rischio a livello utente è un'indicazione (elevato, medio o basso) la probabilità che sia stata compromessa l'identità dell'utente. Viene calcolato in base sugli eventi di rischio utente che sono associati l'identità dell'utente. 

Lo stato di un evento di rischio è **attivo** o **chiuso**. Solo gli eventi di rischio **attivi** collaborazione per il calcolo del rischio utente. 

Il livello di rischio utente viene calcolato utilizzando input seguenti:

- Eventi di rischio attivo impatto l'utente
- Livello di rischio gli eventi 
- Se sono state prese le azioni di monitoraggio e aggiornamento 

![Rischi per gli utenti] (./media/active-directory-identityprotection/1001.png "Rischi per gli utenti")



È possibile utilizzare i livelli di rischio utente per creare criteri di accesso condizionale per impedire l'accesso agli utenti rischiosi o imporre loro di modificare in modo sicuro la propria password. 


## <a name="closing-risk-events-manually"></a>Chiudere gli eventi di rischio manualmente

Nella maggior parte dei casi, scattate azioni di correzione, ad esempio una password di protezione Reimposta per chiudere automaticamente gli eventi di rischio. Tuttavia, questa potrebbe non essere sempre possibile.  
Ciò avviene, ad esempio, quando:

- Un utente con gli eventi di rischio attivo è stato eliminato
- Una ricerca rivela che è stato eseguire un evento di rischio segnalati dall'utente legittimi

Perché gli eventi di rischio **attivi** collaborazione per il calcolo del rischio utente, è necessario abbassare manualmente un livello di rischio chiudendo eventi di rischio manualmente.  
Nel corso di indagini, è possibile eseguire una delle seguenti operazioni per modificare lo stato di un evento di rischio:

![Azioni] (./media/active-directory-identityprotection/34.png "Azioni")

- **Risolvere** - se dopo avere esaminato un evento di rischio, è stata eseguita un'operazione di rimedio appropriate all'esterno di protezione dell'identità e si ritiene che l'evento di rischio deve essere considerato chiuso, contrassegnare l'evento come risolto. Risolvere gli eventi verranno impostato lo stato dell'evento di rischio a chiuso e l'evento di rischio non è più contribuisce a rischio utente.

- **Segna come falso positivo** - In alcuni casi, è possibile provare a utilizzare un evento di rischio e scoprire che è stata erroneamente contrassegnata come un rischiosa. È possibile ridurre il numero di tali occorrenze contrassegnando l'evento di rischio come falso positivo. In questo modo la macchina apprendimento algoritmi per migliorare la classificazione di eventi simili in futuro. Lo stato degli eventi di falso positivo è **chiuso** e verrà non contribuiscono a rischio utente.

- **Ignora** - se si dispongano non eseguire le operazioni di monitoraggio e aggiornamento, ma l'evento di rischio da rimuovere dall'elenco attivo, è possibile contrassegnare un evento di rischio Ignora e lo stato dell'evento verrà chiuso. Eventi ignorati non contribuiscono al rischio utente. Questa opzione deve essere utilizzata solo nelle circostanze insolito. 

- **Riattivare** - eventi di rischio che sono state chiuse manualmente (selezionando **risolvere**, **falso positivo**o **Ignora**) può essere riattivato, impostare lo stato dell'evento su **attivo**. Eventi di rischio riattivato collaborazione per il calcolo di livello i rischi utente. Non è possibile riattivare gli eventi di rischio chiusi tramite monitoraggio (ad esempio reimposta una password di protezione). 




**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **eventi di rischio**in **analisi**e il **protezione dell'identità di Azure Active Directory** .

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1002.png "La reimpostazione della password manuale")

2. Nell'elenco **eventi di rischio** , fare clic su un rischio.

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1003.png "La reimpostazione della password manuale")

2. In e il rischio, fare clic su un utente.

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1004.png "La reimpostazione della password manuale")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Chiudere manualmente tutti gli eventi di rischio di un utente

Invece di chiudere manualmente gli eventi di rischio per un utente singolarmente, Azure Active Directory identità protezione anche offre un metodo per chiudere tutti gli eventi per un utente con un solo clic.


![Azioni] (./media/active-directory-identityprotection/2222.png "Azioni")

Quando si fa clic su **Elimina tutti gli eventi**, tutti gli eventi vengono chiuse e l'utente interessato non è più interessati.



## <a name="remediating-user-risk-events"></a>Eventi di rischio utente giù

Un rimedio è un'azione a un'identità o un dispositivo che in precedenza è stato sospetto o noto compromessa protetto. Un'azione rimedi Ripristina l'identità o un dispositivo a uno stato attendibile e risolve precedente eventi di rischio associati all'identità o dispositivo.

Per correggere gli eventi di rischio dell'utente, è possibile:

- Eseguire una sicura la reimpostazione della password per aggiornare manualmente gli eventi di rischio dell'utente 

- Configurare un criterio di sicurezza utente rischio per ridurre o correggere automaticamente gli eventi di rischio dell'utente

- Nuova immagine dispositivo virus  


### <a name="manual-secure-password-reset"></a>Reimpostazione della password di protezione manuale

Reimpostazione della password di protezione è un rimedio efficace per molti eventi di rischio e quando eseguita, viene chiusa questi eventi di rischio e automaticamente viene ricalcolato il livello di rischio utente. È possibile utilizzare il dashboard di identità di protezione per avviare una reimpostazione della password per un utente rischioso. 

Finestra di dialogo correlata sono disponibili due diversi metodi per reimpostare una password:

**Reimpostare la password** , selezionare **Richiedi all'utente di reimpostare la password** per consentire all'utente di self-recuperare se l'utente ha registrato per l'autenticazione a più fattori. Durante il dell'utente successivo accesso, l'utente verrà necessari per risolvere un problema di autenticazione a più fattori correttamente e quindi forzata per cambiare la password. Questa opzione non è disponibile se l'account utente non sia già registrato autenticazione a più fattori.

**Password temporanea** : selezionare **Genera una password temporanea** immediatamente invalidate la password esistente e creare una nuova password temporanea dell'utente. Inviare la nuova password temporanea a un indirizzo di posta elettronica alternativo per l'utente o al responsabile dell'utente. Perché la password temporanea, l'utente verrà richiesto di modificare la password durante l'accesso.


![Criteri] (./media/active-directory-identityprotection/1005.png "Criteri")


**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **utenti contrassegno per il rischio**e il **protezione dell'identità di Azure Active Directory** .

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1006.png "La reimpostazione della password manuale")


2. Nell'elenco di utenti, selezionare un utente con eventi di almeno un rischio.

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1007.png "La reimpostazione della password manuale")


2. Scegliere **Reimposta password**e l'utente.

    ![La reimpostazione della password manuale] (./media/active-directory-identityprotection/1008.png "La reimpostazione della password manuale")





## <a name="user-risk-security-policy"></a>Criteri di sicurezza utente dei rischi

Criteri di sicurezza rischio utente sono un criterio di accesso condizionale che valuta il livello di rischio a un utente specifico e si applica rimedi e attenuazione azioni in base alle regole e condizioni predefinite.


![Criteri di ridk utente] (./media/active-directory-identityprotection/1009.png "Criteri di ridk utente")


Protezione dell'identità di Active Directory Azure consente di gestire la risoluzione di problemi di contrassegnati per il rischio, per consentire agli utenti e attenuazione:

- Impostare gli utenti e gruppi a che il criterio si applica: 

    ![Criteri di ridk utente] (./media/active-directory-identityprotection/1010.png "Criteri di ridk utente")


- Impostare l'utente rischio soglia (minimo, medio o alto) che attiva il criterio: 

    ![Criteri di ridk utente] (./media/active-directory-identityprotection/1011.png "Criteri di ridk utente")


- Impostare i controlli da applicare quando si attiva il criterio:

    ![Criteri per ridk l'utente] (./media/active-directory-identityprotection/1012.png "Criteri di ridk utente")


- Cambiare lo stato dei criteri di:

    ![Criteri per ridk l'utente] (./media/active-directory-identityprotection/403.png "Registrazione MFA")


- Esaminare e valutare l'impatto di una modifica prima di attivarlo:

    ![Criteri di ridk utente] (./media/active-directory-identityprotection/1013.png "Criteri ridk utente")


Scegliendo **la** soglia riduce il numero di volte in cui un criterio viene attivato e ridurre l'impatto per gli utenti.
Tuttavia, esclude gli utenti **medio** e **basso** contrassegnati per il rischio di criteri, che potrebbero non sicura identità o dispositivi o in precedenza sospetti noti per essere compromessa.

Quando l'impostazione del criterio

- Escludere gli utenti possono generare numerose falsi positivi (sviluppatori, gli analisti della protezione)

- Escludere gli utenti locali in cui abilitato il criterio non è pratico (ad esempio senza accesso al servizio di assistenza)

- Usare **la** soglia durante l'iniziale dei criteri è possibile distribuire, o se è necessario ridurre a icona sfide visualizzate dagli utenti finali.

- Se l'organizzazione richiede maggiore protezione, utilizzare una determinata soglia **minimo** . Selezione di una determinata soglia **minimo** introduce utente aggiuntive accesso problematiche, ma maggiore sicurezza.

Il valore predefinito consigliato per la maggior parte delle organizzazioni consiste nel configurare una regola per una determinata soglia **medio** di stabilire un equilibrio tra facilità di utilizzo e la sicurezza.

Per una panoramica dell'esperienza utente correlate, vedere:

- [Flusso di ripristino account Compromised](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Account Compromised bloccati del flusso](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **i criteri di rischio utente**e il **protezione dell'identità di Azure Active Directory** , nella sezione **Configura** .

    ![Criteri di ridk utente] (./media/active-directory-identityprotection/1009.png "Criteri di ridk utente")






## <a name="mitigating-user-risk-events"></a>Fattori gli eventi di rischio dell'utente
Gli amministratori possono impostare un criterio di protezione di rischio utente per impedire agli utenti al momento dell'accesso a seconda del livello di rischio. 

Bloccare un accesso:
 
- Impedisce la generazione di nuovi eventi di rischio utente per l'utente interessato

- Consente agli amministratori di correggere gli eventi di rischio che interessano l'identità dell'utente e ripristinare a uno stato protetto manualmente



## <a name="what-is-a-sign-in-risk-level"></a>Che cos'è un livello di rischio di accesso?

Un livello di rischio di accesso è un'indicazione (elevato, medio o basso) la probabilità che per uno specifico accesso-, in un altro utente sta tentando di eseguire l'autenticazione con l'identità dell'utente. Il livello di rischio di accesso viene valutato al momento dell'accesso-in e vengono tenuti in considerazione gli eventi di rischio e indicatori rilevato tempo reale per tale accesso specifica. 

## <a name="mitigating-sign-in-risk-events"></a>Fattori eventi di rischio di accesso 
Un attenuazione è un'azione per limitare la possibilità di attacco di sfruttare un'identità compromesso o dispositivo senza ripristinare l'identità o un dispositivo a uno stato attendibile. Un attenuazione non consente di risolvere gli eventi di rischio di accesso precedenti associati all'identità o dispositivo.

È possibile utilizzare access condizionale in protezione dell'identità di Azure Active Directory per ridurre automaticamente gli eventi di rischio di accesso. Grazie a questi criteri, è consigliabile il livello di rischio dell'utente o il segno aggiuntivo per bloccare gli accessi rischiosi o richiedere all'utente di eseguire l'autenticazione a più fattori. Queste azioni potrebbero impedire sfruttare un'identità furto per danneggiare e potrebbero attendere alcuni istanti per proteggere l'identità. 


## <a name="sign-in-risk-security-policy"></a>Criteri di sicurezza di rischio di accesso

Un criterio di accesso, rischio è un criterio di accesso condizionale che valuta il rischio a un accesso aggiuntivo specifico e si applica riduzioni disponibili in base alle regole e condizioni predefinite.

![Accesso rischio] (./media/active-directory-identityprotection/1014.png "Accesso rischio")


Protezione dell'identità di Active Directory Azure consente di gestire attenuazione dei componenti aggiuntivi di accesso rischiosi, consentendo di:

- Impostare gli utenti e gruppi a che il criterio si applica: 

    ![Accesso rischio] (./media/active-directory-identityprotection/1015.png "Accesso rischio")


- Impostare il rischio di accesso soglia (bassa, Media o alta) che attiva il criterio: 

    ![Accesso rischio] (./media/active-directory-identityprotection/1016.png "Accesso rischio")


- Impostare i controlli da applicare quando si attiva il criterio:  

    ![Accesso rischio] (./media/active-directory-identityprotection/1017.png "Accesso rischio")
    

- Cambiare lo stato dei criteri di:

    ![Registrazione MFA] (./media/active-directory-identityprotection/403.png "Registrazione MFA")

- Esaminare e valutare l'impatto di una modifica prima di attivarlo: 

    ![Accesso rischio] (./media/active-directory-identityprotection/1018.png "Accesso rischio")


### <a name="what-you-need-to-know"></a>Informazioni utili

È possibile configurare un criterio di protezione di rischio di accesso per richiedere l'autenticazione a più fattori:

![Accesso rischio] (./media/active-directory-identityprotection/1017.png "Accesso rischio")

Tuttavia, per motivi di sicurezza, questa impostazione vengono funziona solo per gli utenti già registrati per l'autenticazione a più fattori. Se viene soddisfatta la condizione per richiedere l'autenticazione a più fattori per un utente che non è ancora registrato per l'autenticazione a più fattori, l'utente è bloccato. 

Si consiglia, se si vuole richiedere l'autenticazione a più fattori per gli accessi rischiosi, è necessario:

1. Attivare il [criterio di registrazione di autenticazione a più fattori](#multi-factor-authentication-registration-policy) per gli utenti interessati.
2. Chiedere agli utenti interessati all'account di accesso in una sessione non rischiosa per eseguire una registrazione MFA

Completare la procedura seguente garantisce che l'autenticazione a più fattori necessario per un accesso rischiosa. 


### <a name="best-practices"></a>Procedure consigliate

 
Scegliendo **la** soglia riduce il numero di volte in cui un criterio viene attivato e ridurre l'impatto per gli utenti.  
 
Tuttavia, esclude **medio** e **basso** accessi contrassegnati per il rischio di criteri, che non riesca a bloccare un attacco sfruttare un'identità compromessa da. 

Quando l'impostazione del criterio 

- Escludere gli utenti che non / non è possibile specificare l'autenticazione a più fattori

- Escludere gli utenti locali in cui abilitato il criterio non è pratico (ad esempio senza accesso al servizio di assistenza)

- Escludere gli utenti possono generare numerose falsi positivi (sviluppatori, gli analisti della protezione)

- Usare **la** soglia durante l'iniziale dei criteri è possibile distribuire, o se è necessario ridurre a icona sfide visualizzate dagli utenti finali.

- Se l'organizzazione richiede maggiore protezione, utilizzare una determinata soglia **minimo** . Selezione di una determinata soglia **minimo** introduce utente aggiuntive accesso problematiche, ma maggiore sicurezza.

Il valore predefinito consigliato per la maggior parte delle organizzazioni consiste nel configurare una regola per una determinata soglia **medio** di stabilire un equilibrio tra facilità di utilizzo e la sicurezza.

 
Il criterio di accesso, rischio è:

- Applicata a tutto il traffico del browser e accessi mediante l'autenticazione moderno.
- Non applicate alle applicazioni che utilizzano versioni precedenti protocolli di protezione disabilitando il punto finale WS-Trust IDP federato, ad esempio ADFS.

Nella pagina di **Eventi di rischio** nella console di protezione dell'identità sono elencati tutti gli eventi:

- Questo criterio è stato applicato a
- È possibile verificare l'attività e determinare se l'operazione ha avuto appropriato o meno 

Per una panoramica dell'esperienza utente correlate, vedere:

- [Ripristino di accesso rischioso](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Rischiosa accesso aggiuntivo bloccato](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Registrazione di autenticazione a più fattori durante un accesso rischiosa](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **accesso rischio**e il **protezione dell'identità di Azure Active Directory** , nella sezione **Configura** .

    ![Criteri per ridk l'utente] (./media/active-directory-identityprotection/1014.png "Criteri di ridk utente")





## <a name="multi-factor-authentication-registration-policy"></a>Criteri di registrazione di autenticazione a più fattori

Autenticazione a più fattori Azure è un metodo di verifica che ci si trova che richiede l'uso di più solo un nome utente e la password. Fornisce un secondo livello di sicurezza per gli accessi utente e le transazioni.  
È consigliabile si richiede l'autenticazione a più fattori Azure per gli accessi utente perché è:

- Offre autenticazione avanzata con un intervallo delle opzioni di verifica semplice

- Svolge un ruolo fondamentale in preparazione dell'organizzazione per proteggere e ripristinare da compromessi account

![Criteri per ridk l'utente] (./media/active-directory-identityprotection/1019.png "Criteri di ridk utente")



Per ulteriori informazioni, vedere [che cos'è l'autenticazione a più fattori Azure?](../multi-factor-authentication/multi-factor-authentication.md)


Protezione dell'identità di Active Directory Azure consente di gestire le rullino di registrazione di autenticazione a più fattori configurando un criterio che consente di: 



- Impostare gli utenti e gruppi a che il criterio si applica: 

    ![Criteri MFA] (./media/active-directory-identityprotection/1020.png "Criteri MFA")



- Impostare i controlli da applicare quando si attiva il criterio:  

    ![Criteri MFA] (./media/active-directory-identityprotection/1021.png "Criteri MFA")


- Cambiare lo stato dei criteri di:

    ![Criteri MFA] (./media/active-directory-identityprotection/403.png "Criteri MFA")

- Visualizzare lo stato di registrazione corrente: 

    ![Criteri MFA] (./media/active-directory-identityprotection/1022.png "Criteri MFA")


Per una panoramica dell'esperienza utente correlate, vedere:

- [Flusso di registrazione di autenticazione a più fattori](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registrazione di autenticazione a più fattori durante un accesso rischiosa](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Per aprire la finestra di dialogo configurazione correlate**:

1. Scegliere **la registrazione di autenticazione a più fattori**e il **protezione dell'identità di Azure Active Directory** , nella sezione **Configura** .

    ![Criteri MFA] (./media/active-directory-identityprotection/1019.png "Criteri MFA")





## <a name="next-steps"></a>Passaggi successivi

 - [Canale 9: Azure AD e Mostra identità: anteprima di protezione di identità](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipi di eventi di rischio rilevati da Azure Active Directory identità protezione](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilità rilevata da Azure Active Directory identità protezione](active-directory-identityprotection-vulnerabilities.md)
 - [Notifiche di Azure Active Directory identità protezione](active-directory-identityprotection-notifications.md)
 - [Azure playbook la protezione dell'identità di Active Directory](active-directory-identityprotection-playbook.md)
 - [Glossario di Azure Active Directory identità protezione](active-directory-identityprotection-glossary.md)

 - [Esperienze di accesso con la protezione dell'identità di Azure Active Directory](active-directory-identityprotection-flows.md)

 - [Per consentire la protezione dell'identità di Azure Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory la protezione dell'identità - come sbloccare gli utenti](active-directory-identityprotection-unblock-howto.md)

 - [Iniziare a utilizzare la protezione dell'identità Azure Active Directory e Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


