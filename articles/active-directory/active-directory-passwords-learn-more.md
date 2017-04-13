<properties
    pageTitle="Altre informazioni: Gestione di Azure Active Directory Password | Microsoft Azure"
    description="Argomenti avanzati sulla gestione di Azure Active Directory Password, inclusi password writeback funzionamento, protezione writeback tramite password, come la reimpostazione della password works portale e quali dati vengono utilizzati per la reimpostazione della password."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Ulteriori informazioni sulla gestione delle Password

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Se già distribuito la gestione delle Password o è semplice per altre informazioni tecniche dettagliate che del funzionamento prima di distribuire, in questa sezione si otterranno un'ampia panoramica dei concetti tecniche di servizio. Verranno illustrate le operazioni seguenti:

* [**Panoramica di writeback di password**](#password-writeback-overview)
  - [Come funziona il writeback di password](#how-password-writeback-works)
  - [Scenari supportati per il writeback di password](#scenarios-supported-for-password-writeback)
  - [Modello di sicurezza writeback di password](#password-writeback-security-model)
* [**Come la reimpostazione della password portale aziendale?**](#how-does-the-password-reset-portal-work)
  - [Quali dati vengono utilizzati per la reimpostazione della password?](#what-data-is-used-by-password-reset)
  - [Come accedere a password Reimposta i dati per gli utenti](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Panoramica di writeback di password
Password writeback è un componente di [Azure Active Directory connettersi](active-directory-aadconnect.md) che può essere attivato e utilizzato dal server di sottoscrizione corrente di Azure Active Directory Premium. Per ulteriori informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Writeback di password consente di configurare il tenant cloud per scrivere le password al mittente Active Directory locale.  Parola chiave Elimina di è di dover configurare e gestire una soluzione di reimpostazione password self-service locale complicate e fornisce un modo comodo basate su cloud per gli utenti reimpostare le password in locale nel punto in cui sono.  Sono disponibili informazioni su alcune delle caratteristiche chiave di writeback di password:

- **Commenti e suggerimenti zero ritardo.**  Password writeback è un'operazione icona del.  Se la propria password non soddisfano criteri o non è possibile reimpostare o modificato per qualsiasi motivo, gli utenti verranno visualizzati immediatamente.
- **Supporta reimpostazione della password per gli utenti che utilizzano ADFS o altre tecnologie di federazione.**  Con writeback di password, come gli account utente federato vengono sincronizzati nel tenant di Azure Active Directory, sarà in grado di gestire loro locale password di Active Directory dal cloud.
- **Supporta reimpostazione della password per gli utenti che utilizzano la sincronizzazione delle password hash.** Quando il servizio di reimpostazione password rileva che un account utente sincronizzata sia abilitato per la sincronizzazione delle password hash, si reimposta la locale dell'account e la password cloud contemporaneamente.
- **Supporta la modifica delle password dal Pannello di accesso e Office 365.**  Quando federato o la sincronizzazione delle password degli utenti vengano per cambiare la propria password scaduto o non scaduto, in cui verranno scritti tali password tornare all'ambiente di Active Directory locale.
- **Supporta la scrittura nuovamente le password quando un amministratore Reimposta direttamente dalla** [**Portale di gestione azure**](https://manage.windowsazure.com).  Ogni volta che un amministratore reimposta una password di un utente nel [Portale di gestione Azure](https://manage.windowsazure.com), se tale utente federato o la sincronizzazione delle password, è necessario impostare la password che l'amministratore seleziona sull'annuncio locale, nonché.  Questa operazione non è supportata nel portale di amministrazione di Office.
- **Vengono applicate le locale criteri password Active Directory.**  Quando si reimposta la sua password, occorre assicurarsi che soddisfi i locale dei criteri di Active Directory prima di applicare alla directory.  Sono inclusi cronologia, complessità, età, filtri password e altre limitazioni di password che è stata definita nell'Active Directory locale.
- **Non sono necessarie tutte le regole del firewall in entrata.**  Password writeback utilizza un inoltro Bus di servizio Azure come un canale di comunicazione sottostante, ovvero non è necessario aprire le porte del firewall per questa caratteristica per l'uso in ingresso.
- **Non è supportata per gli account utente che si trovano all'interno di gruppi protetti in Active Directory locale.** Per ulteriori informazioni sui gruppi protetti, vedere [account protetta e gruppi di Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Come funziona il writeback di password
Password writeback sono previsti tre passaggi principali:

- Servizio cloud di reimpostazione password (questo è anche integrato con pagine cambia la password di Azure Active Directory)
- Inoltro di Azure servizio Bus tenant specifiche
- Endpoint la reimpostazione della password locale

Si combinano come descritto nel diagramma qui sotto:

  ![][001]

Quando un hash federato o la password Sincronizza da utente è disponibile per reimpostare o modificare la password nel cloud, si verifica quanto segue:

1.  È possibile controllare il tipo di password l'utente dispone.  Se è presente che la password viene gestita in locale, è garantire che il servizio writeback sia in esecuzione.  In caso affermativo, è consentire all'utente di continuare, in caso contrario, è fornire all'utente che non è possibile reimpostare la password di seguito.
2.  Successivamente, l'utente passa gates autenticazione appropriata e raggiunge la schermata di reimpostazione password.
3.  L'utente seleziona una nuova password e conferma in.
4.  Facendo clic su Invia è crittografare la password non crittografata con una chiave simmetrica creato durante il processo di configurazione writeback.
5.  Dopo la crittografia della password, è includerli in un payload che viene inviato tramite un canale HTTPS per l'inoltro di bus di servizio specifico tenant (che è necessario anche impostare per è durante il processo di configurazione writeback).  L'inoltro è protetto da password casuale che conosca solo l'installazione locale.
6.  Quando il messaggio raggiunge bus di servizio, l'endpoint di reimpostazione password automaticamente riattiva e vede che dispone di una richiesta di reimpostazione in sospeso.
7.  Il servizio quindi consente di cercare l'utente in questione utilizzando l'attributo di ancoraggio cloud.  Per questa ricerca venga eseguita correttamente, l'oggetto utente deve essere presente nello spazio connettore Active Directory, deve essere collegato all'oggetto MV corrispondente e deve essere collegato all'oggetto connettore AAD corrispondente. Infine, in ordine per la sincronizzazione trovare l'account utente, il collegamento a oggetto connettore AD MV deve avere la regola di sincronizzazione `Microsoft.InfromADUserAccountEnabled.xxx` sul collegamento.  Questa operazione è necessaria in quanto quando la chiamata viene dal cloud, utilizza l'attributo cloudAnchor per cercare l'oggetto di spazio connettore AAD il motore di sincronizzazione, quindi segue il collegamento all'oggetto MV e quindi il collegamento all'oggetto Active Directory. Poiché potrebbero essere presenti più oggetti Active Directory (multi-foresta) per lo stesso nome utente, il motore di sincronizzazione si basa sul `Microsoft.InfromADUserAccountEnabled.xxx` collegamento scegliere quello corretto.
8.  Una volta trovata l'account utente, si tenta di reimpostare la password direttamente nella foresta Active Directory appropriata.
9.  Se l'operazione di impostazione password ha esito positivo, è fornire all'utente la propria password è stata modificata e che possono essere inserite nel loro strada.
10. Se la password impostata operazione ha esito negativo, stiamo restituire l'errore all'utente che consenta di riprovare.  L'operazione può avere esito negativo perché il servizio non difetto perché la password siano selezionati non soddisfatto dei criteri dell'organizzazione, perché non è stato possibile trovare l'utente in Active Directory locale, o in alcuni casi.  Si dispone di un messaggio specifico per molti di questi casi e fornire all'utente di operazioni che può eseguire per risolvere il problema.

### <a name="scenarios-supported-for-password-writeback"></a>Scenari supportati per il writeback di password
Nella tabella seguente descrive quali scenari sono supportati per le versioni della funzionalità di sincronizzazione.  In generale, si consiglia di installare l'ultima versione di [Azure AD Connect](active-directory-aadconnect.md#install-azure-ad-connect) se si desidera utilizzare writeback di password.

  ![][002]

### <a name="password-writeback-security-model"></a>Modello di sicurezza writeback di password
Password writeback è un servizio potente e sicuro.  Per proteggere le informazioni, attivare un modello di sicurezza a livelli 4 descritta di seguito.

- **Inoltro bus di servizio specifico tenant** : quando si configura il servizio, è necessario impostare un inoltro bus specifiche del tenant servizio protetto da una password complessa casuale che Microsoft non ha accesso a.
- **Blocco dei chiave di crittografia password crittografia avanzata** – dopo aver creato l'inoltro di bus del servizio, viene creata una chiave simmetrica avanzata utilizzabile per crittografare la password proviene in rete.  Questo tasto si trova solo nell'archivio segreta della società nel cloud, in modo rilevante bloccato e controllati, come le password nella directory.
- Si verifica **TLS standard di settore** : quando si reimposta la password o di una modifica nel cloud, si accetta la password non crittografata e crittografa con la chiave pubblica.  È quindi plop che in un messaggio di HTTPS che viene inviato su un canale crittografato tramite i certificati SSL Microsoft per l'inoltro di bus di servizio.  Dopo il messaggio arriva in Bus di servizio, l'agente locale riattiva, autentica al servizio Bus con una password complessa che era stata generata in precedenza, recupera il messaggio crittografato, decrittografa utilizzando la chiave privata che è generati e tentativi per impostare la password tramite l'API di SetPassword Active Directory.  Questo passaggio è cosa consente di applicare i criteri di password locale di Active Directory (complessità, età, cronologia, filtri, e così via) nel cloud.
- **I criteri di scadenza** , infine, se per qualche motivo il messaggio si trova nel servizio Bus perché il servizio locale è inattivo, verrà timeout e rimosse dopo alcuni minuti per aumentare ulteriormente la protezione.

## <a name="how-does-the-password-reset-portal-work"></a>Come la reimpostazione della password portale aziendale?
Quando un utente passa a portal di reimpostare la password, viene avviato un flusso di lavoro per determinare se tale account utente è valido, quali organizzazione gli utenti a cui appartiene, dove viene gestita password dell'utente e l'utente è concesso in licenza per usare la caratteristica o meno.  Leggere la procedura seguente per conoscere la logica alla base pagina di reimpostazione della password.

1.  Si fa clic sul non è possibile accedere al collegamento dell'account o passare direttamente a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Utente ha digitato un id utente e passa un captcha.
3.  Azure Active Directory consente di verificare se l'utente è possibile utilizzare questa caratteristica eseguendo le operazioni seguenti:
    - Verifica che l'utente disponga di questa funzione attivata e assegnata una licenza di Azure Active Directory.
        - Se l'utente non dispone di abilitare questa funzionalità o una licenza assegnata, l'utente viene richiesto di contattare il suo amministratore per chiedere la reimpostazione della password.
    - Controlli che gli utenti abbiano destra destinate a utenti dati definiti nel proprio account, in base ai criteri di amministratore.
        - Se criteri richiedono solo uno dei problemi, viene garantito che gli utenti abbiano i dati appropriati definiti per almeno uno dei problemi di attivazione dai criteri di amministratore.
          - Se l'utente non è configurato, l'utente è consigliabile contattare il suo amministratore per chiedere la reimpostazione della password.
        - Se il criterio richiede due problemi, viene garantito che gli utenti abbiano i dati appropriati definiti per almeno due dei problemi di attivazione dai criteri di amministratore.
          - Se l'utente non è configurato, quindi è l'utente è consigliabile contattare il suo amministratore per chiedere la reimpostazione della password.
    - Verifica la presenza o meno la password dell'utente viene gestita in locale (federato o se la sincronizzazione delle password hash).
       - Se viene distribuito writeback e la password dell'utente viene gestita in locale, l'utente è possibile continuare a eseguire l'autenticazione e reimpostare la propria password.
       - Se non è stato distribuito writeback e la password dell'utente viene gestita in locale, l'utente viene richiesto di contattare il suo amministratore per chiedere la reimpostazione della password.
4.  Se si stabilisce che l'utente è in grado di correttamente reimpostare la password, quindi l'utente verrà guidato nel processo di ripristino.

Ulteriori informazioni su come distribuire writeback di password in [Guida introduttiva: gestione delle Password di Azure Active Directory](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Quali dati vengono utilizzati per la reimpostazione della password?
La tabella seguente vengono illustrati dove e come questi dati viene utilizzati durante la reimpostazione della password e consentono di stabilire quali opzioni di autenticazione sono appropriate per la propria organizzazione. Questa tabella illustra anche eventuali requisiti di formattazione per i casi in cui si desidera fornire dati per conto di utenti da tracciati di input non convalidati questi dati.

> [AZURE.NOTE] Telefono ufficio non viene visualizzato nel portale di registrazione perché gli utenti possono attualmente non modificare questa proprietà nella directory.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome del metodo di contatto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento di dati di Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Usati / che è possibile impostare in?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisiti di formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefono ufficio</p>
            </td>
            <td>
              <p>NumeroTelefono</p>
              <p>ad esempio Set-MsolUser - UserPrincipalName JWarner@contoso.com - NumeroTelefono "1234567890 + 1 x 1234"</p>
            </td>
            <td>
              <p>Usate in:</p>
              <p>Portale di reimpostazione password</p>
              <p>Impostabile da:</p>
              <p>NumeroTelefono è possibile impostare da PowerShell, DirSync, il portale di gestione di Azure e il portale di amministrazione di Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (ad esempio 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Deve fornire un codice paese<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve fornire un prefisso (se applicabile)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Fornire codice un + in primo piano del paese<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve avere uno spazio tra il resto del numero e il codice paese<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Estensioni non sono supportate, se si dispone di tutte le estensioni specificate, si verrà rimuoverlo dal numero prima di inviare la chiamata telefonica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefono cellulare</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OR</p>
              <p>MobilePhone</p>
              <p>(L'autenticazione di che usare il telefono se sono presenti dati, in caso contrario questa passa al campo telefono cellulare).</p>
              <p>ad esempio Set-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone "1234567890 + 1 x 1234"</p>
            </td>
            <td>
              <p>Usate in:</p>
              <p>Portale di reimpostazione password</p>
              <p>Portale di registrazione</p>
              <p>Impostabile da: </p>
              <p>È possibile impostare dal portale di registrazione di reimpostazione password o il portale di registrazione MFA AuthenticationPhone.</p>
              <p>MobilePhone è possibile impostare da PowerShell, DirSync, il portale di gestione di Azure e il portale di amministrazione di Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (ad esempio 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Fornire un codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Fornire un prefisso (se applicabile).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Devono avere fornire codice un + in primo piano del paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve avere uno spazio tra il resto del numero e il codice paese.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Estensioni non sono supportate, se si dispone di tutte le estensioni specificate, si ignorarlo in fase di invio della chiamata telefonica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Posta elettronica alternativo</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OR</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(L'autenticazione di che posta elettronica viene utilizzato se non sono presenti dati, in caso contrario questo ricorre nel campo di posta elettronica alternativo).</p>
              <p>Nota: il campo di posta elettronica alternativo viene specificato come matrice di stringhe nella directory.  Serve la prima voce nella matrice.</p>
              <p>ad esempio Set-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Usate in:</p>
              <p>Portale di reimpostazione password</p>
              <p>Portale di registrazione</p>
              <p>Impostabile da: </p>
              <p>È possibile impostare dal portale di registrazione di reimpostazione password o il portale di registrazione MFA AuthenticationEmail.</p>
              <p>AlternateEmail è possibile impostare da PowerShell, il portale di gestione di Azure e il portale di amministrazione di Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>o甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Messaggi di posta elettronica è necessario seguire la formattazione standard in base alle.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Messaggi di posta elettronica Unicode sono supportati.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Domande e risposte sicurezza</p>
            </td>
            <td>
              <p>Non è disponibile per la modifica direttamente nella directory.</p>
            </td>
            <td>
              <p>Usate in:</p>
              <p>Portale di reimpostazione password</p>
              <p>Portale di registrazione </p>
              <p>Impostabile da: </p>
              <p>L'unico modo per impostare le domande di sicurezza è tramite il portale di gestione di Azure.</p>
              <p>L'unico modo per impostare le risposte alle domande di sicurezza per un determinato utente è tramite il portale di registrazione.</p>
            </td>
            <td>
              <p>Domande sulla protezione dispone di un massimo di 200 caratteri e un valore minimo di 3 caratteri</p>
              <p>Risposte dispongono di un massimo di 40 caratteri e un valore minimo di 3 caratteri</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Come accedere a password Reimposta i dati per gli utenti
####<a name="data-settable-via-synchronization"></a>Dati che è possibile impostare tramite la sincronizzazione
I campi seguenti è possono sincronizzare i locale:

* Telefono cellulare
* Telefono ufficio

####<a name="data-settable-with-azure-ad-powershell"></a>Dati che è possibile impostare con PowerShell di Azure Active Directory
I campi seguenti sono accessibili con PowerShell di Azure Active Directory e l'API di grafico.

* Posta elettronica alternativo
* Telefono cellulare
* Telefono ufficio
* Autenticazione telefono
* Posta elettronica di autenticazione

####<a name="data-settable-with-registration-ui-only"></a>Dati che è possibile impostare con la registrazione dell'interfaccia utente
Sono accessibili tramite la registrazione SSPR dell'interfaccia utente (https://aka.ms/ssprsetup) solo i campi seguenti:

* Domande e risposte sicurezza

####<a name="what-happens-when-a-user-registers"></a>Cosa accade quando un utente si registra?
Quando si registra un utente, la pagina di registrazione verrà **sempre** impostare i campi seguenti:

* Autenticazione telefono
* Posta elettronica di autenticazione
* Domande e risposte sicurezza

Se si dispone di un valore per **telefono cellulare** o un **Messaggio di posta elettronica alternativo**, gli utenti possono immediatamente usare quelle per reimpostare la password, anche se non registrato per il servizio.  Inoltre, gli utenti vedere quei valori per la registrazione per la prima volta e se si desidera modificare.  Tuttavia, dopo che viene eseguito correttamente al registrato, tali valori verranno mantenuti nel campi **Telefono di autenticazione** e la **Posta elettronica di autenticazione** , rispettivamente.

Può essere utile per lo sblocco di un numero elevato di utenti a utilizzare SSPR consentendo agli utenti di convalidare tali informazioni tramite il processo di registrazione.

####<a name="setting-password-reset-data-with-powershell"></a>Dati con PowerShell la reimpostazione della password di impostazione
È possibile impostare i valori per i campi seguenti con PowerShell di Azure Active Directory.

* Posta elettronica alternativo
* Telefono cellulare
* Telefono ufficio

Per iniziare, è innanzitutto necessario [scaricare e installare il modulo di Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Dopo aver installato, è possibile eseguire la procedura seguente per configurare ogni campo.

#####<a name="alternate-email"></a>Posta elettronica alternativo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Telefono cellulare
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Telefono ufficio
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Dati con PowerShell la reimpostazione della password di lettura
È possibile leggere i valori per i campi seguenti con PowerShell di Azure Active Directory.

* Posta elettronica alternativo
* Telefono cellulare
* Telefono ufficio
* Autenticazione telefono
* Posta elettronica di autenticazione

Per iniziare, è innanzitutto necessario [scaricare e installare il modulo di Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Dopo aver installato, è possibile eseguire la procedura seguente per configurare ogni campo.

#####<a name="alternate-email"></a>Posta elettronica alternativo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Telefono cellulare
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Telefono ufficio
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Autenticazione telefono
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>Posta elettronica di autenticazione
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Collegamenti a password reimpostare documentazione
Di seguito sono disponibili collegamenti a tutte le pagine della documentazione di reimpostazione della Password di Azure Active Directory:

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [**Come funziona**](active-directory-passwords-how-it-works.md) - informazioni sui diversi componenti del servizio e cosa sei ognuno di essi
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - informazioni su come consentire agli utenti di reimpostare e cambiare le password cloud o locale
* [**Personalizza**](active-directory-passwords-customize.md) - informazioni su come personalizzare l'aspetto e l'aspetto e comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md) - informazioni su come distribuire rapidamente e gestire in modo efficiente password all'interno dell'organizzazione
* [**Ottenere informazioni approfondite**](active-directory-passwords-get-insights.md) - informazioni sulla funzionalità di creazione di report integrata
* [**Domande frequenti**](active-directory-passwords-faq.md) - risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md) - informazioni su come risolvere rapidamente i problemi con il servizio



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
