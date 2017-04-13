<properties
    pageTitle="Applicazione di linguaggio complesse su una macchina virtuale | Microsoft Azure"
    description="Informazioni su come creare una macchina virtuale Azure che esegue un'applicazione di linguaggio complesse che è possibile monitorare da un'altra applicazione di linguaggio."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Esecuzione di un'attività complesse in linguaggio in una macchina virtuale

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Con Azure, è possibile utilizzare una macchina virtuale per gestire attività complesse. Ad esempio, una macchina virtuale possa gestire le attività e recapitare risultati ai computer client o applicazioni mobili. Dopo aver letto in questo articolo, è necessario conoscere le procedure creare una macchina virtuale che esegue un'applicazione di linguaggio complesse che è possibile monitorare da un'altra applicazione di linguaggio.

In questa esercitazione si presuppone scoprire come creare applicazioni console Java, importare raccolte all'applicazione Java e può generare un archivio di linguaggio (VASO). Nessuna conoscenza di Microsoft Azure.

Si apprenderà:

* Come creare una macchina virtuale con un linguaggio Development Kit (JDK) già installato.
* Come eseguire l'accesso remoto al computer virtuale.
* Come creare uno spazio dei nomi di servizio bus.
* Come creare un'applicazione Java che effettua un'attività complesse.
* Come creare un'applicazione Java che esegue il monitoraggio dello stato di avanzamento dell'attività complesse.
* Come eseguire le applicazioni di linguaggio.
* Come interrompere le applicazioni di linguaggio.

In questa esercitazione verrà utilizzato il problema viaggiatore per l'attività complesse. Di seguito è riportato un esempio di applicazione Java esecuzione dell'attività complesse.

![Risolutore viaggiatore problema][solver_output]

Di seguito è riportato un esempio di applicazione Java Monitoraggio attività complesse.

![Client viaggiatore problema][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Per creare una macchina virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**, fare clic su **calcolare**, fare clic su **macchina virtuale**e quindi fare clic su **Dalla raccolta**.
3. Nella finestra di dialogo **Seleziona immagine di macchina virtuale** selezionare **JDK 7 Windows Server 2012**.
Si noti che **JDK 6 Windows Server 2012** è disponibile nel caso in cui si dispone di applicazioni legacy che non sono ancora pronte per l'esecuzione in JDK 7.
4. Fare clic su **Avanti**.
4. Nella finestra di dialogo **configurazione macchina virtuale** :
    1. Specificare un nome per la macchina virtuale.
    2. Specificare la dimensione da utilizzare per la macchina virtuale.
    3. Immettere un nome per l'amministratore nel campo **Nome utente** . Tenere presente questo nome e la password che immessa successivamente, verranno utilizzati quando si accede in remoto alla macchina virtuale.
    4. Immettere una password nella casella **nuova password** e immettere nuovamente nella casella **Conferma** . Questa è la password dell'account di amministratore.
    5. Fare clic su **Avanti**.
5. Nella finestra successiva finestra di dialogo **configurazione macchina virtuale** :
    1. Per il **servizio Cloud**, usare il valore predefinito **Crea un nuovo servizio cloud**.
    2. Il valore di **nome DNS del servizio Cloud** deve essere univoco in cloudapp.net. Se necessario, modificare il valore in modo che Azure indica che sia univoco.
    2. Specificare l'area geografica, gruppo affinità o virtuali. Per motivi di questa esercitazione, specificare un'area, ad esempio **US ovest**.
    2. Per **Account di archiviazione**, selezionare **Usa un account di archiviazione generato automaticamente**.
    3. Per **Impostare la disponibilità**, selezionare **(nessuno)**.
    4. Fare clic su **Avanti**.
5. In finale nella finestra di dialogo **configurazione macchina virtuale** :
    1. Accettare le voci dell'endpoint.
    2. Fare clic su **completa**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Per accedere in remoto a macchina virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Fare clic su **macchine virtuali**.
3. Fare clic sul nome del computer virtuale che si desidera accedere a.
4. Fare clic su **Connetti**.
5. Seguire le istruzioni visualizzate in base alle esigenze per connettere la macchina virtuale. Quando viene richiesto il nome amministratore e la password, utilizzare i valori forniti quando è stata creata.

Si noti che la funzionalità di Bus di servizio Azure richiede Baltimora CyberTrust certificato deve essere installato come parte dell'archivio **cacerts** del JRE. Questo certificato vengono incluse automaticamente in linguaggio Runtime ambiente (JRE) utilizzato da questa esercitazione. Se non si dispone il certificato nell'archivio **cacerts** JRE, vedere [aggiunta di un certificato per l'archivio certificati di linguaggio CA] [ add_ca_cert] per informazioni sull'aggiunta di essa (nonché informazioni sulla visualizzazione i certificati nell'archivio cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Come creare uno spazio dei nomi di servizio bus

Per iniziare a utilizzare code Bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio. Uno spazio dei nomi del servizio fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi di servizio:

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com).
2.  Nel riquadro di spostamento in basso a sinistra del portale di classica Azure fare clic su **Bus di servizio, il controllo di accesso e memorizzazione nella cache**.
3.  Nel riquadro superiore sinistro del portale di classica Azure, fare clic sul nodo **Bus di servizio** e quindi fare clic sul pulsante **Nuovo** .  
    ![Schermata di nodo Bus di servizio][svc_bus_node]
4.  Nella finestra di dialogo **Crea un nuovo Namespace servizio** immettere un **Namespace**e quindi per assicurarsi che sia univoco, fare clic sul pulsante **Verifica disponibilità** .  
    ![Creare una schermata nuovi Namespace][create_namespace]
5.  Al termine disponibili accertarsi che il nome dello spazio dei nomi, selezionare il paese o area geografica in cui devono essere ospitati dello spazio dei nomi e quindi fare clic sul pulsante **Crea Namespace** .  

    Spazio dei nomi che è stato creato verrà visualizzato nel portale di classica Azure e richiede molto tempo per l'attivazione. Attendere fino a ottenere lo stato **attivo** prima di procedere con il passaggio successivo.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Ottenere le credenziali di gestione predefinito per lo spazio dei nomi

Per eseguire operazioni di gestione, ad esempio la creazione di una coda nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione dei nomi.

1.  Nel riquadro di spostamento sinistra, fare clic su nodo **Bus di servizio** per visualizzare l'elenco dei nomi disponibili.
    ![Schermata di spazi dei nomi disponibile][avail_namespaces]
2.  Selezionare lo spazio dei nomi che appena creato dall'elenco visualizzato.
    ![Schermata elenco Namespace][namespace_list]
3.  Riquadro attività **proprietà** destro Elenca le proprietà per il nuovo spazio dei nomi.
    ![Proprietà riquadro schermata][properties_pane]
4.  Il **Tasto predefinito** è nascosta. Fare clic sul pulsante di **visualizzazione** per visualizzare le credenziali di sicurezza.
    ![Schermata di chiave predefinita][default_key]
5.  Come si utilizzeranno queste informazioni riportata di seguito per eseguire operazioni con lo spazio dei nomi, prendere nota dell' **Emittente predefinito** e la **Chiave predefinita** .

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Come creare un'applicazione Java che effettua un'attività complesse

1. Nel computer di sviluppo (che non può essere la macchina virtuale creato), scaricare [Azure SDK per Java](https://azure.microsoft.com/develop/java/).
2. Creare un'applicazione di console di linguaggio mediante il codice di esempio alla fine di questa sezione. In questa esercitazione si userà **TSPSolver.java** come nome del file Java. Modificare il **il\_servizio\_bus\_spazio dei nomi**, **il\_servizio\_bus\_proprietario**, e **il\_servizio\_bus\_chiave** i segnaposto per utilizzare il servizio bus **dello spazio dei nomi**, **Emittente predefinito** e i valori **Predefiniti chiave** , rispettivamente.
3. Dopo la codifica, esportare l'applicazione in un archivio di linguaggio eseguibile (VASO) e creare un pacchetto raccolte necessari in VASO generato. In questa esercitazione si userà **TSPSolver.jar** come nome VASO generato.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Come creare un'applicazione Java che esegue il monitoraggio dello stato di avanzamento dell'attività complesse

1. Nel computer di sviluppo, creare un'applicazione di console Java utilizza il codice di esempio alla fine di questa sezione. In questa esercitazione si userà **TSPClient.java** come nome del file Java. Come mostrato in precedenza, modificare il **il\_servizio\_bus\_spazio dei nomi**, **il\_servizio\_bus\_proprietario**, e **il\_servizio\_bus\_chiave** i segnaposto per utilizzare il servizio bus **dello spazio dei nomi**, **Emittente predefinito** e i valori **Predefiniti chiave** , rispettivamente.
2. Esportare l'applicazione in un VASO eseguibile e creare un pacchetto raccolte necessari in VASO generato. In questa esercitazione si userà **TSPClient.jar** come nome VASO generato.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Come eseguire applicazioni Java
Eseguire l'applicazione complesse, prima di tutto creare coda, per risolvere il problema di Saleseman in viaggio, che aggiungerà migliore route corrente alla coda bus del servizio. Mentre l'applicazione complesse è in esecuzione, o in un secondo momento, eseguire il client per visualizzare i risultati dalla coda bus del servizio.

### <a name="to-run-the-compute-intensive-application"></a>Per eseguire l'applicazione complesse

1. Accedere al computer virtuale.
2. Creare una cartella in cui si eseguirà l'applicazione. Ad esempio **c:\TSP**.
3. Copiare **TSPSolver.jar** **c:\TSP**
4. Creare un file denominato **c:\TSP\cities.txt** con il seguente contenuto.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. Al prompt dei comandi, passare alla c:\TSP.
6. Verificare che nella cartella bin di JRE nella variabile di ambiente PATH.
7. È necessario creare la coda bus servizio prima di eseguire le permutazioni Risolutore TSP. Eseguire il seguente comando per creare la coda bus di servizio.

        java -jar TSPSolver.jar createqueue

8. Una volta creato il coda, è possibile eseguire le permutazioni Risolutore TSP. Ad esempio, il seguente comando per l'esecuzione del Risolutore per 8 città.

        java -jar TSPSolver.jar 8

 Se non si specifica un numero, verrà eseguita per 10 città. Come il Risolutore trova route più brevi corrente, li aggiunge alla coda.

> [AZURE.NOTE]
> Maggiore è il numero che specifica più a lungo eseguirà il Risolutore. In esecuzione, ad esempio, per 14 città potrebbe richiedere alcuni minuti ed esecuzione per 15 città potrebbe richiedere alcune ore. Aumento a 16 o più città potrebbe causare giorni di runtime (eventualmente settimane, mesi e anni). Ciò è dovuto rapido aumenta il numero delle permutazioni valutato per il Risolutore come il numero di città aumenta.

### <a name="how-to-run-the-monitoring-client-application"></a>Come eseguire l'applicazione client monitoraggio
1. Accedere al computer in cui si eseguirà l'applicazione client. Questa operazione non è necessario essere nello stesso computer che esegue l'applicazione **TSPSolver** , anche se può essere.
2. Creare una cartella in cui si eseguirà l'applicazione. Ad esempio **c:\TSP**.
3. Copiare **TSPClient.jar** **c:\TSP**
4. Verificare che nella cartella bin di JRE nella variabile di ambiente PATH.
5. Al prompt dei comandi, passare alla c:\TSP.
6. Eseguire il comando seguente.

        java -jar TSPClient.jar

    Facoltativamente, è possibile specificare il numero di minuti di inattività tra controllare la coda passando un argomento della riga di comando. Il periodo di inattività predefinito per controllare la coda è 3 minuti, che viene utilizzato se viene passato alcun argomento della riga di comando per **TSPClient**. Se si desidera utilizzare un valore diverso per l'intervallo di sospensione, ad esempio un minuto, eseguire il comando seguente.

        java -jar TSPClient.jar 1

    Il client rimarrà attivo finché non visualizzato un messaggio di coda di "Completare". Si noti che se si esegue più occorrenze del Risolutore senza eseguire il client, potrebbe essere necessario eseguire il client più volte per svuotare completamente la coda. In alternativa, è possibile eliminare la coda e crearne uno nuovo. Per eliminare la coda, eseguire il seguente comando **TSPSolver** (non **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    Il Risolutore verrà eseguito fino al termine della esaminare tutte le route.

## <a name="how-to-stop-the-java-applications"></a>Come interrompere le applicazioni Java
Per il Risolutore e le applicazioni client, è possibile premere **Ctrl + C** per uscire se si vuole terminare prima del normale completamento.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
