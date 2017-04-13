<properties
    pageTitle="Domande frequenti: Gestione di Azure Active Directory Password | Microsoft Azure"
    description="Domande frequenti domande frequenti sulla gestione di password in Azure Active Directory, inclusione reimpostazione della password, registrazione, report e writeback di Active Directory locale."
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

# <a name="password-management-frequently-asked-questions"></a>Domande frequenti sulla gestione delle password

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Di seguito sono alcune domande frequenti su tutti gli aspetti relativi alla gestione delle password.

Se è necessario con una domanda che non conosce la risposta o ricerca di informazioni relative a un particolare problema che è affiancate, è possibile leggere in seguito per verificare se è stata coperta già.  Se non è già stato fatto, tenere presente che! È possibile inserire qualsiasi domanda, si dispone non fornite di seguito nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) e si otterranno all'utente come è possibile.

In questo argomento viene diviso nelle sezioni seguenti:

- [**Domande sulla registrazione di reimpostazione Password**](#password-reset-registration)
- [**Domande sulla reimpostazione della Password**](#password-reset)
- [**Domande sui report di gestione Password**](#password-management-reports)
- [**Domande sulla Writeback di Password**](#password-writeback)

## <a name="password-reset-registration"></a>Registrazione di reimpostazione della password
 - **D: gli utenti è possibile registrare i propri dati di reimpostare la password?**

 > **A:** Sì, purché la reimpostazione della password è attivata e sono concessi in licenza, vengano possibile accedere al portale di registrazione di reimpostare la Password all'http://aka.ms/ssprsetup per registrare le informazioni di autenticazione da utilizzare con la reimpostazione della password. Gli utenti possono inoltre registrare passare al pannello di accesso in http://myapps.microsoft.com, facendo clic sulla scheda profilo e scegliere l'opzione Registra per la reimpostazione della Password. Per ulteriori informazioni su come ottenere gli utenti configurati per la reimpostazione, leggere come ottenere gli utenti configurati per la reimpostazione della password della password.

 - **D: è possibile definire i dati di reimpostare la password per conto di utenti?**

 > **A:** Sì, è possibile eseguire con DirSync o PowerShell o tramite il portale di amministrazione di Office o [Il portale di gestione di Azure](https://manage.windowsazure.com) . Ulteriori informazioni su questa caratteristica sul post di blog migliorata informativa sulla Privacy per MFA di Azure Active Directory e numeri di telefono di reimpostare la Password e leggendo informazioni su come i dati vengono utilizzati da password reimpostare.

 - **D: è possibile sincronizzare i dati per eventuali domande di protezione da locale?**

 > **A:** No, questo non è possibile oggi, ma si sta valutando la possibilità di esso.

 - **D: è possibile gli utenti registrare i dati in modo che altri utenti non è possibile visualizzare i dati?**

 > **A:** Sì, quando gli utenti registrare i dati tramite il portale di registrazione di reimpostare la Password ottiene salvata privata autenticazione nei campi in cui sono visibili solo da amministratori globali e l'utente stesso. Ulteriori informazioni su questa caratteristica sul post di blog migliorata informativa sulla Privacy per MFA di Azure Active Directory e numeri di telefono di reimpostare la Password e leggendo informazioni su come i dati vengono utilizzati da password reimpostare.

 - **D: gli utenti è necessario essere registrati prima di poter utilizzare la reimpostazione della password?**

 > **A:** No, se si definiscono sufficienti informazioni di autenticazione per suo conto, gli utenti non avranno la registrazione. La reimpostazione della password funziona anche come è stato formattato correttamente i dati archiviati nei campi appropriati nella directory. Informazioni, vedere altre informazioni su leggendo informazioni su come i dati vengono utilizzati per la reimpostazione della password.

 - **D: è possibile sincronizzare o impostare i campi di posta elettronica di autenticazione o telefono autenticazione alternativo, autenticazione telefonica per conto di utenti?**

 > **A:** Attualmente non, ma si sta valutando la possibilità di abilitare questa funzionalità.

 - **D: come il portale di registrazione che le opzioni per visualizzare gli utenti?**

 > **A:** Il portale di registrazione di reimpostazione password Visualizza solo le opzioni che è stata attivata per gli utenti nella sezione criteri di reimpostazione Password utente della scheda Configura della directory. Ciò significa che se non si attiva, ad esempio, domande di sicurezza, quindi non sarà necessario registrarsi per questa opzione.

 - **Q: quando un utente è considerato registrato?**

 > **A:** Un utente è considerato registrato quando di cui dispone almeno parti N di informazioni di autenticazione definito, dove N è il numero di autenticazione metodi necessari impostati nel [Portale di gestione Azure](https://manage.windowsazure.com). Per ulteriori informazioni, vedere il criterio di reimpostazione Password personalizzazione utente.


## <a name="password-reset"></a>Reimpostazione della password

 - **D: come tempo occorre perché per ricevere un messaggio di posta elettronica, SMS o chiamata telefonica dalla reimpostazione della password?**

 > **A:** Posta elettronica, messaggi SMS e telefonate dovrebbe arrivare in 1 minuto, con il caso normale 5-20 secondi. Se non si riceve la notifica in questo periodo di tempo, controllare la cartella posta indesiderata, che il numero / posta elettronica contattato sia quello previsto e che i dati di autenticazione nella directory sia stati formattati correttamente. Per ulteriori informazioni sulla formattazione di numeri di telefono e posta elettronica indirizzi per l'utilizzo con la reimpostazione della password, vedere informazioni come i dati vengono utilizzati per la reimpostazione della password.

 - **D: quali lingue sono supportate per la reimpostazione della password?**

 > **A:** Reimpostare la password dell'interfaccia utente, messaggi SMS e le chiamate vocali vengono localizzate nella stessa 40 lingue supportate in Office 365. Si tratta: arabo, bulgaro, cinese semplificato, cinese tradizionale, croato, ceco, danese, olandese, inglese, estone, finlandese, francese, tedesco, greco, ebraico, Hindi, ungherese, indonesiano, italiano, giapponese, kazaco, coreano, lettone, lituano, malese (Malaysia), norvegese (Bokmål), polacco, portoghese (Brasile), portoghese (Portogallo), Romeno, russo, Serbo (alfabeto latino), slovacco, sloveno, spagnolo, svedese, Thai, turco, ucraino e vietnamita.

 - **Scheda del Configura q: parti dell'esperienza di reimpostazione password ottenere personalizzate quando si imposta la personalizzazione nella directory aziendale?**

 > **A:** Il portale di reimpostare la password verrà visualizzato il logo aziendale e anche consentono di configurare il contatto che il collegamento di amministratore in modo che puntino a un messaggio di posta elettronica personalizzato o un URL. Qualsiasi messaggio di posta elettronica viene inviato per la reimpostazione della password sarà incluso il logo dell'organizzazione, i colori (in questo caso rossi), nome nel corpo del messaggio di posta elettronica e personalizzate dal nome. Visualizzare un esempio con tutti gli elementi personalizzati riportata di seguito. Per ulteriori informazioni, leggere l'aspetto di reimpostazione della Password di personalizzazione.

  ![][001]

 - **D: come è possibile informare gli utenti su come reimpostare la password?**

 > **A:** È possibile inviare agli utenti di https://passwordreset.microsoftonline.com direttamente o si insegna a fare clic sul non è possibile accedere al collegamento dell'account nella schermata di qualsiasi accesso dell'istituto di istruzione o l'ID di lavoro. È possibile è possibile pubblicare questi collegamenti (o creare reindirizzamenti URL ad essi) nel punto in cui è facilmente accessibile agli utenti.

 - **D: è possibile utilizzare questa pagina da un dispositivo mobile?**

 > **A:** Sì, questa pagina funziona nei dispositivi mobili.

 - **D: è supportata sblocco degli account di dominio active directory locale quando gli utenti reimpostare le password?**

 > **A:** Sì, quando si reimposta la password o Writeback di Password è stata distribuita con tutte le versioni di Azure AD Connect o versioni di sincronizzazione di Active Directory Azure 1.0.0485.0222 o versioni successive, account dell'utente sarà pertanto sbloccate automaticamente quando l'utente Reimposta la password.

 - **D: come è possibile integrare direttamente al desktop esperienza di accesso personale dell'utente reimpostazione della password?**

 > **A:** Questa operazione non è possibile oggi. Tuttavia, se si assolutamente necessaria questa funzionalità e i clienti di Azure Active Directory Premium, è possibile installare Microsoft Identity Manager senza costi aggiuntivi e distribuire la soluzione di reimpostazione password locale trovata al suo interno per risolvere questo requisito.

 - **D: è possibile impostare le domande di protezione diversi per impostazioni locali diverse?**

 > **A:** No, questo non è possibile oggi, ma si sta valutando la possibilità di esso.

 - **D: come molte domande è possibile configurare per l'opzione autenticazione sicurezza domande?**

 > **A:** È possibile configurare un massimo di 20 domande di sicurezza personalizzata nel [Portale di gestione Azure](https://manage.windowsazure.com).

 - **D: come lungo domande sulla protezione sia?**

 > **A:** Domande sulla protezione può essere compreso tra 3 e 200 caratteri.

 - **D: come lungo le risposte alle domande di sicurezza sia?**

 > **A:** Risposte potrebbero essere 3 a 40 caratteri.

 - **D: sono presenti duplicati le risposte alle domande di sicurezza rifiutate?**

 > **A:** Sì, è Rifiuta duplicate risposte alle domande di sicurezza.

 - **Q: maggio un utente registrare più di uno stesso domanda di sicurezza?**

 > **A:** No, dopo che un utente si registra una domanda, lui potrebbe non eseguire la registrazione per la domanda una seconda volta.

 - **D: è possibile impostare un limite minimo di domande di sicurezza per la registrazione e reimpostare?**

 > **A:** Sì, è possibile impostare un limite per la registrazione e l'altra per reimpostare. domande sulla protezione di 3-5 potrebbe essere necessari per la registrazione e 3 a 5 potrebbe essere necessario per reimpostare.

 - **D: se un utente ha registrato più il numero massimo di domande necessari per reimpostare, come domande sulla protezione selezionare durante la reimpostazione?**

 > **A:** Domande sulla protezione N verranno selezionate in modo casuale rispetto al numero totale di domande che un utente ha registrato, dove N è il numero minimo di domande necessari per la reimpostazione della password. Ad esempio, se un utente ha 5 domande sulla protezione registrati, ma solo 3 sono necessari per reimpostare, 3 di tali 5 verrà selezionato in modo casuale e presentati all'utente in fase di reimpostazione. Se l'utente ottiene le risposte alle domande errata, il processo di selezione ripresenta per evitare martellamenti domanda.

 - **D: impedire agli utenti di cercare tutte le volte in un breve periodo di tempo reimpostazione della password?**

 > **A:** Sì, esistono alcune funzionalità di sicurezza integrate la reimpostazione della password. Gli utenti possono provare solo 5 tentativi di reimpostazione password entro un'ora prima di essere bloccato per 24 ore. Gli utenti possono solo tentare di convalidare un numero di telefono 5 volte all'interno di un'ora prima di essere bloccato per 24 ore. Gli utenti possono solo provare un metodo di autenticazione singola 5 volte all'interno di un'ora prima di essere bloccato per 24 ore.

 - **D: per quanto tempo sono validi il messaggio di posta elettronica e il passcode monouso SMS?**

 > **A:** La durata della sessione per la reimpostazione della password è 105 minuti. Ciò significa che dall'inizio della password Reimposta operazione, l'utente ha 105 minuti per reimpostare la propria password. Il messaggio di posta elettronica e il passcode monouso SMS non sono validi dopo il periodo di tempo.


## <a name="password-management-reports"></a>Report di gestione delle password

 - **D: come tempo per i dati da vengono visualizzate in report di gestione password?**

 > **A:** Dati dovrebbero essere visualizzate nei report di gestione password all'interno di 5-10 minuti. In alcuni casi può essere necessaria fino a un'ora visualizzare.

 - **D: come è possibile filtrare i report di gestione password?**

 > **A:** È possibile filtrare il report di gestione password facendo clic sulla lente di ingrandimento small per l'estrema destra delle etichette di colonna, verso la parte superiore del rapporto (vedere la schermata). Se si desidera applicare un filtro più complesse, è possibile scaricare il report in excel e creare una tabella pivot.

  ![][002]

 - **D: qual è il numero massimo di eventi vengono archiviati i report di gestione delle password?**

 > **A:** Fino a 1.000 password eventi di registrazione di reimpostazione Ripristina o la password vengono archiviati nei rapporti di gestione password.  Stiamo lavorando per espandere il numero in modo da includere altri eventi.

 - **Q: come fino alla versione passare i report di gestione delle password?**

 > **A:** I report di gestione password mostrano le operazioni eseguite negli ultimi 30 giorni. È in corso ulteriori ricerche come impostare un periodo più lungo. Per ora, se è necessario archiviare i dati, si scarica periodicamente i report e salvarli in un percorso diverso.

 - **D: è presente un numero massimo di righe che è possibile visualizzare i report di gestione delle password?**

 > **A:** Sì, un massimo di 1.000 righe possono essere visualizzati su uno dei report di gestione delle Password, se vengono visualizzate nell'interfaccia utente o il download. È in corso ulteriori ricerche come aumentare questo limite.

 - **D: è presente un'API per accedere alla reimpostazione della password o registrazione reporting dati?**

 > **A:** Sì, vedere la documentazione seguente per informazioni su come è possibile accedere a reimpostazione della password report flusso di dati.  [Informazioni sull'accesso password eventi dei report di reimpostazione a livello di programmazione](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Writeback di password
 - **D: come funziona Writeback di Password in background?**

 > **A:** Per informazioni su cosa accade quando si attiva Writeback di Password, nonché come dati fluisce attraverso il sistema di nuovo l'ambiente locale, vedere [che come Password Writeback funziona](active-directory-passwords-learn-more.md#how-password-writeback-works) . Vedere [modello di sicurezza Writeback di Password](active-directory-passwords-learn-more.md#password-writeback-security-model) in modalità Password Writeback funziona per informazioni su come è assicurare che writeback di Password è un servizio di protezione avanzata.

 - **D: come Password Writeback tempo per l'uso?  Esiste un ritardo di sincronizzazione come con la sincronizzazione delle password hash?**

 > **A:** Password Writeback è immediato. Si tratta di un'icona del pipeline che funziona sostanzialmente diverso dalla sincronizzazione hash password. Password Writeback consente agli utenti di ricevere commenti e suggerimenti in tempo reale sull'esito loro la reimpostazione della password o operazione di modifica. Il tempo medio per un corretto writeback di una password è in 500 ms.

 - **D: quali tipi di account funziona Writeback di Password per?**

 > **A:** Password Writeback funziona per federato e sincronizzati Hash Password degli utenti.

 - **D: Password Writeback applicare i criteri di password del proprio dominio?**

 > **A:** Sì, Password Writeback vengono applicate password età, cronologia, complessità, filtri e qualsiasi altra restrizione che può inserire nella posizione sulle password del proprio dominio locale.

 - **D: è protetto Writeback Password?  Com'è possibile che non individuato?**

 > **A:** Sì, Password Writeback è molto sicuro. Per ulteriori informazioni 4 livelli di protezione implementata dal servizio Writeback di Password, estrarre il [modello di protezione Password Writeback](active-directory-passwords-learn-more.md#password-writeback-security-model) di funzioni come Password Writeback.




## <a name="links-to-password-reset-documentation"></a>Collegamenti a password reimpostare documentazione
Di seguito sono disponibili collegamenti a tutte le pagine della documentazione di reimpostazione della Password di Azure Active Directory:

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [**Come funziona**](active-directory-passwords-how-it-works.md) - informazioni sui diversi componenti del servizio e cosa sei ognuno di essi
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - informazioni su come consentire agli utenti di reimpostare e cambiare le password cloud o locale
* [**Personalizza**](active-directory-passwords-customize.md) - informazioni su come personalizzare l'aspetto e l'aspetto e comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md) - informazioni su come distribuire rapidamente e gestire in modo efficiente password all'interno dell'organizzazione
* [**Ottenere informazioni approfondite**](active-directory-passwords-get-insights.md) - informazioni sulla funzionalità di creazione di report integrata
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md) - informazioni su come risolvere rapidamente i problemi con il servizio
* [**Altre informazioni**](active-directory-passwords-learn-more.md) - profondità passare direttamente ai dettagli tecnici del funzionamento del servizio


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
