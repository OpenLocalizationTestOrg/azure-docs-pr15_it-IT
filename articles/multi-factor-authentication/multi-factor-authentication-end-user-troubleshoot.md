<properties
    pageTitle="Risolvere i problemi di verifica in due passaggi | Microsoft Azure"
    description="In questo documento fornisce informazioni sulle operazioni da eseguire se vengono eseguite in un problema con l'autenticazione a più fattori Azure."
    services="multi-factor-authentication"
    keywords = "client di autenticazione a più fattori, il problema di autenticazione, l'ID di correlazione"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>In caso di problemi con la verifica in due passaggi

In questo articolo vengono illustrati alcuni problemi che possono verificarsi con la verifica in due passaggi. Se non è incluso il problema riscontrato, fornire un feedback dettagliato nella sezione commenti in modo da poter continuare a migliorare.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Perdita telefono o è stato furto

Esistono due modi per tornare al proprio account. Il primo consiste nell'accedere con il numero di telefono alternativo autenticazione, se è stata configurata una. Il secondo consiste nel chiedere all'amministratore per cancellare le impostazioni.

Se il telefono è stata persa o furto, è consigliabile che si chiedere all'amministratore di reimpostare la password dell'app e deselezionare qualsiasi ricordati dispositivi. Se l'amministratore non sa come eseguire questa operazione, scegliere in questo articolo: [Gestione utenti e i dispositivi](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Utilizzare un numero di telefono alternativo

Se è stata configurata più le opzioni di verifica, ad esempio un numero di telefono secondario o un'app autenticatore su un dispositivo diverso, è possibile usare uno di questi piani effettuare l'accesso.

Per accedere con il numero di telefono alternativo, procedere come segue:

1. Effettuare l'accesso come di consueto.
2. Quando viene richiesto di verificare ulteriormente l'account, scegliere **Usa un'opzione di verifica diverso**.

    ![Gerarchia](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Selezionare il numero di telefono che è possibile accedere a.

    ![Altro telefono](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Dopo aver verificato nel proprio account, [gestire le impostazioni](multi-factor-authentication-end-user-manage-settings.md) per modificare il numero di telefono di autenticazione.

>[AZURE.IMPORTANT]
>È importante configurare un numero di telefono di autenticazione secondaria. Se il numero di telefono principale e le app per dispositivi mobili sono in corso la telefonata stessa, è necessario una terza alternativa se il telefono perdito o furto.

### <a name="clear-your-settings"></a>Cancellare le impostazioni

Se non è stato configurato un numero di telefono di autenticazione secondario, sarà necessario contattare l'amministratore per assistenza. Chiedere di cancellare le impostazioni per consentire al successivo accesso, verrà richiesto di [configurare l'account di](multi-factor-authentication-end-user-first-time.md) nuovo.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Non si ricevono un testo o chiamata al telefono

Esistono diversi motivi per cui è possibile provare a effettuarlo messaggio, ma non il testo o una telefonata. Se è stata ricevuta correttamente testi o telefonate al telefono in passato, si tratta probabilmente un problema con il provider di telefono, non l'account. Assicurarsi che devono segnale cella efficace e se si sta tentando di ricevere un messaggio di testo assicurarsi che il piano di servizio e telefono supporto per i messaggi di testo.

Se è stata attesa alcuni minuti prima di un testo o una chiamata, il modo più rapido per accedere all'account è tentare un'opzione diversa.

1. Selezionare **utilizza un'opzione di verifica diverso** nella pagina che è in attesa per la verifica.

    ![Gerarchia](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Selezionare il metodo di recapito o numero di telefono che si desidera utilizzare.

    Se si è ricevuto più codici di verifica, funziona solo a quello più recente.

Se non si dispone di un altro metodo configurato, rivolgersi all'amministratore e chiedere di cancellare le impostazioni. Al successivo che accesso, è necessario [configurare l'autenticazione a più fattori](multi-factor-authentication-end-user-first-time.md) nuovamente.


Se hai spesso ritardi a causa di segnale di cella non valido, è consigliabile che utilizzare [Microsoft autenticatore app](multi-factor-authentication-microsoft-authenticator.md) su smartphone. L'app può generare i codici di sicurezza casuale che consente di accedere e tali codici non richiedono tutte le connessioni internet o segnale di cella.


## <a name="app-passwords-are-not-working"></a>Password dell'App non funzionano

Prima di tutto, assicurarsi di avere immesso correttamente la password di app.  Se ancora non funziona provare l'accesso e [creare una nuova password di app](multi-factor-authentication-end-user-app-passwords.md).  Se questa operazione non funziona, contattare l'amministratore e devono essere [eliminare le password app esistente](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e quindi è possibile crearne uno nuovo.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Non è riuscita trovare una risposta a un problema.

Se si sono ancora in esecuzione dei problemi dopo aver provato queste procedure di risoluzione dei problemi, contattare l'amministratore o la persona che ha configurato l'autenticazione a più fattori dell'utente. Si dovrebbe essere per consentire l'identificazione.

Inoltre, è possibile inserire una domanda nel [forum di Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o [contattare il supporto tecnico](https://support.microsoft.com/contactus) ed è necessario rispondere al problema come è possibile.

Se si contatta il supporto, includono le seguenti informazioni:

- **ID utente** : che cos'è l'indirizzo di posta elettronica che si è tentato di per l'accesso?
- **Descrizione generale dell'errore** : il messaggio di errore hai acquistato vedere?  Se si è non verificato nessun messaggio di errore, viene descritto il comportamento imprevisto notato, in modo dettagliato.
- **Pagina** – quali pagina presenti quando si è visto l'errore (includere l'URL)?
- **Codice di errore** - si riceve il codice di errore specifico.
- **ID sessione** - l'id di sessione specifici vengono visualizzati.
- **ID di correlazione** : qual è il codice di id di correlazione generato quando l'utente visto l'errore.
- **Timestamp** : che cos'è stata preciso data e ora è visualizzato l'errore (includere il fuso orario)?

Molte di queste informazioni sono disponibili nella pagina dell'accesso. Quando non verificare l'accesso in ora, selezionare **Visualizza dettagli**.

![Accedere dettagli sull'errore](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Queste informazioni consentono di risolvere il problema al più presto.

## <a name="related-topics"></a>Argomenti correlati
- [Gestire le impostazioni per la verifica in due passaggi](multi-factor-authentication-end-user-manage-settings.md)  
- [Domande frequenti su applicazione Microsoft Authenticator](multi-factor-authentication-app-faq.md)
