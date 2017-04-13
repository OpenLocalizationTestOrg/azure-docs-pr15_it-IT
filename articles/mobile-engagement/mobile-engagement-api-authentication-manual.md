<properties 
    pageTitle="Eseguire l'autenticazione con le API REST coinvolgimento Mobile - configurazione manuale"
    description="Descrive come configurare manualmente l'autenticazione per API REST coinvolgimento Mobile" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Eseguire l'autenticazione con le API REST coinvolgimento Mobile - configurazione manuale

Si tratta di una documentazione appendice in [autentica con API REST coinvolgimento Mobile](mobile-engagement-api-authentication.md). Assicurarsi di leggerlo prima di tutto per ottenere il contesto. Descrive un modo alternativo per eseguire la configurazione monouso per configurare l'autenticazione per le API REST di impegno Mobile tramite il portale di Azure. 

>[AZURE.NOTE] Le istruzioni riportate di seguito sono in base a questa [Guida di Active Directory](../resource-group-create-service-principal-portal.md) e personalizzate che cos'è necessario per l'autenticazione per dispositivi mobili coinvolgimento API. Fare riferimento a tale se si desidera conoscere la procedura seguente in modo dettagliato. 

1. Accedere al proprio Account Azure tramite il [portale classica](https://manage.windowsazure.com/).

2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![Selezionare Active Directory][1]

3. Scegliere **Predefinita Active Directory** nel portale di Azure. 

     ![Scegliere directory][2]

    >[AZURE.IMPORTANT] Questo approccio funziona solo quando si lavora in Active Directory del proprio account predefinito e non funziona se si sta eseguendo in Active Directory creato nel proprio account. 

4. Per visualizzare le applicazioni nella directory, fare clic su **applicazioni**.

     ![applicazioni di visualizzazione][3]

5. Fare clic su **Aggiungi**. 

     ![aggiunta dell'applicazione][4]

6. Fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**

     ![nuova applicazione][5]

6. Immettere nome dell'applicazione e selezionare il tipo di applicazione come **API WEB e/o di applicazione WEB** e fare clic sul pulsante Avanti.

     ![applicazione di nome][6]

7. È possibile specificare tutti gli URL fittizi per **URL di accesso via** e **APP ID URI**. Non vengono utilizzate per questo scenario e gli URL stessi non vengono convalidati.  

     ![proprietà delle applicazioni][7]

8. Al termine di questa è un'app AAD con il nome specificato in precedenza simile al seguente. Si tratta del **Active Directory\_APP\_nome** e prendere nota del.  

     ![nome dell'App][8]

9. Fare clic sul nome dell'app e fare clic su **Configura**.

     ![configurare app][9]

10. Prendere nota dell'ID CLIENT che verrà utilizzato come **CLIENT\_ID** per l'API delle chiamate. 

     ![configurare app][10]

11. Scorrere fino alla sezione **chiavi** e aggiungere una chiave con preferenza durata 2 anni (scadenza) e fare clic su **Salva**. 

     ![configurare app][11]


12. Copiare immediatamente il valore che viene visualizzato per la chiave, che viene visualizzata solo ora e non è archiviato in modo che non verranno mai nuovamente visualizzati. Se si perde sarà necessario generare una nuova chiave. Questo sarà **CLIENT_SECRET** per le chiamate API. 

     ![configurare app][12]

    >[AZURE.IMPORTANT] Questo tasto scadrà alla fine della durata specificata, in modo assicurarsi di rinnovarlo al momento opportuno in caso contrario l'autenticazione di API non funzionerà più. È inoltre possibile eliminare e ricreare questa chiave se si ritiene che sia stata compromessa.
 
13. Fare clic sul pulsante di **Visualizzazione endpoint** ora che verrà visualizzata la finestra di dialogo **App endpoint** . 

    ![][13]

14. Nella finestra di dialogo App endpoint copiare **OAUTH 2.0 TOKEN ENDPOINT**. 

    ![][14]

15. Questo endpoint sarà il seguente formato in cui il GUID nell'URL è il **TENANT_ID** annotare in modo che ne: 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. A questo punto si procederà alla configurare le autorizzazioni dell'app. Per questa operazione è necessario aprire il [portale di Azure](https://portal.azure.com). 

17. Fare clic su **Gruppi di risorse** e individuare il gruppo di risorse **Coinvolgimento Mobile** .  

    ![][15]

18. Fare clic sul gruppo di risorse **Coinvolgimento Mobile** e passare a e **l'Impostazioni** qui. 

    ![][16]

19. Fare clic su **utenti** e l'impostazioni e quindi fare clic su **Aggiungi** per aggiungere un utente. 

    ![][17]

20. Fare clic su **Selezionare un ruolo**

    ![][18]

21. Fare clic su **proprietario**

    ![][19]

22. Ricerca del nome dell'applicazione **Active Directory\_APP\_nome** nella casella di ricerca. Verrà non visualizzato per impostazione predefinita di seguito. Se si trova il, selezionarla e fare clic su **Selezionare** nella parte inferiore della stessa e. 

    ![][20]

23. In e **l'Aggiungere Access** , verrà visualizzato come **gruppi di 0, 1 l'utente**. In questo blade di confermare la modifica, fare clic su **OK** . 

    ![][21]

È stata completata la configurazione di AAD necessaria e sono tutte impostate su chiamare le API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



