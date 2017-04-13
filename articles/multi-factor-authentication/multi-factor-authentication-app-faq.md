<properties
    pageTitle="App Microsoft autenticatore domande frequenti"
    description="Fornisce un elenco di domande e risposte relativi all'app Microsoft Authentication e l'autenticazione a più fattori Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Domande frequenti su applicazione Microsoft Authenticator

L'app Microsoft Authenticator sostituita l'app autenticatore Azure e app consigliati quando si usa l'autenticazione a più fattori Azure. L'app è disponibile per Windows Phone, Android e iOS.

## <a name="frequently-asked-questions"></a>Domande frequenti

- **Dov'è di Azure autenticazione, autenticazione a più fattori e App account Microsoft?**

    L'app Microsoft Authenticator sostituisce reciprocamente queste app. Azure autenticatore effettuato l'aggiornamento a Microsoft Authenticator. Se si utilizza l'autenticazione a più fattori o App account Microsoft, installare Microsoft Authenticator e aggiungere di nuovo gli account. Assicurarsi che completare l'aggiunta di account per la nuova app prima di eliminare quelle precedenti.

- **Si sta già utilizzando l'applicazione di Microsoft Authenticator per i codici di verifica. Come attivare le notifiche push di un solo clic?**  

    Approvazione di un accesso aggiuntivo tramite notifica push è disponibile solo per gli account Microsoft e non per gli account di terze parti come Google o Facebook. Per gli account di Microsoft aziendale o dell'istituto di istruzione, l'organizzazione può scegliere di disattivare questa opzione, attraverso.

    Se si usa un account Microsoft per l'account personale e si vuole passare le notifiche push, è necessario aggiungere il proprio account. Registrare nuovamente il dispositivo con il proprio account e configurare le notifiche push.  

    Se l'account non dispone di attiva la verifica in due passaggi, vedere [sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per decidere se è appropriato.  

- **Quando sarà in grado di usare le notifiche push fare clic su un iPhone o iPad?**  

    Questa caratteristica è disponibile la versione beta fino alla fine del mese di agosto, quando diventa reso disponibile per gli account Microsoft. Se si desidera partecipare al programma beta, inviare messaggi di posta elettronica msauthenticator@microsoft.com. Includere il nome, cognome e l'ID Apple nel messaggio.  

- **Notifiche push di un solo clic funzionano per gli account non Microsoft?**  

    No, le notifiche push funzionano solo con account Microsoft e di Azure Active Directory. Se il lavoro o dell'istituto di istruzione utilizza account Azure Active Directory, è possibile disattivare questa caratteristica.  

- **Ripristinato il dispositivo da un backup e i codici di account sono mancanti o non funziona. Cos'è successo?**  

    Per motivi di sicurezza, non il ripristino di account da un backup di app. Se si ripristina l'app iOS da un backup, gli account vengono comunque visualizzati ma non funzionano per ricevere verifiche di accesso o generare i codici di sicurezza. Dopo avere ripristinato l'app, eliminare gli account e aggiungerle di nuovo.

- **Viene visualizzato un nuovo dispositivo. Come rimuovere l'app Microsoft Authenticator dal dispositivo precedente e passare a quello nuovo**

    Aggiungere l'app Microsoft Authenticator a un nuovo dispositivo non automaticamente rimuoverlo da altri dispositivi. Per gestire i dispositivi sono configurati per l'account, visitare il sito Web stesso che consente di gestire la verifica in due passaggi e scegliere di rimuovere App precedente.

    Per gli account Microsoft personali, il sito Web è la pagina di [protezione dell'account](https://account.microsoft.com/security) . Per gli account di Microsoft aziendale o dell'istituto di istruzione, il sito Web può essere [App personali](https://myapps.microsoft.com) o un portale personalizzato che l'organizzazione ha configurato l'archiviazione.

## <a name="contact-us"></a>Contatta Microsoft

Se non è stato rispondere alla domanda qui, lasciare un commento nella parte inferiore della pagina. In alternativa, [contattare il supporto tecnico](https://support.microsoft.com/contactus) e si verrà rispondere al problema come è possibile.

Se si contatta il supporto, è possibile includere al meglio le informazioni seguenti:

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

- [Domande frequenti su Azure autenticazione a più fattori](multi-factor-authentication-faq.md)  
- [Informazioni sulla verifica in due passaggi](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) per gli account Microsoft
- [In caso di problemi con la verifica in due passaggi?](multi-factor-authentication-end-user-troubleshoot.md)
