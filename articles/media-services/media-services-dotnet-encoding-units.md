<properties 
    pageTitle="Come aggiungere unità codifica" 
    description="Informazioni su come aggiungere unità codifica con .NET"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>Come ridurre la codifica con .NET SDK

> [AZURE.SELECTOR]
- [Portale](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Panoramica

>[AZURE.IMPORTANT] Assicurarsi di leggere l'argomento [introduttivo](media-services-scale-media-processing-overview.md) per ottenere ulteriori informazioni sul ridimensionamento media elaborazione argomento.
 
Per modificare il tipo di unità riservate e il numero di codifica riservata unità utilizzando .NET SDK, eseguire le operazioni seguenti:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>Apertura di un Ticket di supporto

Per impostazione predefinita per ogni account servizi multimediali ridimensionare su fino a 25 codifica e 5 su richiesta Streaming unità riservato. È possibile richiedere un limite superiore aprendo un ticket di supporto.

###<a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Per aprire un supporto ticket eseguire le operazioni seguenti:

1. Fare clic su [supporto](https://manage.windowsazure.com/?getsupport=true). Se non si è connessi, verrà richiesto di immettere le credenziali.

1. Selezionare l'abbonamento.

1. In tipo di supporto, selezionare "Tecnici".

1. Fare clic su "Creare Ticket".

1. Selezionare "Azure servizi multimediali di" nell'elenco dei prodotti presentate nella pagina successiva.

1. Selezionare un tipo di problema"" appropriato per il problema.

1. Fare clic su Continua.

1. Seguire le istruzioni nella pagina successiva e quindi immettere i dettagli del problema.

1. Fare clic su Invia per aprire il ticket.



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
