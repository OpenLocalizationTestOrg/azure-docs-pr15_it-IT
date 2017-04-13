<properties
    pageTitle="Guida introduttiva a IoT Hub Gateway SDK | Microsoft Azure"
    description="In questa procedura dettagliata Azure IoT Gateway SDK utilizza Linux per illustrare i concetti fondamentali per comprendere quando si utilizza Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>Azure IoT Gateway SDK (beta) - iniziare a utilizzare Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Modalità di compilazione di esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo] [ lnk-setupdevbox] per l'utilizzo di SDK su Linux.

1. Aprire una shell.
2. Passare alla cartella radice nella copia locale del repository **iot di azure-sdk di gateway** .
3. Eseguire lo script **tools/build.sh** . Questo script utilizza l'utilità **cmake** per creare una cartella denominata **creare** nella cartella radice della propria copia locale del repository **iot di azure-sdk di gateway** e generare un makefile. Lo script quindi compila la soluzione e consente di eseguire il test.

> [AZURE.NOTE]  Ogni volta che si esegue lo script **build.sh** , Elimina e quindi ricrea cartella **compilazione** nella cartella radice della propria copia locale del repository **iot di azure-sdk di gateway** .

## <a name="how-to-run-the-sample"></a>Come eseguire l'esempio

1. Lo script **build.sh** genera l'output nella cartella di **compilazione** nella copia locale del repository **iot di azure-sdk di gateway** . Sono inclusi i due moduli utilizzati in questo esempio.

    Script di compilazione contenuto in cifre **liblogger_hl.so** nel **Genera/moduli/logger/** cartella e **libhello_world_hl.so** nel **Genera/moduli/hello_world/** cartella. Usare questi percorsi per il valore di **percorso di modulo** , come illustrato nel file di impostazioni JSON riportata di seguito.

2. Il file **hello_world_lin.json** nella cartella **esempi/hello_world/src** è un file di impostazioni JSON di esempio per Linux che è possibile utilizzare per eseguire l'esempio. Impostazioni di JSON di esempio riportate di seguito si presuppone che si eseguirà il campione dalla radice della copia locale del repository **iot di azure-sdk di gateway** .

3. Per il modulo **logger_hl** , nella sezione **argomenti** impostare il valore di **nome file** il nome e il percorso del file contenenti i dati del registro.

    Questo è un esempio di un file di impostazioni JSON per Linux verrà scritta in **txt** alla cartella in cui è stato eseguito il campione.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Nella shell, passare alla cartella **iot di azure-sdk di gateway** .
4. Eseguire il comando seguente:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
