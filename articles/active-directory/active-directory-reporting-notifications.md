<properties
    pageTitle="Notifiche di creazione di report di Azure Active Directory"
    description="Come usare Azure Active Directory reporting le notifiche relative a accesso sospetti aggiuntivi."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notifiche di creazione di report di Azure Active Directory

## <a name="what-reports-generate-email-notifications"></a>Quali rapporti generano notifiche tramite posta elettronica

Al momento solo irregolari Accedi attivazione report delle attività di posta elettronica le notifiche.

## <a name="what-is-an-irregular-sign-in"></a>Che cos'è un "irregolari Accedi"?

Accessi irregolari sono quelli che sono stati identificati tramite il nostro algoritmi, in base a una condizione "Impossibile viaggio" combinati con il dispositivo e un percorso di accesso anomala di apprendimento. È possibile che un pirata informatico sta tentando di accedere con l'account.

## <a name="who-receives-the-email-notifications"></a>Chi riceverà le notifiche di posta elettronica?

Messaggio di posta elettronica viene inviato a tutti gli amministratori globali che sono stati assegnati una licenza di Active Directory Premium. Per assicurarsi che venga recapitata, abbiamo inviarla a amministratori indirizzo di posta elettronica alternativo anche. Gli amministratori devono includere aad-alerts-noreply@mail.windowsazure.com nell'elenco Mittenti attendibili, in modo che non perdere la posta elettronica.

## <a name="how-often-are-these-emails-sent"></a>Con quale frequenza gli questi messaggi di posta elettronica inviati?

Messaggio di posta elettronica viene inviato se 10 nuova irregolari accesso attività eseguite negli ultimi 30 giorni oppure dopo l'ultimo messaggio di posta elettronica è stato inviato, seconda del valore è minore di.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Come si accede a report indicato nel messaggio di posta elettronica?

Quando si fa clic sul collegamento, si verrà reindirizzati alla pagina del report all'interno del portale classica Azure. Per accedere al report, è necessario essere sia:

- Un amministratore o co-amministratore dell'abbonamento Azure

- Un amministratore globale nella directory e assegnata una licenza di Active Directory Premium. Per ulteriori informazioni, vedere [edizioni di Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>È possibile disattivare questi messaggi di posta elettronica?

Sì, per disattivare le notifiche relative a anomali accessi all'interno del portale classico Azure, fare clic su **Configura**e quindi selezionare **disattivato** nella sezione **notifiche** .

## <a name="whats-next"></a>Che cos'è successiva
- Desiderano ulteriori informazioni sui report di protezione, controllo e attività sono disponibili? Estrarre [sicurezza di Azure Active Directory, controllo e report di attività](active-directory-view-access-usage-reports.md)
- [Guida introduttiva di Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Aggiungere marchio a Accedi e pannello accesso pagine della società](active-directory-add-company-branding.md)
