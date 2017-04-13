<properties
    pageTitle="Active Directory Federation Services gestione e personalizzazione con Azure AD Connect | Microsoft Azure"
    description="Gestione di ADFS Active Directory con Azure AD Connect e la personalizzazione delle AD FS accesso esperienza utente con Azure AD Connect e PowerShell."
    keywords="ADFS, ADFS, ADFS gestione AAD connettersi, connettersi, accesso in ADFS personalizzazione, ripristinare la federazione di protezione, Office 365, componente"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Active Directory Federation Services gestione e personalizzazione con Azure AD Connect

Attività di dettagli in questo articolo correlati alla Active Directory Federation Services (ADFS) che possono essere eseguite con Azure Active Directory connettersi e altre attività di ADFS comuni che possono essere necessari per una configurazione di una farm di ADFS.

| Argomento | Vengono illustrate |
|:------|:-------------|
|**Gestione di ADFS Active Directory**|
|[Ripristinare la protezione](#repairthetrust)| Il ripristino di questo tipo di relazione con Office 365 |
|[Aggiungere un server ADFS](#addadfsserver) | Espansione della farm di ADFS con un server ADFS|
|[Aggiungere un server di proxy ADFS applicazioni web](#addwapserver) | Espansione della farm di ADFS con un server WAP aggiuntivo|
|[Aggiungere un dominio federato](#addfeddomain)| Aggiunta di un dominio federato|
| **Personalizzazione di ADFS Active Directory**|
|[Aggiungere un logo della società personalizzato o illustrazione](#customlogo)| Personalizzare una pagina di accesso di ADFS con un logo della società e di un'illustrazione |
|[Aggiungere una descrizione di accesso](#addsignindescription) | Aggiunta di una descrizione della pagina di accesso |
|[Modificare le regole attestazione di ADFS](#modclaims) | Modifica delle attestazioni ADFS per i diversi scenari di federazione |

## <a name="ad-fs-management"></a>Gestione di ADFS Active Directory

Azure AD Connect offre diverse attività correlate alla ADFS che possono essere eseguite tramite la creazione guidata Azure AD Connect con intervento minimo dell'utente. Al termine dell'installazione di Azure AD Connect eseguendo la procedura guidata, è possibile eseguire la procedura per eseguire attività aggiuntive.

### Ripristinare la protezione<a name=repairthetrust></a>

Azure AD Connect possibile verificare lo stato corrente del trust ADFS e Azure Active Directory e intraprendere azioni appropriate per ripristinare la protezione. Seguire la procedura seguente per ripristinare un annuncio Azure e attendibilità ADFS.

1. Selezionare **Ripristina AAD e considerate attendibili ADFS** dall'elenco di attività aggiuntive.
![Ripristinare AAD e ADFS attendibile](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Nella pagina **connessione a Azure Active Directory** fornire le credenziali di amministratore globale per Azure Active Directory e fare clic su **Avanti**.
![Connettersi a Azure Active Directory](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Nella pagina delle **credenziali di accesso remoto** , immettere le credenziali per l'amministratore del dominio.
![Credenziali di accesso remoto](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Dopo aver fatto clic **successivo**, Azure AD Connect verrà verificare la presenza di integrità di certificato e visualizzare eventuali problemi.

    ![Stato dei certificati](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    La pagina **pronto per la configurazione** verrà visualizzato l'elenco delle azioni eseguite per ripristinare la protezione.

    ![Pronto per la configurazione](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Fare clic su **Installa** per ripristinare la protezione.

>[AZURE.NOTE] Azure AD Connect può solo ripristino oppure agire sui certificati presenti autofirmato. Azure AD Connect non è possibile ripristinare i certificati di terze parti.

### Aggiungere un server ADFS<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect richiede il file di certificato PFX per aggiungere un server ADFS. Di conseguenza, è possibile eseguire questa operazione solo se è stato configurato la farm di ADFS con Azure AD Connect.

1. Selezionare **Distribuisci un Server federativo aggiuntivi** e fare clic su **Avanti**.
![Server federativo aggiuntive](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Nella pagina **connessione a Azure Active Directory** immettere le credenziali di amministratore globale per Azure Active Directory e fare clic su **Avanti**.
![Connettersi a Azure Active Directory](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Specificare il dominio le credenziali di amministratore.
![Credenziali di amministratore di dominio](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect chiederà la password del file PFX forniti durante la configurazione della nuova farm di ADFS con Azure AD Connect. Fare clic su **Immettere la Password** per specificare la password per il file PFX.
![Password del certificato](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Specificare il certificato SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Nella pagina **AD FS server** immettere il nome del server o l'indirizzo IP da aggiungere alla farm ADFS.
![Server ADFS Active Directory](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Fare clic su **Avanti** e scorrere la pagina **Configura** finale. Al termine dell'aggiunta di server farm ADFS Azure AD Connect, sarà l'opzione per verificare la connettività.
![Pronto per la configurazione](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Installazione completata](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Aggiungere un server di proxy ADFS applicazioni web<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect richiede il file di certificato PFX per aggiungere un server proxy di applicazioni web. Di conseguenza, sarà possibile eseguire questa operazione solo se è stato configurato la farm di ADFS con Azure AD Connect.

1. Selezionare **Distribuire Proxy di applicazione Web** nell'elenco delle attività disponibili.
![Distribuire proxy di applicazione web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Specificare le credenziali di amministratore globale Azure.
![Connettersi a Azure Active Directory](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Nella pagina **certificato SSL specificare** specificare la password per il file PFX forniti durante la configurazione della farm di ADFS con Azure AD Connect.
![Password del certificato](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Specificare il certificato SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Aggiungere il server da aggiungere come un proxy di applicazione web. Perché il server proxy di applicazione web potrebbe non essere unito al dominio, la procedura guidata chiederà di credenziali amministrative per il server da aggiungere.
![Credenziali del server amministrativo](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Nella pagina delle **credenziali di protezione Proxy** , specificare le credenziali di amministratore per configurare l'attendibilità proxy e accedere al server primario della farm di ADFS.
![Credenziali di protezione del proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Nella pagina **pronto per la configurazione** guidata mostra l'elenco di azioni che verranno eseguite.
![Pronto per la configurazione](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Fare clic su **Installa** per completare la configurazione. Una volta completata la configurazione, questa procedura guidata offre la possibilità di verificare la connessione al server. Fare clic su **verifica** per controllare la connettività.
![Installazione completata](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Aggiungere un dominio federato<a name=addfeddomain></a>

È facile aggiungere un dominio federato con Azure Active Directory utilizzando Azure AD Connect. Azure AD Connect aggiunge il dominio per la federazione e modifica le regole di richiesta di rimborso per riflettere correttamente autorità quando si hanno più domini federati con Azure Active Directory.

1. Per aggiungere un dominio federato, selezionare l'attività **aggiungere un ulteriore dominio Azure AD**.
![Ulteriori dominio di Azure Active Directory](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Nella pagina successiva della procedura guidata, specificare le credenziali di amministratore globale per Azure Active Directory.
![Connettersi a Azure Active Directory](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Nella pagina delle **credenziali di accesso remoto** , specificare il dominio le credenziali di amministratore.
![Credenziali di accesso remoto](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Nella pagina successiva, la procedura guidata verrà visualizzato un elenco dei domini di Azure Active Directory con cui è possibile impostare la federazione directory locale. Selezionare il dominio dall'elenco.
![Azure dominio di Active Directory](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Dopo aver scelto il dominio, la procedura guidata verranno forniti informazioni appropriate relative alle altre azioni che verrà eseguita la procedura guidata e del relativo impatto della configurazione. In alcuni casi, se si seleziona un dominio che non è ancora verificato in Azure Active Directory, la procedura guidata fornirà con informazioni che consentono di verificare il dominio. Per ulteriori informazioni, vedere [aggiungere il nome di dominio personalizzato per Azure Active Directory](active-directory-add-domain.md) .

5. Fare clic su **successiva**e pagina **pronto per la configurazione** verrà visualizzato l'elenco delle azioni eseguite Azure AD Connect. Fare clic su **Installa** per completare la configurazione.
![Pronto per la configurazione](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalizzazione di ADFS Active Directory

Nelle sezioni seguenti forniscono informazioni dettagliate su alcune operazioni comuni che potrebbe essere necessario eseguire per personalizzare la pagina di accesso di ADFS.

### Aggiungere un logo della società personalizzato o illustrazione<a name=customlogo></a>

Per modificare il logo della società che viene visualizzato nella pagina di **accesso** , usare il seguente cmdlet di Windows PowerShell e sintassi.

> [AZURE.NOTE] Le dimensioni consigliate per il logo sono 260 x 35 @ 96 dpi con una dimensione non superiore a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Il parametro *TargetName* è obbligatorio. Il tema predefinito che viene rilasciato con ADFS denominato predefinito.


### Aggiungere una descrizione di accesso<a name=addsignindescription></a>

Per aggiungere una descrizione della pagina di accesso alla **pagina di accesso**, usare il seguente cmdlet di Windows PowerShell e sintassi.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificare le regole attestazione di ADFS<a name=modclaims></a>

ADFS supporta un linguaggio Richiedi RTF che è possibile utilizzare per creare regole attestazione personalizzata. Per ulteriori informazioni, vedere [Il ruolo del linguaggio di regola attestazione](https://technet.microsoft.com/library/dd807118.aspx).

Le sezioni seguenti descrivono come è possibile scrivere regole personalizzate per alcuni scenari relative ai Azure Active Directory e federazione ADFS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID immutabile condizionale su un valore è presente nell'attributo

Azure AD Connect consente di specificare un attributo da utilizzare come un ancoraggio origine quando gli oggetti vengono sincronizzati in Azure Active Directory. Se il valore dell'attributo personalizzato non è vuoto, è consigliabile inviare una richiesta di ID non modificabile. Ad esempio, si potrebbe selezionare **ms-ds-consistencyguid** come l'attributo per l'ancoraggio di origine e rilasciare **ImmutableID** come **ms-ds-consistencyguid** nel caso in cui l'attributo è un valore sulla base di essa. Se non c'è alcun valore sulla base dell'attributo, problema **objectGuid** come ID non modificabile.  È possibile creare il set di regole attestazione personalizzata come descritto nella sezione seguente.

**Regola 1: Gli attributi di Query**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

In questa regola, si esegue una query i valori di **ms-ds-consistencyguid** e **objectGuid** per l'utente da Active Directory. Modificare il nome di archivio in un nome di archivio appropriato nella distribuzione di ADFS. Inoltre è possibile modificare il tipo di reclami a un tipo di reclami corretta per la federazione come definito per **objectGuid** e **ms-ds-consistencyguid**.

Inoltre, utilizzando **aggiungere** e non **problema**evitare di aggiungere un problema in uscita per l'entità e si possono usare i valori come valori intermedi. Si verrà generato la richiesta in una regola successiva dopo avere stabilito il valore da utilizzare come ID non modificabile.

**Regola 2: Verifica l'esistenza di ms-ds-consistencyguid per l'utente**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Questa regola definisce un contrassegno temporaneo denominato **idflag** è impostato su **useguid** se non esiste alcun **ms-ds-concistencyguid** popolato per l'utente. La logica alla base questa operazione consiste nel fatto che ADFS non consentita sulle attestazioni vuote. Pertanto quando si aggiungono delle attestazioni http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid nella regola 1, si finisce per ottenere un **msdsconsistencyguid** richiedere solo se il valore viene inserito per l'utente. Se non è vuoto, ADFS vede che avrà un valore vuoto e inserisce immediatamente. Tutti gli oggetti avrà **objectGuid**, in modo che la domanda viene mantenuto sono dopo l'esecuzione di regola 1.

**Regola 3: Problema ms-ds-consistencyguid come ID non modificabile, se presenta**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Si tratta di un controllo **esistente** implicito. Se è presente il valore per la richiesta, problema che come ID non modificabile. Nell'esempio precedente vengono utilizzate Richiedi **nameidentifier** . È necessario modificare questa al tipo di domanda appropriato per l'ID non modificabile nel proprio ambiente.

**Regola 4: Se non è presente ms-ds-consistencyGuid problema objectGuid come ID non modificabile**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

In questa regola che si intende archiviare semplicemente il contrassegno temporaneo **idflag**. È possibile decidere se si desidera inviare la domanda in base al valore.

> [AZURE.NOTE] La sequenza di queste regole è importante.

#### <a name="sso-with-a-subdomain-upn"></a>Single Sign-On con un sottodominio UPN

È possibile aggiungere più di un dominio deve essere federato utilizzando Azure AD Connect, come descritto nella [aggiungere un nuovo dominio federato](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). È necessario modificare la richiesta UPN in modo che l'ID emittente corrisponde al dominio radice e non il sottodominio, poiché il dominio radice federati riguarda anche l'elemento figlio.

Per impostazione predefinita, la regola di richiesta di rimborso per ID emittente impostata come:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Richiesta di rimborso ID emittente predefinito](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La regola predefinita è sufficiente accetta il suffisso UPN e viene usato nella richiesta di ID emittente. Ad esempio John è un utente in sub.contoso.com e contoso.com è federato con Azure Active Directory. John immette john@sub.contoso.com il nome utente durante l'accesso a Azure Active Directory e l'ID di emittente predefinito richiedere regola in ADFS viene gestita nel modo seguente.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valore attestazione:** http://sub.contoso.com/adfs/services/trust/

Per salvare solo il dominio radice nel valore Richiedi emittente, modificare la regola Richiedi come segue.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle [Opzioni di accesso utente](active-directory-aadconnect-user-signin.md).
