<properties
    pageTitle="Non è possibile eseguire l'accesso all'abbonamento Azure | Microsoft Azure"
    description="In questo articolo viene descritto come risolvere alcuni problemi di accesso abbonamento Azure comuni."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Non è possibile accedere a gestire l'abbonamento Azure

In questo articolo vengono illustrati alcuni metodi più comuni per risolvere i problemi di accesso.

## <a name="page-hangs-in-the-loading-status"></a>Pagina si blocca nello stato di caricamento

Se si blocca pagina browser internet, provare a ognuna delle seguenti operazioni fino a quando non è possibile accedere al [portale di Azure](https://portal.azure.com).

-   Aggiornare la pagina.
-   Utilizzare un browser diverso.
-   Se si usa Microsoft Internet Explorer, passare al portale di Azure utilizzando la modalità di InPrivate Browsing. 

    RISPOSTE.  Fare clic su **Strumenti** ![pulsante strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png) > **sicurezza** > **InPrivate Browsing**.

    B.  Passare al [portale di Azure](https://portal.azure.com)e quindi eseguire l'accesso al portale.

## <a name="error-message-no-subscriptions-found"></a>Messaggio di errore "Sottoscrizione non trovata"

Se l'account non dispone di autorizzazioni sufficienti, si può vedere un messaggio di errore **Nessuna sottoscrizione trovata** . Solo un amministratore dell'account possano accedere al [Centro Account](https://account.windowsazure.com/), non amministratori dei servizi (SA) o coamministratori (CA).

**Scenario 1: Messaggio di errore verrà ricevuto nel [portale di Azure](https://portal.azure.com)**

Per risolvere il problema, [aggiungere il ruolo di amministratore condivisa o proprietario](billing-add-change-azure-subscription-administrator.md) dell'account.

**Scenario 2: Messaggio di errore verrà ricevuto nel [Centro di Account Azure](https://account.windowsazure.com/Subscriptions)**

Verificare che l'account usato sia l'amministratore dell'account. Per verificare che l'amministratore dell'account, procedere come segue:

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Nel menu Hub selezionare **abbonamento**.
3.  Selezionare l'abbonamento a cui si desidera controllare e quindi selezionare **Impostazioni**.
4.  Scegliere **proprietà**. L'amministratore dell'account della sottoscrizione è visualizzato nella casella **Account di amministratore** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automaticamente è effettuato l'accesso come utente diverso

Questo errore può verificarsi se si usa più di un account utente in un browser Internet.

Per risolvere il problema, provare uno dei metodi seguenti:

-   Cancellare la cache ed eliminare i cookie di Internet. In Internet Explorer, fare clic su **Strumenti** ![pulsante strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opzioni Internet** > **eliminare**. Assicurarsi che siano selezionate le caselle di controllo per i file temporanei, cookie, la password e sfogliare la cronologia e quindi fare clic su Elimina.

-   Ripristinare le impostazioni di Internet Explorer per ripristinare le impostazioni personali che sono state apportate. Fare clic su **Strumenti** ![pulsante strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opzioni Internet** > **Avanzate** > selezionare la casella **Elimina impostazioni personali** > **Reimposta**.

-   Passare al portale di Azure in modalità di InPrivate Browsing. Fare clic su **Strumenti** ![pulsante strumenti](./media/billing-cannot-login-subscription/Toolsbutton.png) > **sicurezza** > **InPrivate Browsing**.

## <a name="need-help-contact-support"></a>Servono altre informazioni? Contattare il supporto tecnico. 

Se sono necessarie informazioni, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere il problema risolto rapidamente. 