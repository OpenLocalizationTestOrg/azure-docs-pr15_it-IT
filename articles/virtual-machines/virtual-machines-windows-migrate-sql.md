<properties
    pageTitle="Eseguire la migrazione di un database SQL Server per SQL Server in una macchina virtuale | Microsoft Azure"
    description="Informazioni su come eseguire la migrazione di un database utente locale a SQL Server in una macchina virtuale Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Eseguire la migrazione di un database SQL Server per SQL Server in una macchina virtuale Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Modello di gestione delle risorse.


Sono disponibili numerosi metodi per la migrazione di un database utente di SQL Server locale a SQL Server in una macchina virtuale Azure. In questo articolo verrà brevemente illustrati vari metodi, consiglia la soluzione migliore per i diversi scenari e includono un' [esercitazione](#azure-vm-deployment-wizard-tutorial) per informazioni utili per la procedura guidata **Deploy un Database di SQL Server per una macchina virtuale Azure Microsoft** tramite l'utilizzo. 

Il metodo utilizzando la procedura guidata **Deploy un Database di SQL Server per una macchina virtuale Azure Microsoft** descritta nell' [esercitazione](#azure-vm-deployment-wizard-tutorial) è per il modello di distribuzione classica. 

## <a name="what-are-the-primary-migration-methods"></a>Che cosa sono i metodi di migrazione principale?

I metodi di migrazione principali sono:

- Utilizzare un Database di SQL Server per una procedura guidata Microsoft Azure VM di distribuzione
- Eseguire il backup locale con la compressione e copiare manualmente il file di backup nella macchina virtuale Azure
- Eseguire un backup di URL e il ripristino alla macchina virtuale Azure dall'URL
- Scollegare e quindi copiare i file di registro e dati di archiviazione blob Azure e quindi collegare a SQL Server in macchine Virtuali di Azure da URL
- Convertire computer fisico locale dei dischi rigidi Virtuali Hyper-V, caricare di archiviazione Blob di Azure e quindi distribuire come nuova macchina virtuale tramite caricato dei dischi rigidi Virtuali
- Disco rigido Shipping utilizzo del servizio di importazione/esportazione di Windows
- Se si dispone di una distribuzione di AlwaysOn locale, utilizzare la [Replica guidata Azure](virtual-machines-windows-classic-sql-onprem-availability.md) per creare una replica in Azure e quindi il failover, gli utenti che punta all'istanza del database Azure
- Utilizzare SQL Server, [la replica transazionale](https://msdn.microsoft.com/library/ms151176.aspx) di configurare l'istanza di Azure SQL Server come server di sottoscrizione e quindi disabilitare la replica, gli utenti che punta all'istanza del database Azure



## <a name="choosing-your-migration-method"></a>Scelta del metodo di migrazione

Per le prestazioni di trasferimento dei dati ottimale, la migrazione dei file di database nella macchina virtuale Azure utilizzando un file di backup compresso è in genere il metodo migliore. Questo è il metodo utilizzato per [distribuire un Database di SQL Server per una procedura guidata Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) . La procedura guidata è il metodo consigliato per la migrazione di un'esecuzione database utente locale in SQL Server 2005 o superiori a SQL Server 2014 o versione successiva quando il file di backup di database compresso è minore di 1 TB.

Per ridurre al minimo i tempi di inattività durante il processo di migrazione di database, utilizzare l'opzione AlwaysOn o la replica transazionale.

Se non è possibile utilizzare i metodi sopra elencati, manualmente la migrazione del database. Questo metodo è in genere inizierà con un backup del database seguito da una copia del database di backup in Azure e quindi eseguire il ripristino del database. È possibile anche copiare i file di database se stessi in Azure e collegare i documenti. Esistono diversi metodi per cui è possibile eseguire questa procedura manuale di migrazione di un database in una macchina virtuale Azure.

> [AZURE.NOTE] Durante l'aggiornamento a SQL Server 2014 o SQL Server 2016 rispetto alle versioni precedenti di SQL Server, è consigliabile utilizzare se sono necessarie modifiche. Si consiglia di occuparsi di tutte le dipendenze dalle caratteristiche non supportate per la nuova versione di SQL Server come parte del progetto di migrazione. Per ulteriori informazioni sulle edizioni supportate e gli scenari, vedere [eseguire l'aggiornamento a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Nella tabella seguente sono elencati tutti i metodi di migrazione primario e viene illustrato quando l'utilizzo di ogni metodo risulta più appropriato.

| Metodo  | Versione del database di origine  |  Versione del database di destinazione | Vincolo di dimensione di backup del database di origine  | Note  |
|---|---|---|---|---|
| [Utilizzare la distribuzione di un Database di SQL Server per una procedura guidata Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 o versione successiva | SQL Server 2014 o versione successiva | < 1 TB  | Metodo più semplice e rapido, utilizzare ogni volta che è possibile eseguire la migrazione a un'istanza di SQL Server nuova o esistente in una macchina virtuale Azure | 
| [Utilizzare l'aggiunta guidata Replica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 o versione successiva | SQL Server 2012 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Riduce al minimo i tempi di inattività, da utilizzare quando si dispone di una distribuzione locale di AlwaysOn |
| [Utilizzare la replica transazionale di SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizzare quando sono necessarie per ridurre al minimo i tempi di inattività e non dispongono di una distribuzione locale di AlwaysOn |
| [Eseguire il backup locale con la compressione e copiare manualmente il file di backup nella macchina virtuale Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizzare solo se non è possibile utilizzare la procedura guidata, ad esempio quando la versione del database di destinazione è minore di SQL Server 2012 SP1 CU2 o la dimensione di backup del database è maggiore di 1 TB (12,8 TB con SQL Server 2016) |
| [Eseguire un backup di URL e il ripristino alla macchina virtuale Azure dall'URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 o versione successiva | SQL Server 2012 SP1 CU2 o versione successiva | < 12,8 TB per SQL Server 2016, altrimenti < 1 TB | È in genere utilizzare [backup URL](https://msdn.microsoft.com/library/dn435916.aspx) non piuttosto semplice ed equivalente delle prestazioni per la creazione guidata |
| [Scollegare e quindi copiare i file di registro e dati di archiviazione blob Azure e quindi collegare a SQL Server in macchine virtuali Azure da URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 o versione successiva | SQL Server 2014 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizzare questo metodo se si prevede di [archiviare questi file utilizzo del servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx) e li collegare a SQL Server in esecuzione in una macchina virtuale Azure, in particolare con database di grandi dimensioni |
| [Convertire il computer locale in dischi rigidi virtuali Hyper-V, caricare in archiviazione Blob Azure e quindi distribuire una nuova macchina virtuale tramite VHD caricati](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizzo quando [portare il proprio licenza di SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md), durante la migrazione di un database in cui vengono eseguiti in una versione precedente di SQL Server o durante la migrazione utente e sistema database contemporaneamente come parte della migrazione di database dipendente in altri database utente e/o database di sistema. |
| [Utilizzo del servizio di importazione/esportazione Windows Shipping disco rigido](#ship-hard-drive) | SQL Server 2005 o versione successiva | SQL Server 2005 o versione successiva | [Limite di archiviazione macchina virtuale Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizzare il [Servizio di importazione/esportazione di Windows](../storage/storage-import-export-service.md) quando il metodo di copia manuale è troppo lento, ad esempio con database di grandi dimensioni |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Esercitazione di Azure VM distribuzione guidata

Utilizzare la procedura guidata **Deploy un Database di SQL Server per una macchina virtuale Azure Microsoft** in Microsoft SQL Server Management Studio per eseguire la migrazione di SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, 2014 di SQL Server o SQL Server 2016 utente locale del database (fino a 1 TB) per SQL Server 2014 o SQL Server 2016 in una macchina virtuale Azure. Utilizzare la procedura guidata per eseguire la migrazione di un database utente per una macchina virtuale Azure esistente o per una macchina virtuale Azure con SQL Server creata dalla procedura guidata durante il processo di migrazione. Durante la migrazione di un database a una versione più recente di SQL Server, il database viene aggiornato automaticamente durante il processo.

Il metodo è per il modello di distribuzione classica. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Leggi ultima versione della distribuzione di un Database di SQL Server per una procedura guidata Microsoft Azure VM

Utilizzare la versione più recente di Microsoft SQL Server Management Studio per SQL Server per verificare di avere la versione più recente della procedura guidata **Deploy un Database di SQL Server per una macchina virtuale Azure Microsoft** . La versione più recente della procedura guidata include gli aggiornamenti più recenti per il portale classico Azure e supporta le immagini di macchine Virtuali di Azure più recente nella raccolta (le versioni precedenti della procedura guidata potrebbero non funzionare). Per ottenere la versione più recente di Microsoft SQL Server Management Studio di SQL Server, [scaricarlo](http://go.microsoft.com/fwlink/?LinkId=616025) e installarlo in un computer client con la connettività al database che si prevede di eseguire la migrazione e a internet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Configurare la macchina virtuale Azure esistente e l'istanza di SQL Server (se applicabile)

Se esegue la migrazione a una macchina virtuale Azure esistente, sono necessari che i passaggi di configurazione seguenti:

- Configurare la macchina virtuale Azure e l'istanza di SQL Server per consentire la connettività da un altro computer seguendo i passaggi per la [connessione all'istanza di macchina virtuale di SQL Server da SQL Server Management Studio in un altro computer](virtual-machines-windows-sql-connect.md). Se si esegue la migrazione utilizzando la procedura guidata, sono supportate solo le immagini 2014 di SQL Server e SQL Server 2016 nella raccolta.
- Configurare un endpoint per il servizio SQL Server Cloud scheda aperto sul gateway Microsoft Azure con porta privata di 11435. Questa porta viene creata come parte di SQL Server 2014 o SQL Server 2016 provisioning in una macchina virtuale Azure Microsoft. La scheda Cloud consente inoltre di creare una regola di Windows Firewall per consentire le connessioni in ingresso TCP alla porta predefinita 11435. L'endpoint consente la creazione guidata per l'utilizzo del servizio Cloud scheda per copiare i file di backup dall'istanza locale per la macchina virtuale Azure. Per ulteriori informazioni, vedere [Scheda Cloud per SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Creare Cloud scheda Endpoint](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Eseguire l'utilizzo di distribuire un Database SQL Server per una procedura guidata Microsoft Azure VM

1. Aprire Microsoft SQL Server Management Studio per Microsoft SQL Server 2016 e connettersi all'istanza di SQL Server contenente il database utente che si desidera migrare a una macchina virtuale Azure.
2. Destro del mouse sul database che sta eseguendo la migrazione, scegliere attività e quindi scegliere Distribuisci in una macchina virtuale Azure Microsoft.

    ![Avviare guidata](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Nella pagina Introduzione fare clic su Avanti.
4. Nella pagina Impostazioni origine connettersi all'istanza di SQL Server contenente il database che si intende eseguire la migrazione a una macchina virtuale Azure.
5. Specificare un percorso temporaneo per i file di backup. Se la connessione a un server remoto, è necessario specificare un'unità di rete.

    ![Impostazioni di origine](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Fare clic su Avanti.
7. Nella pagina Microsoft Azure Sign-In fare clic su Accedi e l'accesso all'account di Azure.
8. Selezionare la sottoscrizione che si desidera utilizzare e fare clic su Avanti.

    ![Accesso di Azure](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. Nella pagina Impostazioni distribuzione, è possibile specificare un nuovo o un servizio Cloud esistente nome e il nome della macchina virtuale:

 - Specificare un nuovo nome macchina virtuale e nome del servizio Cloud per creare un nuovo servizio Cloud con una nuova macchina virtuale Azure con un'immagine di SQL Server 2014 o raccolta 2016 di SQL Server.

     - Se si specifica un nuovo nome del servizio Cloud, specificare l'account di archiviazione che verrà utilizzato.

     - Se si specifica un nome di servizio Cloud esistente, l'account di archiviazione verrà recuperato e per consentire l'immissione.

 - Specificare un nome di servizio Cloud esistente e il nuovo nome della macchina virtuale per creare una nuova macchina virtuale Azure in un servizio Cloud esistente. Specificare solo nella galleria 2014 di SQL Server o SQL Server 2016.
 - Specificare un nome di servizio Cloud esistente e nome della macchina virtuale per utilizzare una macchina virtuale Azure esistente. Deve essere un'immagine creata con un'immagine raccolta 2014 di SQL Server o SQL Server 2016.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Scegliere impostazioni
  - Se è stato specificato un nome servizio Cloud esistente e il nome della macchina virtuale, verrà chiesto di specificare il nome utente e la password.

        ![Impostazioni del computer Azure](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Se è stato specificato un nuovo nome della macchina virtuale, verrà chiesto di selezionare un'immagine nell'elenco delle immagini raccolta e forniscono le informazioni seguenti:
      - Immagine: consente di selezionare solo 2014 di SQL Server o SQL Server 2016
        - Nome utente
        - Nuova password
        - Conferma password
        - Percorso di
        - Dimensione.
    - Inoltre, fare clic per accettare i certificati generati automaticamente per questa nuova Microsoft Azure macchina virtuale e quindi scegliere OK.

    ![Azure nuove impostazioni di sistema](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Specificare il nome di database di destinazione se diverso dal nome del database di origine. Se il database di destinazione esiste già, il sistema verrà automaticamente incrementare il nome del database anziché sovrascrivere il database esistente.
12. Fare clic su Avanti e quindi fare clic su Fine.

    ![Risultati](./media/virtual-machines-windows-migrate-sql/results.png)

13. Al termine della procedura guidata, la connessione alla macchina virtuale e verificare che il database è stata eseguita la migrazione.
14. Se è stata creata una nuova macchina virtuale, configurare la macchina virtuale Azure e l'istanza di SQL Server seguendo i passaggi per la [connessione all'istanza di macchina virtuale di SQL Server da SQL Server Management Studio in un altro computer](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Backup di file e copia alla macchina virtuale e il ripristino

Utilizzare questo metodo quando non è possibile utilizzare distribuire un Database di SQL Server per una procedura guidata Microsoft Azure VM perché si esegue la migrazione a una versione di SQL Server prima di SQL Server 2014 o il file di backup è maggiore di 1 TB. Se il file di backup è maggiore di 1 TB, è necessario stripe poiché la dimensione massima di un disco macchina virtuale è 1 TB. Utilizzare la procedura seguente per eseguire la migrazione di un database utente utilizzando il metodo manuale:

1.  Eseguire un backup completo del database in una posizione locale.
2.  Creare o caricare una macchina virtuale con la versione di SQL Server desiderato.
3.  Installazione di integrazione applicativa in base alle specifiche esigenze. Vedere [connettersi a una macchina virtuale SQL Server su Azure (Manager delle risorse)](virtual-machines-windows-sql-connect.md).
4.  Copiare i file di backup di una macchina virtuale tramite desktop remoto, in Esplora risorse o il comando Copia da un prompt dei comandi.

## <a name="backup-to-url-and-restore"></a>Backup di URL e il ripristino

Utilizzare il metodo di [backup URL](https://msdn.microsoft.com/library/dn435916.aspx) quando non è possibile utilizzare la distribuzione di un Database di SQL Server per una procedura guidata Microsoft Azure VM perché il file di backup è maggiore di 1 TB e migrazione da e a SQL Server 2016. Per i database è minori di 1 TB in esecuzione una versione di SQL Server prima di SQL Server 2016, è consigliabile utilizzare della procedura guidata. Con SQL Server 2016, i set di backup con striping sono supportati, sono consigliati per le prestazioni e necessari per superare i limiti di dimensione per blob. Per il database di grandi dimensioni, è consigliabile l'utilizzo del [Servizio di importazione/esportazione di Windows](../storage/storage-import-export-service.md) .

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Scollegare e copia a URL e si collega da URL

Utilizzare questo metodo quando si prevede di [archiviare questi file mediante il servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/dn385720.aspx) e li collegare a SQL Server in esecuzione in una macchina virtuale Azure, in particolare con database di grandi dimensioni. Utilizzare la procedura seguente per eseguire la migrazione di un database utente utilizzando il metodo manuale:

1.  Scollegare i file di database dall'istanza locale del database.
2.  Copiare i file di database scollegati in archiviazione blob Azure utilizzando l' [utilità da riga di comando AZCopy](../storage/storage-use-azcopy.md).
3.  Collegare i file di database dall'URL Azure all'istanza di SQL Server nella macchina virtuale Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Convertire alla macchina virtuale e caricare di URL e distribuire come nuova macchina virtuale

Utilizzare questo metodo per eseguire la migrazione di tutti i database di sistema e degli utenti in un'istanza di SQL Server locale alla macchina virtuale Azure. Utilizzare la procedura seguente per eseguire la migrazione di un'intera istanza di SQL Server utilizzando il metodo manuale:

1.  Convertire le macchine virtuali o computer fisici in dischi rigidi virtuali Hyper-V tramite [Microsoft Virtual Machine convertitore](http://technet.microsoft.com/library/dn873998.aspx).
2.  Caricare file VHD Azure archiviazione utilizzando il [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.  Distribuire una nuova macchina virtuale utilizzando il disco rigido virtuale caricato.

> [AZURE.NOTE] Per eseguire la migrazione di un'intera applicazione, è consigliabile utilizzare [Il ripristino del sito di Azure](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Disco rigido spedizione

Utilizzare il [metodo del servizio di importazione/esportazione di Windows](../storage/storage-import-export-service.md) per il trasferimento di grandi quantità di dati dei file di archiviazione Blob Azure in situazioni in cui caricamento sulla rete estremamente costosa o non realizzabile. Con questo servizio, si invia una o più unità disco rigido contenente dati a un centro dati Azure, in cui verranno caricati i dati al proprio account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'esecuzione di SQL Server nelle macchine virtuali Azure, vedere [SQL Server in macchine virtuali di Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Per istruzioni sulla creazione di una macchina virtuale di Azure SQL Server da un'immagine acquisita, vedere [consigli sulla clonazione macchine virtuali di Azure SQL da immagini acquisite e suggerimenti](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) sul blog tecnici CSS SQL Server.
