<properties
    pageTitle="Azure AD Connect: Guida introduttiva con le impostazioni express | Microsoft Azure"
    description="Informazioni su come scaricare, installare ed eseguire l'installazione guidata di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Guida introduttiva di Azure AD Connect con le impostazioni express
Azure AD Connect **Impostazioni rapide** viene utilizzata quando si dispone di una singola foresta topologia e [la sincronizzazione delle password](../active-directory-aadconnectsync-implement-password-synchronization.md) per l'autenticazione. **Impostazioni rapide** è l'opzione predefinita e viene utilizzato per lo scenario più comunemente distribuito. Si sta solo pochi clic breve fuori sede per estendere la directory locale nel cloud.

Prima di iniziare l'installazione di Azure AD Connect, assicurarsi di [scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e completare i passaggi di prerequisito nel [Azure AD Connect: Hardware e i prerequisiti](../active-directory-aadconnect-prerequisites.md).

Se la topologia non corrisponde a impostazioni express, vedere [la documentazione correlata](#related-documentation) per altri scenari.

## <a name="express-installation-of-azure-ad-connect"></a>Installazione di Azure AD Connect rapida
È possibile vedere le seguenti operazioni nell'azione nella sezione [video](#videos) .

1. Accedere come amministratore al server che si desidera installare Azure AD Connect in locale. Eseguire questa operazione nel server che si desidera essere il server di sincronizzazione.
2. Individuare e fare doppio clic su **AzureADConnect.msi**.
3. Nella schermata iniziale, selezionare la casella delle condizioni di licenza e fare clic su **Continua**.  
4. Nella schermata Impostazioni Express, fare clic su **Usa impostazioni express**.  
![Connettere benvenuto in Azure Active Directory](./media/active-directory-aadconnect-get-started-express/express.png)
5. Nella schermata Connetti al Azure Active Directory, immettere il nome utente e la password di un amministratore globale per la tua promozione Azure. Fare clic su **Avanti**.  
![Connettersi a Active Directory Azure](./media/active-directory-aadconnect-get-started-express/connectaad.png) se si riceve un messaggio di errore e problemi di connettività, vedere [risolvere i problemi di connettività](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. Nella schermata Connetti al dominio Active Directory, immettere il nome utente e la password per un account di amministratore dell'organizzazione. È possibile immettere la parte del dominio nel formato FQDN o NetBios, vale a dire FABRIKAM\administrator o fabrikam.com\administrator. Fare clic su **Avanti**.  
![Connettersi a Active Directory](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. La pagina di [**Azure Active Directory accesso configurazione**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) Mostra solo se non è stato completato in [Prerequisiti](../active-directory-aadconnect-prerequisites.md) [verificare i domini](../active-directory-add-domain.md) .
![Domini non verificati](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Se viene visualizzata questa pagina, quindi esaminare ogni dominio contrassegnata **Non è stato aggiunto** e **Non verificato**. Assicurarsi che questi domini che si usa verificati in Azure Active Directory. Quando si sono verificati i domini, fare clic sul simbolo di aggiornamento.
8. Nella schermata Configura dello schermo fare clic su **Installa**.
    - Facoltativamente nella schermata per configurare la pagina, è possibile deselezionare la casella di controllo **Avvia il processo di sincronizzazione non appena viene completato la configurazione** . È consigliabile deselezionare questa casella di controllo se si desidera apportare modifiche alla configurazione, ad esempio [il filtro](../active-directory-aadconnectsync-configure-filtering.md). Se si deseleziona questa opzione, la procedura guidata Configura sincronizzazione ma lascia l'utilità di pianificazione disabilitato. Non viene eseguito fino a quando non è attivato manualmente, [ripetere la procedura guidata installazione](../active-directory-aadconnectsync-installation-wizard.md).
    - Se si dispone di Exchange in Active Directory locale, inoltre disponibile un'opzione per consentire [**la distribuzione ibrida di Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Abilitare questa opzione se si prevede di cassette postali di Exchange sia nel cloud e locale nello stesso momento.
![Pronto per la configurazione di Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Al termine dell'installazione, fare clic su **Esci**.
10. Al termine dell'installazione, approvare e accedere nuovamente prima di utilizzare Gestione servizio di sincronizzazione o Editor di regole di sincronizzazione.

## <a name="videos"></a>Video

Per un video su utilizzando l'installazione rapida, vedere:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato Azure AD Connect installato è possibile [verificare l'installazione e assegnare licenze](../active-directory-aadconnect-whats-next.md).

Ulteriori informazioni su queste funzionalità, hanno appurate con l'installazione: [aggiornamento automatico](../active-directory-aadconnect-feature-automatic-upgrade.md), [Impedisci accidentali eliminazioni](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)e [Azure Active Directory connettersi integrità](../active-directory-aadconnect-health-sync.md).

Altre informazioni su questi argomenti comuni: [utilità di pianificazione e la procedura di attivazione di sincronizzazione](../active-directory-aadconnectsync-feature-scheduler.md).

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentazione correlata

Argomento |  
--------- | ---------
Panoramica di Azure AD Connect | [Integrazione le identità locali con Azure Active Directory](../active-directory-aadconnect.md)
Installare con impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Eseguire l'aggiornamento da DirSync | [Eseguire l'aggiornamento da strumento di sincronizzazione di Azure Active Directory (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Account usato per l'installazione | [Ulteriori informazioni sulle autorizzazioni e account Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)
