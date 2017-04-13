<properties
    pageTitle="Azure Active Directory servizi di dominio: Scenari di distribuzione | Microsoft Azure"
    description="Scenari di distribuzione per i servizi di dominio Active Directory di Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Scenari di distribuzione e casi di utilizzo
In questa sezione, si osserva alcuni scenari di e casi di utilizzo che vantaggi offerti dai servizi di dominio di Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Amministrazione semplice e sicuro macchine virtuali Azure
È possibile utilizzare i servizi di dominio di Azure Active Directory per gestire le macchine virtuali Azure in modo semplificato. È possibile collegare macchine virtuali di Azure al dominio gestito, consentendo di utilizzare le credenziali di Active Directory aziendale agli utenti di accedere. Questo approccio consente di evitare problemi di gestione delle credenziali, ad esempio la gestione di account di amministratore locale su ciascuna delle macchine virtuali Azure.

Macchine virtuali di server che fanno parte di dominio gestito possono anche essere gestite protette tramite i criteri di gruppo. È possibile applicare le previsioni protezione richiesti alle macchine virtuali Azure e bloccandolo conformemente alle linee guida di protezione a livello aziendale. Ad esempio, è possibile utilizzare le funzionalità di gestione dei criteri di gruppo per limitare i tipi di applicazioni che è possono avviare in queste macchine virtuali.

![Amministrazione semplificata di Azure macchine virtuali](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Come server e altri infrastruttura raggiunga la fine del ciclo di vita, Contoso sta molte applicazioni attualmente ospitate in locale nel cloud. Utilizzo di loro standard IT corrente è necessario che le applicazioni aziendali che ospitano deve essere gestiti tramite criteri di gruppo e dominio. Contoso amministratore IT preferite in macchine virtuali di join dominio distribuite in Azure, per semplificare l'amministrazione. Di conseguenza, gli amministratori e utenti possono accedere usando le proprie credenziali aziendale. Allo stesso tempo, è possibile i computer siano configurati per rispettare le previsioni di protezione necessari tramite criteri di gruppo. Contoso preferisce non è necessario distribuire, monitorare e gestire controller di dominio in Azure per proteggere macchine virtuali di Azure. Servizi di dominio Active Directory Azure è un'ottima soluzione per questo caso di utilizzo.

**Note sulla distribuzione**

Prendere in considerazione i punti importanti seguenti per questo scenario di distribuzione:

- Domini gestiti forniti da servizi di dominio Active Directory Azure costituiscono una struttura semplice OU (unità organizzativa) per impostazione predefinita. Tutti i computer di dominio si trovano in un'unica unità Organizzativa flat. Tuttavia, è possibile creare unità organizzative personalizzate.

- Servizi di dominio Active Directory Azure supporta semplice i criteri di gruppo in forma di un incorporato oggetto Criteri di gruppo ogni per gli utenti e computer contenitori. Non è possibile dei criteri di gruppo per unità Organizzativa/reparto di destinazione, eseguire il filtro WMI o creare un gruppo personalizzato.

- Servizi di dominio Active Directory Azure supporta il base schema oggetto computer di Active Directory. Non è possibile estendere lo schema dell'oggetto computer.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Ascensore e MAIUSC un'applicazione locale che utilizza l'autenticazione di associazione LDAP a servizi di infrastruttura di Windows Azure

![Associazione LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso è un'applicazione locale che è stata acquistata presso un ISV molti anni fa. L'applicazione è in modalità di manutenzione dall'ISV e la richiesta di modifiche all'applicazione è estremamente costosa di Contoso. Questa applicazione presenta un basato sul web front-end che raccoglie le credenziali utente per utilizzare un modulo web e quindi autentica gli utenti tramite l'esecuzione di un'associazione LDAP in Active Directory aziendale. Contoso desidera eseguire la migrazione dell'applicazione in servizi di infrastruttura di Windows Azure. È preferibile che l'applicazione funzioni come, senza eseguire alcuna modifica. Inoltre, gli utenti dovrebbero essere possibile autenticare usando le proprie credenziali aziendale esistente e senza la necessità di addestrare nuovamente gli utenti per eseguire operazioni in modo leggermente diverso. In altre parole, gli utenti finali dovrebbero essere indipendentemente nel punto in cui l'applicazione è in esecuzione e la migrazione deve essere trasparente ad essi.

**Note sulla distribuzione**

Prendere in considerazione i punti importanti seguenti per questo scenario di distribuzione:

- Assicurarsi che l'applicazione non è necessario modificare/scrittura alla directory. Accesso in scrittura LDAP ai domini gestiti forniti da servizi di dominio Active Directory Azure non è supportata.

- Non è possibile modificare le password direttamente per il dominio gestito. Gli utenti possono modificare la password sia utilizzando meccanismo Cambia password self-service di Azure Active Directory o in base alla directory locale. Queste modifiche vengono automaticamente sincronizzate e disponibile nel dominio gestito.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Per saperne di un'applicazione locale che utilizza LDAP ascensore e MAIUSC per accedere alla directory di servizi di infrastruttura di Windows Azure
Contoso è un'applicazione line-of-business (Line) locale sviluppato quasi dieci anni fa. Questa applicazione è presente directory e progettata per funzionare con Windows Server Active Directory. L'applicazione utilizza LDAP (Lightweight Directory Access Protocol) per leggere informazioni/attributi sugli utenti da Active Directory. L'applicazione non o modificare gli attributi in caso contrario scrittura alla directory. Contoso desidera eseguire la migrazione dell'applicazione in servizi di infrastruttura di Windows Azure e ritirare l'hardware locale aging attualmente hosting dell'applicazione. L'applicazione non è possibile riscrittura per utilizzare una versione recente directory API, ad esempio l'API di Azure Active Directory grafico basato su resto. Di conseguenza, un'opzione di ascensore e MAIUSC desiderata mediante l'applicazione può essere migrato per eseguire nel cloud, senza modificare il codice o riscrittura dell'applicazione.

**Note sulla distribuzione**

Prendere in considerazione i punti importanti seguenti per questo scenario di distribuzione:

- Assicurarsi che l'applicazione non è necessario modificare/scrittura alla directory. Accesso in scrittura LDAP ai domini gestiti forniti da servizi di dominio Active Directory Azure non è supportata.

- Assicurarsi che l'applicazione non è necessario un schema di Active Directory personalizzato/esteso. Estensioni dello schema non sono supportate in servizi di dominio Active Directory Azure.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Eseguire la migrazione di un'applicazione di servizio o daemon locale di servizi di infrastruttura di Windows Azure
Alcune applicazioni sono costituiti da più livelli, in uno dei livelli deve eseguire chiamate autenticate a un livello di back-end, ad esempio un livello di database. Gli account di servizio Active Directory vengono in genere utilizzati per questi casi di utilizzo. È possibile ascensore e MAIUSC tali applicazioni servizi di infrastruttura di Windows Azure e usare Azure servizi di dominio Active Directory per soddisfare le esigenze di identità di queste applicazioni. È possibile scegliere di utilizzare lo stesso account di servizio la sincronizzazione dalla directory locale di Azure Active Directory. In alternativa, è possibile prima di tutto creare un'unità Organizzativa e quindi creare un account di servizio distinto nell'unità Organizzativa per la distribuzione di tali applicazioni.

![Account del servizio utilizzando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso è un'applicazione di archivio di software personalizzata che include un front-end web, SQL server e un server di back-end FTP. Autenticazione integrata di Windows degli account del servizio viene utilizzato per eseguire l'autenticazione front-end web al server FTP. Front-end web sia configurato per l'esecuzione come account di servizio. Il server di back-end è configurato per consentire l'accesso dall'account del servizio per front-end web. Contoso non preferisce distribuire una macchina virtuale controller di dominio nel cloud per spostare l'applicazione di servizi di infrastruttura di Windows Azure. Contoso amministratore IT può distribuire il server che ospitano front-end web, SQL server e al server FTP di Azure macchine virtuali. Queste macchine quindi fanno parte di un dominio gestito di servizi di dominio Active Directory Azure. È quindi possibile utilizzare lo stesso account di servizio nella directory di locale per l'autenticazione dell'applicazione. Questo account del servizio sincronizzato con il dominio gestito di servizi di dominio Active Directory Azure ed è disponibile per l'utilizzo.

**Note sulla distribuzione**

Prendere in considerazione i punti importanti seguenti per questo scenario di distribuzione:

- Assicurarsi che l'applicazione utilizza nome utente e password per l'autenticazione. Autenticazione certificato smart card in base a non è supportata da servizi di dominio Active Directory Azure.

- Non è possibile modificare le password direttamente per il dominio gestito. Gli utenti possono modificare la password sia utilizzando meccanismo Cambia password self-service di Azure Active Directory o in base alla directory locale. Queste modifiche vengono automaticamente sincronizzate e disponibile nel dominio gestito.


## <a name="azure-remoteapp"></a>RemoteApp Azure
RemoteApp Azure consente all'amministratore di Contoso creare un insieme di dominio. Questa funzionalità consente di applicazioni remote rappresentate in Azure RemoteApp per l'esecuzione nel computer di dominio e per accedere ad altre risorse mediante l'autenticazione integrata di Windows. Contoso è possibile utilizzare servizi di dominio Active Directory Azure per fornire un dominio gestito utilizzato da Azure RemoteApp dominio raccolte.

![RemoteApp Azure](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Per ulteriori informazioni su questo scenario di distribuzione, vedere l'articolo di Blog di Servizi Desktop remoto [ascensore e MAIUSC i carichi di lavoro con RemoteApp di Azure e Azure servizi di dominio Active Directory](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
