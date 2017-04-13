## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Utilizzo dell'archivio credenziali per l'autenticazione con il servizio di Backup di Azure

Il server locale (Windows client o server Windows Server o Data Protection Manager) deve essere autenticati con un archivio di backup prima eseguire il backup dei dati di Azure. L'autenticazione viene eseguita mediante "archivio credenziali". Il concetto di credenziali archivio è simile a quello di un file "pubblica impostazioni" viene usato in Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>Che cos'è il file di archivio credenziali?

Il file di archivio credenziali è un certificato generato dal portale per ogni archivio di backup. Il portale quindi carica la chiave pubblica in Access Control Service (ACS). La chiave privata del certificato è disponibile per l'utente come parte del flusso di lavoro fornito come input per il flusso di lavoro di registrazione di computer. Consente di autenticare il computer per inviare i dati di backup in un archivio identificato nel servizio di Azure Backup.

Le credenziali di archivio viene utilizzata solo durante il flusso di lavoro di registrazione. È responsabilità dell'utente per garantire che il file di archivio credenziali non è stata compromessa. Se si trova nelle mani di qualsiasi utente non autorizzato, il file di archivio credenziali può essere utilizzato per registrare altri computer contro lo stesso archivio. Tuttavia, come i dati di backup sono crittografati tramite una frase che appartiene al cliente, non è possibile compromessa dati di backup esistenti. Per ovviare a questo problema, archivio credenziali sono impostate su una scadenza 48hrs. È possibile scaricare le credenziali di archivio di un archivio di backup qualsiasi numero di volte, ma solo il più recente archivio credenziali file applicabile durante il flusso di lavoro di registrazione.

### <a name="download-the-vault-credential-file"></a>Scaricare il file di archivio credenziali

Il file di archivio credenziali viene scaricato tramite un canale sicuro dal portale di Azure. Il servizio di Backup di Azure non riconosce la chiave privata del certificato e la chiave privata non viene mantenuta nel portale o il servizio. Utilizzare la procedura seguente per scaricare il file di archivio credenziali in un computer locale.

1.  Accedere al [portale di gestione](https://manage.windowsazure.com/)
2.  Fare clic su **Servizi di recupero** nel riquadro di spostamento a sinistra e selezionare l'archivio di backup è stato creato. Fare clic sull'icona cloud per passare alla visualizzazione scheda Quick Start dell'archivio di backup.

    ![Visualizzazione rapida](./media/backup-download-credentials/quickview.png)

3.  Nella pagina Guida introduttiva, fare clic su **Scarica archivio credenziali**. Il portale genera il file archivio credenziali, che è disponibile per il download.

    ![Download](./media/backup-download-credentials/downloadvc.png)

4.  Il portale genererà credenziali archivio utilizzando una combinazione di nome dell'archivio e la data corrente. Fare clic su **Salva** per scaricare le credenziali di archivio per cartella dei download dell'account locale oppure scegliere Salva con nome dal menu Salva per specificare un percorso per le credenziali di archivio.

### <a name="note"></a>Nota
- Assicurarsi che le credenziali di archivio viene salvato in un percorso accessibile dal proprio computer. Se è archiviato in un file condivisione/piccole e medie imprese, controllare le autorizzazioni di accesso.
- Il file di archivio credenziali viene utilizzato solo durante il flusso di lavoro di registrazione.
- Il file di archivio credenziali scadenza 48hrs e può essere scaricato dal portale.
- Vedere la sezione Backup Azure [domande frequenti](../articles/backup/backup-azure-backup-faq.md) per eventuali domande sul flusso di lavoro.
