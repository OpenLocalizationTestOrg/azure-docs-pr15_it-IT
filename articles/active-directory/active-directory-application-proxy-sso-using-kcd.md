<properties
    pageTitle="Single sign-on con Proxy applicazione | Microsoft Azure"
    description="Viene illustrato come fornire il single sign-on con Azure Active Directory applicazione Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Single sign-on con Proxy dell'applicazione

Il Single sign-on è un elemento principale del Proxy di applicazione Azure Active Directory. Offre la migliore esperienza utente con la procedura seguente:

1. Un utente accede al cloud  
2. Convalida di sicurezza tutti si verifica nel cloud (autenticazione preliminare)  
3. Quando la richiesta viene inviata all'applicazione locale, il connettore Proxy applicazione rappresenta l'utente. L'applicazione di back-end ritiene che si tratta di un utente regolare provenienti da un dispositivo di dominio.

![Diagramma di Access da utente finale, tramite Proxy di applicazione, alla rete aziendale](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy di applicazione Azure Active Directory consente di assicurare un'esperienza single sign-on (SSO) per gli utenti. Attenersi alle istruzioni seguenti per pubblicare le app con Single Sign-on:


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>Single Sign-on per le app integrata di Windows locale con KCD Proxy dell'applicazione
È possibile abilitare il single sign-on alle applicazioni mediante integrata l'autenticazione di Windows integrata (Windows) assegnando autorizzazione applicazione Proxy connettori di Active Directory per rappresentare utenti e inviare e ricevere i token suo conto.


### <a name="network-diagram"></a>Diagramma reticolare

Il diagramma viene illustrato il flusso quando si tenta di accedere a un'applicazione locale che utilizza integrata di Windows.

![Diagramma di flusso di autenticazione Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L'utente immette l'URL per accedere all'applicazione locale tramite Proxy dell'applicazione.
2. Proxy applicazione reindirizza la richiesta di servizi di autenticazione di Azure Active Directory preautenticare. A questo punto, Azure Active Directory applica qualsiasi di autenticazione e criteri di autorizzazione, ad esempio l'autenticazione. Se l'utente viene convalidata, Azure Active Directory crea un token e viene inviato all'utente.
3. L'utente passa il token al Proxy di applicazione.
4. Proxy di applicazione convalidato il token e recupera il nome dell'entità utente (UPN) del file e quindi invia la richiesta, l'UPN e il nome dell'entità servizio (SPN) al connettore tramite un canale sicuro multiplo autenticato.
5. Il connettore esegue stato Kerberos vincolato delega (KCD) con la locale Active Directory, che rappresenta l'utente per ottenere un token Kerberos all'applicazione.
6. Active Directory invia il token Kerberos per l'applicazione al connettore.
7. Il connettore invia la convocazione originale nel server di applicazioni, utilizzando il token Kerberos ricevuto da Active Directory.
8. L'applicazione invia la risposta al connettore quindi restituita al servizio Proxy di applicazione e infine all'utente.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare con Single Sign-on per Proxy di applicazione, assicurarsi che l'ambiente sia pronto con le configurazioni e impostazioni seguenti:

- L'App, ad esempio App Web di SharePoint, sono configurate per utilizzare l'autenticazione di Windows. Per ulteriori informazioni vedere [Attivare il supporto per l'autenticazione Kerberos](https://technet.microsoft.com/library/dd759186.aspx)o per SharePoint, vedere [pianificare per l'autenticazione Kerberos in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- I nomi dell'entità servizio non sono tutte le app.

- Server che esegue il connettore e server che esegue l'app sono dominio e parte dello stesso dominio o attendibilità domini. Per ulteriori informazioni sull'aggiunta al dominio, vedere [partecipare a un Computer a un dominio](https://technet.microsoft.com/library/dd807102.aspx).

- Server che esegue il connettore ha accesso a leggere la TokenGroupsGlobalAndUniversal degli utenti. Si tratta di un'impostazione che potrebbe interessata per l'ambiente di protezione. È possibile ottenere ulteriore assistenza con questo valore in [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configurazione di Active Directory

Configurazione di Active Directory varia a seconda che il connettore Proxy applicazione e il server pubblicato attiva nello stesso dominio o meno.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Connettore e server pubblicati nello stesso dominio

1. In Active Directory, fare clic su **Strumenti** > **utenti e computer**.
2. Selezionare il server che esegue il connettore.
3. Pulsante destro del mouse e scegliere **proprietà** > **la delega**.
4. Selezionare **computer attendibile per la delega solo ai servizi specificati** e in **servizi a cui l'account può presentare credenziali delegate**, aggiungere il valore per l'identità SPN del server dell'applicazione.
5. In questo modo il connettore Proxy di applicazione rappresentare utenti in Active Directory relativi alle applicazioni definite nell'elenco.

![Schermata della finestra delle proprietà di connettore server](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Connettore e server pubblicato in domini diversi

1. Per un elenco di prerequisiti per l'utilizzo di KCD in domini, vedere [Kerberos alla delega in domini](https://technet.microsoft.com/library/hh831477.aspx).
2. In Windows 2012 R2, utilizzare la `principalsallowedtodelegateto` proprietà sul server Connector per attivare il Proxy di applicazione al delegato per il server di connettore, dove si trova il server pubblicato `sharepointserviceaccount` e il server delega `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`può essere l'account del computer stored procedure o un account di servizio in cui è in esecuzione il pool di applicazioni SP.


### <a name="azure-classic-portal-configuration"></a>Configurazione del portale classica Azure

1. Pubblicare l'applicazione seguendo le istruzioni nelle [applicazioni di pubblicazione con Proxy dell'applicazione](active-directory-application-proxy-publish.md). Assicurarsi di selezionare **Azure Active Directory** come il **Metodo di autenticazione preliminare**.
2. Dopo l'applicazione viene visualizzata nell'elenco delle applicazioni, selezionarla e fare clic su **Configura**.
3. In **proprietà**, impostare **Il metodo di autenticazione interno** per **L'autenticazione di Windows**.  
  ![Configurazione avanzata dell'applicazione](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Immettere **SPN applicazione interno** del server dell'applicazione. In questo esempio, l'entità servizio per l'applicazione pubblicata sia http/lob.contoso.com.  

>[AZURE.IMPORTANT] Se l'UPN locale e l'UPN di Azure Active Directory non sono identici, sarà necessario configurare l' [identità di accesso delegati](#delegated-login-identity) per autenticazione preliminare per l'uso.

|  |  |
| --- | --- |
| Metodo di autenticazione interna | Se si usa Azure Active Directory per autenticazione preliminare, è possibile impostare un metodo di autenticazione interna per consentire agli utenti di trarre vantaggio dal single sign-on (SSO) a questa applicazione. <br><br> Selezionare **L'autenticazione di Windows** (integrata di Windows) se l'applicazione utilizza integrata di Windows ed è possibile configurare Kerberos vincolato delega (KCD) per attivare Single Sign-on per questa applicazione. È necessario configurare le applicazioni che utilizzano integrata di Windows tramite KCD, in caso contrario Proxy dell'applicazione non saranno in grado di pubblicare queste applicazioni. <br><br> Se l'applicazione non utilizza integrata di Windows, selezionare **Nessuno** . |
| Applicazione interna SPN | Questo è il nome di identità di servizio (SPN) dell'applicazione interno come configurato in Azure Active Directory in locale. L'entità servizio viene utilizzato il connettore Proxy di applicazione per recuperare i token Kerberos per l'applicazione utilizzando KCD. |


## <a name="sso-for-non-windows-apps"></a>Single Sign-on per le applicazioni non Windows
Il flusso di delega Kerberos in Azure Active Directory applicazione Proxy viene avviato quando Azure Active Directory autentica nel cloud. Dopo la richiesta arriva locale, il connettore Proxy di Azure Active Directory applicazione un ticket Kerberos per conto dell'utente tramite l'interazione con Active Directory locale. Questo processo viene indicato come Kerberos vincolato delega (KCD). Nella fase successiva viene inviata una richiesta all'applicazione back-end con questo ticket Kerberos. Sono disponibili numerosi protocolli che definiscono come inviare richieste. La maggior parte dei server di Windows non prevedono Negotiate/SPNego che ora supportato su Proxy di applicazione Azure Active Directory.

![Diagramma di Single Sign-On non Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identità di accesso delegati
Identità di accesso delegati consente di gestire due scenari di accesso diversi:

- Le applicazioni non Windows che in genere ottengono identità utente sotto forma di un nome utente o il nome dell'account Giorgio, non un indirizzo di posta elettronica (username@domain).
- Configurazioni di accesso alternativo nel punto in cui l'UPN in Azure Active Directory e l'UPN di Active Directory locale sono diversi.

Proxy di applicazione, è possibile selezionare identità da utilizzare per ottenere il ticket Kerberos. Questa impostazione è per ogni applicazione. Alcune di queste opzioni sono adatti per sistemi che non accettare il formato dell'indirizzo di posta elettronica, gli altri utenti sono progettati per l'account di accesso alternativo.

![Schermata di parametro identità di accesso delegati](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se viene utilizzata l'identità di accesso delegati, il valore potrebbe non essere univoco per tutti i domini o insiemi di strutture all'interno dell'organizzazione. Per evitare questo problema, queste applicazioni due volte con due diversi gruppi di connettore di pubblicazione. Poiché ogni applicazione dispone di un gruppo di destinatari utente diverso, è possibile unire i connettori in un dominio diverso.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Uso di Single Sign-on quando locali e cloud identità non sono uguali
Se non diversamente configurato, Proxy di applicazione presuppone che gli utenti abbiano esattamente la stessa identità nel cloud e locale. È possibile configurare, per ogni applicazione, quali identità deve essere utilizzato quando si esegue il single sign-on.  

Questa funzionalità consente molte organizzazioni che hanno identità locale e cloud diversa di Single Sign-on dal cloud alle App locale senza richiedere agli utenti di immettere diversi nomi utente e password. Sono comprese le organizzazioni che:

- Hanno più domini internamente (joe@us.contoso.com, joe@eu.contoso.com) e un unico dominio nel cloud (joe@contoso.com).

- Disporre di nome di dominio non routing internamente (joe@contoso.usa) e una persona nel cloud.

- Non utilizzare nomi di dominio internamente (Fausto)

- Usare diversi alias locale e nel cloud. Ad esempio joe-johns@contoso.comVS.joej@contoso.com  

Inoltre consente con le applicazioni che non accetta indirizzi sotto forma di indirizzo di posta elettronica, è uno scenario molto comune per i server di back-end non Windows.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Impostazione di Single Sign-on per diverse identità cloud e locale

1. Configurare le impostazioni di Azure AD Connect in modo che l'identità principale sia l'indirizzo di posta elettronica (posta elettronica). In tal caso come parte del processo di personalizzazione, modificando il campo **Nome dell'entità utente** nelle impostazioni di sincronizzazione. Nota che queste impostazioni determinano anche come utenti accedono a Office 365, dispositivi Windows10 e altre applicazioni che utilizzano Azure Active Directory come archivio identità.  
  ![Schermata di identificazione utenti - elenco a discesa Nome dell'entità utente](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. In impostazioni di configurazione dell'applicazione per l'applicazione di cui che si desidera modificare, selezionare l' **Identità di accesso delegati** da utilizzare:
  - Nome dell'entità utente:joe@contoso.com  
  - Nome dell'entità utente alternativo:joed@contoso.local  
  - Nome utente parte del nome dell'entità utente: Fausto  
  - Nome utente parte del nome dell'entità utente alternativo: joed  
  - Nome dell'account locale Giorgio: seconda della configurazione di controller di dominio locale

  ![Schermata di accesso delegati identità dal menu a discesa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Risoluzione dei problemi di Single Sign-on per diverse identità
Se si verifica un errore nel processo di Single Sign-on verrà visualizzato nel registro eventi machine connettore come descritto nella [risoluzione dei problemi](active-directory-application-proxy-troubleshoot.md).
Ma in alcuni casi, la richiesta verrà correttamente inviata all'applicazione back-end mentre l'applicazione risponderà in vari altri risposte HTTP. Risoluzione dei problemi di questi casi dovrebbe iniziare esaminando il numero di eventi 24029 sul computer connettore nel registro eventi sessione Proxy dell'applicazione. L'identità dell'utente utilizzato per la delega verrà visualizzato nel campo "utente" all'interno i dettagli dell'evento. Per attivare registro sessione, selezionare **Mostra analitico e del Registro di debug** nel caso in cui il Visualizzatore menu Visualizza.


## <a name="see-also"></a>Vedere anche

- [Pubblicare applicazioni con Proxy dell'applicazione](active-directory-application-proxy-publish.md)
- [Risoluzione dei problemi che si verificano con Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)
- [Utilizzo di applicazioni sulle attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
