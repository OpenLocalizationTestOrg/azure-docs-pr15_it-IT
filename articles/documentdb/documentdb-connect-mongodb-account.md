<properties 
    pageTitle="Connettersi a un account DocumentDB con protocollo per MongoDB | Microsoft Azure" 
    description="Informazioni su come connettersi a un account DocumentDB con protocollo per MongoDB, ora disponibile per l'anteprima. Connettersi usando la stringa di connessione MongoDB." 
    keywords="stringa di connessione MongoDB"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Come connettersi a un account DocumentDB con protocollo per MongoDB

Informazioni su come connettersi a un account Azure DocumentDB con supporto del protocollo per MongoDB utilizzando il formato URI stringa di connessione MongoDB standard.  

## <a name="get-the-accounts-connection-string-information"></a>Ottenere informazioni sulla stringa di connessione dell'account

1. In una nuova finestra, accedere al [Portale di Azure](https://portal.azure.com).
2. Nella barra di **Spostamento a sinistra** della stessa e Account, fare clic su **Stringa di connessione**. Per passare a **Account Blade**, scegliere il Jumpbar fare clic su **Altri servizi**, fare clic su **DocumentDB (NoSQL)** e quindi selezionare l'account DocumentDB con protocollo per MongoDB.

    ![Schermata della stessa e tutte le impostazioni](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. E **l'Informazioni della stringa di connessione** viene aperto e include tutte le informazioni necessarie per connettersi all'account utilizzando un driver per MongoDB, tra cui una stringa di connessione predefiniti.

    ![Schermata della stessa e stringa di connessione](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisiti della stringa di connessione

È importante tenere presente che DocumentDB supporta MongoDB connessione stringa URI formato standard, con un paio di requisiti specifici: account DocumentDB richiedono l'autenticazione e la comunicazione sicura tramite SSL.  In questo modo, il formato di stringa di connessione è:

    mongodb://username:password@host:port/[database]?ssl=true

Nel punto in cui i valori di questa stringa sono disponibili in e lo stringa di connessione illustrato sopra.

- Nome utente (obbligatorio)
    - Nome dell'account DocumentDB
- Password (obbligatoria)
    - Password dell'account DocumentDB
- Host (obbligatorio)
    - Nome di dominio completo di DocumentDB account
- Porta (obbligatoria)
    - 10250
- Database (facoltativo)
    - Il database predefinito utilizzato per la connessione
- SSL = true (obbligatorio)

Si consideri ad esempio account visualizzate le informazioni di stringa di connessione sopra.  Una stringa di connessione valido è:
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>La connessione con il driver c# per MongoDB
Come già detto, tutti gli account DocumentDB richiedono l'autenticazione e la comunicazione sicura tramite SSL. Mentre il formato URI stringa di connessione MongoDB supporta ssl = parametro stringa query true, uso con MongoDB c# driver richiede l'uso dell'oggetto MongoClientSettings durante la creazione di un MongoClient.  In base alle informazioni di account in precedenza, il frammento di codice seguenti viene illustrato come connettersi all'account e lavorare con il database di "Attività".

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Passaggi successivi


- Informazioni su come [utilizzare MongoChef](documentdb-mongodb-mongochef.md) con un account DocumentDB con protocollo di supporto per MongoDB.
- Esplorare DocumentDB con protocollo per MongoDB [esempi](documentdb-mongodb-samples.md).

 
