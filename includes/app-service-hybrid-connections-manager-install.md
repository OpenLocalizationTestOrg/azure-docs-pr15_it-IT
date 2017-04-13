
1. In e il **connessioni ibrido** , fare clic sulla connessione ibrido che appena creato, quindi fare clic su **Imposta comunicare ascoltatore**.
    
    ![Fare clic su Imposta comunicare ascoltatore](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4. Verrà visualizzata e il **Proprietà connessione ibrida** . In programma di **Gestione connessione ibrido locale**, scegliere **scaricare e configurare manualmente**, salvare il pacchetto HybridConnectionManager.msi scaricato e copiare la stringa di connessione di gateway.
    
    ![Fare clic qui per installare](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. Da un prompt dei comandi di amministratore, digitare il seguente comando per avviare il programma di installazione:

        start HybridConnectionManager.msi
 
7. Quando si esegue il programma di installazione, fare clic su **non ora**, quindi passare alla cartella %ProgramFiles%\Microsoft\HybridConnectionManager, eseguire HCMConfigWizard.exe e fare clic su **Sì** nella finestra di dialogo **Controllo dell'Account utente** .
        
7. Incollare la stringa di connessione ibrido copiato in precedenza e fare clic su **OK**. 
    
    ![L'installazione](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. Dopo avere completato l'installazione, fare clic su **Chiudi**.
    
    ![Fare clic su Chiudi](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    In e il **connessioni ibrido** nella colonna **stato** verrà visualizzato **connesso**. 
    
    ![Allo stato di connessione](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)