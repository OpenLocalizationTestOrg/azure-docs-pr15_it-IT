<properties
    pageTitle="Risoluzione dei problemi l'estensione del Pannello di accesso per Internet Explorer | Microsoft Azure"
    description="Modalità di utilizzo dei criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale di App personali."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Risoluzione dei problemi l'estensione del Pannello di accesso per Internet Explorer

In questo articolo viene spiegato come risolvere i problemi seguenti:

- Non è possibile accedere alle App tramite il portale di App personali durante l'uso di Internet Explorer.
- Viene visualizzato il messaggio "Installa Software" anche se è già stato installato il software.

Se è un amministratore, vedere anche: [come distribuire l'estensione del Pannello di accesso per Internet Explorer tramite criteri di gruppo](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Eseguire lo strumento di diagnostica

Consente di individuare problemi di installazione con l'estensione del pannello Access scaricando ed eseguendo lo strumento di diagnostica pannello accesso:

1. [Fare clic qui per scaricare lo strumento di diagnostica.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Aprire il file e premere tasto **estrarre tutti** .

    ![Premere estrarre tutti](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Quindi fare clic sul pulsante **Estrai** per continuare.

    ![Premere Estrai](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Per eseguire lo strumento, fare clic sul file denominato **AccessPanelExtensionDiagnosticTool**, quindi selezionare **Apri con > Microsoft Windows in base a Script Host**.

    ![Apri con > in base a Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Quindi verrà visualizzata la finestra di diagnostica seguente, che descrive cosa potrebbe contenere informazioni errate con l'installazione.

    ![Un esempio della finestra di diagnostica](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Fare clic su "**Sì**" per consentire il programma di risolvere i problemi che sono stati trovati.

7. Per salvare le modifiche, chiudere ogni finestra di Internet Explorer e quindi riaprire Internet Explorer.<br />Se non è possibile accedere le app, provare la procedura seguente.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Verificare che sia abilitata l'estensione del Pannello di Access

Per verificare che l'estensione del Pannello di accesso sia attivato in Internet Explorer:

1. In Internet Explorer, fare clic sull' **icona dell'ingranaggio** nell'angolo superiore destro della finestra. Selezionare **Opzioni Internet**.<br />(Nelle versioni precedenti di Internet Explorer è possibile trovare in **Strumenti > Opzioni Internet**.

    ![Passare a strumenti > Opzioni Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Fare clic sulla scheda **programmi** , quindi fare clic sul pulsante **Gestisci componenti aggiuntivi** .

    ![Fare clic su Gestisci componenti aggiuntivi](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. In questa finestra di dialogo, selezionare **l'Estensione del Pannello di accesso** e quindi fare clic sul pulsante **Abilita** .

    ![Fare clic su Attiva](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Per salvare le modifiche, chiudere ogni finestra di Internet Explorer e quindi riaprire Internet Explorer.

##<a name="enable-extensions-for-inprivate-browsing"></a>Attivare le estensioni per InPrivate Browsing

Se si utilizza la modalità di InPrivate Browsing:

1. In Internet Explorer, fare clic sull' **icona dell'ingranaggio** nell'angolo superiore destro della finestra. Selezionare **Opzioni Internet**.<br />(Nelle versioni precedenti di Internet Explorer è possibile trovare in **Strumenti > Opzioni Internet**.

    ![Un esempio della finestra di diagnostica](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Fare clic sulla scheda **Privacy** , quindi **deselezionare** la casella di **disattivare le barre degli strumenti ed estensioni all'avvio di InPrivate Browsing**</p>

    ![Deselezionare le estensioni e disabilitare le barre degli strumenti Avvio InPrivate Browsing](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Per salvare le modifiche, chiudere ogni finestra di Internet Explorer e quindi riaprire Internet Explorer.

##<a name="uninstall-the-access-panel-extension"></a>Disinstallare l'estensione del Pannello di Access

Per disinstallare l'estensione del Pannello di accesso dal computer:

1. Sulla tastiera, premere il **tasto Windows** per aprire il menu Start. Quando viene aperto il menu, è possibile digitare alcuna operazione per eseguire una ricerca. Digitare "Pannello di controllo" e quindi aprire il **Pannello di controllo** quando viene visualizzata nei risultati della ricerca.

    ![Cercare il pannello di controllo](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. In alto a destra del Pannello di controllo, impostare l'opzione di **visualizzazione per** **icone grandi**. Quindi individuare e fare clic su **programmi e funzionalità** .

    ![Chang la visualizzazione per icone grandi](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Nell'elenco, selezionare **l'Estensione del Pannello di accesso**e fare clic sul pulsante **Disinstalla** .

    ![Fare clic su Disinstalla](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. È quindi possibile provare a installare l'estensione nuovamente per verificare se è stato risolto il problema.

Se si verificano problemi di disinstallazione dell'estensione, è possibile rimuovere utilizzando lo strumento [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Come distribuire l'estensione del Pannello di accesso per Internet Explorer tramite criteri di gruppo](active-directory-saas-ie-group-policy.md)
