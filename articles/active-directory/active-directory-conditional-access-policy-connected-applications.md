<properties
    pageTitle="Impostare i criteri di accesso condizionale basato su dispositivo Applications connessi Azure Active Directory | Microsoft Azure"
    description="Impostare i criteri di accesso condizionale basato su dispositivo Applications Azure Active Directory connessi."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Impostare i criteri di accesso condizionale basato su dispositivo Applications connessi Azure Active Directory


Azure Active Directory (Azure Active Directory) basate su dispositivo accesso condizionato può aiutare a proteggere le risorse dell'organizzazione da:

- Tentativi di accesso da dispositivi sconosciuti o non gestiti.
- Dispositivi che non soddisfano i criteri di sicurezza dell'organizzazione.

Per una panoramica dell'accesso condizionato, vedere [accesso condizionato Azure Active Directory](active-directory-conditional-access.md).

È possibile impostare criteri di accesso condizionale basato su dispositivo per la protezione queste applicazioni:

- Office 365 SharePoint Online, per proteggere i siti e documenti dell'organizzazione
- Office 365 Exchange Online, per la protezione posta elettronica dell'organizzazione
- Software come le applicazioni di servizio che si è connessi alla Azure Active Directory per l'autenticazione
- Le applicazioni pubblicate tramite servizi Proxy di applicazione Azure Active Directory locale

Per impostare un criterio di accesso condizionale basato sul dispositivo, nel portale di Azure, passare all'applicazione specifica nella directory.


  ![Elenco di applicazioni nella directory portale Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Applicazioni")


Selezionare l'applicazione e quindi fare clic sulla scheda **Configura** per impostare il criterio di accesso condizionale.  


  ![Configurazione dell'applicazione] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Access regole basate sul dispositivo")




Per impostare un criterio di accesso condizionale basato sul dispositivo, nella sezione **accesso regole basate sul dispositivo** , **Abilitare le regole di accesso**, fare clic **su**.

Un criterio di accesso condizionale basate su dispositivo sono previsti tre componenti:

- **Applicare a**. L'ambito di utenti applicato il criterio.

- **Le regole di dispositivi**. Le condizioni di un dispositivo deve soddisfare prima di poter accedere l'applicazione.

- **Applicazione dell'applicazione**. Le applicazioni client (nativi e browser) viene applicato il criterio.

  ![I tre componenti di un criterio di accesso basato sul dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Accesso regole basate sul dispositivo")


## <a name="select-the-users-the-policy-applies-to"></a>Selezionare gli utenti per a che il criterio si applica

Nella sezione **Applica a** è possibile selezionare l'ambito di questo criterio si applica a utenti.

Quando si crea un ambito di criteri di accesso per gli utenti sono disponibili due opzioni:

- **Tutti gli utenti**. Applicare il criterio a tutti gli utenti che accedono all'applicazione.

- **Gruppi**. Limitare i criteri per gli utenti che fanno parte di un gruppo specifico.

![Applicare criteri a tutti gli utenti o a un gruppo] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Applicare a")


 Per escludere un utente da un criterio, selezionare la casella di controllo **eccetto** . Questa funzionalità è utile quando è necessario assegnare autorizzazioni a un utente specifico per accedere temporaneamente l'applicazione. Selezionare questa opzione, ad esempio, se alcuni utenti che dispongono di dispositivi non sono pronti per l'accesso condizionale. I dispositivi non sia registrati ancora o cui devono essere fuori conformità.


## <a name="select-the-conditions-that-devices-must-meet"></a>Selezionare le condizioni che devono soddisfare i dispositivi

Utilizzare le **Regole di dispositivo** per impostare le condizioni di un dispositivo deve soddisfare per accedere all'applicazione.

È possibile impostare access condizionale basate su dispositivo per questi tipi di dispositivo:

- Aggiornamento di un anniversario Windows 10, Windows 8.1 e Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
- dispositivi iOS (iPad, iPhone)
- Dispositivi Android

Supporto per Mac è disponibile a breve.

  ![Applicare criteri ai dispositivi] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Applicazioni")

 >[AZURE.NOTE] Per informazioni sulle differenze tra i dispositivi di dominio e Azure fanno parte di Active Directory, vedere [dispositivi con Windows 10 dell'area di lavoro](active-directory-azureadjoin-windows10-devices.md).

Sono disponibili due opzioni per le regole di dispositivo:

- **Tutti i dispositivi devono essere conformi**. Tutte le piattaforme di dispositivi che accedono all'applicazione devono essere conforme. Accesso negati dispositivi che eseguono piattaforme che non supportano l'accesso condizionale basato sul dispositivo.

- **Solo determinati dispositivi devono essere conformi**. Solo piattaforme di dispositivi specifici devono essere conforme. Altre piattaforme o altre piattaforme che possono accedere all'applicazione hanno accesso.

  ![Impostare l'ambito per le regole di dispositivi] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Applicazioni")

Azure fanno parte di Active Directory i dispositivi sono compatibili se sono contrassegnati come **conforme** nella directory da Intune o da un sistema di gestione di dispositivi di terze parti che si integra con Azure Active Directory.

Un dispositivo dominio è conforme se:

- È registrato con Azure Active Directory. Molte organizzazioni indicazione dominio dispositivi dispositivi attendibili.
- È contrassegnato come **compatibile** in Azure Active Directory da System Center Configuration Manager 2016.

 ![Dominio dispositivi che sono compatibili] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Regole di dispositivi")

Dispositivi personali Windows sono compatibili con se sono contrassegnati come **conforme** nella directory da Intune o da un sistema di gestione di dispositivi di terze parti che si integra con Azure Active Directory.

Dispositivi Windows non sono compatibili con se sono contrassegnati come **conforme** nella directory da Intune.

 >[AZURE.NOTE] Per ulteriori informazioni su come configurare Azure Active Directory per la conformità dispositivo nei sistemi di gestione diverso, vedere [accesso condizionato Azure Active Directory](active-directory-conditional-access.md).


È possibile selezionare una o più piattaforme di dispositivi per un criterio di accesso basato sul dispositivo. Sono inclusi Android, iOS, un dispositivo Windows Mobile (Windows 8.1 telefoni e Tablet) e Windows (tutti gli altri dispositivi Windows, inclusi tutti i dispositivi Windows 10).
La valutazione dei criteri si verifica solo per le piattaforme selezionate. Se un dispositivo che tentativi di accesso non è in esecuzione una delle piattaforme selezionate, il dispositivo di accedere all'applicazione se gli utenti abbiano accesso. Non viene valutato alcun criterio dispositivo.

![Selezionare piattaforme per le regole di dispositivi] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Regole di dispositivi")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Impostare la valutazione dei criteri per un tipo di applicazione

Nella sezione **Imposizione di applicazione** , impostare il tipo di applicazioni che il criterio verrà valutata per utente o un accesso per i dispositivi.

Sono disponibili due opzioni per il tipo di applicazione per includere:

- Browser e le applicazioni native
- Solo le applicazioni native

![Scegliere browser o applicazioni native] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Applicazioni")

Per applicare criteri di accesso per le applicazioni, selezionare **per applicazioni native e browser**. Quindi, è possibile includere:

- Browser (ad esempio Microsoft Edge in Windows 10) o Safari in iOS.
- Applicazioni che utilizzano la raccolta di autenticazione Active Directory (ADAL) su qualsiasi piattaforma o che utilizzano il WebAccountManager (WAM) API di Windows 10.

>[AZURE.NOTE] Per ulteriori informazioni sui browser supportati e altre considerazioni per un utente che accede a un dispositivo basato su, applicazione protetta Autorità certificato, vedere [accesso condizionato Azure Active Directory](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Proteggere l'accesso alla posta elettronica dalle applicazioni basate su Exchange ActiveSync

Nelle applicazioni di Office 365 Exchange Online, è possibile utilizzare Exchange ActiveSync per bloccare l'accesso alla posta elettronica alle applicazioni di posta basato su Exchange ActiveSync.

![Opzioni di conformità di Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Applicazioni")

![Richiede un dispositivo conforme per accedere alla posta elettronica] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Applicazioni")


## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)
