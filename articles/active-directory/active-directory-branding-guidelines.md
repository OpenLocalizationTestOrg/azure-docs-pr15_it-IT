<properties
   pageTitle="Personalizzazione delle linee guida per le applicazioni | Microsoft Azure"
   description="Guida alle risorse orientato agli sviluppatori di Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Personalizzazione delle linee guida per le applicazioni


In questo argomento vengono illustrate le linee guida personalizzazione che è necessario utilizzare quando si sviluppano applicazioni con Azure Active Directory (Azure Active Directory). Questi attenersi alle linee guida per indicare a usare i clienti quando si desidera utilizzare loro account aziendale o dell'istituto di istruzione, gestito in Azure Active Directory, o per l'account loro personale per abbonamento e accesso a un'applicazione.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Account personale rispetto al lavoro o dell'istituto di istruzione da Microsoft.

Microsoft gestisce due tipi di account utente:

- **Account personale** (precedentemente noto come Windows Live ID). Questi account rappresentano la relazione tra *singoli* utenti e Microsoft e vengono utilizzati per accedere a dispositivi e servizi Microsoft. Questi account sono destinati per uso personale.

- **Account aziendale o dell'istituto di istruzione.** Questi account vengono gestiti da Microsoft per conto di organizzazioni che usano Azure Active Directory. Questi account vengono utilizzati per accedere a Office 365 e altri servizi professionali Microsoft.

Microsoft account aziendale o dell'istituto di istruzione in genere assegnati agli utenti finali (dipendenti, studenti, i dipendenti federali) per le organizzazioni (società, dell'istituto di istruzione, agenzia per enti pubblici). Questi account vengono acquisiti direttamente nel cloud, piattaforma Azure Active Directory o sincronizzati con Azure Active Directory da una directory locale, ad esempio Windows Server Active Directory. Microsoft è *depositaria* degli account aziendale o dell'istituto di istruzione, ma l'account di proprietà e controllate dall'organizzazione.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Fare riferimento all'account Azure Active Directory dell'applicazione

Microsoft non esporre gli utenti finali di Azure o i nomi di marchio Active Directory e non è necessario.

- Dopo l'accesso agli utenti, è necessario utilizzare nome e il logo al massimo dell'organizzazione. Questo è preferibile rispetto all'utilizzo generici termini come "organizzazione".

- Quando gli utenti non sono connessi, è necessario fare riferimento ai relativi account come "lavoro o dell'istituto di istruzione account" e utilizzare il logo di Microsoft per comunicare che questi account siano gestiti da Microsoft. Non usare termini come "account enterprise", "account business" o "account aziendale" che creano confusione utente.

## <a name="user-account-pictogram"></a>Pittogramma di account utente
In una versione precedente di queste linee guida, è consigliabile usare un pittogramma "badge blu". In base a utenti e sviluppo commenti e suggerimenti, ora invece è consigliabile l'utilizzo del logo di Microsoft. In questo modo gli utenti conoscono poter riutilizzare account che utilizzano con Office 365 o un altro account di Microsoft business servizi per accedere all'app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Iscrizione e accedere con Azure Active Directory

L'app potrebbe presentare tracciati per abbonamento e l'accesso e le sezioni seguenti includono indicazioni visive per entrambi gli scenari.

**Se l'app supporta l'accesso degli utenti finali alto (ad esempio gratuito al modello di valutazione o freemium)**: È possibile visualizzare un pulsante di **accesso** che consente agli utenti di accedere a un'applicazione con il proprio account di lavoro o il proprio account personale. Azure Active Directory viene visualizzata una richiesta di consenso la prima volta che accedono l'app.

**Se l'app richiede le autorizzazioni che solo gli amministratori possono consenso, o se l'app richiede una licenza aziendale**: È consigliabile separare acquisizione di amministrazione di accesso dell'utente. Il **pulsante "Ottieni questa app"** reindirizzeranno amministratori per l'accesso, chiedere loro di concedere l'autorizzazione per conto di utenti dell'organizzazione. Questo è il vantaggio di eliminazione degli utenti finali consenso istruzioni per l'app.

## <a name="visual-guidance-for-app-acquisition"></a>Visual indicazioni per l'acquisizione di app

Il collegamento "ottenere l'app" necessario reindirizzare l'utente per concedere l'accesso di Azure Active Directory (autorizzare) pagina, per consentire l'amministratore dell'organizzazione autorizzare l'app di accedere ai dati della propria organizzazione sono ospitati da Microsoft. Ulteriori informazioni su come richiedere l'accesso sono descritti nell'articolo [Applicazioni integrazione con Azure Active Directory](active-directory-integrating-applications.md) .

Dopo che gli amministratori consenso all'applicazione in uso, possono scegliere per aggiungerlo al (accessibile dalla forma di griglia e da [https://portal.office.com/myapps](https://portal.office.com/myapps)) esperienza icona di avvio delle app Office 365 agli utenti. Se si desidera annunciare questa funzionalità, è possibile usare termini come "Aggiungere l'app per l'organizzazione" e visualizzare un pulsante alla seguente:

![Tipi di applicazioni e scenari](./media/active-directory-branding-guidelines/add-to-my-org.png)

Tuttavia, è consigliabile scrivere il testo esplicativo invece dei pulsanti. Per esempio:
> *Se si usa già Office 365 o un altro servizio di business da Microsoft, è possibile concedere semplicemente < your_app_name > accesso ai dati dell'organizzazione. In questo modo sarà agli utenti di accedere < your_app_name > con il proprio account di lavoro esistente.*


## <a name="visual-guidance-for-sign-in"></a>Visual indicazioni per l'accesso
L'app deve essere visualizzato un segno in pulsante che reindirizza gli utenti per l'endpoint di accesso corrispondente al protocollo da utilizzare per l'integrazione con Azure Active Directory. La sezione seguente fornisce dettagli sull'aspetto tale pulsante.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pittogramma e "Accedere usando Microsoft"
È l'associazione del logo di Microsoft e le condizioni di "Accesso in con Microsoft" che rappresenta in modo univoco Azure Active Directory tra altri provider di identità può supportare l'app. Se non si dispone di spazio sufficiente per "Accesso in con Microsoft", è ok per ridurre a "Accesso".

![Tipi di applicazioni e scenari](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Tipi di applicazioni e scenari](./media/active-directory-branding-guidelines/sign-in-light.png)

È anche possibile utilizzare una combinazione di colori scuro per i pulsanti.

![Tipi di applicazioni e scenari](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Tipi di applicazioni e scenari](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Personalizzazione accorgimenti e gli errori

**Utilizzate "account aziendale o dell'istituto di istruzione" in combinazione con il pulsante "Accesso in con Microsoft" per fornire ulteriori spiegazioni per aiutare gli utenti finali di riconoscere se potrà usarla.** **Non** usare altre condizioni, ad esempio "account enterprise", "account business" o "account aziendale".

**Non** usare "Office 365 ID" o "Azure". Office 365 è anche il nome di un consumer che offre la possibilità di Microsoft che non utilizzano Azure Active Directory per l'autenticazione.

**Non** modificare il logo di Microsoft.

**Non** esporre agli utenti finali di marchi Azure o Active Directory. È tuttavia ok per utilizzare i termini che seguono con gli sviluppatori, i professionisti IT e gli amministratori.

## <a name="navigation-dos-and-donts"></a>Spostamento accorgimenti e gli errori

**Eseguire** consentono agli utenti di eseguire la disconnessione e passare a un altro account utente. Durante la maggior parte delle persone dispone di un singolo account personali da Microsoft/Facebook/Google/Twitter, persone associate spesso più di un'organizzazione. Supporto per più utenti eseguito l'accesso è disponibile a breve.
