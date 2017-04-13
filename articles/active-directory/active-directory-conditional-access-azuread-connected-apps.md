<properties
    pageTitle="Azure accesso condizionato per le applicazioni SaaS | Microsoft Azure"
    description="Accesso condizionato in Azure Active Directory consente di configurare le regole di accesso di autenticazione a più fattori per applicazione e la possibilità di bloccare l'accesso per gli utenti non in una rete attendibile. "
    services="active-directory"
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
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Guida introduttiva di Azure Active Directory condizionale accesso

Azure Active Directory condizionale Access per le applicazioni [SaaS](https://azure.microsoft.com/overview/what-is-saas/) e Azure Active Directory connessa consente di App che si configura l'accesso condizionale in base a gruppo, luogo e riservatezza del contenuto dell'applicazione. 

Con l'accesso condizionale in base a riservatezza applicazione, è possibile impostare regole di accesso di autenticazione a più fattori (MFA) per ogni applicazione. MFA per ogni applicazione consente di bloccare l'accesso per gli utenti che non sono presenti in una rete attendibile. È possibile applicare regole MFA a tutti gli utenti assegnati all'applicazione o solo per gli utenti all'interno di gruppi di sicurezza specificato.  Gli utenti possono essere esclusi dal requisito MFA se accedono l'applicazione da un indirizzo IP all'interno della rete dell'organizzazione.

Queste funzionalità saranno disponibili per i clienti che hanno acquistato una licenza di Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Prerequisiti di uno scenario
* Licenza per Premium Azure Active Directory

* Federati o gestito tenant di Azure Active Directory

* Tenant federati richiedono l'autenticazione a più fattori sia attivato.

## <a name="configure-per-application-access-rules"></a>Configurare regole di accesso per ogni applicazione

In questa sezione viene descritto come configurare le regole di accesso per ogni applicazione.

1. Accedere al portale classico Azure usando un account che sia un amministratore globale per Azure Active Directory.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Nella scheda Directory selezionare directory.
4. Selezionare la scheda **applicazioni** .
5. Selezionare l'applicazione impostate per la regola.
6. Selezionare la scheda **Configura** .
7. Scorrere fino alla sezione di regole di accesso. Selezionare la regola di accesso desiderato.
8. Specificare gli utenti che verrà applicata la regola.
9. Attivare il criterio selezionando **abilitata essere acceso**.

##<a name="understanding-access-rules"></a>Informazioni sulle regole di accesso

In questa sezione fornisce una descrizione dettagliata delle regole di accesso è supportato in Azure condizionale applicazione accesso.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Specificare gli utenti alle regole per l'accesso

Per impostazione predefinita, il criterio verrà applicato a tutti gli utenti che hanno accesso all'applicazione. È inoltre possibile limitare i criteri per gli utenti che appartengono a gruppi di sicurezza specificati. Pulsante **Aggiungi gruppo** viene utilizzato per selezionare uno o più gruppi nell'apposita finestra di dialogo Selezione applicabili per la regola di accesso. Questa finestra di dialogo può anche essere utilizzato per rimuovere gruppi selezionati. Quando si selezionano le regole da applicare ai gruppi, le regole di accesso verranno applicate solo per gli utenti che appartengono a uno dei gruppi di sicurezza specificati.

Gruppi di sicurezza possono anche essere esplicitamente escluso dai criteri selezionando l'opzione **eccetto** e specificando uno o più gruppi. Gli utenti che fanno parte di un gruppo nell'elenco **tranne** non saranno soggette a requisiti di autenticazione a più fattori, anche se sono membri di un gruppo a cui viene applicata la regola di accesso.
La regola di accesso riportata di seguito richiederà tutti gli utenti del gruppo di responsabili di Usa l'autenticazione a più fattori quando si accede all'applicazione.

![Impostazione delle regole di accesso condizionato con MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Regole di accesso condizionale con MFA
Se un utente è stato configurato utilizzando la funzionalità di autenticazione a più fattori per utente, questa impostazione all'utente verrà combinare con le regole di autenticazione a più fattori dell'app. Questo errore indica un utente che è stato configurato per l'autenticazione a più fattori per utente verrà richiesto di eseguire l'autenticazione a più fattori anche se sono stati esenti dalle regole di autenticazione a più fattori dell'applicazione. Ulteriori informazioni sulle impostazioni di autenticazione e per utente a più fattori.

### <a name="access-rule-options"></a>Opzioni delle regole di accesso
Sono supportate le seguenti opzioni:

* **Richiede l'autenticazione a più fattori**: gli utenti a cui applicano le regole di accesso, sarà necessario per completare l'autenticazione a più fattori prima dell'applicazione in cui è applicato il criterio di accesso.

* **Richiede l'autenticazione a più fattori quando non è in ufficio**: un utente provenienti da un indirizzo IP attendibile non sia necessario eseguire l'autenticazione a più fattori. Nella pagina Impostazioni di autenticazione a più fattori, è possono configurare gli intervalli di indirizzi IP attendibili.

* **Bloccare l'accesso quando non è in ufficio**: un utente che non proviene da un indirizzo IP attendibile verrà bloccato. Nella pagina Impostazioni di autenticazione a più fattori, è possono configurare gli intervalli di indirizzi IP attendibili.

### <a name="setting-rule-status"></a>Impostazione dello stato di regola
Stato regola Access consente di attivare o disattivare le regole. Quando le regole di accesso siano disattivate, il requisito di autenticazione a più fattori non viene applicato.

### <a name="access-rule-evaluation"></a>Valutazione delle regole di accesso

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID connettersi, SAML o WS-Federation valutazione delle regole di accesso. Inoltre, le regole di accesso vengono valutate quando 2.0 OAuth di connettersi OpenID utilizza un token di aggiornamento per acquisire un token di accesso. Se la valutazione dei criteri non riesce quando si utilizza un token di aggiornamento, verrà restituito l' errore **invalid_grant** , indica che l'utente deve eseguire nuovamente l'autenticazione al client.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurare i servizi di federazione per fornire l'autenticazione a più fattori

Per i tenant federati, può essere eseguita MFA oppure Azure Active Directory locale nel server ADFS.

Per impostazione predefinita, MFA si verificherà una pagina ospitata da Azure Active Directory. Per configurare MFA locale, è necessario impostare la proprietà **– SupportsMFA** **true** di Azure Active Directory, utilizzando il modulo di Azure Active Directory per Windows PowerShell.

Nell'esempio seguente viene illustrato come abilitare MFA locale utilizzando il [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Oltre a impostare questo flag, l'istanza di ADFS federato tenant Active Directory deve essere configurato per eseguire l'autenticazione a più fattori. Seguire le istruzioni per [la distribuzione di autenticazione a più fattori Azure locale](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Articoli correlati

- [Protezione dell'accesso a Office 365 e altre App connessi a Azure Active Directory](active-directory-conditional-access.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
