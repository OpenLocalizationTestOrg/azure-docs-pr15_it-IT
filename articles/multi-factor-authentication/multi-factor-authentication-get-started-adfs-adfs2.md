<properties
    pageTitle="Utilizzare Server Azure MFA con ADFS 2.0 | Microsoft Azure"
    description="Questa è la pagina di autenticazione a più fattori Azure che descrive come iniziare a utilizzare MFA Azure e ADFS 2.0."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Proteggere le risorse cloud e locali con Azure a più fattori autenticazione Server ADFS 2.0

In questo articolo è per le organizzazioni che federate con Azure Active Directory e si desidera proteggere le risorse locali o nel cloud. Proteggere le risorse usando il Server di autenticazione a più fattori Azure e configurarlo per l'uso con ADFS in modo che la verifica in due passaggi è attivata per valore alto il punto finale.

In questa documentazione viene utilizzando il Server di autenticazione a più fattori Azure con ADFS 2.0.  È possibile ottenere ulteriori informazioni sulle [risorse locali e cloud protezione tramite Server di autenticazione a più fattori Azure con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteggere ADFS 2.0 con un proxy
Per proteggere con un proxy ADFS 2.0, installare il Server di autenticazione a più fattori Azure sul server proxy ADFS e configurare il Server.

### <a name="configure-iis-authentication"></a>Configurare l'autenticazione IIS

1. All'interno del Server di autenticazione a più fattori di Azure, fare clic sull'icona di **Autenticazione IIS** nel menu a sinistra.
2. Fare clic sulla scheda **Basate su moduli** .
3. Scegliere il **pulsante Aggiungi....** pulsante.
<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Per rilevare automaticamente il nome utente, la password e le variabili del dominio, immettere l'URL di accesso (ad esempio https://sso.contoso.com/adfs/ls) nella finestra di dialogo Auto-Configure Form-Based sito Web e fare clic su OK.
5. Selezionare la casella **utente richiede l'autenticazione a più fattori di Azure corrispondono** se tutti gli utenti sono stati o verranno importati nel Server e soggetto a verifica in due passaggi. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di verifica in due passaggi, lasciare la casella deselezionata. Per ulteriori informazioni su questa caratteristica, vedere il file della Guida.
6. Se non è possibile rilevare automaticamente le variabili di pagina, fare clic sullo **Specificare manualmente...** pulsante nella finestra di dialogo Auto-Configure Form-Based sito Web.
7. Nella finestra di dialogo Add Form-Based sito Web, immettere l'URL della pagina di accesso ADFS nel campo URL inviare (ad esempio https://sso.contoso.com/adfs/ls) e immettere il nome di un'applicazione (facoltativo). Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato all'interno di SMS o App Mobile messaggi di autenticazione. Vedere la Guida per altre informazioni sull'URL inviare.
8. Impostare il formato della richiesta "Inserire POST o ottenere".
9. Immettere il nome utente variabile (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e la Password (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se la pagina di accesso in base a maschera viene visualizzata una casella di testo dominio, immettere anche la variabile di dominio. Potrebbe essere necessario passare alla pagina di accesso in un web browser del mouse sulla pagina e scegliere **Visualizza origine** per trovare i nomi delle caselle di input all'interno della pagina di accesso.
10. Selezionare la casella **utente richiede l'autenticazione a più fattori di Azure corrispondono** se tutti gli utenti sono stati o verranno importati nel Server e soggetto a verifica in due passaggi. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di verifica in due passaggi, lasciare la casella deselezionata.
<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Fare clic su **Avanzate...** pulsante per verificare le impostazioni avanzate. È possibile configurare impostazioni inclusa la possibilità di selezionare un file di pagina personalizzato negazione per memorizzare nella cache l'autenticazione ha esito positivo per il sito Web con i cookie e per selezionare la modalità di autenticazione delle credenziali principale.
12. Poiché il server proxy ADFS non possano essere aggiunto al dominio, è possibile utilizzare LDAP per connettersi a controller di dominio per l'importazione utente e pre-autenticazione. Nella finestra di dialogo Advanced Form-Based sito Web fare clic sulla scheda **Autenticazione principale** e selezionare **Binding LDAP** per il tipo di autenticazione pre-autenticazione.
13. Al termine, fare clic su **OK** per tornare alla finestra di dialogo Add Form-Based sito Web. Vedere la Guida per altre informazioni sulle impostazioni avanzate.
14. Fare clic su **OK** per chiudere la finestra di dialogo.
15. Dopo le variabili di URL e pagina sono state rilevate o immesse, i dati sui siti Web viene visualizzato nel riquadro basate su moduli.
16. Fare clic sulla scheda **Modulo nativo** e selezionare il server, il sito Web in cui il proxy ADFS è in esecuzione in (ad esempio "sito Web predefinito") o l'applicazione proxy ADFS (come "ls" in "adfs") per abilitare IIS plug-nel livello desiderato.
17. Selezionare la casella **autenticazione abilitare IIS** nella parte superiore dello schermo.
18. L'autenticazione di IIS è abilitato.

### <a name="configure-directory-integration"></a>Configurare l'integrazione di directory

È abilitata l'autenticazione di IIS, ma per eseguire pre-l'autenticazione per i Active Directory (AD) tramite LDAP è necessario configurare la connessione LDAP al controller di dominio.

1. Fare clic sull'icona di **Integrazione della Directory** .
2. Nella scheda Impostazioni, selezionare il pulsante di opzione **utilizza la configurazione LDAP specifica** .
<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Fare clic su di **Modifica** pulsante.
4. Nella finestra di dialogo Modifica configurazione LDAP compilare i campi con le informazioni necessarie per connettersi al controller di dominio Active Directory. Descrizioni dei campi sono inclusi nella tabella seguente. Queste informazioni sono incluse anche nel file della Guida Server di autenticazione a più fattori Azure.
5. Verificare la connessione LDAP facendo clic sul pulsante **Test** .
<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Se il test di connessione LDAP è stato completato, fare clic su **OK** .

### <a name="configure-company-settings"></a>Configurare le impostazioni della società

1. Quindi fare clic sull'icona **Impostazioni della società** e selezionare la scheda **Risoluzione nome utente** .
2. Selezionare il pulsante di opzione **utilizza LDAP attributo identificatore univoco per i nomi utente corrispondenti** .
3. Se gli utenti immettere il proprio nome utente nel formato "dominio\nome utente", il Server deve essere in grado di rimuovere il dominio disattivare il nome utente per la creazione di query LDAP. Che può essere eseguita tramite un'impostazione del Registro di sistema.
4. Aprire l'editor del Registro di sistema e passare a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo reti/PhoneFactor in un server a 64 bit. Se in un server a 32 bit, richiedere "Wow6432Node" fuori del percorso. Creare una chiave del Registro di sistema DWORD denominata "UsernameCxz_stripPrefixDomain" e il valore impostato su 1. Autenticazione a più fattori Azure è ora protezione proxy ADFS.

Assicurarsi che gli utenti sono stati importati da Active Directory nel Server. Se si preferisce indirizzi IP interni proprietà consentite in modo che la verifica in due passaggi non è necessaria effettuare l'accesso al sito Web da tali percorsi, vedere la [sezione indirizzi IP attendibili](#trusted-ips) .

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>ADFS 2.0 diretta senza un proxy

È possibile proteggere ADFS quando non viene utilizzato il proxy ADFS. Installare il Server di autenticazione a più fattori Azure nel server ADFS e configurare il Server per la procedura seguente:

1. All'interno del Server di autenticazione a più fattori di Azure, fare clic sull'icona di **Autenticazione IIS** nel menu a sinistra.
2. Fare clic sulla scheda **HTTP** .
3. Scegliere il **pulsante Aggiungi....** pulsante.
4. Nella finestra di dialogo Aggiungi Base URL immettere l'URL per il sito Web ADFS in cui viene eseguita l'autenticazione HTTP (ad esempio https://sso.domain.com/adfs/ls/auth/integrated) nel campo URL di Base. Immettere il nome di un'applicazione (facoltativo). Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato all'interno di SMS o App Mobile messaggi di autenticazione.
5. Se lo si desidera, regolare il tempo di inattività e massimo volte sessione.
6. Selezionare la casella **utente richiede l'autenticazione a più fattori di Azure corrispondono** se tutti gli utenti sono stati o verranno importati nel Server e soggetto a verifica in due passaggi. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di verifica in due passaggi, lasciare la casella deselezionata. Per ulteriori informazioni su questa caratteristica, vedere il file della Guida.
7. Se lo si desidera, selezionare la casella di cache cookie.
<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Fare clic su **OK** .
9. Fare clic sulla scheda **Modulo nativo** e selezionare il server, il sito Web (ad esempio "sito Web predefinito") o nell'applicazione di ADFS (ad esempio "ls" in "adfs") per abilitare IIS plug-nel livello desiderato.
10. Selezionare la casella **autenticazione abilitare IIS** nella parte superiore dello schermo. Autenticazione a più fattori Azure è ora protezione ADFS.

Assicurarsi che gli utenti sono stati importati da Active Directory nel Server. Se si preferisce indirizzi IP interni proprietà consentite in modo che la verifica in due passaggi non è necessaria effettuare l'accesso al sito Web da tali percorsi, vedere la sezione indirizzi IP attendibili.


## <a name="trusted-ips"></a>IP di attendibile
IP di attendibile consentire agli utenti di ignorare l'autenticazione a più fattori Azure per le richieste di sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, può essere necessario utenti esenti da attivata la verifica al momento dell'accesso dall'ufficio. A tale scopo, è necessario specificare subnet office come una voce di indirizzi IP attendibili.

### <a name="to-configure-trusted-ips"></a>Per configurare IP attendibile


1. Nella sezione autenticazione IIS, fare clic sulla scheda **Indirizzi IP attendibili** .
1. Scegliere il **pulsante Aggiungi....** pulsante.
1. Quando viene visualizzata la finestra di dialogo Aggiungi IP attendibili, selezionare uno dei pulsanti di opzione **IP singolo**, **intervalli IP**o **Subnet** .
1. Immettere l'indirizzo IP, intervallo di indirizzi IP o subnet che deve essere whitelisted. Se si immette una subnet, selezionare la maschera appropriato e fare clic su **OK** . IP attendibile è stato aggiunto.


<center>![Programma di installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
