<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - definire una strategia di adozione di identità ibrido | Microsoft Azure"
    description="Controllo di accesso condizionato, Azure Active Directory controlla le condizioni specifiche scelto per l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Quando sono soddisfatte le condizioni, l'utente viene autenticato e autorizzato ad accedere all'applicazione."
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


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definire una strategia di adozione di identità ibrido

In questa attività, è necessario definire la strategia di adozione di identità ibrido per la soluzione di identità ibrido soddisfare le esigenze aziendali descritti in:

- [Determinare le esigenze aziendali](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Requisiti di sincronizzazione della directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinare i requisiti di autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definire una strategia di gestione delle esigenze aziendali
I primi indirizzi attività determinazione dell'azienda organizzazioni deve.  Può essere molto generali e dello slittamento ambito può verificarsi se non si utilizza.  All'inizio privilegiare la semplicità ma ricordarsi di pianificare una struttura che verrà adattato semplificare modifica in futuro.  Indipendentemente dal fatto che si tratti di una struttura semplice o un'altra estremamente complessa, Azure Active Directory è la piattaforma Microsoft Identity che supporta le applicazioni di Office 365, Microsoft Online Services e cloud importanti.

## <a name="define-an-integration-strategy"></a>Definire una strategia di integrazione
Microsoft ha tre scenari di integrazione principale, ovvero identità cloud, sincronizzata identità e identità federate.  È consigliabile adozione di una di queste strategie di integrazione.  La strategia che scelta può variare e possono includere le decisioni in scegliere un'opzione, il tipo di esperienza utente si desidera fornire viene offerto alcune dell'infrastruttura esistente già sul posto e qual è la più conveniente.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Gli scenari definiti nella figura precedente sono:

- **Identità cloud**: si tratta di identità presenti esclusivamente nel cloud.  In caso di Azure Active Directory, da risiedono in particolare nella directory di Azure Active Directory.
- **Sincronizzato**: si tratta di identità presenti in locale e nel cloud.  Usa Azure AD Connect, questi utenti creati o uniti tramite join con account Azure Active Directory esistenti.  Hash della password dell'utente vengono sincronizzati tra l'ambiente locale nel cloud in quello che viene definito un hash delle password.  Durante la sincronizzazione con una sola avvertenza è che se un utente è disabilitato nell'ambiente locale, può richiedere fino a 3 ore per quel lo stato degli account per la visualizzazione in Azure Active Directory.  Ciò è dovuto l'intervallo di tempo di sincronizzazione.
- **Federato**: queste identità sono presenti entrambe locale e nel cloud.  Usa Azure AD Connect, questi utenti creati o uniti tramite join con account Azure Active Directory esistenti.  
 
>[AZURE.NOTE]
Per ulteriori informazioni sulla sincronizzazione opzioni leggere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).

Nella tabella seguente può essere utile per determinare i vantaggi e svantaggi di ognuna delle seguenti strategie:

| Strategia         | Vantaggi                                                                                                                                                                                                                                                  | Svantaggi                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identità cloud** | Più semplice da gestire per piccola azienda. <br> Alcuna operazione per l'installazione su No locale hardware aggiuntivi necessari<br>Se l'utente lascia l'azienda facilmente disattivato                                                                                                   | Gli utenti dovranno effettuare l'accesso quando si accede a carichi di lavoro nel cloud <br> Le password possono o non sia la stessa operazione per le identità cloud e locale                                                                                                                                                                                                                     |
| **La sincronizzazione**     | Password locale verranno autenticare entrambi locali e cloud directory <br>Più semplice da gestire per le organizzazioni di piccole dimensioni, medie-grandi <br>Gli utenti possono avere single sign-on (SSO) per alcune risorse <br> Metodo Microsoft preferito per la sincronizzazione <br> Più semplice da gestire | Alcuni clienti possono essere riluttanti a sincronizzare le directory con il cloud scadenza polizia specifici della società                                                                                                                                                                                                                                                  |
| **Federato**        | Gli utenti possono avere single sign-on (SSO) <br>Se un utente viene interrotta o si lascia, l'account è disabilitato immediatamente e revocare l'accesso,<br> Scenari avanzati che supporta non possono essere eseguite con la sincronizzazione                                           | Altre procedure per installare e configurare <br> Manutenzione superiore <br> Può essere richiesto hardware aggiuntive per l'infrastruttura di servizio token di sicurezza <br> Potrebbe essere necessario hardware aggiuntivo per installare il server federativo. Se si utilizza ADFS, è necessario software aggiuntivo <br> Richiedere l'impostazione estesa Single Sign-on <br> Punto critico di errore se il server federativo verso il basso, gli utenti non potranno eseguire l'autenticazione |

### <a name="client-experience"></a>Esperienza client
La strategia che usa determinerà l'esperienza di accesso dell'utente.  Le tabelle seguenti vengono fornite informazioni su quali utenti aspettarsi l'esperienza di accesso a.  Si noti che non tutti i provider di identità federata supportano Single Sign-On in tutti gli scenari.

**Le applicazioni di rete privata e aggiungono più domini**:
 

|                              | Identità sincronizzata      | Identità federata                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Web browser                 | Autenticazione basata su moduli | accesso singolo via, a volte è necessario fornire ID organizzazione |
| Outlook                      | Richiedi credenziali     | Richiedi credenziali                                       |
| Skype for Business (Lync)    | Richiedi credenziali     | Single-sign-on per Lync, viene richiesto di credenziali per Exchange   |
| SkyDrive Pro                 | Richiedi credenziali     | Single sign-on                                               |
| Office Pro Plus abbonamento | Richiedi credenziali     | Single sign-on                                               |

**Esterno o non attendibile origini**:

|                              | Identità sincronizzata      | Identità federata                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Web browser                 | Autenticazione basata su moduli |  Autenticazione basata su moduli |
| Outlook, Skype for Business (Lync), Skydrive Pro, abbonamento a Office| Richiedi credenziali     | Richiedi credenziali                                       |
| Exchange ActiveSync    | Richiedi credenziali     | Single-sign-on per Lync, viene richiesto di credenziali per Exchange   |
| App per dispositivi mobili                 | Richiedi credenziali     | Richiedi credenziali                                               |

Se è stabilito da attività 1 di avere un 3o IdP o sono corso per usare uno per fornire la federazione con Azure Active Directory di terze parti, è necessario tenere presenti le seguenti funzionalità supportate:
- Provider di SAML 2.0 è conforme per il profilo SP Lite possono supportare l'autenticazione di Azure Active Directory e applicazioni associate
- Supporta l'autenticazione passiva, che facilita auth a OWA, SharePoint Online e così via.
- Client di Exchange Online può essere supportato tramite il SAML 2.0 Enhanced Client profilo (ECP)

È inoltre necessario tenere presenti le funzionalità non saranno disponibili:

- Senza supporto WS protezione la federazione, verranno interrotte tutti gli altri client attivo
 - Questo significa nessun client Lync, il client OneDrive, abbonamento a Office, Office Mobile prima di Office 2016
- Transizione di Office per l'autenticazione passiva consentirà di supportare pure SAML 2.0 IdPs, ma sono ancora in base al client dal client


>[AZURE.NOTE]
Per l'elenco più aggiornato leggere http://aka.ms/ssoproviders articolo.

## <a name="define-synchronization-strategy"></a>Definire una strategia di sincronizzazione
In questa attività si definiranno gli strumenti che verranno utilizzati per la sincronizzazione dell'organizzazione locale dati cloud e cosa è necessario utilizzare topologia.  Perché la maggior parte delle organizzazioni utilizzano Active Directory, vengono fornite informazioni sull'utilizzo di Azure AD Connect per rispondere a domande indicate in modo dettagliato.  Per gli ambienti che non dispongono di Active Directory, presenza di informazioni sull'utilizzo di FIM 2010 R2 o 2016 MIM per pianificare la strategia.  Tuttavia, nelle versioni future di Azure AD Connect supporterà directory LDAP, pertanto, a seconda del tipo nella sequenza temporale, potrebbero essere possibile supportare queste informazioni.

###<a name="synchronization-tools"></a>Strumenti di sincronizzazione
Nel corso degli anni diversi strumenti di sincronizzazione hanno esistenti e utilizzati per diversi scenari.  Azure AD Connect è al momento Vai a uno strumento di scelta per tutti gli scenari supportati.  AAD Sync e DirSync sono ancora intorno e possono anche essere ora presenti nel proprio ambiente. 

>[AZURE.NOTE]
Per informazioni aggiornate relative alle funzionalità supportate di ogni strumento, leggere l'articolo di [confronto degli strumenti di integrazione di Directory](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologie supportate
Quando si definisce una strategia di sincronizzazione, è necessario determinare la topologia utilizzato. A seconda di informazioni che è state definite nel passaggio 2 è possibile determinare quali topologia è quella appropriata da utilizzare. La singola foresta singola topologia di Azure Active Directory più comuni e costituito da una singola foresta Active Directory e una sola istanza di Azure Active Directory.  Si sta per essere usate nella maggior parte degli scenari e la topologia prevista quando si utilizza l'installazione di Azure Active Directory connettersi Express, come illustrato nella figura seguente.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Singolo uno Scenario con più è molto comune per organizzazioni di grandi e piccole anche disporre di più insiemi di strutture, come illustrato nella figura 5.

>[AZURE.NOTE]
Per ulteriori informazioni sui diversi in locale e topologie di Azure Active Directory con Azure AD Connect sincronizzazione leggere l'articolo [topologie per Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scenario con più utenti

Se in questo caso quindi la topologia multi-forest-singolo Azure Active Directory deve essere considerata se le seguenti condizioni sono vere:

- Gli utenti hanno solo 1 identità tra insiemi di strutture tutti: univoco utenti sezione seguente descrive l'oggetto in modo più dettagliato.
- Autenticazione dell'utente per l'insieme di strutture in cui si trova la propria identità
- Da questa struttura venga recapitata UPN e ancoraggio di origine (non modificabile id)
- Tutti gli insiemi di strutture sono accessibili da Azure AD Connect: questo errore indica che non è necessario essere dominio aggiunto e può essere posizionato in una rete Perimetrale se questo semplifica l'esecuzione di questa.
- Gli utenti hanno solo una cassetta postale
- Insieme di strutture che ospita postale una cassetta è la migliore qualità di dati per gli attributi visibili in di Exchange elenco (globale)
- Se non esiste nessuna cassetta postale dell'utente, un insieme di strutture può essere utilizzato per fornire questi valori
- Se si dispone di una cassetta postale collegata, quindi è inoltre disponibile un altro account in un'altra foresta usata per accedere.

>[AZURE.NOTE]
Gli oggetti presenti in entrambi in locale e nel cloud "connessi" tramite un identificatore univoco. Nel contesto di sincronizzazione della Directory, questo identificatore univoco viene definito il SourceAnchor. Nel contesto di Single Sign-On, questo viene definito il ImmutableId. Per ulteriori informazioni sull'utilizzo di SourceAnchor i [concetti di progettazione per Azure AD Connect](active-directory-aadconnect-design-concepts.md#sourceanchor) .

Se le risposte sopra non sono true e si dispone di più account attivo o più cassette postali, Azure AD Connect verrà selezionarne uno e ignorare l'altro.  Se sono stati collegati cassette postali ma nessun altro account, tale utente non sarà un membro di gruppi di questi account non verranno esportati di Azure Active Directory.  Questa è la differenza tra la modalità di stato in passato con DirSync ed è il supporto intenzionale al meglio questi scenari più insiemi di strutture. Uno scenario con più insiemi di strutture è illustrato nella figura seguente.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Più insiemi di strutture uno scenario con più Azure Active Directory**

Si consiglia di utilizzare solo una singola directory in Azure Active Directory per un'organizzazione, ma questa caratteristica è supportata in una relazione 1:1 viene mantenuta tra un server di sincronizzazione di Azure AD Connect e una directory di Azure Active Directory.  Per ogni istanza di Azure Active Directory, sarà necessario un'installazione di Azure AD Connect.  Inoltre, Azure Active Directory, per impostazione predefinita è isolata e gli utenti in una sola istanza di Azure Active Directory non sarà possibile visualizzare gli utenti in un'altra istanza.

È possibile e supportati per la connessione di un'istanza locale di Active Directory a più directory di Azure Active Directory come illustrato nella figura seguente:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Filtro singola foresta**

Per eseguire questa operazione seguenti devono essere vere:

- Azure AD Connect sincronizzazione server devono essere configurati per i filtri in modo che hanno un insieme di oggetti che si escludono a vicenda.  Questa operazione completata, ad esempio, per ogni server per un determinato dominio o unità Organizzativa di ambito.
- Un dominio DNS può essere registrato solo in una singola directory di Azure Active Directory in modo che il UPN degli utenti in locale in Active Directory è necessario utilizzare spazi dei nomi distinti
- Gli utenti in una sola istanza di Azure Active Directory solo sarà possibile vedere users from l'istanza.  Non sarà possibile visualizzare gli utenti di altre istanze
- Solo una delle Azure Active Directory possibile abilitare la distribuzione ibrida di Exchange con locale in Active Directory
- Esclusione reciproca vale anche per writeback.  In questo modo alcune funzionalità di writeback non sono supportate con questa topologia perché questi presuppongono una configurazione locale singola.  Sono inclusi:
 - Raggruppare writeback con configurazione predefinita
 - Dispositivo writeback


Tenere presente che le operazioni seguenti non sono supportati e non deve essere scelto come implementazione:

- Non è supportata per avere più server di sincronizzazione di Azure AD Connect connessione alla stessa directory di Azure Active Directory, anche se sono stati configurati per la sincronizzazione si escludono a vicenda insieme di oggetti
- Non è supportato per sincronizzare lo stesso account utente più directory di Azure Active Directory. 
- Non è supportato anche per modificare una configurazione per segnalare agli utenti in un annuncio Azure come contatti in un altro Azure Active directory. 
- È inoltre non supportata per la modifica di sincronizzazione di Azure AD Connect per connettersi a più directory di Azure Active Directory.
- Azure Active Directory sono in base alla progettazione isolato. È supportata per modificare la configurazione della sincronizzazione di Azure AD Connect per leggere i dati da un'altra directory di Azure Active Directory durante un tentativo di creare un elenco indirizzi globale comune e unificato tra le directory. Non è supportato anche per esportare gli utenti come contatti in un altro locale Active Directory utilizzando sincronizzazione di Azure AD Connect.


>[AZURE.NOTE]
Se l'organizzazione limita computer della rete di connettersi a Internet, in questo articolo elenca gli endpoint (FQDN, IPv4 e intervalli di indirizzi IPv6) che è necessario includere nell'uscita Consenti elenchi e Internet Explorer siti attendibili del client computer per assicurarsi che il computer è possibile utilizzare correttamente Office 365. Per ulteriori informazioni vedere [intervalli di indirizzi IP e URL di Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Definizione strategia di autenticazione a più fattori
Definire la strategia di autenticazione a più fattori da utilizzare.  Autenticazione a più fattori Azure è disponibile in due versioni diverse.  Uno basato su cloud e l'altro locali con Azure MFA Server.  In base alla valutazione è sopra, è possibile determinare quale soluzione sia corretta per la strategia.  Utilizzare la tabella seguente per determinare quale opzione di progettazione migliore soddisfare i requisiti di sicurezza della società:

Opzioni di progettazione a più fattori:

| Risorse per la protezione                                               | MFA nel cloud | MFA locale |
|---------------------------------------------------------------|------------------|----------------|
| Applicazioni di Microsoft                                                | Sì              | Sì            |
| App SaaS nella raccolta app                                  | Sì              | Sì            |
| Applicazioni di IIS pubblicate tramite Azure Active Directory App Proxy         | Sì              | Sì            |
| Applicazioni di IIS non pubblicate tramite il Proxy di Azure Active Directory App | No               | Sì            |
| Accesso remoto come VPN, RDG                                     | No               | Sì            |

Anche se può scelto su una soluzione per la strategia, è necessario usare la valutazione dall'alto in cui si trovano gli utenti.  Ciò potrebbe causare la soluzione modificare.  Usare la tabella seguente per facilitare la determinazione seguente:

| Percorso utente                                                       | Opzione di progettazione preferito                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi-FactorAuthentication nel cloud |
| Azure Active Directory e Active Directory utilizzando la federazione con ADFS in locale             | Entrambe                                    |
| Azure Active Directory e locale AD uso Azure AD non Connect sincronizzazione delle password | Entrambe                                    |
| Azure Active Directory e locali con Azure AD Connect la sincronizzazione delle password  | Entrambe                                    |
| Active Directory locale                                                      | Server di autenticazione a più fattori      |

>[AZURE.NOTE]
È necessario che l'opzione di progettazione di autenticazione a più fattori che è stato selezionato supporta le caratteristiche che sono necessari per la progettazione.  Per ulteriori informazioni vedere [scegliere la soluzione di protezione a più fattori dell'utente](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Provider di autenticazione a più fattori
Autenticazione a più fattori è disponibile per impostazione predefinita per gli amministratori globali che hanno un tenant di Azure Active Directory. Tuttavia, se si desidera estendere l'autenticazione a più fattori per tutti gli utenti e/o essere per gli amministratori globali in grado di trarre vantaggio funzioni, ad esempio il portale di gestione, messaggio di saluto personalizzato e report, quindi è necessario acquistare e configurare il Provider di autenticazione a più fattori.

>[AZURE.NOTE]
È necessario che l'opzione di progettazione di autenticazione a più fattori che è stato selezionato supporta le caratteristiche che sono necessari per la progettazione. 

##<a name="next-steps"></a>Passaggi successivi
[Determinare i requisiti di protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
