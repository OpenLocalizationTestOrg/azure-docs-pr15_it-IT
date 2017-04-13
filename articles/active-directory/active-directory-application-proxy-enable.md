<properties
    pageTitle="Attivare il Proxy di applicazione di Azure Active Directory | Microsoft Azure"
    description="Attivare il Proxy dell'applicazione nel portale di classica Azure e installare i connettori per il proxy inverso."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Abilitare i Proxy di applicazione nel portale di Azure

In questo articolo sono illustrati i passaggi per abilitare Proxy di applicazione di Microsoft Azure Active Directory per la directory cloud in Azure Active Directory.

Se non si ha familiarità con i Proxy di applicazione consente di eseguire, ulteriori informazioni su [come specificare l'accesso remoto sicuro a locale applicazioni](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Prerequisiti Proxy dell'applicazione
Prima di attivare e utilizzare i servizi Proxy di applicazione, è necessario che:

- Una [sottoscrizione di base o avanzata di Microsoft Azure Active Directory](active-directory-editions.md) e una directory di Azure Active Directory di cui si è un amministratore globale.
- Un server che esegue Windows Server 2012 R2 o Windows 8.1 o versione successiva, in cui è possibile installare il connettore Proxy applicazione. Il server invia richieste ai servizi Proxy di applicazione nel cloud e richiede una connessione HTTP o HTTPS alle applicazioni che si desidera pubblicare.

    - Single sign-on per le applicazioni pubblicate, questo computer deve essere dominio nello stesso dominio di Active Directory le applicazioni che si desidera pubblicare.

- Se esiste un firewall nel percorso, assicurarsi che è aperto in modo che il connettore può richiedere HTTPS (TCP) al Proxy di applicazione. Il connettore utilizza queste porte insieme sottodomini che fanno parte di alto livello domini msappproxy.net e servicebus.windows.net. Assicurarsi di aprire le seguenti porte per il traffico **in uscita** :

  	| Numero di porta | Descrizione |
  	| --- | --- |
  	| 80 | Abilitare il traffico HTTP in uscita per la convalida di sicurezza. |
  	| 443 | Attivare l'autenticazione utente contro Azure Active Directory (obbligatorio solo per il processo di registrazione connettore) |
  	| 10100-10120 | Abilitare risposte LOB HTTP inviate al proxy di |
  	| 9352, 5671 | Attivare le comunicazioni tra il connettore verso il servizio di Azure le richieste in arrivo. |
  	| 9350 | Facoltativo, per consentire prestazioni migliori per le richieste in arrivo |
  	| 8080 | Abilitare la sequenza di avvio di connettore e aggiornamenti automatici connettore |
  	| 9090 | Attivare la registrazione di connettore (obbligatorio solo per il processo di registrazione connettore) |
  	| 9091 | Attivare il rinnovo automatico connettore protezione certificato |

    Se il firewall impone il traffico in base agli utenti di origine, aprire le porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Inoltre, assicurarsi di abilitare la porta 8080 per il sistema operativo NT.

- Se l'organizzazione utilizza server proxy per la connessione a internet, richiedere un'occhiata ai post di blog [utilizzo di server proxy locale esistenti](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) per informazioni dettagliate su come configurarli.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Passaggio 1: Abilitare Proxy dell'applicazione in Azure Active Directory
1. Accedere come amministratore nel [portale classica Azure](https://manage.windowsazure.com/).
2. Passare a Active Directory e selezionare la directory in cui si vuole abilitare Proxy dell'applicazione.

    ![Active Directory: icona](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selezionare **Configura** nella pagina della directory e scorrere fino a **Proxy dell'applicazione**.
4. Attivare/disattivare **Attiva servizi Proxy di applicazioni per la Directory** **abilitate**.

    ![Attivare il Proxy dell'applicazione](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selezionare **Download**. Verrà visualizzato di **Azure Active Directory Application Proxy connettore Download**. Leggere e accettare le condizioni di licenza e fare clic su **Download** per salvare il file di Windows Installer (.exe) per il connettore.

## <a name="step-2-install-and-register-the-connector"></a>Passaggio 2: Installare e registrare il connettore
1. Eseguire **AADApplicationProxyConnectorInstaller.exe** nel server preparato secondo i prerequisiti.
2. Seguire le istruzioni della procedura guidata per l'installazione.
3. Durante l'installazione, verrà chiesto di registrare il connettore con Proxy di applicazione del tenant di Azure Active Directory.

  - Specificare le credenziali di amministratore globale di Azure Active Directory. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
  - Verificare che l'amministratore che registra il connettore sia nella stessa directory in cui è abilitato il servizio Proxy di applicazione. Ad esempio, se il dominio tenant contoso.com, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias su tale dominio.
  - Se **La protezione avanzata di Internet Explorer** è impostato su **On** nel server in cui si installa il connettore, la schermata di registrazione potrebbe risultare bloccata. Seguire le istruzioni nel messaggio di errore per consentire l'accesso. Assicurarsi che la protezione avanzata di Internet Explorer è disattivata.
  - Se la registrazione di connettore non riesce, vedere [Risolvere i problemi di Proxy di applicazione](active-directory-application-proxy-troubleshoot.md).  

4. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server:

    - **Microsoft AAD applicazione Proxy Connector** consente la connettività
    - Applicazione di **Microsoft AAD applicazione Proxy connettore aggiornamento** è un servizio di aggiornamento automatico, quale periodicamente verifica la presenza di nuove versioni del connettore e quindi aggiornare il connettore in base alle esigenze.

    ![Servizi Proxy Connector App - schermata](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Fare clic su **Fine** nella finestra di installazione.

Ai fini della disponibilità, è necessario distribuire almeno due connettori. Per distribuire più connettori, ripetere i passaggi 2 e 3, sopra. Ogni connettore deve essere registrata separatamente.

Se si vuole disinstallare il connettore, disinstallare il servizio Connector e il servizio di aggiornamento. Riavviare il computer per rimuovere completamente il servizio.


## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti per [applicazioni di pubblicazione con Proxy dell'applicazione](active-directory-application-proxy-publish.md).

Se si dispone di applicazioni disponibili in diverse posizioni o reti separate, è possibile utilizzare i gruppi di connettore per organizzare i connettori diversi in unità logiche. Ulteriori informazioni [sull'uso dei connettori Proxy dell'applicazione](active-directory-application-proxy-connectors.md).
