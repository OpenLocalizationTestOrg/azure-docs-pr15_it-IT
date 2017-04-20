## <a name="typical-output"></a>Output tipico

Di seguito è riportato un esempio dell'output scritti nel file di registro da esempio Hello World. Caratteri di nuova riga e scheda sono state aggiunte per migliorare la leggibilità:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Frammenti di codice

In questa sezione vengono illustrati alcuni elementi chiave del codice di esempio Hello World.

### <a name="gateway-creation"></a>Creazione di gateway

Gli sviluppatori devono scrivere il *processo di gateway*. Questo programma crea l'infrastruttura interna (broker), carica i moduli e tutto il contenuto viene impostato per funzionare correttamente. SDK include la funzione **Gateway_Create_From_JSON** che consentono di eseguire l'avvio di un gateway da un file JSON. Utilizzare la funzione **Gateway_Create_From_JSON** che è necessario passare al percorso in un file JSON che specifica i moduli per il caricamento. 

È possibile trovare il codice per il processo di gateway nell'esempio di Hello World in [Main. c] [ lnk-main-c] file. Per migliorare la leggibilità, il frammento di codice seguente viene illustrata una versione ridotta di codice del processo gateway. Questo programma consente di creare un gateway e quindi attende che all'utente di premere il tasto **INVIO** prima che elimina il gateway. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

Il file di impostazioni JSON contiene un elenco di moduli da caricare. Ogni modulo deve specificare r:

- **nome_modulo**: un nome univoco per il modulo.
- **module_path**: il percorso per la raccolta contenente il modulo. Per Linux si tratta di un file .so, in Windows si tratta di un file con estensione dll.
- **args**: le informazioni di configurazione è necessario il modulo.

Il file JSON contiene inoltre collegamenti tra i moduli che verranno passati al broker. Un collegamento ha due proprietà:
- **origine**: il nome di un modulo dalla `modules` sezione o "\*".
- **sink**: il nome di un modulo dal `modules` sezione

Ogni collegamento definisce una route di messaggio e la direzione. I messaggi da modulo `source` devono essere inviati al modulo `sink`. Il `source` può essere impostato su "\*", che indica che i messaggi da qualsiasi modulo verranno ricevuti da `sink`.

Nell'esempio seguente viene illustrato il file di impostazioni JSON consente di configurare esempio Hello World su Linux. Tutti i messaggi generati dal modulo `hello_world` verranno utilizzati dal modulo `logger`. Indica se un modulo richiede che un argomento dipende dalla progettazione del modulo. In questo esempio, il modulo logger accetta un argomento è il percorso del file di output e il modulo Hello World non accetta argomenti:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Pubblicazione dei messaggi di Hello World modulo

È possibile trovare il codice utilizzato dal modulo di "hello world" per pubblicare i messaggi in ['hello_world.c'] [ lnk-helloworld-c] file. Il frammento di codice seguente viene illustrata una versione modificata con i commenti aggiuntivi e alcuni codice rimosso per migliorare la leggibilità di gestione degli errori:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Elaborazione dei messaggi di Hello World modulo

Il modulo Hello World mai deve elaborare tutti i messaggi che altre funzionalità di pubblicazione al broker. In questo modo implementazione del callback messaggio del modulo Hello World una funzione di alcuna operazione.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Messaggio modulo logger di pubblicazione e l'elaborazione

Il modulo Logger riceve messaggi da Service broker e li scrive un file. Non vengono pubblicati i messaggi. Di conseguenza, il codice del modulo logger mai chiama la funzione **Broker_Publish** .

La funzione **Logger_Recieve** in [logger.c] [ lnk-logger-c] file viene richiamata broker richiama per recapitare i messaggi al modulo logger. Il frammento di codice seguente viene illustrata una versione modificata con i commenti aggiuntivi e alcuni codice rimossa per migliorare la leggibilità gestione degli errori:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come utilizzare il SDK di Gateway IoT, vedere gli argomenti seguenti:

- [IoT Gateway SDK-inviare messaggi dispositivo al cloud con un dispositivo simulato utilizza Linux][lnk-gateway-simulated].
- [Azure IoT Gateway SDK] [ lnk-gateway-sdk] su GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md