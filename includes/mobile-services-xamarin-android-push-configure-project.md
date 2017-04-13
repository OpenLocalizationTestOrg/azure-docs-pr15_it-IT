
1. Nella visualizzazione soluzione (o **Esplora soluzioni** in Visual Studio), fare clic sulla cartella **componenti** , fare clic su **Guida più componenti...**, eseguire una ricerca per il componente **Client di messaggistica di Google Cloud** e aggiungerlo al progetto.

2. Aprire il file di progetto ToDoActivity.cs e aggiungere la seguente istruzione alla classe:

        using Gcm.Client;

3. In classe **ToDoActivity** aggiungere il nuovo codice seguente: 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    In questo modo è accedere all'istanza client mobile dal processo di push gestore del servizio.

4.  Aggiungere il codice seguente al metodo **OnCreate** dopo la creazione di **MobileServiceClient** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Il **ToDoActivity** a questo punto è pronto per l'aggiunta di notifiche push.