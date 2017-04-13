<properties
    pageTitle="Gestione delle applicazioni con Azure Active Directory | Microsoft Azure"
    description="In questo articolo i vantaggi dell'integrazione di Azure Active Directory locale, cloud e applicazioni SaaS."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Gestione delle applicazioni con Azure Active Directory

Oltre ai flussi di lavoro effettivo o contenuto, le aziende hanno due requisiti di base per tutte le applicazioni:

1. Per aumentare la produttività, applicazioni dovrebbero essere facile individuare e accedere

2. Per abilitare la sicurezza e la governance, l'organizzazione deve supervisione su chi può ed effettivamente accede ogni applicazione e controllo

Nel mondo di applicazioni cloud ciò può meglio ottenuto utilizzando l'identità di controllo "*chi è autorizzato a eseguire le operazioni*".

Nei sistemi informatici della terminologia:

- *Chi* è noto come *identità* - la gestione di utenti e gruppi

- *Quali* è nota come *gestione degli accessi* : la gestione dell'accesso alle risorse protette

Entrambi i componenti insieme sono noti come *identità e gestione di Access (IAM)*, che è definita da [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) group come "*disciplina di sicurezza che consente a singoli appropriate accedere alle risorse destra destra volte per motivi*".

Passiamo cos'è il problema? Se *non è gestito* in un'unica posizione con una soluzione integrata IAM:

- Gli amministratori di identità sono necessario creare singolarmente e aggiornare gli account utente in tutte le applicazioni separatamente, un'attività ridondante e richiedere molto tempo.

- Gli utenti devono memorizzare più le credenziali per accedere alle applicazioni che necessarie per l'uso con. Di conseguenza, gli utenti comandi sono particolarmente utili per annotare le password o usare altre soluzioni di gestione password che introduce altri rischi di protezione dei dati.

- Attività ridondanti, tempo ridurre la quantità di utenti e amministratori lavora sulle attività dell'azienda che aumentare conclusione dell'azienda.

Pertanto, quali in genere impedisce alle organizzazioni di adottare soluzioni integrate IAM?

- Soluzioni più tecniche sono basate su piattaforme di software che devono essere distribuiti e adattato per ogni organizzazione per le applicazioni.

- Applicazioni cloud spesso adozione una velocità maggiore rispetto a organizzazione IT può essere integrato con le soluzioni IAM esistenti.

- Sicurezza e il monitoraggio utensili richiedono un'ulteriore personalizzazione e l'integrazione di realizzare scenari E2E completi.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory integrato con le applicazioni

Azure Active Directory è identità completa di Microsoft come una soluzione di servizio (IDaaS) che:

- Consente di IAM come un servizio cloud 

- Fornisce la gestione centrale accesso single-sign-on (SSO) e creazione di report 

- Gestione di accesso integrato supporta per [migliaia di applicazioni](https://azure.microsoft.com/marketplace/active-directory/) nella raccolta di applicazione, inclusi Salesforce, Google Apps, casella, Concur e così via. 


Con Azure Active Directory, tutte le applicazioni di cui si pubblica per i partner e clienti (azienda o ai clienti) hanno la stessa identità e l'accesso alle funzionalità di gestione.<br> In questo modo è possibile ridurre sensibilmente i costi operativi.

Cosa fare se è necessario implementare un'applicazione che non è ancora elencata nella raccolta dell'applicazione? Mentre si tratta di un po' più tempo rispetto alla configurazione dei Single Sign-on per le applicazioni dalla raccolta applicazione, Azure Active Directory offre una procedura guidata che consente di con la configurazione.

Il valore di Azure Active Directory non è limitate "semplicemente" applicazioni cloud. È anche possibile usarlo con le applicazioni locale fornendo accesso remoto protetto. Con l'accesso remoto sicura, è possibile eliminare la necessità di VPN o altri implementazione della gestione delle tradizionale accesso remoto.

Grazie alla gestione degli accessi centrale e single sign-on (SSO) per tutte le applicazioni, Azure Active Directory fornisce per risolvere problemi di sicurezza e la produttività dati principale.

- Gli utenti possono accedere più applicazioni con un segno in cui sono disponibili più tempo a reddito generazione o per le aziende le attività di operazioni eseguite.

- Gli amministratori di identità possono gestire l'accesso alle applicazioni in un'unica posizione.

Il vantaggio per l'utente e per la propria azienda è più evidente. Diamo un'occhiata ai vantaggi per un amministratore di identità e all'organizzazione.

## <a name="integrated-application-benefits"></a>Vantaggi di applicazione integrata

Il processo di Single Sign-on include due passaggi:

- Autenticazione, il processo di convalida l'identità dell'utente.

- Autorizzazione, la decisione per enable o bloccare l'accesso a una risorsa a un criterio di accesso.

Quando si usa Azure Active Directory per gestire l'applicazione e abilitare Single Sign-on:

- Autenticazione viene eseguita in locale (ad esempio AD) o un account Azure Active Directory dell'utente.

- Autorizzazione esegue sui criteri di assegnazione e protezione dell'Azure Active Directory garantire esperienza utente coerente e che consente di aggiungere un'assegnazione, posizioni e condizioni MFA in qualsiasi applicazione, indipendentemente dalla relativa funzionalità interna.

È importante tenere presente che la modalità di autorizzazione venga aggiunto nell'applicazione di destinazione varia a seconda di come l'applicazione è stata integrata con Azure Active Directory.

- **Le applicazioni pre-integrate dal provider del servizio** Ad esempio Office 365 e Azure, si tratta di applicazioni create direttamente su Azure Active Directory e basarsi su di esso per le funzionalità di gestione delle identità e accesso complete. Accedere a queste applicazioni tramite le informazioni della directory e rilascio token.

- **Le applicazioni pre-integrate da Microsoft e applicazioni personalizzate** Si tratta di applicazioni cloud indipendente che si basano su una directory di interno dell'applicazione e possono funzionare indipendente Azure Active Directory. Accedere a queste applicazioni eseguendo una credenziale specifica applicazione assegnata a un account di applicazione. A seconda delle funzionalità dell'applicazione le credenziali potrebbero essere token federazione o nome utente e password di un account in precedenza è stato eseguito il provisioning dell'applicazione.

- **Applicazioni locale** Le applicazioni pubblicate attraverso il proxy di applicazione di Azure Active Directory principalmente per consentire l'accesso alle applicazioni locale. Queste applicazioni si basano su centrale nella directory locale come Windows Server Active Directory. Accesso a queste applicazioni è attivato per attivare il proxy per fornire il contenuto per l'utente finale rispettando il requisito di accesso locale.

Ad esempio, se un utente viene aggiunto all'organizzazione, è necessario creare un account per l'utente in Azure Active Directory per le operazioni di accesso principale. Se l'utente richiede l'accesso a un'applicazione gestita, ad esempio Salesforce, è necessario creare un account per l'utente in Salesforce e collegare all'account Azure per utilizzare la funzionalità Single Sign-on. Quando l'utente lascia l'organizzazione, è consigliabile eliminare l'account Azure Active Directory e tutti gli account corrispondente il IAM memorizza delle applicazioni di cui l'utente abbia accesso alla.

## <a name="access-detection"></a>Rilevamento di Access

In una versione recente aziende, i reparti IT spesso non sono compatibili con tutte le applicazioni cloud in uso. In combinazione con individuazione App Cloud, Azure Active Directory offre una soluzione per rilevare queste applicazioni.

## <a name="account-management"></a>Gestione degli account

In genere, la gestione degli account in varie applicazioni è un processo manuale eseguito da IT o supporto tecnico personale nell'organizzazione. Azure Active Directory completamente automatizzata gestione degli account in tutte le applicazioni di servizio provider integrata e le applicazioni di Microsoft che supporta il provisioning automatico dell'utente o SAML JIT pre-integrate.

## <a name="automated-user-provisioning"></a>Il provisioning automatico dell'utente

Alcune applicazioni offrono interfacce di automazione per la creazione e rimozione (o la disattivazione) degli account. Se un provider offre un'interfaccia, viene utilizzata da Azure Active Directory. Consente di ridurre i costi operativi perché le attività amministrative eseguito automaticamente e migliora la protezione dell'ambiente poiché riduce la possibilità di accesso non autorizzato.

## <a name="access-management"></a>Gestione dell'accesso

Utilizzo di Azure Active Directory è possibile gestire accesso alle applicazioni che utilizzano singoli o una regola basata sulle risorse delle assegnazioni. È inoltre possibile delegare accedere a gestione per le persone giuste nell'organizzazione garantire la migliore supervisione e riducendo le attività di supporto tecnico.

## <a name="on-premises-applications"></a>Applicazioni locale

L'applicazione proxy consente di pubblicare le applicazioni locale agli utenti che sia coerente accesso esperienza con applicazione cloud moderno e sui vantaggi offerti dalle funzionalità di monitoraggio, creazione di report e sicurezza di Azure Active Directory.

## <a name="reporting-and-monitoring"></a>Creazione di relazioni e il monitoraggio

Azure Active Directory offre funzionalità che consentono di sapere di monitoraggio e report pre-integrati chi ha accesso alle applicazioni e quando in cui effettivamente utilizzati.

## <a name="related-capabilities"></a>Relative funzionalità

Con Azure Active Directory è possibile proteggere le applicazioni con i criteri di accesso granulare e MFA già integrato. Per ulteriori informazioni sulle MFA Azure, vedere [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Guida introduttiva

Per iniziare a integrazione di applicazioni con Azure Active Directory, consultare la [Guida introduttiva di integrazione di Azure Active Directory con le applicazioni di acquisizione](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Vedere anche

[Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
