<properties
    pageTitle="Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come utilizzare i gruppi di Azure Active Directory per gestire l'accesso degli utenti locali e cloud applicazioni e risorse."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory

Azure Active Directory (Azure Active Directory) è una completa soluzione di gestione di identità e accessi che fornisce un set di funzionalità per gestire l'accesso locale e applicazioni cloud e risorse tra i servizi online di Microsoft Office 365 e un mondo di applicazioni SaaS non Microsoft efficaci. In questo articolo viene fornita una panoramica, ma se si desidera iniziare a utilizzare i gruppi di Azure Active Directory immediatamente, seguire le istruzioni nella [gestione dei gruppi di sicurezza in Azure Active Directory](active-directory-accessmanagement-manage-groups.md). Se si vuole vedere come si può usare PowerShell per gestire i gruppi di Azure Active directory è possibile leggere più in [Azure Active Directory anteprima cmdlet per la gestione del gruppo](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Per utilizzare Azure Active Directory, è necessario un account Azure. Se non si dispone di un account, è possibile [iscriversi a un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).


All'interno di Azure Active Directory, uno dei principali caratteristiche è la possibilità di gestire l'accesso alle risorse. Queste risorse possono far parte della directory, come nel caso delle autorizzazioni per gestire gli oggetti tramite i ruoli nella directory o risorse esterne alla directory, ad esempio applicazioni SaaS, servizi di Azure e i siti di SharePoint o sulle risorse locale. Sono disponibili quattro modi che un utente può essere assegnato diritti di accesso a una risorsa:


1. Assegnazione diretta

    Gli utenti possono essere assegnati direttamente a una risorsa dal proprietario della risorsa.

2. Appartenenza ai gruppi

    Un gruppo può essere assegnato a una risorsa dal proprietario della risorsa e in questo modo, concessione i membri del gruppo l'accesso alla risorsa. L'appartenenza del gruppo può essere gestito dal proprietario del gruppo. In modo efficace, il proprietario della risorsa delega l'autorizzazione per assegnare gli utenti per le risorse al proprietario del gruppo.

3. Basata su regole

    Il proprietario di risorse è possibile utilizzare una regola per esprimere gli utenti che devono essere assegnati l'accesso a una risorsa. Il risultato della regola dipende gli attributi utilizzati in tale regola e i rispettivi valori per utenti specifici e in questo modo, il proprietario della risorsa delegati in modo efficiente il diritto di gestire l'accesso a loro risorsa all'origine rilevanti per gli attributi utilizzati nella regola. Proprietario della risorsa ancora gestisce la regola e determina quali attributi e valori forniscono l'accesso alle relative risorse.

4. Autorità esterna

    Accesso a una risorsa dipende da un'origine esterna. ad esempio, un gruppo che viene sincronizzato da un'origine rilevanti, ad esempio una directory locale o da un'app SaaS, ad esempio lavorativo. Il proprietario di risorse assegna il gruppo per consentire l'accesso alla risorsa e dell'origine esterna gestisce i membri del gruppo.

  ![Panoramica del diagramma di gestione di access](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Guardare un video che illustra la gestione degli accessi

È possibile guardare un breve video che illustra più su questo:

**Azure Active Directory: Introduzione a dinamico appartenenza per i gruppi**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Come accedere a gestione di lavoro di Azure Active Directory?
Al centro di Azure AD access soluzione è il gruppo di sicurezza. L'uso di un gruppo di sicurezza per gestire l'accesso alle risorse è un paradigma noto, che consente di visualizzare un modo semplice e flessibile fornire l'accesso a una risorsa per il gruppo desiderato degli utenti. Il proprietario di risorse (o l'amministratore della directory) è possibile assegnare un gruppo per consentire a accedere direttamente alle risorse che sono proprietari. I membri del gruppo saranno forniti l'accesso e il proprietario di risorse può delegare il diritto di gestire l'elenco dei membri di un gruppo a un altro utente, ad esempio un responsabile di reparto o un amministratore dell'help desk.

![Diagramma di gestione di Azure Active Directory accesso](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Il proprietario di un gruppo possa inoltre renderne tale gruppo richieste self-service. In questo modo, un utente finale possibile cercare e trovare il gruppo e apportare una richiesta di partecipazione a, in modo efficace la ricerca dell'autorizzazione per accedere alle risorse che vengono gestite tramite il gruppo. Il proprietario del gruppo possa configurare il gruppo in modo che le richieste di join vengono approvate automaticamente o che richiedono l'approvazione dal proprietario del gruppo. Quando si effettua una richiesta di partecipazione a un gruppo, la richiesta di join è inoltrata a proprietari del gruppo. Se uno dei proprietari approva la richiesta, all'utente richiedente riceve una notifica e l'utente viene aggiunto al gruppo. Se uno dei proprietari rifiuta la richiesta, all'utente richiesta una notifica, ma non è stato aggiunto al gruppo.


## <a name="getting-started-with-access-management"></a>Guida introduttiva di gestione dell'accesso
Pronti per iniziare? È consigliabile provare alcune attività di base che è possibile eseguire con i gruppi di Azure Active Directory. Utilizzare queste funzionalità per fornire accesso speciali a diversi gruppi di persone per diverse risorse dell'organizzazione. Un elenco di base primi passi sono elencate di seguito.

* [Creazione di una regola semplice per configurare le appartenenze dinamiche per un gruppo](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Utilizzo di un gruppo per gestire l'accesso alle applicazioni SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Disponibilità di un gruppo per utente finale modalità self-service](active-directory-accessmanagement-self-service-group-management.md)

* [La sincronizzazione di un gruppo locale in Azure con Azure AD Connect](active-directory-aadconnect.md)

* [Gestione dei proprietari di un gruppo](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Passaggi successivi per la gestione di access
Ora che si è compreso nozioni fondamentali sulla gestione dell'accesso, ecco alcune funzionalità avanzate aggiuntive disponibili in Azure Active Directory per gestire l'accesso per le applicazioni e risorse.

* [Utilizzo degli attributi per creare regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gestire i gruppi di sicurezza di Azure Active Directory](active-directory-accessmanagement-manage-groups.md)

* [Impostazione dei gruppi dedicati Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md)

* [Guida di riferimento grafico API per i gruppi](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi](active-directory-accessmanagement-groups-settings-cmdlets.md)
