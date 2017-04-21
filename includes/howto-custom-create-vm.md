#<a name="how-to-create-a-custom-virtual-machine"></a>Come creare una macchina virtuale personalizzata

Una macchina virtuale *personalizzato* fa riferimento a una macchina virtuale creata tramite il metodo **Dalla raccolta** perché consente di configurare le altre opzioni del metodo di **Creazione rapida** . Queste opzioni includono:

- Altre opzioni per l'immagine da utilizzare per creare le macchine)
- Connessione macchina virtuale a una rete
- Aggiungere la macchina virtuale a un servizio cloud esistente
- Aggiunta di macchina virtuale a un set di disponibilità

> [AZURE.IMPORTANT] Se si vuole macchina virtuale per utilizzare una rete virtuale per la connessione a tale direttamente per nome host o configurare le connessioni tra locale, verificare che la rete virtuale è specificare quando si crea la macchina virtuale. Una macchina virtuale possono essere configurata per partecipare a una rete virtuale solo quando si crea la macchina virtuale. Per ulteriori informazioni sulle reti virtuali, vedere [Panoramica di rete virtuale Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Accedere al [portale di Azure](http://manage.windowsazure.com).

2. Barra dei comandi, fare clic su **Nuovo**.

3. Fare clic su **calcolare**, fare clic su **macchina virtuale**e quindi fare clic su **Dalla raccolta**.

4. Scegliere l'immagine da usare e quindi fare clic sulla freccia per continuare.

5. Se più versioni dell'immagine sono disponibili, in **Data di rilascio di versione**, selezionare la versione che si desidera utilizzare.

6. In **Nome macchina virtuale**, digitare il nome che si desidera utilizzare per la macchina virtuale.

7. Consente di selezionare le dimensioni appropriate per la macchina virtuale **livello** e le **dimensioni** . Dimensione selezionata viene applicata a configurazione massima di macchina virtuale, nonché i prezzi. Per informazioni sulla configurazione, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. In **Nome utente**digitare un nome per l'account amministrativo che si desidera utilizzare per gestire il server.

9. Nella casella **Nuova Password**, digitare una password complessa per l'account dell'amministratore. Nella casella **Conferma Password**digitare nuovamente la stessa password.

10. Fare clic sulla freccia per continuare.

11. Nel **Servizio Cloud**, eseguire una delle operazioni seguenti:

    - Se si tratta la macchina virtuale prima o sola nel servizio cloud, selezionare **Crea un nuovo servizio Cloud**. Quindi, in **Nome DNS del servizio Cloud**, digitare un nome che utilizza tra 3 24 maiuscole e minuscole e numeri. Questo nome diventa parte dell'URI utilizzato per contattare la macchina virtuale tramite il servizio cloud.
    - Se questa macchina virtuale viene aggiunto a un servizio cloud, selezionarlo nell'elenco.

    > [AZURE.NOTE] Per ulteriori informazioni sull'immissione macchine virtuali nel servizio cloud stesso, vedere [come connettere macchine virtuali in un servizio cloud](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Nell' **Area/affinità gruppo/virtuale rete**, selezionare l'area geografica, gruppo affinità o virtuali che si desidera utilizzare per la macchina virtuale. Per ulteriori informazioni sui gruppi di affinità, vedere [sui gruppi di affinità per rete virtuale](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

13. In **Account di archiviazione**, selezionare un account di archiviazione esistente per il file disco rigido virtuale oppure utilizzare un account di archiviazione generato automaticamente. Viene creato automaticamente un solo account di archiviazione per ogni area geografica. Tutte le altre macchine virtuali creata con questa impostazione si trovano in questo account di archiviazione. Sono limitati a 20 account di archiviazione.

14. Se si desidera che la macchina virtuale in cui inserire un set di disponibilità, **Impostare la disponibilità**, selezionare **set di disponibilità di creare** o aggiungere a un insieme di disponibilità esistente.

    **Nota**: macchine virtuali in un set di disponibilità sono distribuiti ai domini guasto diverso. Inserire più macchine virtuali in una disponibilità set garantisce che l'applicazione è disponibile durante errori di rete, gli errori hardware disco locale e qualsiasi tempo di inattività.

15.  In **endpoint**, esaminare i nuovi endpoint creato per consentire le connessioni a macchina virtuale, ad esempio tramite Desktop remoto o un client Secure Shell (SSH). È anche possibile aggiungere subito i punti finali o crearli in un secondo momento. Per istruzioni su come crearli in un secondo momento, vedere [come impostare la punti finali a una macchina virtuale](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

16.  In **Macchine Virtuali agente**, decidere se per installare l'agente di macchine Virtuali. L'agente fornisce l'ambiente per l'installazione estensioni che consentono di interagire con la macchina virtuale. Per informazioni dettagliate, vedere [Gestire le estensioni](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Fare clic sulla freccia per creare la macchina virtuale.

    ![Creazione di personalizzato macchina virtuale completata](./media/howto-custom-create-vm/VMSuccessWindows.png)

##<a name="next-steps"></a>Passaggi successivi##
Dopo aver creata la macchina virtuale, viene avviato automaticamente. Quando il portale Mostra lo stato come in esecuzione, è possibile accedere alla macchina virtuale. Per ulteriori informazioni, vedere uno degli articoli seguenti:

- [Come accedere a una macchina virtuale che eseguono Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Come accedere a una macchina virtuale che eseguono Windows Server](../articles/virtual-machines/virtual-machines-windows-classic-connect-logon.md)

