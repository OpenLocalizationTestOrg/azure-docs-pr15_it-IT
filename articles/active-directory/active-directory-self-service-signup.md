<properties
    pageTitle="Che cos'è l'iscrizione Self-Service per Azure? | Microsoft Azure"
    description="Una panoramica sulla modalità self-service l'iscrizione per Azure, gestire il processo di iscrizione a e subentrare un nome di dominio DNS."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>Che cos'è l'iscrizione Self-Service per Azure?

In questo argomento viene illustrato il processo di iscrizione self-service e come subentrare un nome di dominio DNS.  

## <a name="why-use-self-service-signup"></a>Perché usare l'iscrizione self-service?

- È possibile ottenere clienti ai servizi che vogliono più rapidamente.
- Creare offerte basata su posta elettronica di un servizio.
- Creare flussi di iscrizione a basata su posta elettronica che rapidamente consentono agli utenti di creare identità con loro alias di posta elettronica facile da ricordare lavoro.
- Directory di Azure non gestita può essere trasformata in directory gestite in un secondo momento e riutilizzare per altri servizi.

## <a name="terms-and-definitions"></a>Termini e le definizioni

+ **Iscrizione Self-Service**: si tratta del metodo in base al quale si iscrive a un servizio cloud e ha un'identità creata automaticamente in Azure Active Directory (Azure Active Directory) in base al proprio dominio di posta elettronica.
+ **Directory di Azure non gestito**: si tratta della directory in cui è stato creato l'identità. Una directory non gestita è una directory che è privo di amministratore globale.
+ **Verifica posta elettronica utente**: si tratta di un tipo di account utente in Active Directory Azure. Un utente che dispone di un'identità creata automaticamente dopo l'iscrizione a un'offerta modalità self-service è noto come utente amministratore verificato di posta elettronica. Un utente verificato di posta elettronica è un membro regolare di una directory contrassegnata con creationmethod = EmailVerified.

## <a name="user-experience"></a>Esperienza utente

Si supponga ad esempio, un utente il cui indirizzo di posta elettronica Dan@BellowsCollege.com riceve file riservati tramite posta elettronica. I file protetto da Azure Rights Management (RMS Azure). Ma organizzazione di Dan, studi soffietto, non è iscritta per Azure RMS, né è distribuito RMS di Active Directory. In questo caso, Dan possono iscriversi per una sottoscrizione gratuita RMS per le persone per leggere i file protetti.

Se Dan è il primo utente con un indirizzo di posta elettronica da BellowsCollege.com per accedere a questa modalità self-service che offre la possibilità, quindi viene creata una directory non gestita per BellowsCollege.com in Azure Active Directory. Se altri utenti del dominio BellowsCollege.com effettua l'iscrizione per questa offerta o un'offerta modalità self-service simile, avrà anche gli account utente verificato di posta elettronica creati nella stessa directory non gestita in Azure.

## <a name="admin-experience"></a>Esperienza di amministrazione

Un amministratore appartiene il nome di dominio DNS di una directory di Azure non gestito possono essere subentrare oppure unire la directory dopo provare la proprietà. Sezioni che seguono illustrano l'esperienza di amministratore in modo più dettagliato, ma Ecco un riepilogo:

- Quando subentrare una directory di Azure non gestita, è sufficiente diventa l'amministratore globale della directory non gestita. In alcuni casi si tratta di un contenuto interno.
- Quando si unisce una directory di Azure non gestita, aggiungere il nome di dominio DNS della directory non gestito nella directory di Azure gestita e viene creato un mapping di utenti a risorse in modo gli utenti possono continuare ad accedere ai servizi senza interruzioni. In alcuni casi si tratta di un contenuto esterno.

## <a name="what-gets-created-in-azure-active-directory"></a>Cosa viene creato in Azure Active Directory?

#### <a name="directory"></a>Directory

- Viene creata una directory di Azure Active Directory per il dominio, una directory per ogni dominio.
- Directory di Azure Active Directory non ha alcun amministratore globale.

#### <a name="users"></a>Utenti

- Per ogni utente che si iscrive, viene creato un oggetto utente nella directory di Azure Active Directory.
- Tutti gli oggetti utente viene contrassegnato come esterno.
- Ogni utente è concesso l'accesso al servizio che sono iscritti.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Come richiedere un annuncio di Azure modalità self-service directory per un dominio che si è proprietari del?

È possibile richiedere una modalità self-service Azure Active Directory directory eseguendo la convalida del dominio. La convalida dei domini dimostra di che essere proprietari del dominio mediante la creazione di record DNS.

Esistono due modi per eseguire acquisizioni DNS di una directory di Azure Active Directory:

- acquisizione interno (amministratore rileva una directory di Azure non gestita e desidera trasformare in una directory gestita)
- acquisizione esterni (amministratore tenta di aggiungere un nuovo dominio alla directory di Azure gestiti)

Può essere utile per la convalida che si possiede un dominio perché sta eseguendo su una directory non gestita dopo che un utente ha eseguito l'iscrizione self-service o nell'aggiunta di un nuovo dominio alla directory gestito. Ad esempio, si dispone di un dominio denominato contoso.com e si desidera aggiungere un nuovo dominio denominato contoso.co.uk o contoso.uk.

## <a name="what-is-domain-takeover"></a>Che cos'è l'acquisizione di dominio?  

In questa sezione viene descritto come convalidare che si possiede un dominio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Che cos'è la convalida del dominio e il motivo per cui viene utilizzato?

Per eseguire operazioni su una directory, Azure Active Directory richiede convalidare la proprietà del dominio DNS.  Convalida del dominio consente di richiedere la directory e uno alzare di livello nella directory modalità self-service in una directory gestita o unire la directory modalità self-service in una directory gestita esistente.

## <a name="examples-of-domain-validation"></a>Esempi di convalida di dominio

Esistono due modi per eseguire acquisizioni DNS di una directory:

+ acquisizione interno (ad esempio, un amministratore rileva una directory in modalità self-service non gestita e desidera trasformare in directory gestita)
+ acquisizione esterni (ad esempio, un amministratore tenta di aggiungere un nuovo dominio a una directory gestita)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Acquisizione interno - promuovere una directory in modalità self-service non gestita per essere gestita

Quando si esegue l'operazione di acquisizione interno, la directory viene convertita da una directory non gestita in una directory gestita. È necessario completare la convalida del nome di dominio DNS, in cui si crea un record MX o un record TXT in zona DNS. L'azione:

+ Convalida che si è proprietari del dominio
+ Consente di directory gestite
+ Rende l'amministratore globale della directory

Supponiamo che un amministratore IT da studi soffietto scopre che gli utenti dell'istituto hanno effettuato l'iscrizione per le offerte modalità self-service. Come registrato il proprietario del DNS nome BellowsCollege.com, l'amministratore IT può convalidare la proprietà del nome del DNS in Azure e quindi subentrare nella directory non gestita. La directory diventa quindi gestita e l'amministratore IT viene assegnato il ruolo di amministratore globale per la directory BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Contenuto esterno - unire una directory modalità self-service in una directory gestita esistente

In un contenuto esterno, si dispone già di una directory gestita e si desidera che tutti gli utenti e gruppi di una directory non gestito per partecipare a tale directory gestita, anziché due personalizzati separano directory.

Gli amministratori di una directory gestita, l'aggiunta di un dominio e tale dominio accade disporre di una directory non gestita è associata.

Si supponga, ad esempio, si è un amministratore IT e si dispone già di una directory gestita per Contoso.com, un nome di dominio registrato all'organizzazione. Si scopre che gli utenti dell'organizzazione sono state eseguite Self-Service alto per un'offerta di utilizzando il nome di dominio di posta elettronica user@contoso.co.uk, che corrisponde a un altro nome di dominio che appartiene all'organizzazione. Gli utenti dispongono attualmente di account in una directory non gestita contoso.co.uk.

Non si vuole gestire due directory distinte, in modo che non è unire la directory non gestita contoso.co.uk directory IT gestite esistente per contoso.com.

Contenuto esterno segue lo stesso processo di convalida DNS come contenuto interno.  Differenza fase: gli utenti e i servizi vengono rimappati alla directory IT gestiti.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Che cos'è l'impatto dell'esecuzione di un contenuto esterno?

Con un contenuto esterno, viene creato un mapping di utenti a risorse in modo che gli utenti possono continuare ad accedere ai servizi senza interruzioni. Molte applicazioni, tra cui RMS per singoli utenti, gestiscono bene il mapping di utenti a risorse e gli utenti possono continuare ad accedere tali servizi senza alcuna modifica. Se un'applicazione non consente di gestire il mapping di utenti a risorse in modo efficace, contenuto esterno potrebbe essere bloccato in modo esplicito per impedire agli utenti di un'esperienza scarsa.

#### <a name="directory-takeover-support-by-service"></a>supporto assunzione directory dal servizio

I seguenti servizi supportano attualmente contenuto:

- RMS


Breve essere supporto acquisizione i servizi seguenti:

- Ottenere informazioni

Di seguito non e richiede un'azione di amministrazione aggiuntivi per eseguire la migrazione dei dati utente dopo un contenuto esterno.

- SharePoint o OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Come eseguire un acquisizione di nome di dominio DNS

Sono disponibili alcune opzioni per eseguire la convalida di un dominio (ed eseguire acquisizioni se lo si desidera):

1.  Portale di gestione di Azure

    Acquisizioni viene attivata eseguendo un'aggiunta del dominio.  Se esiste già una directory per il dominio, è necessario l'opzione per eseguire un contenuto esterno.

    Accedere al portale di Azure usando le proprie credenziali.  Passare alla directory esistenti e quindi **Aggiungi**dominio.

2.  Office 365

    È possibile utilizzare le opzioni nella pagina [Gestisci domini](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) in Office 365 per l'uso con i domini e record DNS. Vedere [verificare il dominio in Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    I passaggi seguenti sono necessari per la convalida usando Windows PowerShell.

    Passaggio    |   Cmdlet di usare
    ------- | -------------
    Creare un oggetto di credenziali | Get-Credential
    Connettersi a Azure Active Directory | Connettere MsolService
    ottenere un elenco di domini   | Get-MsolDomain
    Creare una richiesta di verifica  | Get-MsolDomainVerificationDns
    Creare record DNS   | Eseguire questa operazione sul server DNS
    Verificare il problema    | Confermare MsolEmailVerifiedDomain

Per esempio:

1. Connettersi a Azure Active Directory utilizzando le credenziali che sono state utilizzate per rispondere all'offerta modalità self-service: importazione modulo MSOnline $msolcred = get-credential connettersi msolservice-$msolcred delle credenziali

2. Ottenere un elenco dei domini:

    Get-MsolDomain

3. Eseguire il cmdlet Get-MsolDomainVerificationDns per creare una richiesta di verifica:

    Get-MsolDomainVerificationDns-DomainName *nome_dominio* – modalità DnsTxtRecord

    Per esempio:

    Get-MsolDomainVerificationDns-DomainName contoso.com: modalità DnsTxtRecord

4. Copiare il valore (verifica) restituito da questo comando.

    Per esempio:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. Nello spazio dei nomi pubblico DNS, creare un record txt DNS che contiene il valore copiato nel passaggio precedente.

    Il nome per il record è il nome del dominio padre, pertanto se si creano record di risorse utilizzando il ruolo di DNS da Windows Server, lasciare Record nome vuoto e solo per incollare il valore nella casella di testo

6. Eseguire il cmdlet conferma MsolDomain per verificare il problema:

    Confermare MsolEmailVerifiedDomain - DomainName *nome_dominio*

    Per esempio:

    Confermare MsolEmailVerifiedDomain - DomainName contoso.com

Un'operazione impegnativa esito negativo restituisce alla richiesta senza un messaggio di errore.

## <a name="how-do-i-control-self-service-settings"></a>Come è possibile controllare le impostazioni modalità self-service?

Gli amministratori hanno oggi due controlli modalità self-servizi. È possibile controllare:

- Se gli utenti possono partecipare directory tramite posta elettronica.
- Se gli utenti possono assegnare licenze sole per applicazioni e servizi.


### <a name="how-can-i-control-these-capabilities"></a>Come è possibile controllare queste funzionalità?

Un amministratore può configurare queste funzionalità utilizzando i parametri di cmdlet Set-MsolCompanySettings Azure Active Directory:

+ **AllowEmailVerifiedUsers** controlla se un utente può creare o partecipare a una directory non gestita. Se si imposta il parametro per $false, nessun utente verificato di posta elettronica possono partecipare nella directory.
+ **AllowAdHocSubscriptions** controlla la possibilità per gli utenti a eseguire Self-Service su. Se si imposta il parametro su $false, non è possibile eseguire l'iscrizione self-service.


### <a name="how-do-the-controls-work-together"></a>Come i controlli funzionano insieme?

Questi due parametri possono essere utilizzati insieme per definire un controllo più preciso Self-Service su. Ad esempio, il comando seguente consente agli utenti di eseguire Self-Service verso l'alto, ma solo se tali utenti hanno già un account in Azure Active Directory (in altre parole, gli utenti che sarà necessario un account di posta elettronica verificato da creare non è possibile eseguire Self-Service alto):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

Diagramma di flusso seguente illustra tutte le combinazioni diverse per i parametri e le condizioni per la directory e Self-Service risultante alto.

![][1]

Per ulteriori informazioni ed esempi su come utilizzare questi parametri, vedere [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Vedere anche

-  [Come installare e configurare PowerShell Azure](../powershell-install-configure.md)

-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Riferimento sui Cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
