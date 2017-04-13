<properties
    pageTitle="Panoramica sui concettuale di nomi di dominio personalizzato in Azure Active Directory | Microsoft Azure"
    description="Spiega concettuale di utilizzo dei nomi di dominio personalizzato di Azure Active directory, compresa la federazione per single sign-on"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Panoramica sui concettuale di nomi di dominio personalizzato in Azure Active Directory

Un nome di dominio è una parte importante dell'identificatore per molte risorse directory: si tratta di parte di un indirizzo di posta elettronica o nome utente per un utente, parte dell'indirizzo di un gruppo, possono far parte della finestra dell'app ID URI per un'applicazione. Una risorsa di Azure Active Directory (Azure Active Directory) è possibile includere un nome di dominio già verificato per la directory che contiene la risorsa di proprietà. Solo un amministratore globale può eseguire le attività di gestione di dominio in Azure Active Directory.

Nomi di dominio in Azure Active Directory sono univoci. Un nome di dominio può essere usato da un singolo annuncio Azure. Se una directory di Azure Active Directory è verificato un nome di dominio, senza altre directory di Azure Active Directory possibile verificare o utilizzare lo stesso nome di dominio.

## <a name="initial-and-custom-domain-names"></a>Nomi di dominio iniziale e personalizzati

Ogni nome di dominio in Azure Active Directory è un nome di dominio iniziale o un nome di dominio personalizzato.

Ogni Azure AD viene fornito con un nome di dominio iniziale in contoso.onmicrosoft.com modulo. Il nome di dominio di livello terzo, in questo esempio "contoso.onmicrosoft.com," è stato stabilito quando nella directory è stata creata, in genere l'amministratore che ha creato nella directory. Il nome di dominio iniziale di una directory non è possibile modificarla o eliminarla. Il nome di dominio iniziale, mentre funzionale, destinato principalmente a essere utilizzata come meccanismo di avvio automatico fino a quando non è verificato un nome di dominio personalizzato.

Nella maggior parte degli ambienti di produzione, una directory ha almeno un dominio verificato di personalizzato, ad esempio "contoso.com," ed è il dominio personalizzato è visibile agli utenti finali. Un nome di dominio personalizzato è un nome di dominio di proprietà e da tale organizzazione, ad esempio "contoso.com," usi, ad esempio il proprio sito web di hosting. Il nome di dominio è familiare ai dipendenti perché fa parte del nome dell'utente che vengono utilizzati per accedere alla rete aziendale, o per inviare e recuperare posta elettronica.

Prima che può essere utilizzato da Azure Active Directory, il nome di dominio personalizzato deve essere aggiunto alla directory e verificato.

## <a name="verified-and-unverified-domain-names"></a>Nomi di dominio verificato e non verificati

Il nome di dominio iniziale di una directory viene valutato in modo implicito verificati da Azure Active Directory. Un amministratore aggiunge un nome di dominio personalizzato per un annuncio Azure, viene visualizzato inizialmente in uno stato non verificato. Azure Active Directory non sarà necessario usare un nome di dominio non verificati risorse directory. In questo modo che solo una directory può usare un nome di dominio e che l'organizzazione utilizza il nome di dominio proprietario effettivo tale nome di dominio.

Azure Active Directory consente di verificare la proprietà di un nome di dominio cercando una voce specifica nel file di zona DNS (service) nome di dominio corrispondente al nome di dominio. Per verificare la proprietà di un nome di dominio, un amministratore ottiene la voce DNS da Azure Active Directory che cercherà Azure Active Directory e aggiunge tale voce al file di zona DNS per il nome di dominio. File di zona DNS verrà gestito dal proprio registrar per tale dominio. Vedere l'articolo per [l'aggiunta di un dominio personalizzato nella directory di Azure Active Directory](active-directory-add-domain.md)vengono illustrati i passaggi per verificare un dominio.

Aggiunta di una voce DNS per il file di zona per il nome di dominio non ha alcun effetto altri servizi di dominio, ad esempio posta elettronica o il Web.

## <a name="federated-and-managed-domain-names"></a>Nomi di dominio federato e gestiti

Un nome di dominio personalizzato in Azure Active Directory può essere configurato per consentire agli utenti un accesso federato esperienza tra il Active Directory locale e Azure Active Directory. Configurazione di un dominio per la federazione richiede aggiornamenti per le risorse con privilegi di Azure Active Directory e anche per Windows Server Active Directory. Configurare che un dominio federato deve essere completato da Azure AD Connect o tramite PowerShell. Dal portale di classica Azure non è possibile iniziare la federazione di un dominio personalizzato. [Guardare questo video per informazioni sulla configurazione di ADFS per effettuare l'accesso utente con Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Dominio non federato detti anche domini gestiti. Il dominio iniziale di una directory di Azure Active Directory in modo implicito viene valutato come un dominio gestito.

## <a name="primary-domain-names"></a>Nomi di dominio primario

Il nome di dominio primario per una directory è il nome di dominio già selezionato come valore predefinito per la parte 'dominio' del nome utente quando un amministratore crea un nuovo utente nel [portale classica Azure](https://manage.windowsazure.com/) o un altro portale, ad esempio il portale di amministrazione di Office 365. Una directory consentito solo un nome di dominio primario. Un amministratore può modificare il nome di dominio primario sia qualsiasi verificato il dominio personalizzato che non è federato, o per il dominio iniziale.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nomi di dominio in Azure Active Directory e altri servizi Online Microsoft

Un nome di dominio deve essere verificato in Azure Active Directory prima che possa essere utilizzata da un altro servizio Online Microsoft come Exchange Online, SharePoint Online e Intune. Questi altri servizi in genere richiedono un amministratore aggiungere uno o più voci DNS specifiche per il servizio.

Un'app web Azure utilizza il proprio meccanismo per verificare la proprietà di un dominio. Anche se in precedenza verificato per l'uso da un'app web Azure di un abbonamento che si basa su tale annuncio di Azure, è necessario verificare un dominio per l'utilizzo con Azure Active Directory. Un'app web Azure possa utilizzare un nome di dominio è stato verificato in una directory diversa dalla directory che consente di proteggere l'applicazione web.

## <a name="managing-domain-names"></a>Gestione dei nomi di dominio

Dal portale di classica Azure e da PowerShell, è possono eseguire le attività di gestione di dominio. Molte attività può essere completata tramite l'API di Azure Active Directory grafico (in anteprima pubblico).

-   [Aggiunta e la verifica di un nome di dominio personalizzato](active-directory-add-domain.md)

-   [La gestione dei domini nel portale di classica Azure](active-directory-add-manage-domain-names.md)

-   [Uso di PowerShell per gestire i nomi di dominio in Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso dell'API di Azure Active Directory grafico per gestire i nomi di dominio in Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
