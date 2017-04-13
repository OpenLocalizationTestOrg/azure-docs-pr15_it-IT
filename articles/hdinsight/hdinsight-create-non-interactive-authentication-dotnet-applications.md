<properties
    pageTitle="Creare l'autenticazione non interattiva HDInsight .NET OLAF | Microsoft Azure"
    description="Informazioni su come creare autenticazione non interattiva applicazioni .NET HDInsight."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Creare l'autenticazione non interattiva applicazioni .NET HDInsight

È possibile eseguire l'applicazione di .NET Azure HDInsight con identità dell'applicazione (non interattiva) o con l'identità dell'utente connesso dell'applicazione (interattivo). Per un esempio di applicazione interattiva, vedere [inviare Hive/maialino/Sqoop processi mediante HDInsight .NET SDK](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). In questo articolo viene illustrato come creare un'applicazione di .NET autenticazione non interattiva di connettersi a Azure HDInsight e inviare un processo Hive.

Dall'applicazione di .NET, sarà necessario:

- ID abbonamento Azure tenant
- l'ID di Azure Directory applicazione client
- chiave segreta di Azure Directory dell'applicazione.  

Il processo principale include i passaggi seguenti:

2. Creare un'applicazione di Azure Directory.
2. Assegnare ruoli all'applicazione di Active Directory.
3. Sviluppare l'applicazione client.


##<a name="prerequisites"></a>Prerequisiti

- Cluster HDInsight. È possibile creare uno usando le istruzioni incluse nell' [esercitazione introduttiva](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Creare applicazioni Azure Directory 
Quando si crea un'applicazione di Active Directory, viene effettivamente creato l'applicazione e un servizio principale. È possibile eseguire l'applicazione in identità dell'applicazione.

Attualmente, è necessario utilizzare il portale classico Azure per creare una nuova applicazione di Active Directory. Questa caratteristica verrà aggiunto al portale di Azure in una versione successiva. È anche possibile eseguire questa procedura tramite PowerShell Azure o CLI Azure. Per ulteriori informazioni sull'utilizzo di PowerShell o CLI con il servizio principale, vedere [servizio di autenticazione principale Gestione risorse Azure](../resource-group-authenticate-service-principal.md).

**Per creare un'applicazione di Azure Directory**

1.  Accedere al [portale classica Azure]( https://manage.windowsazure.com/).
2.  Selezionare **Active Directory** dal riquadro di sinistra.

    ![Azure classica portale active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Selezionare la cartella che si desidera utilizzare per creare la nuova applicazione. Deve trattarsi di uno esistente.
4.  Fare clic su **applicazioni** nella parte superiore per visualizzare un elenco di applicazioni esistenti.
5.  Fare clic su **Aggiungi** dal basso per aggiungere una nuova applicazione.
6.  Immettere **nome**, selezionare **applicazione Web e/o API Web**e quindi fare clic su **Avanti**.

    ![nuova applicazione di azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Immettere **l'URL Sign-on** e **App ID URI**. Per **Accedere via URL**, fornire URI a un sito web che descrive l'applicazione. L'esistenza del sito web non viene convalidata. Per APP ID URI, fornire URI che identifica l'applicazione. E quindi fare clic su **completa**.
Bastano pochi minuti per creare l'applicazione.  Dopo aver creato l'applicazione, il portale viene mostrata la pagina rapido uso della nuova applicazione. Non chiudere il portale. 

    ![nuova proprietà di applicazione di azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Per ottenere l'applicazione client ID e la chiave privata**

1.  Dalla pagina di applicazione di Active Directory appena creata, scegliere **Configura** dal menu superiore.
2.  Creare una copia **dell'ID Client**. Sarà necessario nell'applicazione .NET.
3.  Nelle **chiavi**, fare clic su elenco a discesa **Selezionare durata** e selezionare **anno 1** o **2 anni**. Il valore di chiave non essere visualizzato finché non si salva la configurazione.
4.  Fare clic su **Salva** nella parte inferiore della pagina. Quando viene visualizzata la chiave privata, eseguire una copia della chiave. Sarà necessario nell'applicazione .NET.

##<a name="assign-ad-application-to-role"></a>Assegnare un'applicazione di Active Directory di ruolo

È necessario assegnare un'applicazione di un [ruolo](../active-directory/role-based-access-built-in-roles.md) per concedere autorizzazioni per l'esecuzione di azioni. È possibile impostare l'ambito a livello della sottoscrizione, gruppo di risorse o delle risorse. Le autorizzazioni vengono ereditate inferiore livelli di ambito (ad esempio, aggiunta di un'applicazione il ruolo di lettore per un gruppo di risorse indica che è possibile leggere il gruppo di risorse e le risorse che contiene). In questa esercitazione verrà impostata l'ambito il livello di gruppo di risorse.  Poiché il portale classico Azure non supporta i gruppi di risorse, questa parte deve essere eseguita dal portale di Azure. 

**Per aggiungere il ruolo di proprietario per l'applicazione di Active Directory**

1.  Accedere al [portale di Azure](https://portal.azure.com).
2.  Fare clic su **Gruppo di risorse** dal riquadro di sinistra.
3.  Fare clic sul gruppo di risorse contenente cluster HDInsight nel punto in cui verrà eseguita la query Hive più avanti in questa esercitazione. Se sono presenti troppi gruppi di risorse, è possibile usare il filtro.
4.  Fare clic su **accesso** e il raggruppamento.

    ![icona del cloud e thunderbolt = Guida introduttiva](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Fare clic su **Aggiungi** e **l'utenti** .
6.  Seguire le istruzioni per aggiungere il ruolo di **proprietario** per l'applicazione di Active Directory che è stato creato nella procedura precedente. Dopo aver completato correttamente, risulta applicazione elencata nella e utenti con il ruolo di proprietario.


##<a name="develop-hdinsight-client-application"></a>Sviluppare l'applicazione client HDInsight

Creare un'applicazione console c# .net seguendo le istruzioni disponibili nella [Hadoop inviare processi in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Quindi sostituire il metodo GetTokenCloudCredentials con le operazioni seguenti:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Per recuperare l'ID Tenant tramite PowerShell:

    Get-AzureRmSubscription

In alternativa, CLI Azure:

    azure account show --json

      
## <a name="see-also"></a>Vedere anche

- [Inviare i processi di Hadoop in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Creare applicazioni di Active Directory e dell'entità servizio nel portale](../resource-group-create-service-principal-portal.md)
- [Eseguire l'autenticazione dell'entità servizio Gestione risorse Azure](../resource-group-authenticate-service-principal.md)
- [Controllo dell'accesso basato sui ruoli Azure](../active-directory/role-based-access-control-configure.md)
