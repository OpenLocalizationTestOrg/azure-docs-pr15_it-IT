
<properties
    pageTitle="Integrità utilizzando Azure Active Directory metta ADFS | Microsoft Azure"
    description="Questa è la pagina di Azure Active Directory connettersi integrità come monitorare le locale infrastruttura ADFS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>Collegare mediante Azure Active Directory integrità con ADFS
La documentazione seguente è specifica di monitoraggio dell'infrastruttura di ADFS con Azure Active Directory connettersi integrità. Per informazioni sul controllo Azure AD Connect (sincronizzazione) con integrità di connessione di Azure Active Directory, vedere [Integrità connettersi con Azure Active Directory per la sincronizzazione](active-directory-aadconnect-health-sync.md). Inoltre, per informazioni sul monitoraggio dei servizi di dominio Active Directory con integrità di connessione di Azure Active Directory, vedere [Integrità connettersi con Azure Active Directory con Active Directory](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Avvisi per AD FS
La sezione di Azure Active Directory connettersi integrità avvisi fornisce l'elenco di avvisi attivi. Ogni avviso include informazioni rilevanti, procedure di risoluzione e collegamenti alla documentazione correlata.

Fare doppio clic su un avviso attivo o risolto, per aprire una nuova pala con ulteriori informazioni, i passaggi da eseguire per risolvere l'avviso e collegamenti alla documentazione. È anche possibile visualizzare i dati cronologici sugli avvisi che sono stati risolti in passato.

![Azure AD Connect integrità portale](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>L'uso Analitica per AD FS
L'uso Analitica Azure integrità di connettersi AD Foundation analizza il traffico di autenticazione dei server federativi. È possibile fare doppio clic sull'utilizzo analitica, per aprire e l'analitica l'utilizzo, che include diverse metriche e raggruppamenti.

>[AZURE.NOTE] Per utilizzare Analitica l'uso con ADFS, è necessario assicurarsi che il controllo di ADFS è abilitato. Per ulteriori informazioni, vedere [Abilitare il controllo per AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Azure AD Connect integrità portale](./media/active-directory-aadconnect-health/report1.png)

Per selezionare statistiche aggiuntive, specificare un intervallo di tempo o per modificare il raggruppamento, pulsante destro del mouse sul grafico analitica l'utilizzo e selezionare Modifica grafico. Quindi è possibile specificare l'intervallo di tempo, selezionare una metrica diversa e modificare il raggruppamento. È possibile visualizzare la distribuzione del traffico di autenticazione in base a diversi "metriche" e raggruppare ogni unità di misura metriche utilizzando parametri rilevanti "Raggruppa per" descritti nella tabella riportata di seguito:

| Unità di misura metriche | Raggruppa per | Di conseguenza, quali il raggruppamento e perché è utile? |
| ------ | -------- | -------------------------------------------- |
| Totale richieste: Il numero totale di richieste elaborate dal servizio federativo | Tutti | Mostra il conteggio del numero totale di richieste senza raggruppamento. |
|  | Applicazione | Gruppi Totale richieste in base alla parte componente destinazione. Il raggruppamento è utile comprendere l'applicazione che sta ricevendo come percentuale del traffico totale. |
|  | Server | Gruppi Totale richieste basate sul server che la richiesta di elaborazione. Il raggruppamento è utile comprendere la distribuzione di caricamento del traffico totale. |
|  | Partecipa alla rete aziendale | Gruppi Totale richieste in base a provenire da dispositivi luogo di lavoro eseguito il join (noto). Il raggruppamento è utile per capire se le risorse sono accessibili tramite dispositivi sconosciuti per l'infrastruttura di identità. |
|  | Metodo di autenticazione | Gruppi Totale richieste in base al metodo di autenticazione utilizzato per l'autenticazione. Il raggruppamento è utile comprendere il metodo di autenticazione comuni che viene utilizzato per l'autenticazione. Ecco i metodi di autenticazione possibili <ol> <li>Autenticazione di Windows (Windows)</li> <li>Autenticazione (moduli) basata su moduli</li> <li>Single Sign-On (Single Sign-On)</li> <li>X509 certificato di autenticazione (certificato)</li> <br>Se il server federativi ricevono la convocazione con un Cookie Single Sign-on, tale richiesta viene conteggiata come Single Sign-On (Single Sign On). In tal caso, se il cookie è valido, l'utente non viene chiesto di specificare le credenziali e ottiene accesso ottimale all'applicazione. Questo comportamento risulta comune se si dispone di più parti componente protette da server federativi. |
|  | Percorso di rete | Gruppi Totale richieste in base alla posizione di rete dell'utente. Può trattarsi di una intranet o extranet. Il raggruppamento è utile sapere quali percentuale del traffico proviene da una intranet o extranet. |
| Numero totale di richieste non riuscito: Il numero totale richieste elaborate dal servizio federativo non è riuscita. <br> (Questa metrica è disponibile solo in ADFS per Windows Server 2012 R2)| Tipo di errore | Mostra il numero di errori in base ai tipi di errore predefinito. Il raggruppamento è utile comprendere i tipi di errori comuni. <ul><li>Nome utente o la Password non corretti: errori a causa di nome utente non corretto o la password.</li> <li>"Blocco extranet": errori a causa di richieste ricevute da un utente che è stato bloccato da extranet </li><li> "Scadenza Password": errori da parte degli utenti registrazione con una password scaduta.</li><li>"Account disattivato": errori a causa di utenti che si connettono con un account disattivato.</li><li>"L'autenticazione dei dispositivi": errori da parte degli utenti non funziona eseguire l'autenticazione mediante l'autenticazione di dispositivo.</li><li>"L'autenticazione utente": errori da parte degli utenti non funziona eseguire l'autenticazione a causa di un certificato non valido.</li><li>"MFA": errori a causa di utente non funziona eseguire l'autenticazione mediante l'autenticazione a più fattori.</li><li>"Altri credenziali": "Autorizzazione del rilascio": errori a causa di errori di autorizzazione.</li><li>"La delega del rilascio": errori a causa errori di delega del rilascio.</li><li>"Token accettazione": errori a causa di ADFS rifiuto token da un Provider di identità di terze parti.</li><li>"Protocollo": errore a causa degli errori del protocollo.</li><li>"Sconosciuto": rilevare tutte. Eventuali altri errori che non rientrano in categorie definite.</li> |
|  | Server | Gruppi degli errori basati sul server. Il raggruppamento è utile comprendere la distribuzione di errore su server. Distribuzione uniforme può essere un indicatore di un server in uno stato difettoso. |
|  | Percorso di rete | Gruppi degli errori in base alla posizione di rete delle richieste (extranet vs intranet). Il raggruppamento è utile comprendere il tipo di richieste che hanno esito negativo. |
|  | Applicazione | Raggruppa gli errori in base all'applicazione di destinazione (componente parti). Il raggruppamento è utile comprendere dall'applicazione di destinazione visualizzi maggior numero di errori. |
| Numero di utenti: numero medio di utenti univoci attivi nel sistema | Tutti | Questa metrica fornisce un conteggio del numero medio di utenti che utilizzano il servizio federativo nella sezione tempo selezionato. Gli utenti non sono raggruppati. <br>La media dipende l'intervallo di tempo selezionato. |
|  | Applicazione | Gruppi numero medio di utenti in base all'applicazione di destinazione (componente parti). Il raggruppamento è utile conoscere il numero di utenti usano dall'applicazione. |


## <a name="performance-monitoring-for-ad-fs"></a>Monitoraggio delle prestazioni per ADFS
Azure Active Directory connettersi integrità il monitoraggio delle prestazioni fornisce informazioni di monitoraggio su metriche. Selezionare la casella di monitoraggio, verrà visualizzata una nuova pala con informazioni dettagliate sulle metriche.


![Azure AD Connect integrità portale](./media/active-directory-aadconnect-health/perf1.png)


Se si seleziona l'opzione di filtro nella parte superiore e il, è possibile filtrare dal server per vedere metriche di un singolo server. Per modificare la metrica, pulsante destro del mouse sul grafico monitoraggio in e il monitoraggio e selezionare Modifica grafico. Quindi, dalla nuova pala visualizzata, è possibile selezionare statistiche aggiuntive dall'elenco a discesa e specificare un intervallo di tempo per la visualizzazione dei dati sulle prestazioni.

## <a name="reports-for-ad-fs"></a>Report per ADFS
Azure Active Directory connettersi integrità fornisce report sull'attività e le prestazioni di ADFS. Questi report consentono agli amministratori di comprendere le attività nei loro server ADFS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Top 50 utenti con gli account di accesso non riuscito di nome utente e Password

Uno dei motivi comuni per una richiesta di autenticazione non riuscita in un server ADFS è una richiesta con credenziali non valide, vale a dire un nome utente errato o una password. In genere si verifica per gli utenti a causa di password complesse, password dimenticata o errori di battitura.

Ma sono disponibili altri motivi che possono causare un numero imprevisto di richieste gestite dal server ADFS, ad esempio: un'applicazione che scadono le credenziali utente per cache e le credenziali o un utente non autorizzato si tenta di accedere a un account con una serie di password noto. Due esempi seguenti vengono illustrati motivi validi che potrebbero causare un aumento nelle convocazioni.

Azure Active Directory connettersi integrità per ADFS fornisce un report sui principali 50 utenti con tentativi di accesso non riuscita a causa di nome utente non valido o la password. Il report viene eseguito mediante l'elaborazione di controllo generati da tutti i server ADFS nella farm

![Azure AD Connect integrità portale](./media/active-directory-aadconnect-health-adfs/report1a.png)

In questo report è possibile accedere facilmente alle seguenti informazioni:

- Numero totale di richieste non riuscite con il nome utente/password errata negli ultimi 30 giorni
- Media # degli utenti che non è riuscita con un account di accesso di nome utente/password errate al giorno.


Fare clic su questa parte verrà visualizzata e il report principale che fornisce dettagli aggiuntivi. Questo blade include un grafico con tendenze informazioni che consentono di stabilire una linea di base relative alle richieste con errato nome utente o password. Inoltre, fornisce l'elenco di primi 50 utenti con il numero di tentativi.

Il grafico sono disponibili le informazioni seguenti:

- Numero totale di accessi non riusciti a causa di un nome utente/password errato in base al giorno.
- Numero totale di utenti univoci che non è riuscita gli account di accesso per ogni giorno.
- Indirizzo IP del client di ultima richiesta

![Azure AD Connect integrità portale](./media/active-directory-aadconnect-health-adfs/report3a.png)

Il report include le informazioni seguenti:

| Elemento del report | Descrizione
| ------ | -------- |
|ID utente| Mostra l'ID utente utilizzato. Questo valore è quella digitata dall'utente, che in alcuni casi è errato ID in uso.|
|Tentativi| Mostra il numero totale di tentativi per l'ID utente specifico. La tabella viene ordinata con il maggior numero di tentativi in ordine decrescente.|
|Ultimo errore| Mostra il timestamp quando l'ultimo errore.
|Ultimo errore IP | Mostra l'indirizzo IP Client dalla più recente richiesta non valida.|



>[AZURE.NOTE] Il report viene aggiornato automaticamente dopo ogni due ore con le nuove informazioni raccolte all'interno di un'ora specifica. Di conseguenza, tentativi di accesso nelle ultime due ore potrebbero non inclusi nel report.



## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect dello stato dell'installazione dell'agente](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operazioni integrità](active-directory-aadconnect-health-operations.md)
* [Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Collegare mediante Azure Active Directory integrità con Active Directory](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integrità domande frequenti](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
