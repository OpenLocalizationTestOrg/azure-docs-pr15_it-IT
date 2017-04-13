<properties
    pageTitle="Estendere locale sempre sui gruppi di disponibilità in Azure | Microsoft Azure"
    description="In questa esercitazione utilizza risorse create con il modello di distribuzione classica e viene descritto come utilizzare la procedura guidata Aggiungi Replica in SQL Server Management Studio (SQL Server Management Studio) per aggiungere una replica di sempre nel gruppo di disponibilità in Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Estendere locale sempre sui gruppi di disponibilità in Azure

Sempre in gruppi disponibilità altamente disponibile per i gruppi di database mediante l'aggiunta di repliche secondarie. Consentono a queste repliche non funziona su database in caso di errore. Inoltre può essere utilizzati per trasferire lette carichi di lavoro o le attività di backup.

È possibile estendere i gruppi di disponibilità locale a Microsoft Azure, il provisioning di uno o più macchine virtuali di Azure con SQL Server e quindi aggiungerli come repliche ai gruppi di disponibilità locale.

In questa esercitazione si suppone che le operazioni seguenti:

- Un abbonamento attivo a Azure. È possibile [iscriversi per una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Un sempre nella disponibilità gruppo locale. Per ulteriori informazioni sui gruppi di disponibilità, vedere [Sempre in gruppi disponibilità](https://msdn.microsoft.com/library/hh510230.aspx).

- Connessione tra la rete locale e la rete virtuale Azure. Per ulteriori informazioni sulla creazione di questa rete virtuale, vedere [configurare una connessione VPN da sito nel portale di classica Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Installazione guidata Replica Azure

In questa sezione viene illustrato come utilizzare la **Replica guidata Azure** per estendere la soluzione sempre nel gruppo disponibilità per includere repliche Azure.

1. Dall'interno di SQL Server Management Studio espandere **Sempre sulla disponibilità** > **Disponibilità gruppi** > **[nome del gruppo disponibilità]**.

1. Pulsante destro del mouse **Repliche disponibilità**, quindi fare clic su **Aggiungi Replica**.

1. Per impostazione predefinita, la **Replica di aggiungere la creazione guidata gruppo disponibilità** . Fare clic su **Avanti**.  Se l'opzione di **non visualizzare questa pagina** nella parte inferiore della pagina durante un avvio precedenti della procedura guidata, questa schermata non essere disponibile.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Sarà necessario connettersi a ogni replica secondario esistente. È possibile fare clic su **Connetti...** accanto a ciascun duplicata o è possibile fare clic su **Connetti tutti** nella parte inferiore dello schermo. Dopo l'autenticazione, fare clic su **Avanti** per passare alla schermata successiva.

1. Nella pagina **Specificare repliche** più schede sono elencate nella parte superiore: **repliche**, **i punti finali**, **Le preferenze di Backup**e **comunicare ascoltatore**. Scheda **repliche** fare clic su **Aggiungi Replica di Azure** Per avviare la creazione guidata Replica Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Se è stato installato uno prima di, selezionare un certificato di gestione di Azure esistente dall'archivio certificati di Windows locale. Selezionare o immettere l'id di un abbonamento Azure, se è stata utilizzata una prima. È possibile fare clic su Download per scaricare e installare un certificato di gestione di Azure e scaricare l'elenco delle sottoscrizioni usando un account Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Inserire ogni campo della pagina con i valori che verranno usate per creare il Azure virtuale macchina che ospita la replica.

  	|Impostazione|Descrizione|
|---|---|
|**Immagine**|Selezionare la combinazione desiderata del sistema operativo e SQL Server|
|**Dimensione memoria virtuale**|Selezionare le dimensioni della macchina virtuale che meglio si adatta alle esigenze aziendali|
|**Nome macchina virtuale**|Specificare un nome univoco per la nuova macchina virtuale. Il nome deve contenere tra 3 e 15 caratteri, può contenere solo lettere, numeri e trattini e devono iniziare con una lettera e terminare con una lettera o numero.|
|**Nome utente macchine Virtuali**|Specificare un nome utente che diventerà l'account dell'amministratore nella macchina virtuale|
|**Password di amministratore di macchine Virtuali**|Specificare una password per il nuovo account|
|**Confermare la Password**|Confermare la password del nuovo account|
|**Rete virtuale**|Specificare la rete virtuale Azure che deve utilizzare la nuova macchina virtuale. Per ulteriori informazioni sulle reti virtuali, vedere [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md).|
|**Subnet virtuali**|Specificare la subnet virtuali che devono essere utilizzate nuova macchina virtuale|
|**Dominio**|Verificare che il valore predefinito per il dominio sia corretto|
|**Nome utente**|Specificare un account che si trova nel gruppo administrators locale nei nodi del cluster locale|
|**Password**|Specificare la password per il nome utente di dominio|

1. Fare clic su **OK** per convalidare le impostazioni di distribuzione.

1. Note legali verranno visualizzate di seguito. Leggere e fare clic su **OK** se l'utente acconsente a queste condizioni.

1. Verrà visualizzata la pagina di **Specificare repliche** nuovamente. Verificare le impostazioni per la nuova replica Azure nelle schede **repliche**, **i punti finali**e **Le preferenze di Backup** . Modificare le impostazioni per soddisfare le esigenze aziendali.  Per ulteriori informazioni sui parametri contenuti in queste schede, vedere [Specificare repliche pagina (disponibilità guidata/Aggiungi Replica Creazione guidata nuovo gruppo)](https://msdn.microsoft.com/library/hh213088.aspx). Si noti che listener non è possibile creare mediante la scheda comunicare ascoltatore per i gruppi di disponibilità che contengono repliche Azure. Inoltre, se è già stato creato un comunicare ascoltatore prima di avviare la creazione guidata, si riceverà un messaggio che indica che non è supportato in Azure. Esamineremo come creare listener nella sezione **creare un gruppo di disponibilità comunicare ascoltatore** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Fare clic su **Avanti**.

1. Selezionare il metodo di sincronizzazione di dati che si desidera usare nella pagina **Selezionare la sincronizzazione dei dati iniziale** e fare clic su **Avanti**. Per la maggior parte dei casi, selezionare **La sincronizzazione dei dati completo**. Per ulteriori informazioni sui metodi di sincronizzazione di dati, vedere [Selezionare dati sincronizzazione pagina iniziale (sempre nella disponibilità gruppo procedure guidate)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Esaminare i risultati nella pagina **convalida** . Risolvere i problemi in sospeso ed eseguire nuovamente la convalida se necessario. Fare clic su **Avanti**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Rivedere le impostazioni nella pagina **Riepilogo** , quindi fare clic su **Fine**.

1. Avvia il processo di provisioning. Quando viene eseguito correttamente al termine della procedura guidata, fare clic su **Chiudi** per chiudere la procedura guidata.

>[AZURE.NOTE] La creazione guidata Replica Azure viene creato un file di log in Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Il file di log può essere utilizzato per risolvere distribuzioni replica Azure non riuscita. Se la procedura guidata non eseguire alcuna azione, tutte le operazioni precedenti vengono riportate indietro, inclusa l'eliminazione di macchine Virtuali di provisioning.

## <a name="create-an-availability-group-listener"></a>Creare un comunicare ascoltatore gruppo di disponibilità

Dopo aver creato il gruppo di disponibilità, è necessario creare un comunicare ascoltatore per i client per la connessione alle repliche. Listener indirizzano le connessioni in ingresso primario o una replica secondaria di sola lettura. Per ulteriori informazioni sulla listener, vedere [configurare un comunicare ascoltatore ILB sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Passaggi successivi

Oltre a utilizzare la **Creazione guidata Replica di Azure aggiungere** per estendere il sempre nel gruppo di disponibilità in Azure, è possibile anche spostare alcuni carichi di lavoro di SQL Server completamente in Azure. Per iniziare, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
