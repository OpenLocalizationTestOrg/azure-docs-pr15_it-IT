<properties 
   pageTitle="Protezione dei dati archiviati in archivio Lake dati di Azure | Microsoft Azure" 
   description="Informazioni su come proteggere i dati nell'archivio di Azure Lake dati con i gruppi e gli elenchi di controllo di accesso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protezione dei dati archiviati in archivio Lake dati di Azure

Protezione dei dati nell'archivio Lake dati di Azure è un approccio in tre passaggi.

1. Iniziare con la creazione di gruppi di sicurezza di Azure Active Directory (AAD). I gruppi di sicurezza vengono utilizzati per implementare controllo dell'accesso basato sui ruoli (RBAC) nel portale di Azure. Per ulteriori informazioni, vedere [Controllo dell'accesso basato sui ruoli in Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Assegnare i gruppi di sicurezza AAD all'account Azure dati Lake Store. Controlla l'accesso all'account di archivio di dati Lake operazioni portale e gestione dal portale o API di.

3. Assegnare i gruppi di sicurezza AAD come accesso elenchi di controllo () nel file system archivio Lake dati.

4. È inoltre possibile impostare un intervallo di indirizzi IP per i client che possono accedere ai dati nell'archivio Lake dati.

In questo articolo fornisce istruzioni su come usare il portale di Azure per eseguire le operazioni precedenti. Per informazioni dettagliate sulle modalità di implementazione archivio Lake dati sicurezza a livello di account e dati, vedere [protezione nell'archivio Lake dati di Azure](data-lake-store-security-overview.md). Per approfondire informazioni sulla modalità di implementazione ACL nell'archivio Lake dati di Azure, vedere [Panoramica di controllo di accesso nell'archivio Lake dati](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Account di un archivio Lake dati di Azure**. Per istruzioni su come creare uno, vedere [Guida introduttiva a archivio Lake dati di Azure](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Creare gruppi di sicurezza di Azure Active Directory

Per istruzioni su come creare gruppi di sicurezza AAD e come aggiungere utenti al gruppo, vedere [gestione dei gruppi di sicurezza di Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Assegnare utenti o gruppi di sicurezza per gli account Azure dati Lake archivio

Quando si assegna utenti o gruppi di sicurezza per gli account Azure dati Lake Store, controllare l'accesso a operazioni di gestione dell'account tramite il portale Azure e Azure API di Manager delle risorse. 

1. Aprire un account Azure dati Lake Store. Dal riquadro di sinistra, fare clic su **Sfoglia**e quindi scegliere il nome dell'account a cui si desidera assegnare un utente o gruppo di protezione e il Lake archivio **Dati Lake Store**.

2. In blade l'account archivio Lake dati, fare clic su **Impostazioni**. Da e **l'Impostazioni** , fare clic su **utenti**.

    ![Assegnare il gruppo di sicurezza all'account Azure dati Lake archivio] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare il gruppo di sicurezza all'account Azure dati Lake archivio")

3. E **l'utente** per impostazione predefinita elenchi gruppo di **amministratori di sottoscrizione** come proprietario. 

    ![Aggiungi utenti e ruoli] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Aggiungi utenti e ruoli")
 
    Esistono due modi per aggiungere un gruppo e assegnare ruoli pertinenti.

    * Aggiungere un utente/gruppo all'account e quindi assegnare un ruolo o
    * Aggiungere un ruolo e quindi assegnare utenti/gruppi al ruolo.

    In questa sezione, si osserva il primo approccio, aggiunta di un gruppo e quindi l'assegnazione di ruoli. È possibile eseguire operazioni simili per selezionare un ruolo, quindi assegnare tale ruolo gruppi.
    
4. In e **l'utenti** , fare clic su **Aggiungi** per aprire e il **componente accesso** . In e il **componente accesso** , fare clic su **Seleziona un ruolo**e quindi selezionare un ruolo di utente o del gruppo.

     ![Aggiungere un ruolo per l'utente] (./media/data-lake-store-secure-data/adl.add.user.1.png "Aggiungere un ruolo per l'utente")

    Il **proprietario** e il ruolo di **Collaboratore** fornire l'accesso a una serie di funzioni di amministrazione nella pagina account lake dati. Per gli utenti che interagiscono con i dati nel lake dati, è possibile aggiungerli al ruolo di **lettore **. L'ambito di questi ruoli è limitato alle operazioni di gestione correlate all'account Azure dati Lake Store.

    Per i dati operazioni singole autorizzazioni file system per definire operazioni consentite agli utenti. Di conseguenza, un utente con un ruolo di lettore possa solo visualizzare le impostazioni di amministrazione associate all'account, ma può potenzialmente leggere e scrivere dati in base alle autorizzazioni di sistema di file assegnate. Autorizzazioni del file di archivio di dati Lake sono descritti in [assegnare il gruppo di sicurezza come ACL nel file System archivio Lake dati di Azure](#filepermissions).



5. Scegliere **Aggiungi utenti** per aprire e **l'aggiungere utenti** e il **componente accesso** . In questo blade, cercare il gruppo di sicurezza creato in precedenza in Azure Active Directory. Se si dispone di moltissimi gruppi eseguire la ricerca, utilizzare la casella di testo nella parte superiore per filtrare sul nome del gruppo. Fare clic su **Seleziona**.

    ![Aggiungere un gruppo di sicurezza] (./media/data-lake-store-secure-data/adl.add.user.2.png "Aggiungere un gruppo di sicurezza")

    Se si desidera aggiungere un gruppo/utente che non è elencato, è possibile invitare utilizzando l'icona **Invita** e specificando l'indirizzo di posta elettronica per l'utente/gruppo.

6. Fare clic su **OK**. Verrà visualizzato il gruppo di sicurezza aggiunto come illustrato di seguito.

    ![Gruppo di sicurezza aggiunto] (./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza aggiunto")

7. L'utente/gruppo è ora disponibile l'accesso all'account Azure dati Lake Store. Se si desidera fornire l'accesso a utenti specifici, è possibile aggiungere al gruppo di sicurezza. Analogamente, se si vuole revocare l'accesso per un utente, è possibile rimuoverli dal gruppo di sicurezza. È inoltre possibile assegnare più gruppi di sicurezza a un account. 

## <a name="filepermissions"></a>Assegnare utenti o gruppo di sicurezza come ACL nel file System archivio Lake dati di Azure

Assegnando utenti/gruppi di sicurezza nel file System Lake di dati di Azure, impostare il controllo dell'accesso ai dati archiviati in archivio Lake dati di Azure.

1. In blade l'account archivio Lake dati, fare clic su **Esplora dati**.

    ![Crea directory nell'archivio dati Lake account] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Directory di creare account Lake dati")

2. In e il **Data Explorer** , fare clic su file o sulla cartella per il quale si desidera configurare l'ACL e quindi fare clic su **accesso**. Per assegnare ACL in un file, è necessario fare clic su **accesso** da e il **File di anteprima** .

    ![Impostare ACL Lake dati file System] (./media/data-lake-store-secure-data/adl.acl.1.png "Impostare ACL Lake dati file System")

3. E **l'accesso** Elenca l'accesso standard e personalizzati già stata assegnata alla radice. Fare clic sull'icona **Aggiungi** per aggiungere ACL livello personalizzato.

    ![Accesso standard e personalizzato di elenco] (./media/data-lake-store-secure-data/adl.acl.2.png "Accesso standard e personalizzato di elenco")

    * **Accedere a standard** è l'accesso di tipo UNIX, specificare lettura, scrittura, esecuzione (rwx) a tre classi utente distinti: proprietario, gruppo e gli altri utenti.
    * **Access personalizzata** corrisponde agli ACL POSIX che consente di impostare le autorizzazioni per specifici nomi degli utenti o gruppi e non solo il file proprietario o un gruppo. 
    
    Per ulteriori informazioni, vedere [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Per ulteriori informazioni sulla modalità di implementazione ACL nell'archivio Lake dati, vedere [Controllo di accesso nell'archivio Lake dati](data-lake-store-access-control.md).

4. Fare clic sull'icona **Aggiungi** per aprire e **l'Aggiungere Access personalizzata** . In questo blade, fare clic su **Seleziona utente o gruppo**e quindi in **Seleziona utente o gruppo** blade, cercare il gruppo di sicurezza creato in precedenza in Azure Active Directory. Se si dispone molte gruppi eseguire la ricerca, utilizzare la casella di testo nella parte superiore per filtrare sul nome del gruppo. Fare clic sul gruppo che si desidera aggiungere e quindi fare clic su **Seleziona**.

    ![Aggiungere un gruppo] (./media/data-lake-store-secure-data/adl.acl.3.png "Aggiungere un gruppo")

5. Fare clic su **Selezionare autorizzazioni**selezionare le autorizzazioni e se si desidera assegnare le autorizzazioni per impostazione predefinita ACL, accedere a ACL o entrambe. Fare clic su **OK**.

    ![Assegnare autorizzazioni a gruppo] (./media/data-lake-store-secure-data/adl.acl.4.png "Assegnare autorizzazioni a gruppo")

    Per ulteriori informazioni sulle autorizzazioni in Lake archivio di dati e ACL/accesso predefinito, vedere [Controllo di accesso nell'archivio Lake dati](data-lake-store-access-control.md).


6. In e **l'Aggiungere Access personalizzata** , fare clic su **OK**. Gruppo appena aggiunto, con le autorizzazioni associate a questo punto essere elencato nel e **Access** .

    ![Assegnare autorizzazioni a gruppo] (./media/data-lake-store-secure-data/adl.acl.5.png "Assegnare autorizzazioni a gruppo")

    > [AZURE.IMPORTANT] Nella versione corrente è consentito solo 9 voci in **Access personalizzata**. Se si desidera aggiungere più di 9 utenti, è consigliabile creare gruppi di sicurezza, aggiunta di utenti ai gruppi di sicurezza, fornire l'accesso ai gruppi di sicurezza per l'account di archivio di dati Lake.

7. Se necessario, è inoltre possibile modificare le autorizzazioni di accesso dopo aver aggiunto il gruppo. Deselezionare o selezionare la casella di controllo per ogni tipo di autorizzazione (lettura, scrittura, esecuzione) in base a se si desidera rimuovere o assegnare l'autorizzazione per il gruppo di sicurezza. Fare clic su **Salva** per salvare le modifiche o **Annulla** per annullare le modifiche.

## <a name="set-ip-address-range-for-data-access"></a>Impostare l'intervallo di indirizzi IP per l'accesso ai dati

Archivio Lake dati di Azure consente di bloccare ulteriormente l'accesso all'archivio dati a livello di rete. Si abilita firewall, specificare un indirizzo IP o definire un intervallo di indirizzi IP per i client attendibili. Dopo l'abilitazione, solo i clienti che dispongono di indirizzi IP all'interno di un intervallo definito possono connettersi all'archivio.

![Impostazioni del firewall e accesso IP] (./media/data-lake-store-secure-data/firewall-ip-access.png "Impostazioni del firewall e l'indirizzo IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Rimuovere i gruppi di sicurezza per un account Azure dati Lake archivio

Quando si rimuovono i gruppi di sicurezza da account Azure dati Lake archivio, si sta modificando l'accesso alle operazioni di gestione dell'account tramite il portale di Azure e Azure API di Manager delle risorse.

1. In blade l'account archivio Lake dati, fare clic su **Impostazioni**. Da e **l'Impostazioni** , fare clic su **utenti**.

    ![Assegnare il gruppo di sicurezza all'account Azure dati Lake] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Assegnare il gruppo di sicurezza all'account Azure dati Lake")

2. Selezionare il gruppo di sicurezza che si desidera rimuovere e **l'utenti** .

    ![Gruppo di sicurezza da rimuovere] (./media/data-lake-store-secure-data/adl.add.user.3.png "Gruppo di sicurezza da rimuovere")

3. In blade per il gruppo di sicurezza, fare clic su **Rimuovi**.

    ![Gruppo di sicurezza rimosso] (./media/data-lake-store-secure-data/adl.remove.group.png "Gruppo di sicurezza rimosso")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Rimuovere il gruppo di sicurezza ACL dal sistema di file di archivio Lake dati di Azure

Quando si rimuovono i gruppi di sicurezza ACL dal file system archivio Lake dati di Azure, di modificare l'accesso ai dati nell'archivio Lake dati.

1. In blade l'account archivio Lake dati, fare clic su **Esplora dati**.

    ![Directory di creare account Lake dati] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Directory di creare account Lake dati")

2. In e il **Data Explorer** , fare clic su file o sulla cartella per il quale si desidera rimuovere l'ACL e blade l'account, fare clic sull'icona di **Access** . Per rimuovere ACL per un file, è necessario fare clic su **accesso** da e il **File di anteprima** .

    ![Impostare ACL Lake dati file System] (./media/data-lake-store-secure-data/adl.acl.1.png "Impostare ACL Lake dati file System")

3. Selezionare il gruppo di sicurezza che si desidera rimuovere e **l'accesso** , nella sezione **Accesso personalizzato** . In e il **Access personalizzata** , fare clic su **Rimuovi** e quindi fare clic su **OK**.

    ![Assegnare autorizzazioni a gruppo] (./media/data-lake-store-secure-data/adl.remove.acl.png "Assegnare autorizzazioni a gruppo")


## <a name="see-also"></a>Vedere anche

- [Panoramica dell'archivio Lake dati di Azure](data-lake-store-overview.md)
- [Copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Guida introduttiva a archivio di Lake dati tramite PowerShell](data-lake-store-get-started-powershell.md)
- [Guida introduttiva a archivio Lake utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Log diagnostici di Access per archivio Lake dati](data-lake-store-diagnostic-logs.md)
