<properties
    pageTitle="Risoluzione dei problemi per problemi di accesso di Azure Active Directory | Microsoft Azure"
    description="Informazioni su altre operazioni che è possibile eseguire per risolvere i problemi di accesso alle risorse online dell'organizzazione."
    services="active-directory"
    keywords="basate su dispositivo condizionale l'accesso, la registrazione di dispositivo, abilitare la registrazione di dispositivo, registrazione periferica e MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Risoluzione dei problemi per problemi di accesso di Azure Active Directory

Si tenta di accedere a SharePoint Online intranet aziendale e viene visualizzato un messaggio di errore "accesso negato". Che lavoro fai?

In questo articolo è descritta procedura per la risoluzione che può aiutare a risolvere i problemi di accesso alle risorse online dell'organizzazione.

Per assistenza nella risoluzione Azure Active Directory (Azure Active Directory) problemi di accesso, passare alla sezione vedere l'articolo che illustra la piattaforma del dispositivo:

-   Dispositivo Windows
-   dispositivo iOS (selezionare torna presto per assistenza con iPhone e iPad.)
-   Dispositivo Android (ricontrollare breve per assistenza con Android telefoni e Tablet.)

## <a name="access-from-a-windows-device"></a>Accesso da un dispositivo Windows

Se il dispositivo è in esecuzione una delle seguenti piattaforme, sarà nelle sezioni successive del messaggio di errore visualizzato quando si tenta di accedere a un'applicazione o servizio:

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Dispositivo non è registrato

Se il dispositivo non è registrato con Azure Active Directory e l'applicazione è protetto con un criterio basato sul dispositivo, potrebbe comparire una pagina che mostra uno di questi messaggi di errore:

!["Non è possibile ottenere sono da questa posizione" messaggi per i dispositivi non registrati] (./media/active-directory-conditional-access-device-remediation/01.png "Scenario:")

Se il dispositivo è dominio di Active Directory nell'organizzazione, provare questa procedura:

1.  Verificare che accedere a Windows con l'account di lavoro (account Active Directory).
2.  Connettersi alla rete aziendale tramite una rete privata virtuale (VPN) o DirectAccess.
3.  Dopo essersi connessi, premere il tasto logo Windows + tasto L per bloccare la sessione di Windows.
4.  Immettere le credenziali dell'account di lavoro per sbloccare la sessione di Windows.
5.  Attendere qualche minuto e riprovare accedere all'applicazione o servizio.
6.  Se viene visualizzato nella stessa pagina, fare clic sul collegamento **ulteriori dettagli** e quindi contattare l'amministratore e i dettagli.

Se il dispositivo non è dominio ed esegue Windows 10, sono disponibili due opzioni:

- Eseguire Join di Azure Active Directory
- Aggiungere l'account aziendale o dell'istituto di istruzione per Windows

Per informazioni su come queste opzioni sono diverse, vedere [dispositivi con Windows 10 dell'area di lavoro](active-directory-azureadjoin-windows10-devices.md).

Per eseguire Join di Azure Active Directory, attenersi alla seguente procedura per la piattaforma del dispositivo viene eseguito su. (Join di azure Active Directory non è disponibile in Windows Phone.)

**Aggiornamento di Windows 10 anniversario**

1.  Aprire l'app di **Impostazioni** .
2.  Fare clic su **account** > **accesso aziendale o dell'istituto di istruzione**.
3.  Fare clic su **Connetti**.
4.  Fare clic su **Partecipa alla periferica di Azure Active Directory**.
5.  Eseguire l'autenticazione per l'organizzazione, fornire l'autenticazione a più fattori se richiesto e quindi seguire le istruzioni visualizzate.
6.  Eseguire la disconnessione e quindi accedere con l'account di lavoro.
7.  Provare ad accedere all'applicazione.


**Aggiornamento di Windows 10 novembre 2015**

1.  Aprire l'app di **Impostazioni** .
2.  Fare clic su **sistema** > **sulle**.
3.  Fare clic su **Partecipa Azure Active Directory**.
4.  Eseguire l'autenticazione per l'organizzazione, fornire l'autenticazione a più fattori se richiesto e quindi seguire le istruzioni visualizzate.
5.  Eseguire la disconnessione e quindi accedere con l'account di lavoro (account Azure Active Directory).
6.  Provare ad accedere all'applicazione.

Per aggiungere l'account aziendale o dell'istituto di istruzione, eseguire la procedura seguente:

**Aggiornamento di Windows 10 anniversario**

1.  Aprire l'app di **Impostazioni** .
2.  Fare clic su **account** > **accesso aziendale o dell'istituto di istruzione**.
3.  Fare clic su **Connetti**.
4.  Eseguire l'autenticazione per l'organizzazione, fornire l'autenticazione a più fattori se richiesto e quindi seguire le istruzioni visualizzate.
5.  Provare ad accedere all'applicazione.


**Aggiornamento di Windows 10 novembre 2015**

1.  Aprire l'app di **Impostazioni** .
2.  Fare clic su **account** > **gli account**.
3.  Fare clic su **aggiungere aziendale o dell'istituto di istruzione account**.
4.  Eseguire l'autenticazione per l'organizzazione, fornire l'autenticazione a più fattori se richiesto e quindi seguire le istruzioni visualizzate.
5.  Provare ad accedere all'applicazione.

Se il dispositivo non è dominio ed esegue Windows 8.1, per eseguire un Join luogo di lavoro e la registrazione in Microsoft Intune, eseguire la procedura seguente:

1.  Aprire **Impostazioni PC**.
2.  Fare clic su **rete** > **luogo di lavoro**.
3.  Fare clic su **Partecipa**.
4.  Eseguire l'autenticazione per l'organizzazione, fornire l'autenticazione a più fattori se richiesto e quindi seguire le istruzioni visualizzate.
5.  Fare clic **su Attiva**.
6.  Provare ad accedere all'applicazione.


### <a name="browser-is-not-supported"></a>Browser non è supportato

È possibile accedere se si sta tentando di accedere a un'applicazione o servizio utilizzando uno dei browser seguenti:

- Chrome, Firefox o qualsiasi altro browser diverso da Microsoft Edge o Microsoft Internet Explorer in Windows 10 o Windows Server 2016
- Firefox in Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2

Verrà visualizzata una pagina di errore che è simile alla seguente:

![Messaggio "Non è possibile ottenere sono da questa posizione" per i browser non supportato] (./media/active-directory-conditional-access-device-remediation/02.png "Scenario:")

La riparazione solo consiste nell'usare un browser che supporti l'applicazione per la piattaforma del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

[Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)
