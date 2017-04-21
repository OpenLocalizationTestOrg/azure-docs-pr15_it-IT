
   * Accedere all'account Azure immettendo le credenziali.

     Questo metodo è veloce e più semplice, ma se si usa questo metodo non sarà possibile vedere il Database di SQL Azure o servizi Mobile nella finestra **Esplora Server** .

     In **Esplora Server**, fare clic sul pulsante **Connetti a Azure** . In alternativa è possibile rapida nodo **Azure** e quindi fare clic su **Connetti a Azure** nel menu di scelta rapida.

   * Installare un certificato di gestione che consente l'accesso al proprio account.

     In **Esplora Server**destro nodo **Azure** e quindi fare clic su **Gestione delle sottoscrizioni** nel menu di scelta rapida. Nella finestra di dialogo **Gestione delle sottoscrizioni di Azure** , fare clic sulla scheda **certificati** e quindi fare clic su **Importa**. Seguire le istruzioni per scaricare e quindi come importare un file di sottoscrizione (denominato anche un file. *publishsettings* ) per il proprio account Azure.

     > [AZURE.NOTE] Scaricare il file di abbonamento a una cartella all'esterno di directory di codice sorgente (ad esempio, nella cartella download) e quindi eliminarla una volta completata l'importazione. Un utente non autorizzato chi può accedere ai file di sottoscrizione può modificare, creare ed eliminare il provider di servizi Azure.

    Per ulteriori informazioni, vedere [connessione a Azure da Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
