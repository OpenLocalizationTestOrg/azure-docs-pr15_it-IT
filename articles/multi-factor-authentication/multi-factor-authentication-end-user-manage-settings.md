<properties
    pageTitle="Gestire le impostazioni di verifica in due passaggi | Microsoft Azure"
    description="Gestire come si usa l'autenticazione a più fattori Azure ad esempio per modificare le informazioni di contatto o la configurazione di dispositivi."
    services="multi-factor-authentication"
    keywords = "client di autenticazione a più fattori, il problema di autenticazione, l'ID di correlazione"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="manage-your-settings-for-two-step-verification"></a>Gestire le impostazioni per la verifica in due passaggi

In questo articolo fornisce le risposte alle domande su come aggiornare le impostazioni per l'autenticazione a più fattori o verifica in due passaggi. Se si sono verificati problemi di accesso al proprio account, [in caso di problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md) per vedere Risoluzione dei problemi.


## <a name="where-to-find-the-settings-page"></a>Dove trovare la pagina Impostazioni
A seconda di come la propria azienda configura l'autenticazione a più fattori di Azure, esistono alcuni punti in cui è possibile modificare le impostazioni come il numero di telefono.

Se l'amministratore IT inviato un URL specifico o procedura per gestire la verifica in due passaggi, seguire queste istruzioni. In caso contrario, le istruzioni seguenti funzionerà per tutti gli altri partecipanti. Se si seguire questa procedura ma non le stesse opzioni, ciò significa che il lavoro o dell'istituto di istruzione personalizzato le proprie portal. Chiedere all'amministratore per il collegamento al portale di autenticazione a più fattori Azure.


1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Nella parte superiore, selezionare **profilo**.  
3. Selezionare **verifica rafforzare la sicurezza**.  

    ![App personali](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. La pagina di verifica rafforzare la sicurezza carica con le impostazioni.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Si desidera modificare il numero di telefono o aggiungere un numero secondario

È importante configurare un numero di telefono di autenticazione secondaria.  Poiché il numero di telefono principale e le app per dispositivi mobili sono probabilmente in corso la telefonata stesso, il numero di telefono secondario è l'unico modo sarà possibile tornare indietro nel proprio account se il telefono perdito o furto.

> [AZURE.NOTE]
> Se si non hanno accesso al proprio numero di telefono principale e serve aiuto per ottenere l'account, vedere la Guida in [ci sono problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md).

**Per modificare il numero di telefono principale:**  

1. Nella pagina verifica rafforzare la sicurezza, selezionare la casella di testo con il numero di telefono corrente e modificarlo con il nuovo numero di telefono.  
2. Selezionare **Salva**.  
3. Se questo è il numero utilizzato per l'opzione foglia, è necessario verificare il nuovo numero prima di poter salvare.  


**Per aggiungere un numero di telefono secondario:**  

1. Nella pagina verifica rafforzare la sicurezza, selezionare la casella accanto a **telefono autenticazione alternativo.**  
2. Immettere il numero di telefono secondario nella casella di testo.  
3. Alla fine, selezionare **Salva** e le modifiche.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Come pulire Microsoft Authenticator dal dispositivo precedente e passare a un'altra
Quando si disinstalla l'app dal dispositivo o reimpostare il dispositivo, non rimuove l'attivazione back-end. È necessario utilizzare la procedura descritta nel [passaggio a un nuovo dispositivo](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Passaggi successivi
- Ottenere suggerimenti risoluzione dei problemi e Guida [in caso di problemi con la verifica in due passaggi](multi-factor-authentication-end-user-troubleshoot.md)
- Configurare la [password dell'app](multi-factor-authentication-end-user-app-passwords.md) per le applicazioni che non supportano la verifica in due passaggi.
