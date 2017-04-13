<properties
   pageTitle="Azure Active Directory creazione di report: Guida introduttiva | Microsoft Azure"
   description="Sono elencati i diversi report disponibili nei report di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Guida introduttiva di Azure Active Directory creazione di report

## <a name="what-it-is"></a>Che cos'è

Azure Active Directory (Azure Active Directory) include sicurezza, attività e report di controllo per la directory. Di seguito è riportato un elenco di report inclusi:

### <a name="security-reports"></a>Report sulla protezione

- Componenti aggiuntivi di accesso da origini sconosciute
- Accessi dopo più errori
- Componenti aggiuntivi di accesso da più aree geografiche
- Componenti aggiuntivi di accesso da indirizzi IP con attività sospetti
- Attività di accesso irregolare
- Componenti aggiuntivi di accesso da dispositivi eventualmente virus
- Utenti con attività di accesso anomala

### <a name="activity-reports"></a>Report attività

- Utilizzo dell'applicazione: riepilogo
- Utilizzo dell'applicazione: dettagliata
- Dashboard di applicazione
- Account errori di provisioning
- Dispositivi di singoli utenti
- Singolo utente attività
- Report di gruppi di attività
- Report attività registrazione di reimpostazione password
- Attività di reimpostazione della password

### <a name="audit-reports"></a>Report di controllo

- Report audit directory

> [AZURE.TIP] Per altre la documentazione sulla creazione di report di Azure Active Directory, vedere [visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Come funziona


### <a name="reporting-pipeline"></a>Pipeline di creazione di report

Creazione di report pipeline è costituita da tre passaggi principali. Ogni volta che un utente accede o un tipo di autenticazione è una chiamata effettuata, accade quanto segue:

- Prima di tutto, l'utente autenticato (esito positivo o negativo) e il risultato viene archiviato nei database di servizio di Azure Active Directory.
- A intervalli regolari, tutti accesso recenti vengono elaborate aggiuntivi. A questo punto, la sicurezza e algoritmi attività anomala esegue la ricerca accesso recenti tutti aggiuntivi per attività sospetto.
- Dopo l'elaborazione, i report sono scritti, cache e served nel portale di classica Azure.

### <a name="report-generation-times"></a>Segnalare i tempi di generazione

A causa di volume elevato di autenticazione e accedere componenti aggiuntivi di elaborazione della piattaforma Azure Active Directory, il più recenti accessi elaborati sono, in Media, un'ora precedenti. Raramente, potrebbe richiedere fino a 8 ore per l'elaborazione di componenti aggiuntivi segno più recenti.

Sono disponibili il più recente trasformato accesso aggiuntivo esaminando il testo della Guida nella parte superiore di ogni rapporto.

![Il testo nella parte superiore di ogni rapporto della Guida](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Per altre la documentazione sulla creazione di report di Azure Active Directory, vedere [visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Guida introduttiva


### <a name="sign-into-the-azure-classic-portal"></a>Accedere al portale di classica Azure

Prima di tutto, è necessario eseguire l'accesso al [portale classica Azure](https://manage.windowsazure.com) come amministratore globale o amministratore conformità. Deve essere un amministratore del servizio di abbonamento Azure o dall'amministratore condivisa o utilizzare "Accesso a Azure AD" Azure abbonamento.

### <a name="navigate-to-reports"></a>Passare ai report

Per visualizzare i report, passare alla scheda report nella parte superiore della directory.

Se si tratta la prima volta che visualizzare i report, è necessario accettare le condizioni in una finestra di dialogo è possibile visualizzare i report. In questo modo viene è accettabile per gli amministratori dell'organizzazione per visualizzare dati, che possono essere considerati informazioni personali in alcuni paesi.

![Nella finestra di dialogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Esplorare ogni rapporto

Spostarsi in ogni report per visualizzare i dati raccolti e i segno di componenti aggiuntivi di elaborazione. È possibile trovare un [elenco di tutti i report di seguito](active-directory-reporting-guide.md).

![Tutti i report](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Scaricare il report in formato CSV

Ogni rapporto può essere scaricato come file CSV (delimitato da virgole). È possibile utilizzare questi file in Excel, ottenere informazioni o analisi di terze parti applicazioni per l'ulteriore analisi dei dati.

Per scaricare i report in un formato CSV, passare al report e fare clic su "Download" nella parte inferiore.

![Pulsante di download](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Per altre la documentazione sulla creazione di report di Azure Active Directory, vedere [visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Passaggi successivi

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalizzare gli avvisi per accedere anomala in attività

Passare alla scheda "Configura" della directory.

Scorrere fino alla sezione "Notifiche".

Attivare o disattivare la sezione "Notifiche dei componenti aggiuntivi di accesso anomali di posta elettronica".

![Sezione notifiche](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrazione con Azure AD API di segnalazione

Vedere [Guida introduttiva con l'API dei report](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Avviare l'autenticazione a più fattori sugli utenti

Selezionare un utente in un report.

Fare clic sul pulsante "Abilitare MFA" nella parte inferiore dello schermo.

![Pulsante autenticazione a più fattori nella parte inferiore dello schermo](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Per altre la documentazione sulla creazione di report di Azure Active Directory, vedere [visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Ulteriori informazioni


### <a name="audit-events"></a>Controllare gli eventi

Informazioni sulle quali eventi vengono controllate nella directory di [Azure Active Directory dei report di controllo degli eventi](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integrazione di API

Vedere [Guida introduttiva con l'API dei report](active-directory-reporting-api-getting-started.md) e la [documentazione di riferimento API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Stabilire un contatto

Messaggio di posta elettronica [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) per commenti e suggerimenti, Guida o domande potrebbe essere.

> [AZURE.TIP] Per altre la documentazione sulla creazione di report di Azure Active Directory, vedere [visualizzare i report di access e l'uso](active-directory-view-access-usage-reports.md).
