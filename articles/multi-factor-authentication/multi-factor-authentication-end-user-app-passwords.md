<properties
    pageTitle="Quali sono le password App in Azure MFA?"
    description="Questa pagina consente agli utenti di capire quali sono le password app e quali vengono utilizzati per con rispetto a Azure MFA."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Quali sono le password App in Azure autenticazione a più fattori?

Alcune App diverso dal browser, ad esempio il client di posta elettronica nativo Apple che utilizza Exchange Active Sync, non supportano l'autenticazione a più fattori. Autenticazione a più fattori è abilitata per ogni utente. Ciò significa che se un utente è stato abilitato per l'autenticazione a più fattori e si sta tentando di usare App diverso dal browser, non sarà in grado di farlo. Una password per l'app consente che venga eseguita.

>[AZURE.NOTE] Autenticazione moderno per i client di Office 2013
>
> I client di Office 2013 (inclusi Outlook) ora supportano nuovi protocolli di autenticazione e possono essere attivati per supportare l'autenticazione a più fattori.  Questo significa che dopo l'abilitazione, app le password non sono necessari per l'uso con i clienti di Office 2013.  Per ulteriori informazioni vedere [pubblico anteprima annunciato autenticazione moderno Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>Come usare le password app

Di seguito sono riportati alcuni aspetti da ricordare su come usare le password di app.

- La password effettiva viene generata automaticamente e non viene fornita dall'utente. Questo perché la password generata automaticamente, non è più difficile per un indovinare e sicura.
- Al momento non esiste un limite di 40 password per ogni utente. Se si tenta di creare uno dopo che è stato raggiunto il limite, verrà richiesto di eliminare una password app esistente per crearne uno nuovo.
- È consigliabile creare password dell'app per dispositivi e non per applicazione. Ad esempio, è possibile creare una password per l'app per il laptop e usare la password di app per tutte le applicazioni in tale portatile.
- È la prima volta l'accesso in una password per l'app.  Se è necessario aggiuntive, è possibile creare loro.

![Programma di installazione](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Dopo avere inserito una password per l'app, questo utilizzare invece la password originale con queste App diverso dal browser.  Quindi, ad esempio, se si utilizza l'autenticazione a più fattori e il client di posta elettronica nativo Apple sul telefono.  Usare la password per l'app in modo che possa ignorare l'autenticazione a più fattori e continuare a lavorare.

## <a name="creating-and-deleting-app-passwords"></a>Creazione ed eliminazione di password dell'app
Durante l'accesso iniziale viene fornita una password per l'app che è possibile usare.  Inoltre è anche possibile creare ed eliminare le password app in un secondo momento.  Come si esegue questa operazione dipende da come si usa l'autenticazione a più fattori.  Scegliere quello che la maggior parte applicabili.

Come si usa authentiation a più fattori|Descrizione
:------------- | :------------- |
[Si usa con Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Ciò significa che sarà opportuno creare password dell'app tramite il portale di Office 365.
[Non lo so](#creating-and-deleting-app-passwords-with-myapps-portal)|Significa che si desidera creare password app tramite [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Utilizzo di Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Significa che si desidera creare password app tramite il portale di Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Creazione ed eliminazione di password dell'app con Office 365

Se si usa l'autenticazione a più fattori con Office 365 è possibile creare ed eliminare le password app tramite il portale di Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Per creare password dell'app nel portale di Office 365
--------------------------------------------------------------------------------

1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell'angolo superiore destro selezionare il widget e scegliere impostazioni di Office 365.
3. Fare clic su verifica rafforzare la sicurezza.
4. A destra, fare clic sul collegamento che indica che **aggiornare i numeri di telefono utilizzati per la sicurezza di account.** 
 ![Il programma di installazione](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata la pagina che consente di modificare le impostazioni.
![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica rafforzare la sicurezza, fare clic su **password dell'app.**
7. Fare clic su **Crea**.
![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Immettere un nome per la password di app e fare clic su **Avanti**.
![Creare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copiare la password di app negli Appunti e incollarlo in un'applicazione.
![Creare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Per eliminare le password app tramite il portale di Office 365
--------------------------------------------------------------------------------


1. Accedere al [portale di Office 365](https://login.microsoftonline.com/).
2. Nell'angolo superiore destro selezionare il widget e scegliere impostazioni di Office 365.
3. Fare clic su verifica rafforzare la sicurezza.
4. A destra, fare clic sul collegamento che indica che **aggiornare i numeri di telefono utilizzati per la sicurezza di account.** 
 ![Il programma di installazione](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Verrà visualizzata la pagina che consente di modificare le impostazioni.
![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Nella parte superiore, accanto a verifica rafforzare la sicurezza, fare clic su **password dell'app.**
7. Accanto a password app che si desidera eliminare, fare clic su **Elimina**.
![Eliminare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confermare l'eliminazione, fare clic su **Sì**.
![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Dopo la password per l'app viene eliminata è possibile fare clic su **Chiudi**.
![Chiudere](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Creazione ed eliminazione password dell'app il portale di App personali.
Se non si è certi di come si usa l'autenticazione a più fattori, quindi si può sempre creare ed eliminare le password app tramite il portale di App personali.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Per creare una password per l'app portale App personali

1. Accessohttp a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore, selezionare profilo.
3. Selezionare verifica rafforzare la sicurezza.
![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata la pagina che consente di modificare le impostazioni.
![Programma di installazione](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Nella parte superiore, accanto a verifica rafforzare la sicurezza, fare clic su **password dell'app.**
6. Fare clic su **Crea**.
![Creare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Immettere un nome per la password di app e fare clic su **Avanti**.
![Creare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copiare la password di app negli Appunti e incollarlo in un'applicazione.
![Creare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Per eliminare una password per l'app portale App personali

1. Accessohttp a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Nella parte superiore, selezionare profilo.
3. Selezionare verifica rafforzare la sicurezza.
![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Verrà visualizzata la pagina che consente di modificare le impostazioni.
![Programma di installazione](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Nella parte superiore, accanto a verifica rafforzare la sicurezza, fare clic su **password dell'app.**
6. Accanto a password app che si desidera eliminare, fare clic su **Elimina**.
![Eliminare una password per l'app](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confermare l'eliminazione, fare clic su **Sì**.
![Confermare l'eliminazione](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Dopo la password per l'app viene eliminata è possibile fare clic su **Chiudi**.
![Chiudere](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Creare password dell'app nel portale di Azure

Se si usa l'autenticazione a più fattori con Azure è possibile creare password app tramite il portale di Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Per creare password dell'app nel portale di Azure

1. Effettuare l'accesso al portale di gestione di Azure.
2. Nella parte superiore, pulsante destro del mouse sul proprio nome utente e selezionare un'ulteriore verifica di sicurezza.
3. Nella pagina proofup nella parte superiore, fare clic su app password
4. Fare clic su **Crea**.
5. Immettere un nome per la password di app e fare clic su **Avanti**
6. Copiare la password di app negli Appunti e incollarlo in un'applicazione.
![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Per eliminare le password app nel portale di Azure

1. Effettuare l'accesso al portale di gestione di Azure.
2. Nella parte superiore, pulsante destro del mouse sul proprio nome utente e selezionare un'ulteriore verifica di sicurezza.
3. Nella parte superiore, accanto a verifica rafforzare la sicurezza, fare clic su **password dell'app.**
4. Accanto a password app che si desidera eliminare, fare clic su **Elimina**.
5. Confermare l'eliminazione, fare clic su **Sì**.
6. Dopo la password per l'app viene eliminata è possibile fare clic su **Chiudi**.
![Chiudere](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
