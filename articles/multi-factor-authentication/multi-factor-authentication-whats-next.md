<properties
    pageTitle="Autenticazione a più fattori Azure - novità"
    description="Questa è la pagina di autenticazione a più fattori Azure che vengono descritte le operazioni da eseguire con MFA.  Sono inclusi i report, avviso di frode, bypass monouso, messaggi vocali personalizzati, memorizzazione nella cache, le password di indirizzi IP e app attendibili."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configurare l'autenticazione a più fattori Azure

In questo articolo consente di gestire l'autenticazione a più fattori Azure ora che si è in esecuzione.  Coprire una serie di argomenti che consentono di sfruttare al meglio autenticazione a più fattori Azure.  Non tutte queste funzionalità sono disponibili in tutte le versioni di autenticazione a più fattori Azure.

La configurazione per alcune delle caratteristiche seguenti si trova nel portale di gestione di autenticazione a più fattori di Azure. Esistono diversi modi che è possibile accedere al portale di gestione MFA, che vengono eseguiti tramite il portale Azure. Il primo è tramite la gestione di un Provider di autenticazione a più fattori Se Usa MFA in base a consumo. Il secondo consiste nell'utilizzare le impostazioni del servizio MFA. La seconda opzione richiede un Provider di autenticazione a più fattori o una licenza di Azure MFA, Azure Active Directory Premium o Enterprise mobilità Suite.

Per accedere al portale di gestione MFA tramite un Provider di autenticazione a più fattori di Azure, accedere al portale di Azure come amministratore e selezionare l'opzione di Active Directory. Fare clic sulla scheda **Provider di autenticazione a più fattori** , quindi selezionare la directory e fare clic sul pulsante **Gestisci** nella parte inferiore.

Per accedere al portale di gestione MFA tramite la pagina di impostazioni del servizio MFA, accedere al portale di Azure come amministratore e selezionare l'opzione di Active Directory. Fare clic su directory e quindi fare clic sulla scheda **Configura** . Nella sezione autenticazione a più fattori, selezionare **le impostazioni del servizio di gestione**. Nella parte inferiore della pagina Impostazioni servizio MFA, fare clic sul collegamento **Vai al portale** .


Caratteristica| Descrizione| Che cos'è coperto
:------------- | :------------- | :------------- |
[Avviso di frode](#fraud-alert)|Avviso di frode può essere configurato e impostare in modo che gli utenti possono inviare una segnalazione fraudolenti tenta di accedere alle risorse.|Come installare, configurare e segnalazione di frodi
[Ignora occasionale](#one-time-bypass) |Un copia unico bypass consente di eseguire l'autenticazione una sola volta "ignorando" autenticazione a più fattori.|Come installare e configurare un tantum bypass
[Messaggi vocali personalizzati](#custom-voice-messages) |I messaggi vocali personalizzati consentono di utilizzare registrazioni o messaggio di saluto personalizzati con l'autenticazione a più fattori. |Come installare e configurare i messaggi e messaggi di saluto personalizzati
[La memorizzazione nella cache](#caching-in-azure-multi-factor-authentication)|La memorizzazione nella cache consente di impostare un'ora specifica periodo in modo che l'autenticazione successivi tentativi riusciti automaticamente. |Come installare e configurare l'autenticazione di memorizzazione nella cache.
[IP di attendibile](#trusted-ips)|Attendibile che IP è una funzionalità di autenticazione a più fattori che consente agli amministratori di un tenant gestito o federato la possibilità di ignorare l'autenticazione a più fattori per gli utenti di accedere da intranet locale della società.|Configurare e configurare gli indirizzi IP non sono interessati per l'autenticazione a più fattori
[Password dell'App](#app-passwords)|Una password per l'app consente a un'applicazione che non riconosce MFA per ignorare l'autenticazione a più fattori e continuare a lavorare.|Informazioni sulle password app.
[Ricordare di autenticazione a più fattori per i browser e dispositivi da ricordare](#remember-multi-factor-authentication-for-devices-users-trust)|Consente di memorizzare dispositivi per un numero prestabilito di giorni dopo che un utente ha completato l'accesso tramite MFA.|Informazioni sull'attivazione di questa funzionalità e impostare il numero di giorni.
[Metodi di verifica selezionabili](#selectable-verification-methods)|Consente di scegliere i metodi di autenticazione disponibili agli utenti di utilizzare.|Informazioni sull'attivazione o disattivazione di metodi di autenticazione specifico, ad esempio chiamate o messaggi di testo.



## <a name="fraud-alert"></a>Avviso di frode
Avviso di frode può essere configurato e impostare in modo che gli utenti possono inviare una segnalazione fraudolenti tenta di accedere alle risorse.  Gli utenti possono segnalare frode con l'app per dispositivi mobili o tramite il proprio telefono.

### <a name="to-set-up-and-configure-fraud-alert"></a>Per installare e configurare l'avviso di frode

1.  Accedere a http://azure.microsoft.com
2.  Passare al portale di gestione di MFA seguendo le istruzioni nella parte superiore della pagina.
3.  Nel portale di Gestione autenticazione a più fattori Azure fare clic su impostazioni nella sezione Configura.
4.  Nella sezione frode avviso della pagina Impostazioni, selezionare Consenti agli utenti per inviare gli avvisi di frode casella di controllo.
5.  Se si desidera agli utenti di essere bloccati in cui viene indicata frode, inserire un controllo in Blocca utente in cui viene indicata frode.
6.  Nella casella di testo **Codice a Report frodi durante l'iniziale del messaggio di saluto** , immettere un codice numerico che può essere utilizzato durante la verifica della chiamata. Se un utente immette il codice più anziché il segno di # #, verrà segnalato un avviso di frode.
7.  Nell'angolo inferiore fare clic su Salva.

>[AZURE.NOTE]
>Saluto predefinito Microsoft indicare agli utenti di premere # 0 per inviare un avviso di frode. Se si vuole usare un codice diverso da 0, è necessario registrare e caricare il proprio saluto personalizzato con le istruzioni appropriate.


![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Avviso di frode report
Avviso di frode che può essere inserita due modi.  Tramite l'app per dispositivi mobili o tramite telefono.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Avviso di frode report con l'app per dispositivi mobili



1. Quando una verifica viene inviata al telefono, selezionarla per avviare l'app Microsoft Authenticator.
2. Per segnalare le frodi, selezionare il Report frode Annulla. Verrà visualizzata una finestra di dialogo che verrà inviata una notifica personale di supporto IT dell'organizzazione.
3. Fare clic su frode report.
4. App, fare clic su Chiudi.

![Cloud](./media/multi-factor-authentication-whats-next/report1.png)


![Cloud](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Avviso di frode report con il telefono

1. Quando arriva una chiamata di verifica al telefono, rispondere.  
2. Per segnalare le frodi, immettere il codice che è stato configurato in modo da corrispondere con segnalazione di frodi tramite telefono e quindi il simbolo #. Verrà visualizzato un avviso di frode inviato.
3. Terminare la chiamata.

### <a name="to-view-the-fraud-report"></a>Per visualizzare il report frode

1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra, selezionare Active Directory.
3. Nella parte superiore selezionare il provider di autenticazione a più fattori. Verrà visualizzato un elenco di provider di autenticazione a più fattori.
4. Se si dispone di più di un Provider di autenticazione a più fattori, selezionare quello che si desidera visualizzare il report degli avvisi di frode e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di una sola, fare clic su Gestisci. Verrà aperto il portale di Gestione autenticazione a più fattori Azure.
5. Scegliere frode avviso il portale di Gestione autenticazione a più fattori Azure, a sinistra, in Visualizza un Report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È inoltre possibile specificare qualsiasi nomi utente specifici, i numeri di telefono e lo stato dell'utente.
7. Fare clic su Esegui. Verrà visualizzato un report simile a quella riportata di seguito. È anche possibile fare clic su Esporta in CSV se si desidera esportare il report.

## <a name="one-time-bypass"></a>Ignora occasionale

Un copia unico bypass consente di eseguire l'autenticazione una sola volta "ignorando" autenticazione a più fattori. Il bypass è temporaneo e scade dopo il numero di secondi specificato.  In situazioni in cui un telefono o app per dispositivi mobili non riceve una notifica o una chiamata telefonica, pertanto è possibile abilitare un tantum bypass in modo che l'utente può accedere alla risorsa desiderata.

### <a name="to-create-a-one-time-bypass"></a>Per creare un copia unico bypass

1.  Accedere a http://azure.microsoft.com
2.  Passare al portale di gestione di MFA seguendo le istruzioni nella parte superiore della pagina.
3.  Nel portale di Azure a più fattori autenticazione Management, se viene visualizzato il nome del tenant o Azure MFA Provider sul lato sinistro con un + accanto a essa, fare clic sul + vedere diversi gruppi di replica MFA Server e il gruppo di Azure predefinito. Fare clic sul gruppo appropriato.
4.  In amministrazione degli utenti, fare clic su **Ignora singola sessione**.
![Cloud](./media/multi-factor-authentication-whats-next/create1.png)
5.  Nella pagina One Bypass fare clic su **Nuovo Bypass singola sessione**.
6.  Immettere il nome dell'utente, il numero di secondi presenti il bypass, il motivo per cui il bypass e fare clic su **Ignora**.
![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7.  A questo punto, l'utente deve accedere prima della scadenza bypass monouso.



### <a name="to-view-the-one-time-bypass-report"></a>Per visualizzare il report bypass occasionale

1. Accedere a [http://azure.microsoft.com](https://azure.microsoft.com/)
2. A sinistra, selezionare Active Directory.
3. Nella parte superiore selezionare il provider di autenticazione a più fattori. Verrà visualizzato un elenco di provider di autenticazione a più fattori.
4. Se si dispone di più di un Provider di autenticazione a più fattori, selezionare quello che si desidera visualizzare il report degli avvisi di frode e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di una sola, fare clic su Gestisci. Verrà aperto il portale di Gestione autenticazione a più fattori Azure.
5. Scegliere Ignora temporaneo il portale di Gestione autenticazione a più fattori Azure, a sinistra, in Visualizza un Report.
6. Specificare l'intervallo di date che si desidera visualizzare nel report. È inoltre possibile specificare qualsiasi nomi utente specifici, i numeri di telefono e lo stato dell'utente.
7. Fare clic su Esegui. Verrà visualizzato un report simile a quella riportata di seguito. È anche possibile fare clic su Esporta in CSV se si desidera esportare il report.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Messaggi vocali personalizzati

I messaggi vocali personalizzati consentono di utilizzare registrazioni o messaggio di saluto personalizzati con l'autenticazione a più fattori.  Questi possono essere usati in aggiunta a oppure per sostituire Microsoft record.

Prima di iniziare a tenere presente quanto segue:

- I formati di file corrente sono wav e MP3.
- Il limite di dimensioni di file è 5 MB.
- È consigliabile che i messaggi di autenticazione sia non più di 20 secondi. Valori superiori a questo potrebbe causare la verifica non riesca perché l'utente potrebbe non rispondere prima il messaggio di completamento e la verifica timeout.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Per configurare i messaggi vocali personalizzati in Azure autenticazione a più fattori
1.  Creare un messaggio vocale personalizzati utilizzando uno dei formati di file supportati.
2.  Accedere a http://azure.microsoft.com
3.  Passare al portale di gestione di MFA seguendo le istruzioni nella parte superiore della pagina.
4.  Nel portale di Gestione autenticazione a più fattori Azure, fare clic su messaggi vocali nella sezione Configura.
5.  Nella sezione messaggi vocali, fare clic su **Nuovo messaggio vocale**.
![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6.  In Configura: i nuovi messaggi vocali pagina fare clic su **Gestisci file audio**.
![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7.  In Configura: pagina file audio, fare clic su **Carica File audio**.
![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8.  In Configura: caricare File audio, fare clic su **Sfoglia** e passare al messaggio vocale, fare clic su **Apri**.
![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Aggiungere una descrizione e fare clic su Carica.
10. Una volta completata l'attivazione, un messaggio di conferma che è stato inviato il file.
11. Sul lato sinistro fare clic su messaggi vocali.
12. Nella sezione messaggi vocali, fare clic su nuovo messaggio vocale.
13. Dall'elenco a discesa lingua selezionare la lingua.
14. Se il messaggio è per un'applicazione specifica, specificarla nella casella dell'applicazione.
15. Il tipo di messaggio, selezionare il tipo di messaggio vengano sostituiti con il nuovo messaggio personalizzato.
16. Dall'elenco a discesa File audio selezionare il file audio.
17. Fare clic su **Crea**. Un messaggio di conferma che sia stato creato correttamente un messaggio vocale.
![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>La memorizzazione nella cache in Azure autenticazione a più fattori

La memorizzazione nella cache consente di impostare un'ora specifica periodo in modo che l'autenticazione successivi tentativi riusciti automaticamente. Questo viene utilizzato principalmente per sistemi locale, ad esempio VPN inviare più richieste di verifica, mentre la richiesta prima è ancora in corso. In questo modo le successive richieste completare automaticamente dopo l'utente ha avuto esito positivo della verifica in corso. Si noti che la memorizzazione nella cache non devono essere utilizzato per gli accessi di Azure Active Directory.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Per impostare la memorizzazione nella cache di autenticazione a più fattori Azure

1.  Accedere a http://azure.microsoft.com
2.  Passare al portale di gestione di MFA seguendo le istruzioni nella parte superiore della pagina.
3.  Nel portale di Gestione autenticazione a più fattori Azure, fare clic su memorizzazione nella cache nella sezione Configura.
4.  Scegliere Nuova Cache Configura memorizzazione nella cache pagina
5.  Selezionare il tipo di Cache e i secondi di cache. Fare clic su Crea.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IP di attendibile

Attendibile che IP è una funzionalità di autenticazione a più fattori che consente agli amministratori di un tenant gestito o federato la possibilità di ignorare l'autenticazione a più fattori per gli utenti di accedere da intranet locale della società. Le caratteristiche sono disponibili per i tenant di Azure Active Directory che dispongono delle licenze di Azure Active Directory Premium, Enterprise mobilità Suite o autenticazione a più fattori Azure.


Tipo di Azure Active Directory Tenant| Opzioni disponibili IP attendibili
:------------- | :------------- |
Gestite|Intervalli di indirizzi IP specifici: gli amministratori possono specificare un intervallo di indirizzi IP che possono evitare l'autenticazione a più fattori per gli utenti che accesso dalla rete intranet della società.
Federato|<li>Tutti gli utenti federati - tutti gli utenti federati che esegue l'accesso accesso all'interno dell'organizzazione evitano autenticazione a più fattori tramite una richiesta di rimborso emesso da ADFS.</li><li>Intervalli di indirizzi IP specifici: gli amministratori possono specificare un intervallo di indirizzi IP che possono evitare l'autenticazione a più fattori per gli utenti che accesso dalla rete intranet della società.

Questo ignorare funziona solo all'interno di una rete intranet aziendale. Ad esempio, se si seleziona solo tutti gli utenti federati e un utente accede da fuori rete intranet della società, tale utente deve eseguire l'autenticazione mediante l'autenticazione a più fattori anche se l'utente presenta un'attestazione di ADFS. Nella tabella seguente descrive quando le password di autenticazione e app a più fattori necessari all'interno di rete corpnet e di fuori rete corpnet quando attendibili IP è abilitato.


|Attendibili IP abilitato| Attendibili IP disattivato
:------------- | :------------- | :------------- |
Corpnet interno|Per i flussi di browser, autenticazione a più fattori non sono necessarie.|Per i flussi di browser, è necessario autenticazione a più fattori
|Per le applicazioni rich client, password regolare funzionano se l'utente non ha creato le password di app. Dopo aver creata una password per l'app, le password app sono necessarie.|Per le applicazioni rich client, necessarie app password
Corpnet esterno|Per i flussi di browser, autenticazione a più fattori necessari.|Per i flussi di browser, autenticazione a più fattori necessari.
|Per le applicazioni rich client, necessarie app password.|Per le applicazioni rich client, necessarie app password.

### <a name="to-enable-trusted-ips"></a>Per abilitare IP attendibili

1. Effettuare l'accesso al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In, fare clic su Directory nella directory che si desidera configurare IPsing attendibili in.
4. Per la Directory che è stato selezionato, fare clic su Configura.
5. Nella sezione autenticazione a più fattori, fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni del servizio, in IP attendibili selezionare:

    - Per le richieste provenienti da utenti provenienti da personale intranet federati-federato tutti gli utenti che hanno accesso dalla rete aziendale evitano autenticazione a più fattori tramite una richiesta di rimborso emesso da ADFS.
    - Per le richieste provenienti da un determinato intervallo di indirizzi IP pubblico: immettere gli indirizzi IP nelle caselle disponibili utilizzando la notazione CIDR. Ad esempio: xxx.xxx.xxx.0/24 per gli indirizzi IP in xxx.xxx.xxx.1 intervallo – xxx.xxx.xxx.254 o xxx.xxx.xxx.xxx/32 per un solo indirizzo IP. È possibile immettere fino a 50 intervalli di indirizzi IP.

7. Fare clic su Salva.
8. Dopo aver applicati gli aggiornamenti, fare clic su Chiudi.



![IP di attendibile](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Password dell'App

In alcune applicazioni, ad esempio Office 2010 o versioni precedenti e Apple Mail non è possibile utilizzare l'autenticazione a più fattori.  Per usare queste App, è necessario utilizzare "app password" al posto di password tradizionali.  La password di app consente all'applicazione di ignorare l'autenticazione a più fattori e continuare a lavorare.

>[AZURE.NOTE] Autenticazione moderno per i client di Office 2013
>
> I client di Office 2013 (inclusi Outlook) ora supportano nuovi protocolli di autenticazione e possono essere attivati per supportare l'autenticazione a più fattori.  Questo significa che dopo l'abilitazione, app le password non sono necessari per l'uso con i clienti di Office 2013.  Per ulteriori informazioni, vedere [Office 2013 autenticazione moderno pubblico anteprima annunciato](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Importante cose da sapere sulla password dell'app

Di seguito è un elenco di importante operazioni che è necessario conoscere sulle password dell'app.

- Gli utenti possono avere più password app e aumenta la superficie per furto. Poiché password dell'app sono difficili da ricordare, potrebbero incoraggiare gli utenti a scrivere verso il basso. Questo non è consigliabile e deve essere sconsigliato, in quanto solo uno dei fattori viene chiesto di accedere con la password di app.
- App che nella cache le password e usarla in scenari locale può partire non funziona in quanto non si conosce la password per l'app all'esterno di id dell'organizzazione. Un esempio i messaggi di Exchange che sono in locale ma posta archiviata nel cloud. La stessa password non funziona.
- La password effettiva viene generata automaticamente e non viene fornita dall'utente. Questo perché la password generata automaticamente, non è più difficile per un indovinare e sicura.
- Al momento non esiste un limite di 40 password per ogni utente. Verrà richiesto di eliminare una delle password app esistente per crearne uno nuovo.
- Dopo aver abilitato l'autenticazione a più fattori in un account utente, app le password possono essere utilizzate con la maggior parte dei browser non client, ad esempio Outlook e Lync, ma non è possibile utilizzare le attività amministrative usano le password app tramite le applicazioni non browser, ad esempio Windows PowerShell, anche se l'utente dispone di un account amministrativo.  Assicurarsi che si crea un account di servizio con una password complessa per eseguire gli script di PowerShell e non attiva l'account per l'autenticazione a più fattori.

>[AZURE.WARNING]  Password dell'App non funzionano in ambienti ibridi nel punto in cui client comunicare con entrambi locali e cloud endpoint di individuazione automatica. Ciò avviene perché le password di dominio è necessario eseguire l'autenticazione locale e sono necessarie password app per eseguire l'autenticazione con il cloud.


### <a name="naming-guidance-for-app-passwords"></a>Indicazioni per la denominazione per le password App
È consigliabile che i nomi di password app rispecchiare il dispositivo in cui vengono utilizzati. Ad esempio, se si dispone di un computer portatile con le applicazioni non browser, ad esempio Outlook, Word ed Excel, è sufficiente creare una password per l'app denominata Laptop e usare la password per l'app in tutte le applicazioni. Sebbene sia possibile creare password distinte per tutte le applicazioni, non è consigliabile. Il consigliabile modo consiste nell'usare una password per l'app per dispositivi.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Password dell'App federato (SSO)
Azure Active Directory supporta la federazione con locale Windows Server Active Directory servizi di dominio (AD DS). Se la propria organizzazione è federated(SSO) con Azure Active Directory e si sta per essere mediante l'autenticazione a più fattori di Azure, di seguito è informazioni importanti che deve essere presente quando si utilizzano password app. Si applica solo ai clienti di federated(SSO).

- La Password di App verificata da Azure Active Directory e pertanto Ignora federazione. Federazione viene utilizzata in modo attivo solo quando si imposta Password per l'App.
- Per gli utenti federated(SSO), abbiamo mai passare al Provider di identità (IdP) diversamente da quanto succede flusso passivo. Le password sono archiviate nel campo id dell'organizzazione. Se l'utente lascia l'azienda, flusso per id dell'organizzazione tramite DirSync in tempo reale quelle informazioni. Disabilita/eliminazione dell'account può richiedere fino a tre ore per sincronizzare, ritardare disabilita / l'eliminazione della Password per l'App in Azure Active Directory.
- Impostazioni di controllo dell'accesso Client locale non vengono rispettate da App Password
- L'autenticazione non locale registrazione / funzionalità di controllo è disponibile per la Password di App
- Ulteriori per l'istruzione degli utenti finali è necessario per il client di Microsoft Lync 2013. Per i passaggi necessari, vedere come cambiare la password nella posta elettronica con la password di app.
- Alcuni aspetti architettonica avanzate possono richiedere utilizzando una combinazione di nome utente dell'organizzazione e app password quando si usa l'autenticazione a più fattori con client, a seconda di dove l'autenticazione. Per i client autenticare un'infrastruttura locale, utilizzare un nome utente dell'organizzazione e la password. Per i client autenticare Azure Active Directory, utilizzare la password di app.

Ad esempio, se che si dispone di un'architettura che include le operazioni seguenti:

- È federati istanza locale di Active Directory con Azure Active Directory
- Si utilizza Exchange online
- Si usa Lync è in particolare in locale
- Si utilizza l'autenticazione a più fattori Azure


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 In questi casi, è necessario eseguire le operazioni seguenti:

- Quando l'accesso aggiuntivo a Lync, utilizzare le organizzazioni nome utente e password.
- Quando si tenta di accedere alla Rubrica tramite un client di Outlook che si connette a Exchange online, usare una password per l'app.

### <a name="allowing-app-password-creation"></a>Consentire la creazione di password app
Per impostazione predefinita, gli utenti non è possibile creare password dell'app.  Questa funzionalità deve essere abilitata.  Per consentire agli utenti la possibilità di creare password dell'app, utilizzare la procedura seguente.

#### <a name="to-enable-users-to-create-app-passwords"></a>Per consentire agli utenti di creare password app



1. Accedere al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In, fare clic su Directory nella directory dell'utente che si desidera attivare.
4. Nella parte superiore, fare clic su utenti.
5. Nella parte inferiore della pagina, fare clic su Gestisci autenticazione a più fattori  
6. Nella parte superiore della pagina di autenticazione a più fattori, fare clic su impostazioni dei servizi.
7. Assicurarsi che il pulsante di opzione accanto a Consenti agli utenti di creare password dell'app per accedere a applicazioni basate sul browser non sia selezionato.


![Creare password dell'App](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Creazione di una password di app
Gli utenti possono creare password dell'app durante la registrazione iniziale.  Dispongono di un'opzione alla fine del processo di registrazione che consente di crearli.

Inoltre gli utenti possono inoltre creare password dell'app in un secondo momento modificando le impostazioni nel portale di Azure, il portale di Office 365 oppure

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Per creare password dell'app nel portale di Office 365
--------------------------------------------------------------------------------


1. Accedere al portale di Office 365
2. Nell'angolo superiore destro, selezionare widget impostazioni
3. A sinistra, selezionare un'ulteriore verifica di sicurezza
4. A destra, selezionare **Aggiorna i numeri di telefono utilizzati per la sicurezza di account**
5. Nella pagina proofup nella parte superiore, fare clic su app password
6. Fare clic su **Crea**
7. Immettere un nome per la password di app e fare clic su **Avanti**
8. Copiare la password di app negli Appunti e incollarlo in un'applicazione.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Per creare password dell'app nel portale di Azure
--------------------------------------------------------------------------------
1. Accedere al portale di classica Azure.
3. Nella parte superiore, pulsante destro del mouse sul proprio nome utente e selezionare un'ulteriore verifica di sicurezza.
5. Nella pagina proofup nella parte superiore, fare clic su app password
6. Fare clic su **Crea**
7. Immettere un nome per la password di app e fare clic su **Avanti**
8. Copiare la password di app negli Appunti e incollarlo in un'applicazione.


![Password dell'App](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Per creare password dell'app se non si dispone di un abbonamento a Office 365 o Azure
--------------------------------------------------------------------------------
1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore, selezionare profilo.
3. Fare clic sul proprio nome utente e selezionare un'ulteriore verifica di sicurezza.
5. Nella pagina proofup nella parte superiore, fare clic su app password
6. Fare clic su **Crea**
7. Immettere un nome per la password di app e fare clic su **Avanti**
8. Copiare la password di app negli Appunti e incollarlo in un'applicazione.

![Password dell'App](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Ricordare di autenticazione a più fattori per l'attendibilità agli utenti di dispositivi

Ricordare autenticazione a più fattori per i dispositivi e browser che gli utenti protezione è una caratteristica disponibile per tutti gli utenti MFA.  Consente di assegnare agli utenti la possibilità di by-passare MFA per un numero prestabilito di giorni dopo l'esecuzione di una corretta procedi MFA. Ciò consente di migliorare la facilità di utilizzo per gli utenti.

Tuttavia, dal momento che gli utenti sono consentiti da ricordare MFA per i dispositivi attendibili, questa caratteristica può ridurre protezione dell'account. Per garantire la sicurezza di account, è necessario ripristinare l'autenticazione a più fattori per i dispositivi per entrambi gli scenari seguenti:

- Se il proprio account aziendale è stata compromessa
- Se un dispositivo memorizzato perdito o furto

> [AZURE.NOTE] Questa caratteristica è implementata come cache cookie del browser. Questa non funziona se non sono attivati i cookie del browser.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Come abilitare/disabilitare l'autenticazione a più fattori memorizza

1. Accedere al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In Active Directory, fare clic su nella directory che si desidera configurare l'autenticazione a più fattori ricordare per i dispositivi.
4. Per la Directory che è stato selezionato, fare clic su Configura.
5. Nella sezione autenticazione a più fattori, fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni del servizio in gestire le impostazioni dispositivo utente, selezionare o deselezionare **Consenti agli utenti di ricordare autenticazione a più fattori nei dispositivi che devono considerare attendibili**.
![Ricordare dispositivi](./media/multi-factor-authentication-whats-next/remember.png)
8. Impostare il numero di giorni per cui si desidera consentire la sospensione. Il valore predefinito è 14 giorni.
9. Fare clic su Salva.
10. Fare clic su Chiudi.


## <a name="selectable-verification-methods"></a>Metodi di verifica selezionabili
Le versioni locale sia il cloud, è possibile scegliere i metodi di verifica sono disponibili per gli utenti. Nella tabella seguente fornisce una breve panoramica di ogni metodo.

Quando gli utenti di registrare il proprio account per MFA, scelgono il metodo foglia dalle opzioni che è stata attivata. Le linee guida per il processo di registrazione viene descritto in [configurare un account personale per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

Metodo|Descrizione
:------------- | :------------- |
Chiamata al telefono |  Inserisce una chiamata vocale automatico per il telefono di autenticazione. L'utente risponde alla chiamata e preme # nel tastierino del telefono per l'autenticazione. Questo numero di telefono non è sincronizzato con Active Directory locale.
Messaggio di testo al telefono | Invia un messaggio di testo che contiene un codice di verifica all'utente. L'utente è richiesto di uno dei due Rispondi al messaggio di testo con il codice di verifica o immettere il codice di verifica nell'interfaccia di accesso.
Notifiche tramite app per dispositivi mobili | In questa modalità, l'app Microsoft Authenticator impedisce l'accesso non autorizzato ad account e interrompe transazioni fraudolente. Questa operazione viene eseguita mediante una notifica push al telefono o dispositivo registrato. È sufficiente visualizzare la notifica e se è legittimo si tocca verifica. In caso contrario si può scegliere Nega oppure negare e generare report notifica fraudolenta. Per informazioni sulla segnalazione notifiche fraudolente informazioni su come usare la caratteristica di frode Report Nega per l'autenticazione a più fattori.</br></br>L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Codice di verifica dall'app per dispositivi mobili | In questa modalità, utilizzare l'app Microsoft Authenticator come token software per generare il codice di verifica GIURAMENTO. È possibile quindi immettere il codice di verifica insieme al nome utente e password per fornire la seconda forma di autenticazione.</li><br><p> L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Come abilitare/disabilitare i metodi di autenticazione

1. Accedere al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In Active Directory, fare clic su nella directory che si desidera attivare o disattivare i metodi di autenticazione.
4. Per la Directory che è stato selezionato, fare clic su Configura.
5. Nella sezione autenticazione a più fattori, fare clic su Gestisci impostazioni del servizio.
6. Nella pagina Impostazioni del servizio, in Opzioni di verifica, selezionare o deselezionare le opzioni che si desidera utilizzare.</br></br>
![Opzioni di verifica](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.
