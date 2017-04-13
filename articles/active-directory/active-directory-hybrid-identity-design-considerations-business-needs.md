<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare i requisiti di identità | Microsoft Azure"
    description="Identificare esigenze dell'azienda che potrebbero causare è possibile definire i requisiti per la progettazione di identità ibrida."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Requisiti di identità per la soluzione di identità ibrido
Progettazione di una soluzione di identità ibrido il primo passaggio consiste nel determinare i requisiti per l'organizzazione di business che utilizzo di questa soluzione.  Identità ibrido inizia come un ruolo di supporto (supporta tutte le altre soluzioni cloud mediante l'autenticazione) e passa a nuove e interessanti funzionalità che sbloccare carichi di lavoro di nuovi per gli utenti.  Questi carichi di lavoro o servizi che si desiderano adottare per gli utenti determinerà i requisiti per la progettazione di identità ibrida.  Questi servizi e carichi di lavoro bisogno di utilizzare identità ibrido entrambi locale e nel cloud.  

È necessario eseguire questi aspetti chiave dell'azienda a capire cosa è un requisito di ora e la società da prevede in futuro. Se non si ha la visibilità della strategia a lungo termine per la struttura delle identità ibrida, è molto probabile che che la soluzione non saranno scalable come esigenze crescita o di modifica.   T ha diagramma riportata di seguito mostra un esempio di un'architettura di identità ibrida e carichi di lavoro che sono vengano sbloccati per gli utenti. Questo è solo un esempio di tutte le nuove opportunità che possono essere sbloccati e recapitato con una strategia di gestione delle identità ibrido a tinta unita. 
 
Alcuni componenti che fanno parte dell'architettura di identità ibrido![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinare le esigenze aziendali
Ogni società avrà requisiti differenti, anche se tali società fanno parte di settore stesso, reali requisiti possono variare. È comunque possibile sfruttare le procedure consigliate del settore, ma è alla fine le esigenze aziendali della società che potrebbero causare è possibile definire i requisiti per la progettazione di identità ibrida. 

Assicurarsi che rispondere alle domande seguenti per identificare le esigenze dell'organizzazione:

- La propria azienda non per tagliare costi operativi IT?
- La propria azienda non per proteggere le risorse cloud (app SaaS, infrastruttura)?
- La propria azienda non per modernizzare IT?
  - Sono gli utenti mobili più complesse IT per creare eccezioni nella rete Perimetrale per consentire di tipo diverso di traffico per accedere alle risorse diverse?
  - La propria azienda ha legacy App necessaria per la pubblicazione a questi utenti moderni ma non che è facile riscrittura?
  - La propria azienda necessarie per completare queste operazioni e porta sotto controllo nello stesso momento?
- La propria azienda non per proteggere le identità degli utenti e ridurre i rischi per caricarli nuovi strumenti che utilizzano l'esperienza di Azure sicurezza competenze locale Microsoft?
- La propria azienda sta tentando di eliminare l'account "esterni" temuto in locale e spostarli nel cloud in cui non sono più un rischio inattivo all'interno dell'ambiente locale?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analizzare infrastruttura delle identità locali
Dopo aver creato un'idea relative alle proprie esigenze aziendali, è necessario valutare l'infrastruttura di identità locale. Questa valutazione è importante per la definizione di requisiti tecnici per integrare soluzione identità corrente al sistema di gestione delle identità cloud. Assicurarsi che rispondere alle domande seguenti:

- Quale soluzione di autenticazione e l'autorizzazione non la propria azienda Usa in locale? 
- La propria azienda attualmente installata i servizi di sincronizzazione locale?
- Che la propria azienda Usa qualsiasi provider di identità di terze parti (IdP)?

Inoltre, è necessario prestare particolare attenzione dei servizi cloud che potrebbe avere la propria azienda. Esecuzione di una valutazione per comprendere l'integrazione con i modelli di SaaS, IaaS o PaaS nell'ambiente corrente è molto importante. Assicurarsi che rispondere alle domande seguenti durante la valutazione:
- La propria azienda dispone di qualsiasi integrazione con un provider di servizi cloud?
- In caso affermativo, quali servizi sono in uso?
- Questa integrazione attualmente in produzione o si tratta di un progetto pilota?


>[AZURE.NOTE]
Se si non dispone di un mapping accurato di tutte le applicazioni e servizi cloud, è possibile utilizzare lo strumento di individuazione di App Cloud. Questo strumento può offrire il reparto IT visibilità tutte le attività dell'organizzazione e applicazioni basate su cloud consumer. In questo modo più semplice che mai per Individuazione ombreggiatura IT dell'organizzazione, inclusi i dettagli su criteri di utilizzo e tutti gli utenti che accedono le applicazioni cloud. Per accedere a questo strumento, passare a [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Valutare i requisiti per l'integrazione identità
È necessario valutare i requisiti di integrazione delle identità. È importante per definire i requisiti tecnici per come verranno autenticati gli utenti, come apparirà la presenza dell'organizzazione nel cloud, come l'organizzazione consentirà autorizzazione e quali l'esperienza dell'utente sarà la valutazione. Assicurarsi che rispondere alle domande seguenti:

- Utilizzeranno organizzazione la federazione, autenticazione standard o entrambe?
- È un requisito una federazione?  A causa di quanto segue:
 - Basata su Kerberos Single Sign-on
 - La società ha un'applicazione di locale (uno incorporato festa internamente o 3) che utilizza SAML o funzionalità federazione simili.
 - MFA tramite Smart card. RSA SecurID e così via.
 - Regole di accesso client che consente di risolvere le domande seguenti:
     1. È possibile bloccare tutti accesso esterno a Office 365 in base all'indirizzo IP del client?
     1. È possibile bloccare tutti accesso esterno a Office 365, ad eccezione di Exchange ActiveSync?
     1. È possibile bloccare tutti accesso esterno a Office 365, ad eccezione di applicazioni basate su browser (OWA, SharePoint Online)
     1. È possibile bloccare tutti accesso esterno a Office 365 per i membri di gruppi di Active Directory specifici
- Il controllo della protezione/preoccupazioni
- Investimento già esistente in autenticazione federati
- Specificare il nome dell'organizzazione verrà utilizzato per il dominio nel cloud?
- L'organizzazione dispone di un dominio personalizzato?
    1. È tale dominio pubblico e facilmente verificabile tramite DNS?
    1. In caso contrario, quindi si dispone di un dominio pubblico che può essere utilizzato per registrare un UPN alternativo in Active Directory?
- ID utente sono coerenti per rappresentazione cloud? 
- L'organizzazione dispone di applicazioni che richiedono l'integrazione con servizi cloud?
- L'organizzazione dispone di più domini e verranno tutti utilizza l'autenticazione standard o federato?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Valutare le applicazioni che eseguono nel proprio ambiente
Ora che si ha un'idea relative ai propri locali e infrastruttura cloud, è necessario valutare le applicazioni che eseguono in questi ambienti. Questa valutazione è importante per definire i requisiti tecnici di integrare tali applicazioni in modo che il sistema di gestione delle identità cloud. Assicurarsi che rispondere alle domande seguenti:

- Nel punto in cui verranno live delle applicazioni?
- Gli utenti accedono applicazioni locale?  Nel cloud? O entrambe?
- Esistono piani per creare i carichi di lavoro di applicazione esistente e spostarli nel cloud?
- Ci sono previsti piani di sviluppare nuove applicazioni che si trovano in locale o nel cloud che verrà utilizzato il cloud autenticazione?

## <a name="evaluate-user-requirements"></a>Valutare i requisiti per gli utenti
È anche necessario valutare i requisiti dell'utente. Questa valutazione è importante per definire i passaggi necessari per iniziale del e assistenza degli utenti durante la transizione a nel cloud. Assicurarsi che rispondere alle domande seguenti:

- Gli utenti accedono applicazioni locale?
- Gli utenti accedono applicazioni nel cloud?
- Come eseguire in genere utenti di accedere al proprio ambiente locale?
- Come verranno utenti accesso aggiuntivo nel cloud?

>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Requisiti di risposta determinazione](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) verranno esaminate le opzioni disponibili e i professionisti IT/contro delle singole opzioni.  Da con domande tali che verranno selezionati opzione migliore si adatta l'azienda deve.

## <a name="next-steps"></a>Passaggi successivi
[Requisiti di sincronizzazione della directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
