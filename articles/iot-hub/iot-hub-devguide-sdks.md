<properties
 pageTitle="Guida per sviluppatori - IoT Hub SDK | Microsoft Azure"
 description="Azure IoT Hub Guida gli sviluppatori - informazioni e collegamenti a SDK di servizi e periferiche Azure IoT Hub diversi."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016"
 ms.author="dobett"/>

# <a name="iot-hub-sdks"></a>Hub IoT SDK

## <a name="iot-hub-device-sdks"></a>Dispositivo IoT Hub SDK

Il dispositivo di Microsoft Azure IoT SDK contenere codice che facilita la creazione dispositivi e le applicazioni di connettersi e gestite da servizi Azure IoT Hub.

Il dispositivo IoT seguente SDK sono disponibili per il download da GitHub:

- [Azure IoT dispositivo SDK per C] [ lnk-c-device-sdk] scritta in C ANSI (C99) per la compatibilità multipiattaforma e portabilità.
- [Azure IoT dispositivo SDK per .NET][lnk-dotnet-device-sdk]
- [Azure IoT dispositivo SDK per Java][lnk-java-device-sdk]
- [Azure IoT dispositivo SDK per Node][lnk-node-device-sdk]
- [Dispositivo di Microsoft Azure IoT SDK per Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Vedere i file Leggimi negli archivi GitHub per informazioni sull'utilizzo relative a lingua e responsabili specifico della piattaforma pacchetto per installare file binari e le dipendenze nel computer di sviluppo.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilità piattaforme del sistema operativo e hardware

Per ulteriori informazioni sulla compatibilità SDK con specifici dispositivi, vedere [Azure Certified per il catalogo dispositivo IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>IoT Hub servizio SDK

SDK di servizio Microsoft Azure IoT contenere codice che facilita la creazione di applicazioni che interagiscono direttamente con IoT Hub per gestire i dispositivi e sicurezza.

Il servizio IoT seguente SDK sono disponibili per il download da GitHub:

- [Azure IoT servizio SDK per .NET][lnk-dotnet-service-sdk]
- [Azure IoT servizio SDK per Node][lnk-node-service-sdk]
- [Azure IoT servizio SDK per Java][lnk-java-service-sdk]

> [AZURE.NOTE] Vedere i file Leggimi negli archivi GitHub per informazioni sull'utilizzo relative a lingua e responsabili specifico della piattaforma pacchetto per installare file binari e le dipendenze nel computer di sviluppo.

## <a name="azure-iot-gateway-sdk"></a>Gateway IoT Azure SDK

Questo Azure IoT Gateway SDK contiene l'infrastruttura e i moduli per creare soluzioni gateway IoT. È possibile estendere SDK per creare gateway personalizzato in qualsiasi scenario-to-end.

È possibile scaricare [Azure IoT Gateway SDK] [ lnk-gateway-sdk] da GitHub.

## <a name="online-api-reference-documentation"></a>Documentazione di riferimento API online

Di seguito è un elenco di collegamenti alla documentazione online di riferimento API per dispositivo IoT Azure, il servizio e le raccolte di gateway:

- [Internet aspetti (IoT) .NET][lnk-dotnet-ref]
- [Hub IoT resto][lnk-rest-ref]
- [Azure IoT dispositivo SDK per C][lnk-c-ref]
- [Azure IoT dispositivo SDK per Java][lnk-java-ref]
- [Azure IoT servizio SDK per Java][lnk-java-service-ref]
- [Azure IoT dispositivo SDK per Node][lnk-node-ref]
- [Azure IoT servizio SDK per Node][lnk-node-service-ref]
- [Azure IoT gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Passaggi successivi

Altri argomenti in questa guida per sviluppatori IoT Hub includono:

- [Punti finali IoT Hub][lnk-devguide-endpoints]
- [Linguaggio di query per due facce, metodi e processi][lnk-devguide-query]
- [Quote e limitazioni][lnk-devguide-quotas]
- [Supporto IoT Hub MQTT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md