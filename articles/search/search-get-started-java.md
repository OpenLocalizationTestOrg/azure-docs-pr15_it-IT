<properties
    pageTitle="Guida introduttiva di Azure eseguire ricerche in linguaggio | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Come creare un'applicazione di ricerca cloud ospitato su Azure utilizzando Java come il linguaggio di programmazione."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>Guida introduttiva a cerca di Azure Java
> [AZURE.SELECTOR]
- [Portale](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Informazioni su come creare un'applicazione di ricerca linguaggio personalizzata che usa la ricerca di Azure per l'esperienza di ricerca. In questa esercitazione utilizza l' [API REST servizio di ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per creare gli oggetti e operazioni in questo esercizio.

Per eseguire questo esempio, è necessario disporre di un servizio di ricerca di Azure, è possibile accedere nel [Portale di Azure](https://portal.azure.com). Per istruzioni dettagliate, vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) .

Per creare e testare in questo esempio è stato utilizzato il software seguente:

- [Eclipse IDE per sviluppatori Java Edition](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Assicurarsi di scaricare la versione Edition. Uno dei passaggi verifica richiede una funzionalità che è disponibili solo in questa edizione.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Informazioni su come i dati

Questa applicazione di esempio utilizzati i dati i [Servizi geologiche negli Stati Uniti (soltanto)](http://geonames.usgs.gov/domestic/download_data.htm), filtrata in stato di Rhode isola di ridurre le dimensioni del set di dati. Si userà questi dati per creare un'applicazione di ricerca che restituisce edifici punti di riferimento, ad esempio scuole e ospedali, nonché geologiche caratteristiche quali flussi e laghi Summit.

In questa applicazione, il programma **SearchServlet.java** consente di creare e carica dell'indice utilizzando un costrutto [indicizzatore](https://msdn.microsoft.com/library/azure/dn798918.aspx) recupero il set di dati soltanto filtrato da un Database di SQL Azure pubblico. Credenziali predefinite e informazioni di connessione all'origine dati online sono incluse nel codice del programma. In termini di accesso ai dati, non è necessario alcun ulteriori operazioni di configurazione.

> [AZURE.NOTE] È applicato un filtro in questo set di dati per rimanere nel limite di 10.000 documenti del livello prezzo gratuito. Se si utilizza il livello standard, non si applica questo limite ed è possibile modificare il codice per usare un set di dati più grande. Per informazioni dettagliate sulla capacità per ogni livello prezzo, vedere [limiti e vincoli](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Informazioni sui file di programma

L'elenco seguente vengono descritti i file che riguardano in questo esempio.

- Search.jsp: Fornisce l'interfaccia utente
- SearchServlet.java: Fornisce i metodi (simili a un controller di MVC)
- SearchServiceClient.java: Le richieste di punti di manipolazione di HTTP
- SearchServiceHelper.java: Una classe di supporto che fornisce metodi statici
- Document.Java: Fornisce il modello di dati
- config.Properties: imposta la chiave dell'api e l'URL del servizio di ricerca
- POM.XML: Una dipendenza Maven

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trovare il nome del servizio e la chiave dell'api del servizio di ricerca di Azure

Tutte le chiamate API REST in Azure ricerca è necessario disporre l'URL del servizio e un tasto di api. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento, fare clic su **servizio di ricerca** per elencare tutti i servizi di ricerca di Azure viene completato il provisioning per l'abbonamento.
3. Selezionare il servizio che si desidera utilizzare.
4. Nel dashboard di servizio, si noterà riquadri le informazioni essenziali oltre che l'icona per l'accesso alle chiavi amministratore.

    ![][3]

5. Copiare l'URL del servizio e un tasto di amministratore. Saranno necessari in un secondo momento, quando vengono aggiunti al file **config.properties** .

## <a name="download-the-sample-files"></a>Scaricare i file di esempio

1. Passare [all'AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) Github.

2. Fare clic su **Scarica ZIP**, salvare il file con estensione zip su disco e quindi estrarre tutti i file che contiene. Valutare la possibilità di estrazione dei file nell'area di lavoro linguaggio per rendere più semplice trovare il progetto in un secondo momento.

3. I file di esempio sono di sola lettura. Pulsante destro del mouse proprietà della cartella e deselezionare l'attributo sola lettura.

Tutte le modifiche successive e istruzioni fase saranno per i file in questa cartella.  

## <a name="import-project"></a>Importa progetto

1. In Eclisse, scegliere **File** > **importazione** > **generali** > **Progetti esistenti nell'area di lavoro**.

    ![][4]

2. In **Selezionare la directory radice**, passare alla cartella contenente i file di esempio. Selezionare la cartella che contiene la cartella .project. Il progetto dovrebbero essere visualizzate nell'elenco **progetti** come un elemento selezionato.

    ![][12]

3. Fare clic su **Fine**.

4. Utilizzare **Gestione progetti** per visualizzare e modificare i file. Se non è già aperto, fare clic su **finestra** > **Mostra visualizzazione** > **Gestione progetti** o usare il collegamento per aprirlo.

## <a name="configure-the-service-url-and-api-key"></a>Configurare l'URL del servizio e api chiave

1. In **Gestione progetti**, fare doppio clic su **config.properties** per modificare le impostazioni di configurazione contenente il nome del server e api chiave.

2. Consultare la procedura descritta in precedenza in questo articolo, in cui trovare l'URL del servizio e chiave api nel [Portale di Azure](https://portal.azure.com), per ottenere i valori che ora entrerà in **config.properties**.

3. In **config.properties**, sostituire "Api chiave" con la chiave dell'api per il servizio. Successivamente, il nome di servizio (il primo componente di http://servicename.search.windows.net URL) sostituisce "nome del servizio" nello stesso file.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurare gli ambienti di progetto, compilazione e runtime

1. In Eclisse in Esplora progetti, fare clic sul progetto > **proprietà** > **Facet di progetto**.

2. Selezionare **modulo Web dinamico**, **Java**e **JavaScript**.

    ![][6]

3. Fare clic su **Applica**.

4. **Finestra**seleziona > **Preferenze** > **Server** > **ambienti Runtime** > **Add.**.

5. Espandere Apache e selezionare la versione del server Apache Tomcat installati in precedenza. Sul sistema, è installata la versione 8.

    ![][7]

6. Nella pagina successiva, specificare la directory di installazione Tomcat. In un computer Windows, questo sarà probabilmente c:\Programmi\Microsoft Files\Apache Software Foundation\Tomcat *versione*.

6. Fare clic su **Fine**.

7. **Finestra**seleziona > **Preferenze** > **Java** > **installato JREs** > **aggiungere**.

8. In **Aggiungi JRE**, selezionare **Standard macchine Virtuali**.

10. Fare clic su **Avanti**.

11. Nella definizione JRE, in JRE home, fare clic su **Directory**.

12. Passare al **File di programma** > **Java** e selezionare JDK installati in precedenza. È importante selezionare JDK come JRE.

13. Scegliere **JDK**JREs installato. Le impostazioni dovrebbe essere simile alla schermata seguente.

    ![][9]

14. Facoltativamente, selezionare **finestra** > **Browser Web** > **Internet Explorer** per aprire l'applicazione in una finestra del browser esterno. Usare un browser esterno consente di migliorare l'esperienza dell'applicazione Web.

    ![][8]

Le attività di configurazione completata. Successivamente, si verrà creare ed eseguire il progetto.

## <a name="build-the-project"></a>Compilare il progetto

1. In Esplora progetti destro del mouse sul nome del progetto e scegliere **Esegui come** > **Maven creare …** per configurare il progetto.

    ![][10]

8. In Modifica configurazione, obiettivi, digitare "installazione" e quindi fare clic su **Esegui**.

Messaggi di stato vengono restituiti alla finestra della console. Vedere Creare successo che indica il progetto compilato senza errori.

## <a name="run-the-app"></a>Eseguire l'app

In quest'ultimo passaggio, si eseguirà l'applicazione in un ambiente di runtime del server locale.

Se ancora non è stato specificato un ambiente di runtime del server in Eclisse, sarà necessario farlo.

1. In Esplora progetti espandere **DatiWeb**.

5. Pulsante destro del mouse **Search.jsp** > **eseguire come** > **eseguire sul Server**. Selezionare il server Apache Tomcat e quindi fare clic su **Esegui**.

> [AZURE.TIP] Se un'area di lavoro predefinita non è stato utilizzato per l'archiviazione del progetto, è necessario modificare la **Configurazione di esecuzione** in modo che punti al percorso del progetto per evitare errori di avvio server. In Esplora progetti rapida **Search.jsp** > **Esegui come** > **Eseguire configurazioni**. Selezionare il server Apache Tomcat. Fare clic su **argomenti**. Fare clic su **area di lavoro** o **File System** per impostare la cartella contenente il progetto.

Quando si esegue l'applicazione, verrà visualizzato una finestra del browser, fornire una casella di ricerca per l'immissione di termini.

Attendere circa un minuto prima di fare clic su **Cerca** per assegnare l'orario di servizio per creare e caricare l'indice. Se viene visualizzato un errore 404 HTTP, è sufficiente attendere leggermente più prima di riprovare.

## <a name="search-on-usgs-data"></a>Cercare dati soltanto

Il set di dati soltanto include record relativi a stati del Rhode isola. Se si fa clic su **ricerca** su una casella di ricerca vuoto, si otterrà superiore 50 voci, che rappresenta il valore predefinito.

Immettere un termine di ricerca per ottenere il motore di ricerca è necessario passare. Provare a immettere un nome internazionali. "Roger Williams" è stato il primo gestore isola Rhode. Numerosi parco, edifici e scuole vengono denominate in quest'ultimo.

![][11]

È inoltre possibile tentare uno di questi termini:

- Pawtucket
- Pembroke
- oca + capo

## <a name="next-steps"></a>Passaggi successivi

Verrà visualizzata la prima esercitazione di Azure ricerca basato sul linguaggio e il set di dati soltanto. Nel tempo, è possibile estendere questa esercitazione per illustrare le funzionalità di ricerca che è consigliabile utilizzare le soluzioni personalizzate.

Se si dispone già di alcuni concetti di base di ricerca di Azure, è possibile utilizzare in questo esempio prefissati per un'ulteriore sperimentazione, ad esempio si aumenta la [pagina di ricerca](search-pagination-page-layout.md)o l'implementazione di [spostamento in base a facet](search-faceted-navigation.md). È anche possibile migliorare alla pagina di risultati della ricerca aggiungendo conteggi e il batch di documenti in modo che gli utenti possono spostarsi tra i risultati.

Nuovi utenti di Azure ricerca? È consigliabile tentando altre esercitazioni per sviluppare capire cosa è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare altre risorse. Visualizzare i collegamenti nella di [Video ed esercitazioni elenchi](search-video-demo-tutorial-list.md) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
