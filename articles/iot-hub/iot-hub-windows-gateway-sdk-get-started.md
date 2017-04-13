<properties
    pageTitle="Guida introduttiva a IoT Hub Gateway SDK | Microsoft Azure"
    description="Procedura dettagliata IoT Gateway SDK Azure utilizzo di Windows per illustrare i concetti fondamentali per comprendere quando si utilizza Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>Azure IoT Gateway SDK (beta) - iniziare a usare Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Modalità di compilazione di esempio

Prima di iniziare, è necessario [configurare l'ambiente di sviluppo] [ lnk-setupdevbox] per l'utilizzo con SDK in Windows.

1. Aprire un prompt dei comandi di **sviluppatore Prompt dei comandi per VS2015** .
2. Passare alla cartella radice nella copia locale del repository **iot di azure-sdk di gateway** .
3. Eseguire il **strumenti\\build.cmd** script. Questo script crea un file di soluzione Visual Studio, compila la soluzione e consente di eseguire il test. È possibile trovare la soluzione Visual Studio nella cartella **creare** nella copia locale del repository **iot di azure-sdk di gateway** .

## <a name="how-to-run-the-sample"></a>Come eseguire l'esempio

1. Lo script **build.cmd** crea una cartella denominata **compilare** nella copia locale del repository. Questa cartella contiene due moduli utilizzati in questo esempio.

    Script di compilazione contenuto in cifre **logger_hl.dll** nel **creare\\moduli\\logger\\Debug** cartella e **hello_world_hl.dll** nel **build\\moduli\\hello_world\\Debug** cartella. Usare questi percorsi per il valore di **percorso di modulo** , come illustrato nel file di impostazioni JSON riportata di seguito.

2. Il file **hello_world_win.json** nel **esempi\\hello_world\\src** cartella è un file di impostazioni JSON di esempio per Windows che è possibile utilizzare per eseguire l'esempio. Impostazioni di JSON di esempio riportate di seguito si presuppone duplicati repository IoT Gateway SDK nella radice dell'unità **c** . Se scaricata in un'altra posizione, è necessario modificare i valori di **percorso di modulo** nel **esempi\\hello_world\\src\\hello_world_win.json** file di conseguenza.

3. Per il modulo **logger_hl** , nella sezione **argomenti** impostare il valore di **nome file** il nome e il percorso del file contenenti i dati del registro.

    Questo è un esempio di un file di impostazioni JSON per Windows che verrà scritto il file di **log. txt** nella radice dell'unità **c** .

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
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

3. Al prompt dei comandi, passare alla cartella radice della propria copia locale del repository **iot di azure-sdk di gateway** .
4. Eseguire il comando seguente:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md