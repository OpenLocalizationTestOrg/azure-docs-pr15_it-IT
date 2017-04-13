<properties
    pageTitle="Come utilizzare la libreria di Client App Mobile Android"
    description="Informazioni su come usare Android client SDK per App Mobile Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Come utilizzare la libreria Android client per le App per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida viene illustrato come utilizzare il client Android SDK per le App per dispositivi mobili per implementare scenari comuni, ad esempio:

- Ricerca di dati (inserimento, aggiornamento ed eliminazione).
- Autenticazione.
- Gestione degli errori.
- Personalizzazione del client. 

Viene eseguita anche un approfondito. HTML comuni client usati in App per dispositivi mobili più.

Questa guida è incentrata su SDK Android lato client.  Per altre informazioni sul SDK sul lato server per le App per dispositivi mobili, vedere [lavorare con back-end .NET SDK] [ 10] o [su come utilizzare il back-end Node SDK][11].

## <a name="reference-documentation"></a>Documentazione di riferimento

È possibile trovare il [riferimento all'API Javadocs] [ 12] per la raccolta di client Android su GitHub.

## <a name="supported-platforms"></a>Piattaforme supportate

Azure Mobile App Android SDK supporta i livelli di API 19 a 24 (KitKat tramite Nougat).  

L'autenticazione "server del flusso di cassa" utilizza una visualizzazione Web per l'interfaccia utente presentato. Se il dispositivo non è in grado di presentare un UI WebView, altri metodi di autenticazione sono necessarie di fuori dell'ambito del prodotto.  Questo SDK non è adatto per tipo di controllo o dispositivi con restrizioni.

## <a name="setup-and-prerequisites"></a>Il programma di installazione e i prerequisiti

Completare l'esercitazione [Guida introduttiva di App Mobile](app-service-mobile-android-get-started.md) .  Questa operazione garantisce sono stati soddisfatti tutti i prerequisiti per lo sviluppo di App Mobile Azure.  La Guida introduttiva consente inoltre di configurare l'account e creare il primo App Mobile di back-end.

Se si decide di non completare Esercitazione rapida, completare le attività seguenti:

- [creare un back-end App Mobile] [ 13] da utilizzare con l'app di Android.
- Studio Android, [aggiornare i file di compilazione Gradle](#gradle-build).
- [Attivare l'autorizzazione di internet](#enable-internet).

###<a name="gradle-build"></a>Aggiornare il file di compilazione Gradle

Modificare entrambi i file **build.gradle** :

1. Aggiungere il codice del file di *progetto livello **build.gradle** all'interno del tag *buildscript* * :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Aggiungere il codice del file di livello **build.gradle** *app modulo* all'interno del tag *dipendenze* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    La versione più recente è al momento 3.1.0. Le versioni supportate sono elencate [qui][14].

###<a name="enable-internet"></a>Attivare l'autorizzazione di internet

Per accedere a Azure, l'app è necessario disporre dell'autorizzazione di INTERNET abilitata. Se non è già è attivata, aggiungere la riga di codice seguente nel file **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Nozioni di base analisi approfondita

In questa sezione vengono illustrate alcune del codice nell'app Guida introduttiva relativa all'uso App Mobile Azure.  

###<a name="data-object"></a>Definire le classi dati client

Per accedere ai dati di tabelle di SQL Azure, definire le classi di dati client che corrispondono alle tabelle back-end App Mobile. Esempi in questo argomento si supponga di creare una tabella denominata **ToDoItem**, che include le colonne seguenti:

- ID
- testo
- completare

Corrispondente digitato oggetto lato client:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

Il codice risiede in un file denominato **ToDoItem.java**.

Se la tabella di SQL Azure include più colonne, è necessario aggiungere il campo corrispondente a questa classe.  Ad esempio, se la DTO (dati trasferimento oggetto) ha una colonna di priorità integer, quindi è possibile aggiungere questo campo insieme i metodi di richiamo e di impostazione:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Per informazioni su come creare tabelle aggiuntive nel back-end App Mobile, vedere [procedura: definire un controller di tabella] [ 15] (.NET back-end) o [definire le tabelle utilizzando uno Schema dinamico] [ 16] (Node back-end). Per un back-end Node, è anche possibile utilizzare l'impostazione di **facile tabelle** nel [portale di Azure].

###<a name="create-client"></a>Procedura: creare il contesto di client

Questo codice crea l'oggetto **MobileServiceClient** che viene utilizzato per accedere il back-end App Mobile. Il codice viene inserito `onCreate` metodo della classe **attività** specificata in *AndroidManifest.xml* come un'azione **principale** e **pulsante di visualizzazione della** categoria. Nel codice Guida introduttiva passano nel file **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

In questo codice, sostituire `MobileAppUrl` con l'URL del back-end App Mobile, che si trova nel [portale di Azure] in e il per il back-end App Mobile. Per questa riga di codice per la compilazione, è necessario aggiungere l'istruzione **importare** :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Procedura: creare un riferimento di tabella

Il modo più semplice per eseguire una query o modificare dati in back-end è tramite *digitato modello di programmazione*, poiché Java è un linguaggio fortemente. Questo modello consente di continua JSON serializzazione e deserializzazione utilizzando [gson] [ 3] raccolta quando si inviano dati tra gli oggetti client e le tabelle in back-end SQL Azure.

Per accedere a una tabella, prima di tutto creare una [MobileServiceTable] [ 8] oggetto chiamando il **getTable** metodo su [MobileServiceClient][9].  Questo metodo prevede due overload:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Nel codice seguente **mClient** è un riferimento all'oggetto MobileServiceClient.  Il primo overload viene utilizzato in cui il nome della classe e il nome della tabella sono gli stessi e quella utilizzata nella Guida rapida:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

Il secondo overload viene utilizzato il nome della tabella è la differenza tra il nome della classe: il primo parametro è il nome della tabella.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Procedura: associare i dati per l'interfaccia utente

Associazione dati prevede tre componenti:

- L'origine dati
- Il layout dello schermo
- La scheda che collega le due.

Nel codice di esempio, verranno restituiti i dati dalla tabella Mobile App SQL Azure **ToDoItem** in una matrice. Questa attività è un modello comune per le applicazioni di dati.  Query di database spesso restituire un insieme di righe in cui il client ottiene in un elenco o una matrice. In questo esempio la matrice è l'origine dati.

Il codice specifica un layout dello schermo che definisce la visualizzazione dei dati che viene visualizzata sul dispositivo.  I due sono associati insieme a un adattatore, che in questo codice è un'estensione del **ArrayAdapter&lt;ToDoItem&gt; ** classe.

#### <a name="layout"></a>Procedura: definire il Layout

Il layout è definito da molti frammenti di codice XML. Dato un layout esistente, il codice seguente rappresenta **ListView** vogliamo per popolare con i dati del server.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Nell'esempio precedente, l'attributo *listitem* specifica l'id del layout per una singola riga nell'elenco. Questo codice specifica una casella di controllo e il testo associato e viene creato una sola volta per ogni elemento nell'elenco. Questo layout non viene visualizzato il campo **id** e layout più complessi specificare campi aggiuntivi nella visualizzazione. Questo codice si trova il file **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Procedura: definire la scheda

Poiché l'origine dati della visualizzazione è una matrice di **ToDoItem**è sottoclasse la scheda di un **ArrayAdapter&lt;ToDoItem&gt; ** classe. Questa sottoclasse genera una visualizzazione per ogni **ToDoItem** utilizzando il layout **row_list_to_do** .

Nel codice è definire la classe seguente è un'estensione del **ArrayAdapter&lt;E&gt; ** classe:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Ignorare il metodo di **getView** schede. Per esempio:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Viene creata un'istanza di questa classe le attività nel modo seguente:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Il secondo parametro al costruttore ToDoItemAdapter è un riferimento al layout. È ora possibile creare **ListView** e assegnare la scheda a **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>La struttura dell'API

Operazioni con tabelle App mobile e le chiamate API personalizzate sono asincrone. Utilizzare gli oggetti [futuro] e [AsyncTask] per i metodi asincroni relative query, inserimenti, aggiornamenti ed eliminazioni. Uso ritardo, è facile eseguire più operazioni su un thread in background senza necessità di occuparsi di più callback annidate.

Esaminare il file **ToDoActivity.java** nel progetto Guida introduttiva Android dal [portale di Azure] per un esempio.

#### <a name="use-adapter"></a>Procedura: utilizzare la scheda

A questo punto si è pronti a utilizzare l'associazione dati. Il codice seguente viene illustrato come ottenere gli elementi nella tabella e riempimento scheda locale con gli elementi restituiti.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Chiamare la scheda ogni volta che si modifica la tabella **ToDoItem** . Poiché le modifiche vengono eseguite in una base di un record, gestire un'unica riga invece di una raccolta. Quando si inserisce un elemento, chiamare il metodo **add** sulla scheda; Quando si elimina, chiamare il metodo **remove** .

##<a name="querying"></a>Procedura: eseguire Query di dati dal back-end App Mobile

In questa sezione viene descritto come eseguire query nel back-end App Mobile, che include le seguenti operazioni:

- [Restituisce tutti gli elementi]
- [Filtrare i dati restituiti]
- [Ordinare i dati restituiti]
- [Restituire i dati nelle pagine]
- [Selezionare colonne specifiche]
- [Concatena i metodi di query](#chaining)

### <a name="showAll"></a>Procedura: restituire tutti gli elementi di una tabella

La query seguente restituisce tutti gli elementi nella tabella **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

La variabile di *risultati* restituisce il risultato della query come elenco.

### <a name="filtering"></a>Procedura: filtrare i dati restituiti.

Esecuzione della query seguente restituisce tutti gli elementi dalla tabella **ToDoItem** nel punto in cui **completamento** è uguale a **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** è il riferimento alla tabella servizio mobile creata in precedenza.

Definire un filtro utilizzando il metodo **nel punto in cui** nel riferimento di tabella. Il metodo **nel punto in cui** è seguito da un metodo di **campo** seguito da un metodo che specifica il predicato logico. Possibili metodi predicati includono **eq** (uguale), **ne** (diverso da), **gt** (maggiore di), **grandi** (maggiore o uguale a), **lt** (minore), **le** (minore o uguale a). Questi metodi consentono di confrontare i campi numero e stringa a valori specifici.

È possibile filtrare le date. I metodi seguenti consentono di confrontare il campo di data intero o parti della data: **anno**, **mese**, **giorno**, **ora**, **minuto**e la **seconda**. Nell'esempio seguente aggiunge un filtro per gli elementi la cui *scadenza* è uguale a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

I metodi seguenti supportano filtri complessi in campi di tipo stringa: **startsWith**, **endsWith**, **concatena**, **sottostringa**, **indexOf**, **sostituire**, **toLower**, **toUpper**, **tagliare**e **lunghezza**. Nell'esempio seguente i filtri per le righe della tabella in cui la colonna di *testo* inizia con "PRI0".

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

I seguenti metodi di operatore sono supportati in campi numerici: **aggiungere**, **sub**, **mul**, **div**, **mod**, **planimetrie**, **ceiling**e **arrotondare**. Nell'esempio seguente i filtri per righe della tabella in cui la **durata** è un numero pari.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

È possibile combinare predicati con questi metodi logici: **e**, **o** e **non**. Nell'esempio seguente combina due degli esempi precedenti.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Raggruppare e annidare gli operatori logici:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Per informazioni più dettagliate ed esempi di filtro, vedere [Introduzione di sfruttare il modello di query client Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Procedura: ordinare i dati restituiti.

Il codice seguente restituisce che tutti gli elementi da una tabella di **ToDoItems** ordinamento crescente in base al campo di *testo* . *mToDoTable* è il riferimento alla tabella back-end creato in precedenza:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Il primo parametro del metodo **orderBy** è una stringa uguale al nome del campo in cui ordinare. Il secondo parametro utilizza l'enumerazione **QueryOrder** per specificare l'eventuale ordinamento crescente o decrescente.  Se si filtra utilizzando il metodo ***nel punto in cui*** , il metodo ***nel punto in cui*** deve essere richiamato prima del metodo ***orderBy*** .

### <a name="paging"></a>Procedura: restituire i dati nelle pagine

Nel primo esempio viene illustrato come selezionare i primi cinque elementi da una tabella. La query restituisce gli elementi da una tabella di **ToDoItems**. **mToDoTable** è il riferimento alla tabella back-end creato in precedenza:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Ecco una query che ignora le prime cinque voci e quindi restituisce i cinque successivi:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Procedura: selezionare colonne specifiche

Il codice seguente viene illustrato come restituire tutti gli elementi di una tabella di **ToDoItems**, ma vengono visualizzati solo i campi di **completamento** e **testo** . **mToDoTable** è il riferimento alla tabella back-end creata in precedenza.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

I parametri per la funzione di selezione sono i nomi di stringa delle colonne della tabella che si desidera tornare.

Il metodo **select** deve seguire metodi come **posizione** e **orderBy**. Può essere seguito da metodi di spostamento come **parte superiore**.

### <a name="chaining"></a>Procedura: concatenare metodi di query

I metodi utilizzati per eseguire query back-end tabelle possono essere concatenati. Concatenazione metodi di query consente di selezionare colonne specifiche di righe filtrate che vengono ordinate e Paginate. È possibile creare filtri logici complessi.
Ogni metodo query restituisce un oggetto di Query. Per terminare la serie di metodi ed effettivamente eseguire la query, chiamare il metodo **execute** . Per esempio:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

I metodi di query concatenate devono essere ordinati come indicato di seguito:

1. Metodi di filtro (**in**).
2. Metodi di ordinamento (**orderBy**).
3. Metodi di selezione (**Seleziona**).
4. spostamento metodi (**ignorare** e **superiore**).

##<a name="inserting"></a>Procedura: inserire dati back-end

Creare un'istanza della classe *ToDoItem* e impostarne le proprietà.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Usare quindi **Insert ()** per inserire un oggetto:

    ToDoItem entity = mToDoTable.insert(item).get();

Corrispondenze entità restituito i dati inseriti nella tabella back-end, incluso l'ID e qualsiasi altro valore impostato su back-end.

Le tabelle App mobile richiedono una colonna di chiave primaria denominata **id**. Per impostazione predefinita, questa colonna è una stringa. Il valore predefinito della colonna ID è un GUID.  È possibile fornire altri valori univoci, ad esempio nomi utente o gli indirizzi di posta elettronica. Quando un valore di ID stringa non viene fornito un record inserito, back-end genera un nuovo GUID.

Valori degli ID stringa offrono i seguenti vantaggi:

+ Senza un round trip al database, è possono generare ID.
+ I record sono più semplice unire dati da tabelle diverse o nei database.
+ Valori degli ID migliore integrano con una logica dell'applicazione.

Valori degli ID stringa sono **necessarie** per il supporto di sincronizzazione offline.

##<a name="updating"></a>Procedura: aggiornare i dati in un'app per dispositivi mobili

Per aggiornare i dati in una tabella, passare il nuovo oggetto al metodo **Update ()** .

    mToDoTable.update(item).get();

In questo esempio, *elemento* di un riferimento a una riga della tabella *ToDoItem* cui è stata eseguita alcune modifiche apportate a essa.
La riga con lo stesso **id** viene aggiornata.

##<a name="deleting"></a>Procedura: eliminare i dati in un'app per dispositivi mobili

Il codice seguente viene illustrato come eliminare i dati da una tabella specificando l'oggetto di dati.

    mToDoTable.delete(item);

È inoltre possibile eliminare un elemento specificando il campo **id** di riga da eliminare.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Procedura: cercare informazioni su un elemento specifico

Cercare un elemento con un campo specifico **id** con il metodo **Cerca ()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Procedura: utilizzare i dati senza

Il modello di programmazione senza offre esatto controllo JSON.  Esistono alcuni scenari comuni in cui si desidera utilizzare un modello di programmazione senza. Ad esempio, se la tabella di back-end contiene molte colonne ed è sufficiente fare riferimento a un sottoinsieme delle colonne.  Il modello digitato è necessario definire tutte le App per dispositivi mobili colonne della tabella in una classe di dati.  

La maggior parte delle chiamate API per accedere ai dati sono simile alle chiamate programmazione digitate. La differenza principale è nel modello si richiama metodi sull'oggetto **MobileServiceJsonTable** , anziché l'oggetto **MobileServiceTable** .

### <a name="json_instance"></a>Procedura: creare un'istanza di una tabella senza

Simile al modello di digitato, è necessario innanzitutto ottenere un riferimento di tabella, ma in questo caso è un oggetto **MobileServicesJsonTable** . Ottenere il riferimento chiamando il **getTable** metodo in un'istanza del client:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Dopo aver creato un'istanza di **MobileServiceJsonTable**, ha praticamente la stessa API disponibile come con il modello di programmazione digitato. In alcuni casi, i metodi accettano un parametro senza anziché un parametro digitato.

### <a name="json_insert"></a>Procedura: inserire una tabella senza

Il codice seguente viene illustrato come eseguire un'istruzione insert. Il primo passaggio consiste nel creare una [JsonObject][1], che fa parte di [gson] [ 3] raccolta.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Usare quindi **Insert ()** per inserire l'oggetto senza nella tabella.

    mJsonToDoTable.insert(jsonItem).get();

Se è necessario ottenere l'ID dell'oggetto inserito, utilizzare il metodo **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Procedura: eliminare da una tabella senza

Il codice seguente viene illustrato come eliminare un'istanza, in questo caso la stessa istanza di **JsonObject** creata nell'esempio precedente *inserire* . Il codice è uguale a quello digitato, ma il metodo ha una firma diversa dal momento che fa riferimento a un **JsonObject**.

         mToDoTable.delete(jsonItem);

È inoltre possibile eliminare un'istanza direttamente utilizzando il relativo ID:

         mToDoTable.delete(ID);

### <a name="json_get"></a>Procedura: restituire tutte le righe da una tabella senza

Il codice seguente viene illustrato come recuperare un'intera tabella. Poiché si utilizza una tabella di JSON, è possibile recuperare in modo selettivo solo alcune delle colonne della tabella.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Lo stesso insieme di filtri, filtro e spostamento metodi disponibili per il modello digitato sono disponibili per il modello senza anche.

##<a name="custom-api"></a>Procedura: chiamare una API personalizzata

Un'API personalizzata consente di definire personalizzato endpoint che espongono funzionalità del server che non eseguire il mapping a un'istruzione insert, aggiornare, eliminare o operazione di lettura. Tramite un'API personalizzata, è consentito un maggiore controllo messaggistica, inclusa la lettura e impostazione delle intestazioni dei messaggi HTTP e la definizione di un formato corpo del messaggio diverso da JSON.

Da un client Android, il metodo **invokeApi** per chiamare l'endpoint API personalizzato. Nell'esempio seguente viene illustrato come chiamare un endpoint API denominato **completeAll**, che restituisce una classe insieme denominata **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

**InvokeApi** viene chiamato sul client, che consente di inviare una richiesta POST per la nuova API personalizzata. Il risultato restituito da API personalizzato verrà visualizzato in una finestra di dialogo messaggio sono presenti errori. Altre versioni di **invokeApi** consentono di se lo si desidera inviare un oggetto nel corpo della richiesta, specificare il metodo HTTP e inviare i parametri di query con la richiesta. Vengono fornite anche senza versioni di **invokeApi** .

##<a name="authentication"></a>Procedura: aggiungere l'autenticazione applicazione in uso.

Esercitazioni già descrivono in dettaglio come aggiungere queste caratteristiche.

Servizio di App supporta l' [autenticazione degli utenti app](app-service-mobile-android-get-started-users.md) con una vasta gamma di provider di identità esterni: Facebook, Google, Account Microsoft, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni nelle tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È anche possibile utilizzare l'identità degli utenti autenticati per implementare le regole di autorizzazione nel back-end.

Due flussi di autenticazione supportati: un flusso di **server** e un flusso di **client** . Il flusso relativo al server offre l'esperienza di autenticazione più semplice, come si basa sull'interfaccia di web provider di identità.  Nessun SDK aggiuntivi necessari per implementare l'autenticazione del flusso di server. Server flusso autenticazione non fornisce integrazione nel dispositivo mobile e consigliato solo per prova di scenari concetto.

Il flusso relativo al client consente l'integrazione più approfondita con funzionalità specifiche del dispositivo, ad esempio il single sign-on mentre si basa su SDK fornito dal provider di identità.  Ad esempio, è possibile integrare SDK Facebook nell'applicazione per dispositivi mobili.  Client mobili Scambia all'app di Facebook e conferma l'accesso prima di scambio di tornare all'app per dispositivi mobili.

Per abilitare l'autenticazione nell'app sono necessarie quattro passaggi:

- Registrare l'app per l'autenticazione con un provider di identità.
- Configurare il servizio di App back-end.
- Limitare le autorizzazioni di tabella per gli utenti autenticati solo in back-end servizio App.
- Aggiungere il codice di autenticazione all'app.

È possibile impostare le autorizzazioni nelle tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È anche possibile usare SID di un utente autenticato per modificare le richieste.  Per ulteriori informazioni, leggere [iniziare a utilizzare l'autenticazione] e la documentazione per la procedura di SDK Server.

### <a name="caching"></a>Procedura: aggiungere il codice di autenticazione all'app

Il codice seguente avvia un processo di accesso del flusso di server mediante il provider di Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Ottenere l'ID dell'utente ha eseguito l'accesso da un **MobileServiceUser** utilizzando il metodo **getUserId** . Per un esempio di utilizzo di ritardo di chiamare l'account di accesso asincrono API, vedere [Guida introduttiva di autenticazione].

### <a name="caching"></a>Procedura: memorizzare nella Cache token di autenticazione

La memorizzazione nella cache token di autenticazione è necessario memorizzare l'ID utente e token di autenticazione in locale nel dispositivo. La volta successiva che si avvia l'app, controllare la cache, e se sono presenti questi valori, è possibile ignorare il registro nella procedura e reidratarsi il client con i dati. Tuttavia questi dati sono riservati e devono essere memorizzata crittografato per la sicurezza in caso di furto di un telefono.

È possibile visualizzare un esempio completo della modalità di token di autenticazione cache nella [sezione token di autenticazione Cache][7].

Quando si tenta di utilizzare un token di scadenza, si riceve una risposta *401 non autorizzato* . È possibile gestire gli errori di autenticazione utilizzando i filtri.  Filtri intercettano le richieste di back-end servizio App. Il codice di filtro verifica la risposta per un 401, attiva la procedura di accesso e quindi riprende la richiesta che ha generato la 401. 

## <a name="adal"></a>Procedura: autenticazione degli utenti con Active Directory Authentication Library

È possibile utilizzare la libreria di autenticazione Active Directory (ADAL) per eseguire l'accesso agli utenti all'interno dell'applicazione con Azure Active Directory. Con un account di accesso client flusso spesso è preferibile all'utilizzo di `loginAsync()` metodi, poiché consente di mostrato un esempio dell'esperienza utente più nativo e per un'ulteriore personalizzazione.

1. Configurare il back-end di app per dispositivi mobili per l'accesso AAD seguendo l'esercitazione [come configurare il servizio di App per le credenziali Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Assicurarsi che completare il passaggio facoltativo di registrazione di un'applicazione client nativi.

2. Installare ADAL modificando il file build.gradle per includere le definizioni seguenti:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Aggiungere il codice riportato di seguito per l'applicazione, effettuare le seguenti sostituzioni:

* Sostituire **Qui di autorità di inserimento** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. Questo valore può essere copiato dalla scheda dominio di Azure Active Directory in [portal Azure classica].

* Sostituire **Inserisci-risorsa-ID-qui** con l'ID client per il back-end app per dispositivi mobili. È possibile ottenere l'ID client dalla scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.

* Sostituire **Inserisci-CLIENT-ID-qui** con l'ID client sono stati copiati dall'applicazione client nativi.

* Sostituire **Inserisci-reindirizzamento-URI qui** con endpoint _/.auth/login/done_ del sito, utilizzando lo schema HTTPS. Questo valore deve essere simile a _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Procedura: aggiungere la notifica push applicazione in uso.

È possibile [leggere una panoramica] [ 6] che descrive come hub di notifica di Microsoft Azure supporta una vasta gamma di notifiche push.  In [questa esercitazione][5], una notifica push viene inviata a tutti i dispositivi ogni volta che viene inserito un record.

## <a name="how-to-add-offline-sync-to-your-app"></a>Procedura: aggiungere l'app di sincronizzazione offline

Esercitazione rapida contiene codice di implementazione sincronizzazione offline. Cercare indicativo preceduto da commenti:

    // Offline Sync

Rimuovendo le righe di codice seguenti è possibile implementare sincronizzazione offline ed è possibile aggiungere codice simile a altro codice App Mobile.

##<a name="customizing"></a>Procedura: personalizzare il client

Esistono diversi modi per personalizzare il comportamento predefinito del client.

### <a name="headers"></a>Procedura: personalizzare le intestazioni di richiesta

Configurare un **ServiceFilter** per aggiungere un'intestazione HTTP personalizzato a ogni richiesta:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Procedura: personalizzare la serializzazione

Il client presuppone che i nomi di tabella, i nomi delle colonne e dati tipi in back-end tutti corrispondono esattamente gli oggetti di dati definiti nel client. È possibile che in alcuni casi perché i nomi di client e server potrebbero non corrispondono. Nello scenario in uso, è consigliabile eseguire i tipi di personalizzazioni seguenti:

- I nomi delle colonne nella tabella servizio App non corrispondono ai nomi in uso nel client.
- Utilizzare una tabella di servizio App che ha un nome diverso rispetto alla classe che esegue il mapping a nel client.
- Attivare l'iniziale maiuscola automatica delle proprietà.
- Aggiungere proprietà complesse a un oggetto.

### <a name="columns"></a>Procedura: eseguire il mapping di nomi di client e server diversi

Si supponga che il codice di client Java utilizza nomi di tipo Java standard per le proprietà di oggetto **ToDoItem** , ad esempio le proprietà seguenti:

- stringa. Estrai
- mText
- mComplete
- Durata

Serializzano i nomi dei clienti in nomi JSON che corrispondono ai nomi di colonna della tabella **ToDoItem** nel server. Il codice seguente utilizza [gson] [ 3] raccolta per annotare le proprietà:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Procedura: eseguire il mapping di nomi di tabella diverso tra il client e back-end

Mappare il nome della tabella client a un nome di tabella diversi servizi mobile utilizzando un override di [getTable()] [ 4] metodo:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Procedura: automatizzare i mapping di nomi di colonna

È possibile specificare una strategia di conversione che si applica a tutte le colonne tramite [gson] [ 3] API. La libreria client Android utilizza [gson] [ 3] dietro le quinte serializzare oggetti Java ai dati JSON prima che i dati vengono inviati al servizio App Azure.  Il codice seguente utilizza il metodo **setFieldNamingStrategy()** per impostare la strategia. In questo esempio viene eliminato il carattere iniziale (un "m") e quindi minuscole il carattere successivo, per ogni nome di campo. Ad esempio, essa viene attivata "Estrai" in "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Questo codice deve essere eseguito prima di utilizzare **MobileServiceClient**.

### <a name="complex"></a>Procedura: archiviare una proprietà oggetto o in una matrice in una tabella

Finora, esempi serializzazione hanno coinvolge i tipi di base, ad esempio interi e stringhe.  Tipi di base serializzano facilmente in JSON.  Se si desidera aggiungere un oggetto complesso non serializzazione automaticamente JSON, è necessario specificare il metodo di serializzazione JSON.  Per visualizzare un esempio di serializzazione JSON personalizzata, esaminare il post di blog [serializzazione personalizzazione mediante la raccolta di gson nel client Mobile Services Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Restituisce tutti gli elementi]: #showAll
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati nelle pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portale di Azure]: https://portal.azure.com
[Iniziare a utilizzare l'autenticazione]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Futuro]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html