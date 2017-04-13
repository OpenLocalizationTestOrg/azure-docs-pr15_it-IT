<properties 
    pageTitle="Autenticazione IIS e Azure Server di autenticazione a più fattori"
    description="Questa è la pagina di autenticazione a più fattori Azure utili nella distribuzione di autenticazione IIS e Server di autenticazione a più fattori Azure."
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
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>Autenticazione IIS

La sezione autenticazione IIS del Server di autenticazione a più fattori Azure consente di abilitare e configurare l'autenticazione di IIS per l'integrazione con applicazioni web di Microsoft IIS. Il Server di autenticazione a più fattori Azure consente di installare un plug-in cui è possibile filtrare richieste inoltrate al server web IIS per aggiungere l'autenticazione a più fattori Azure. Il plug-in IIS fornisce supporto per l'autenticazione basata su moduli e HTTP Windows integrata. Attendibile che IP possono essere configurate anche a indirizzi IP interni esenzione da autenticazione a due fattori.


![Autenticazione IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Mediante l'autenticazione basata su moduli IIS con Server Azure autenticazione a più fattori

Per proteggere un'applicazione web IIS che utilizza l'autenticazione basata su moduli, installare il Server di autenticazione a più fattori Azure sul server web IIS e configurare il Server per la procedura seguente.

1. All'interno del Server di autenticazione a più fattori Azure fare clic sull'icona di autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda basate su moduli.
3. Fare clic su Aggiungi. pulsante.
4. Per individuare il nome utente, le variabili di password e dominio automaticamente, immettere l'URL di accesso (ad esempio https://localhost/contoso/auth/login.aspx) nella finestra di dialogo Auto-Configure Form-Based sito Web e fare clic su OK.
5. Selezionare la casella di corrispondenza utente richiede l'autenticazione a più fattori se tutti gli utenti sono stati o verranno importati nel Server e soggetto a autenticazione a più fattori. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di autenticazione a più fattori, lasciare selezionata la casella.
6. Se non è possibile rilevare automaticamente le variabili di pagina, fare clic su specificare manualmente. pulsante nella finestra di dialogo Auto-Configure Form-Based sito Web.
7. Nella finestra di dialogo sito Web Add Form-Based immettere l'URL di pagina di accesso nel campo URL di inviare e immettere il nome di un'applicazione (facoltativo). Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato nei messaggi di autenticazione SMS o App Mobile. Vedere la Guida per altre informazioni sull'URL inviare.
8. Selezionare il formato della richiesta corretto. Questo è impostato su "POST o GET" per la maggior parte delle applicazioni web.
9. Immettere il nome utente variabile, variabile Password e variabili di dominio (se viene visualizzato nella pagina di accesso). Potrebbe essere necessario passare alla pagina di accesso in un web browser, pulsante destro del mouse sulla pagina e selezionare "Visualizza origine" per trovare i nomi delle caselle di input all'interno della pagina.
10. Selezionare la casella di corrispondenza utente richiede l'autenticazione a più fattori di Azure se tutti gli utenti sono stati o verranno importati nel Server e soggetto a autenticazione a più fattori. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di autenticazione a più fattori, lasciare selezionata la casella. Vedere la Guida per ulteriori informazioni su questa caratteristica.
11.  Fare clic su Avanzate. pulsante per verificare le impostazioni avanzate, compresa la possibilità di selezionare un file di pagina personalizzato negazione per memorizzare nella cache l'autenticazione ha esito positivo per il sito Web per un periodo di tempo utilizzando i cookie e per selezionare questa opzione per l'autenticazione delle credenziali primarie per il dominio di Windows, una directory LDAP o un server RADIUS. Al termine fare clic sul pulsante OK per tornare alla finestra di dialogo Add Form-Based sito Web. Vedere la Guida per altre informazioni sulle impostazioni avanzate.
12. Fare clic sul pulsante OK.
13. Dopo le variabili di URL e pagina sono state rilevate o immesse, i dati del sito Web verranno visualizzata nel riquadro basate su moduli.
14. Vedere gli abilitare IIS Plug-in per la sezione Server di autenticazione a più fattori Azure direttamente riportata di seguito per completare la configurazione di autenticazione IIS.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Mediante l'autenticazione di Windows integrata con Server Azure autenticazione a più fattori

Per proteggere un'applicazione web IIS che utilizza l'autenticazione integrata Windows HTTP, installare il Server di autenticazione a più fattori Azure sul server web IIS e configurare il Server per la procedura seguente.

1. All'interno del Server di autenticazione a più fattori Azure fare clic sull'icona di autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda HTTP. Fare clic sulla scheda basate su moduli.
3. Fare clic su Aggiungi. pulsante.
4. Nella finestra di dialogo Aggiungi Base URL immettere l'URL per il sito Web in cui l'autenticazione HTTP è eseguita (ad esempio http://localhost/owa) nel campo URL di Base e immettere il nome di un'applicazione (facoltativo). Il nome dell'applicazione all'interno dei report di autenticazione a più fattori Azure e che venga visualizzato nei messaggi di autenticazione SMS o App Mobile.
5. Regolare il tempo di inattività e massimo volte sessione se il valore predefinito non sono sufficiente.
6. Selezionare la casella di corrispondenza utente richiede l'autenticazione a più fattori se tutti gli utenti sono stati o verranno importati nel Server e soggetto a autenticazione a più fattori. Se un numero significativo di utenti non sono stati ancora importato nel Server e/o sarà escluso dall'operazione di autenticazione a più fattori, lasciare selezionata la casella. Vedere la Guida per ulteriori informazioni su questa caratteristica.
7. Se lo si desidera, selezionare la casella di cache cookie.
8. Fare clic sul pulsante OK.
9. Vedere la sezione [abilitare IIS Plug-in per Server di autenticazione a più fattori Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) direttamente riportata di seguito per completare la configurazione di autenticazione IIS.


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Attivare IIS Plug-in per Server Azure autenticazione a più fattori

Dopo aver configurato basata su moduli o URL di autenticazione HTTP e impostazioni, è necessario selezionare il luogo in cui i plug-in IIS di autenticazione a più fattori Azure deve caricato e attivato in IIS. Utilizzare la procedura seguente:

1. Se è in esecuzione su IIS 6, fare clic sulla scheda ISAPI e selezionare il sito Web che l'applicazione web è in esecuzione in (ad esempio sito Web predefinito) per attivare il filtro ISAPI di autenticazione a più fattori Azure plug-in per tale sito.
2. Se è in esecuzione in IIS 7 o versione successiva, fare clic sulla scheda modulo nativo e selezionare il server, siti o applicazioni per abilitare IIS plug-nei livelli desiderati.
3. Selezionare la casella autenticazione abilitare IIS nella parte superiore dello schermo. Autenticazione a più fattori Azure è ora protezione applicazione IIS selezionata. Assicurarsi che gli utenti sono stati importati nel Server. Se si preferisce proprietà consentite che indirizzi IP interna in modo che l'autenticazione a due fattori non è necessario durante l'accesso al sito Web da tali percorsi, vedere la sezione indirizzi IP attendibili riportata di seguito.


## <a name="trusted-ips"></a>IP di attendibile

Gli indirizzi IP attendibili consente agli utenti di ignorare l'autenticazione a più fattori Azure per le richieste di sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è consigliabile agli utenti di esenzione da autenticazione a più fattori Azure durante la registrazione dall'ufficio. A tale scopo, specificare la subnet di office come voce di indirizzi IP attendibili. Per configurare attendibili IP utilizzare la procedura seguente:

1. Nella sezione autenticazione IIS, fare clic sulla scheda indirizzi IP attendibili.
2. Fare clic su Aggiungi. pulsante.
3. Quando viene visualizzata la finestra di dialogo Aggiungi IP attendibili, selezionare la singola IP, intervalli IP o pulsante di opzione Subnet.
4. ntrale indirizzo IP subnet che deve essere whitelisted o intervallo di indirizzi IP. Se si immette una subnet, selezionare la maschera appropriato e fare clic sul pulsante OK. La proprietà consentite a questo punto è stato aggiunto.
