<properties
    pageTitle="Guida introduttiva a inoltro ibrido connessioni | Microsoft Azure"
    description="Come scrivere un'applicazione console c# per le connessioni ibrido"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Guida introduttiva a connessioni ibrido di inoltro

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Cosa viene effettuata

Poiché le connessioni ibrido richiede un client e un componente server, in questa esercitazione si creerà due applicazioni console. Ecco i passaggi:

1. Creare uno spazio dei nomi di inoltro, tramite il portale di Azure.

2. Creare una connessione ibrido tramite il portale di Azure.

3. Scrivere un server applicazione console di ricevere messaggi.

4. Scrivere un client applicazione console per inviare messaggi.

## <a name="prerequisites"></a>Prerequisiti

1. [Visual Studio 2013 o Visual Studio 2015](http://www.visualstudio.com). Gli esempi riportati in questa esercitazione utilizzano Visual Studio 2015.

2. Un abbonamento Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. creare uno spazio dei nomi tramite il portale di Azure

Se si dispone già di uno spazio dei nomi di inoltro creata, passare alla sezione [creare una connessione a una distribuzione ibrida tramite il portale di Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. creare una connessione ibrido tramite il portale di Azure

Se si dispone già di una connessione ibrido creata, passare alla sezione [creare un'applicazione server](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. creare un'applicazione di server (comunicare ascoltatore)

Per ascoltare e ricevere messaggi dall'inoltro, verrà scritto un'applicazione console c# utilizzando Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. creare un'applicazione client (mittente)

Per inviare messaggi al Relay, verrà scritto un'applicazione console c# utilizzando Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. eseguire le applicazioni

1. Eseguire l'applicazione server.

2. Eseguire l'applicazione client e immettere del testo.

3. Assicurarsi che la console di applicazioni server restituisce il testo immesso nelle applicazioni client.

![applicazioni in esecuzione](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Congratulazioni, si è creata un'applicazione di connessioni ibrido-to-end.

## <a name="next-steps"></a>Passaggi successivi:

- [Inoltro domande frequenti](relay-faq.md)
- [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
- [Guida introduttiva a nodo](relay-hybrid-connections-node-get-started.md)