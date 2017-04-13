<properties
    pageTitle="Glossario di protezione di Azure Active Directory identità | Microsoft Azure"
    description="Glossario di protezione identità di Azure Active Directory"
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza, il glossario"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glossario di protezione identità di Azure Active Directory 


### <a name="at-risk-user"></a>Rischio (utente)  
Un utente con uno o più eventi di rischio attivo. 

### <a name="atypical-sign-in-location"></a>Percorso di accesso insolita   
Un effettuare l'accesso da un'area geografica che non è tipica per l'utente specifico, gli utenti simili o tenant.

### <a name="azure-ad-identity-protection"></a>Protezione dell'identità di Azure Active Directory    
Modulo di sicurezza di Azure Active Directory che fornisce una visualizzazione consolidata di eventi di rischio e potenziali vulnerabilità che interessano identità dell'organizzazione.

### <a name="conditional-access"></a>Accesso condizionato  
Criteri per la protezione dell'accesso alle risorse. Le regole di accesso condizionale sono archiviate in Azure Active Directory e vengono valutate da Azure Active Directory prima di concedere l'accesso alla risorsa.  Regole di esempio includono limitazione dell'accesso basato su località utente metodo di autenticazione utente o integrità dispositivo.

### <a name="credentials"></a>Credenziali     
Informazioni che includono identificazione e prova di identificazione utilizzata per ottenere l'accesso a locali e risorse di rete. Esempi di credenziali sono i nomi utente e password, smart card e certificati.

### <a name="event"></a>Evento   
Un record di un'attività di Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falso positivo (evento di rischio) 
Stato dell'evento di rischio impostata manualmente da un utente di protezione dell'identità, che indica che l'evento di rischio è stato esaminato ed è stata erroneamente contrassegnata come un evento di rischio.

### <a name="identity"></a>Identità    
Una persona o entità che deve essere verificato mediante l'autenticazione, in base a criteri, ad esempio password o un certificato.

### <a name="identity-risk-event"></a>Evento di rischio identità     
Evento AAD che è stata contrassegnata come anomala per la protezione dell'identità e può indicare che è stata compromessa un'identità.

### <a name="ignored-risk-event"></a>Ignorati (evento di rischio)    
Stato dell'evento di rischio impostata manualmente da un utente di protezione dell'identità, che indica che l'evento di rischio è chiuso senza eseguire un'operazione di monitoraggio e aggiornamento.

### <a name="impossible-travel-from-atypical-locations"></a>Impossibile viaggio da posizioni insolite   
Un evento di rischio attivato quando vengono rilevati due accessi per lo stesso nome utente, almeno uno di essi è stata inviata da un punto di accesso insolito e l'ora tra i componenti aggiuntivi di accesso è inferiore al tempo minimo che necessario per fisica viaggio tra questi percorsi.  

###<a name="investigation"></a>Indagini    
Il processo di revisione alle attività, i registri e altre informazioni pertinenti relative a un evento di rischio per decidere se sono necessari, monitoraggio o attenuazione passaggi capire se e come è stata compromessa, l'identità e comprendere utilizzo identità compromesso.

### <a name="leaked-credentials"></a>Credenziali persa  

Evento di rischio attivato se vengono rilevate credenziali dell'utente corrente (nome utente e password) pubblicato nel web scuro tramite il nostro ricercatori.

### <a name="mitigation"></a>Riduzione dei rischi  
Un'azione per limitare o eliminare la possibilità degli utenti malintenzionati di sfruttare un'identità compromesso o dispositivo senza ripristinare l'identità o un dispositivo a uno stato attendibile. Un attenuazione non viene risolto precedente eventi di rischio associati all'identità o dispositivo.

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori 
Dispone di un metodo di autenticazione che richiede all'utente di due o più metodi di autenticazione, che possono includere un elemento, ad esempio un certificato; è l'utente conosce, ad esempio nomi utente, password o frasi; attributi fisici, ad esempio un'identificazione personale; e attributi personali, ad esempio una firma personale.

### <a name="offline-detection"></a>Rilevamento non in linea   
Rilevamento di alterazioni e valutazione dei rischi di un evento, ad esempio tentativo di accesso al termine, per un evento che è già trascorsa.

### <a name="policy-condition"></a>Condizione del criterio    
Parte di un criterio di protezione che definiscono le entità (gruppi, utenti, App, piattaforme di dispositivi, stati dispositivo, intervalli di indirizzi IP, tipi di client) incluso nei criteri o da escludere dal.

### <a name="policy-rule"></a>Regola dei criteri     
Parte di un criterio di protezione che descrive le circostanze che attivano il criterio e le azioni intraprese quando il criterio viene attivato.

### <a name="prevention"></a>Prevenzione  
Un'azione per impedire danni dell'organizzazione tramite abuse di un'identità o un dispositivo sospetto o che si vuole essere compromessa. Un'azione programmi non è possibile protetta l'identità o il dispositivo e non consente di risolvere gli eventi di rischio precedenti.

### <a name="privileged-user"></a>Privilegi (utente)
Un utente che al momento dell'evento di rischio, ha autorizzazioni di amministratore permanente o temporanea a uno o più delle risorse di Azure Active Directory, ad esempio un amministratore globale, fatturazione, amministratore del servizio, l'utente amministratore e amministratore Password. 

###<a name="real-time"></a>In tempo reale    
Vedere la sezione rilevamento in tempo reale.

###<a name="real-time-detection"></a>Rilevamento in tempo reale  
Il rilevamento di alterazioni e valutazione dei rischi di un evento, ad esempio il tentativo di accesso prima dell'evento può procedere.

### <a name="remediated-risk-event"></a>Risolte (evento di rischio)     
Stato dell'evento di rischio impostato automaticamente la protezione dell'identità, che indica che l'evento di rischio è stato risolte tramite l'azione di monitoraggio standard per questo tipo di evento di rischio. Quando si reimposta la password dell'utente, ad esempio, vengono risolte automaticamente molti eventi di rischio che indicano che la password precedente è stata compromessa.

### <a name="remediation"></a>Monitoraggio e aggiornamento     
Un'azione per proteggere un'identità o un dispositivo o in precedenza sospetti noti per essere compromessa. Un'azione di monitoraggio e aggiornamento consente di ripristinare l'identità o un dispositivo a uno stato attendibile e risolve precedente eventi di rischio associati all'identità o dispositivo.

### <a name="resolved-risk-event"></a>Risoluzione (evento di rischio)   
Stato dell'evento di rischio impostata manualmente da un utente di protezione dell'identità, che indica che l'utente ha eseguito un'azione rimedio appropriate all'esterno di protezione dell'identità e che l'evento di rischio deve essere considerato chiusa.

###<a name="risk-event-status"></a>Stato dell'evento di rischio    
Proprietà di un evento di rischio, che indica se l'evento è attivo e se chiuso, il motivo per chiuderla.

###<a name="risk-event-type"></a>Tipo di evento di rischio  
Una categoria per l'evento di rischio, che indica il tipo di anomalie che ha causato l'evento da prendere in considerazione rischiosa.

###<a name="risk-level-risk-event"></a>Livello di rischio (evento di rischio)  
Un'indicazione (elevato, medio o basso) gravità dell'evento di rischio per consentire agli utenti di protezione dell'identità di definire le azioni che eseguire per ridurre il rischio all'organizzazione. 

###<a name="risk-level-sign-in"></a>Livello di rischio (accessohttp) 
Un'indicazione (elevato, medio o basso) la probabilità che per uno specifico accesso-, in un altro utente tenta di utilizzare l'identità dell'utente.

###<a name="risk-level-user-compromise"></a>Livello di rischio (compromesso utente) 
Un'indicazione (elevato, medio o basso) la probabilità che sia stata compromessa un'identità.

### <a name="risk-level-vulnerability"></a>Livello di rischio (vulnerabilità)  
Un'indicazione (elevato, medio o basso) gravità della vulnerabilità per consentire agli utenti di protezione dell'identità di definire le azioni che eseguire per ridurre il rischio all'organizzazione.

### <a name="secure-identity"></a>Sicura (identità)   
Azioni di correzione, ad esempio un computer ricostruzione per ripristinare un'identità potenzialmente compromessa in uno stato senza compromessi o modifica della password.

### <a name="security-policy"></a>Criteri di sicurezza
Raccolta di regole e condizioni. Un criterio può essere applicato a entità, ad esempio utenti, gruppi, App, dispositivi, piattaforme di dispositivi, stati dispositivo, intervalli di indirizzi IP e tipi di client Auth2.0. Quando un criterio è attivato, viene valutata ogni volta che un'entità inclusa nei criteri viene assegnata un token per una risorsa.

### <a name="sign-in-v"></a>Accedere (v) 
Per eseguire l'autenticazione a un'identità di Azure Active Directory.

### <a name="sign-in-n"></a>Accesso aggiuntivo (n) 
Il processo o l'azione di autenticazione un'identità di Azure Active Directory e l'evento che acquisisce l'operazione.

###<a name="sign-in-from-anonymous-ip-address"></a>Accessohttp dall'indirizzo IP anonimo    
Un evento di rischio attivato dopo un accesso completato dall'indirizzo IP che è stato identificato come indirizzo IP proxy anonimo.

###<a name="sign-in-from-infected-device"></a>Accessohttp dal dispositivo virus 
Un evento di rischio attivato quando un accesso aggiuntivo proviene da un indirizzo IP noto che verrà utilizzato da uno o più dispositivi compromessi attivamente tenta di comunicare con un server bot.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Effettuare l'accesso dall'indirizzo IP con attività sospetti 
Un evento di rischio attivata dopo un accesso aggiuntivo completato da un indirizzo IP di indirizzi con un numero elevato di tentativi di accesso non tra più account utente per un breve periodo di tempo.

###<a name="sign-in-from-unfamiliar-location"></a>Accessohttp ovunque con 
Un evento di rischio attivato quando un utente accede correttamente da una nuova posizione (IP, latitudine/longitudine e ASN).

###<a name="sign-in-risk"></a>Rischio di accesso     
Vedere rischio livello (accessohttp)

###<a name="sign-in-risk-policy"></a>Accesso rischio.  
Un criterio di accesso condizionale che valuta il rischio a un accesso aggiuntivo specifico e si applica riduzioni disponibili in base alle regole e condizioni predefinite.

###<a name="user-compromise-risk"></a>Rischio di violazione di utente     
Vedere rischio livello (compromesso utente)

###<a name="user-risk"></a>Rischio utente    
Vedere rischio livello (compromesso utente).

###<a name="user-risk-policy"></a>Criteri di rischio utente     
Criterio di accesso condizionale che considera l'accesso e applica riduzioni disponibili in base alle regole e condizioni predefinite.

###<a name="users-flagged-for-risk"></a>Utenti contrassegnati per il rischio   
Utenti che dispongono di eventi di rischio, attivo o risolte

###<a name="vulnerability"></a>Vulnerabilità    
Una configurazione o condizione di Azure Active Directory che semplifica sensibili alle violazioni della directory o rischi.


## <a name="see-also"></a>Vedere anche

- [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)
