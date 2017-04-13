<properties
   pageTitle="Connettere un dispositivo che utilizza C in Windows | Microsoft Azure"
   description="Descrive come collegare un dispositivo a usando un'applicazione scritta in C in esecuzione su Windows Azure IoT Suite preconfigurato remote monitoring soluzione."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Connettere il dispositivo per la soluzione preconfigurata monitoraggio remota (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Creare una soluzione di esempio C in Windows

La procedura seguente viene illustrato come utilizzare Visual Studio per creare un'applicazione client scritta in C che comunica con la soluzione monitoraggio remoto preconfigurato.

Creare un progetto di partenza in Visual Studio 2015 e aggiungere i pacchetti di NuGet IoT Hub dispositivo client:

1. In Visual Studio 2015, creare un'applicazione console C utilizzando il modello di Visual C++ **Applicazione Console Win32** . Nome del progetto **RMDevice**.

2. Nella pagina **Impostazioni di applicazioni** nella **Creazione guidata applicazione Win32**, assicurarsi che **applicazione Console** sia selezionata e deselezionare **intestazione precompilata** e **controllo sviluppo del ciclo di vita SDL (Security)**.

3. In **Esplora soluzioni**, eliminare i file stdafx, targetver.h e stdafx.

4. In **Esplora soluzioni**rinominare il file RMDevice.cpp RMDevice.c.

5. In **Esplora soluzioni**mouse sul progetto **RMDevice** e quindi fare clic su **Gestisci NuGet pacchetti**. Fare clic su **Sfoglia**, quindi cercare e installare i pacchetti NuGet seguenti nel progetto:

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. In **Esplora soluzioni**fare clic su progetto **RMDevice** e quindi fare clic su **proprietà** per aprire la finestra di dialogo **Pagine delle proprietà** del progetto. Per informazioni dettagliate, vedere [Impostazione delle proprietà dei progetti Visual C++][lnk-c-project-properties]. 

7. Fare clic sulla cartella **Linker** , quindi fare clic su pagina delle proprietà **Input** .

8. Aggiungere **crypt32.lib** alla proprietà **Dipendenze aggiuntive** . Fare clic su **OK** e quindi su **OK** nuovamente per salvare il progetto valori di proprietà.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Specificare il comportamento del dispositivo IoT Hub

Le raccolte di client IoT Hub utilizzano un modello per specificare il formato dei messaggi che sul dispositivo invia IoT Hub e i comandi che riceve dall'IoT Hub.

1. In Visual Studio, aprire il file RMDevice.c. Sostituire le attuali `#include` istruzioni con il codice seguente:

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Aggiungere le seguenti dichiarazioni di variabili dopo il `#include` istruzioni. Sostituire i valori di segnaposto [Id dispositivo] e [dispositivo chiave] con i valori per il dispositivo nel dashboard soluzione monitoraggio remoto. Utilizzare il nome host Hub IoT nel dashboard per sostituire [nome IoTHub]. Ad esempio, se il nome host Hub IoT **contoso.azure devices.net**, sostituire [nome IoTHub] con **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Aggiungere il codice seguente per definire il modello che consente di comunicare con IoT Hub. Questo modello consente di specificare che il dispositivo invia temperatura, temperatura esterna, umidità e un id dispositivo come telemetria. Il dispositivo invia anche metadati relativi al dispositivo hub IoT, incluso un elenco di comandi che supporta il dispositivo. Questo dispositivo risponde ai comandi **SetTemperature** e **SetHumidity**:

    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );

    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );

    DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementare il comportamento del dispositivo

Aggiungere codice di implementazione il comportamento definito nel modello.

1. Aggiungere le funzioni seguenti eseguire quando il dispositivo riceve i comandi **SetTemperature** e **SetHumidity** dall'IoT Hub:

    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }

    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```

2. Aggiungere la funzione seguente che invia un messaggio a IoT Hub:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Aggiungere la funzione seguente agganciato libreria serializzazione in SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Aggiungere la funzione seguente per connettersi IoT Hub, inviare e ricevere messaggi e disconnettersi dall'hub. Si noti come il dispositivo invia metadati relativi a se stesso, inclusi i comandi che supporta al IoT Hub durante la connessione. Metadati contribuiscono la soluzione aggiornare lo stato del dispositivo sia in **esecuzione** nel dashboard:

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
    Per riferimento, ecco un esempio **DeviceInfo** messaggio IoT Hub all'avvio:

    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
    
    Per riferimento, ecco un esempio **telemetria** messaggio IoT Hub:

    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    Per riferimento, ecco un esempio ricevuto dall'IoT Hub **comando** :
    
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

5. Sostituire la funzione **principale** con codice riportato di seguito per richiamare la funzione **remote_monitoring_run** :

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. Fare clic su **creare** quindi **Compila soluzione** per creare l'applicazione di dispositivo.

7. In **Esplora soluzioni**fare clic sul progetto **RMDevice** , fare clic su **Debug**e quindi fare clic su **Avvia nuova istanza** per eseguire l'esempio. Console di consente di visualizzare i messaggi come l'applicazione invia telemetria di esempio per IoT Hub e riceve i comandi.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx