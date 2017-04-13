<properties 
    pageTitle="Registrazione per i servizi web di apprendimento | Microsoft Azure" 
    description="Informazioni su come attivare la registrazione per i servizi web di apprendimento. Registrazione fornisce informazioni aggiuntive per risolvere le API." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Attivare la registrazione per i servizi web di apprendimento  

In questo documento vengono fornite informazioni su funzionalità registrazione dei servizi Web classica. Abilitazione della registrazione nei servizi Web fornisce informazioni aggiuntive, oltre a un numero di errore e un messaggio, che consentono di risolvere le chiamate alle API di risorse computer.  

Per attivare la registrazione in servizi Web nel portale di classica Azure:   

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com/)
2.  Nella colonna caratteristiche a sinistra fare clic su **Apprendimento**.
3.  Fare clic su area di lavoro, quindi **Servizi WEB**.
4.  Nell'elenco di servizi Web, fare clic sul nome del servizio Web.
5.  Nell'elenco endpoint fare clic sul nome dell'endpoint.
6.  Fare clic su **Configura**.
7.  Impostare **Il livello di traccia di diagnostica** per *errore* o *tutti*, quindi fare clic su **Salva**.

Per abilitare la registrazione nel portale di servizi Web di Windows Azure computer risorse.

1. Accedere al portale di [Servizi Web di Windows Azure computer risorse](https://services.azureml.net) .
2. Fare clic su servizi Web classica.
3.  Nell'elenco di servizi Web, fare clic sul nome del servizio Web.
4.  Nell'elenco endpoint fare clic sul nome dell'endpoint.
5.  Fare clic su **Configura**.
6.  Impostare **la registrazione** per *errore* o *tutti*, quindi fare clic su **Salva**.

## <a name="the-effects-of-enabling-logging"></a>Gli effetti di attivazione della registrazione

Quando la registrazione è attivata, tutti di diagnostica e gli errori da endpoint selezionato connessi all'Account di archiviazione Azure collegati con area di lavoro dell'utente. È possibile visualizzare l'account di archiviazione nel portale di classica Azure visualizzazione Dashboard (parte inferiore della sezione di riepilogo veloce) della propria area di lavoro.  

Per visualizzare i registri è possano utilizzare uno dei numerosi strumenti disponibili per esplorare un Account di archiviazione Azure. Il metodo più semplice può essere semplicemente passare all'Account di archiviazione nel portale di classica Azure e quindi fare clic su **contenitori**. Quindi si vedrà un contenitore denominato **ml diagnostica**. In questo contenitore contiene tutte le informazioni di diagnostica per tutti gli endpoint del servizio web per tutte le aree di lavoro associati a questo account di archiviazione. 
 
## <a name="log-blob-detail-information"></a>Informazioni dettagliate blob log

Ogni blob nel contenitore contiene le informazioni di diagnostica per esattamente una delle operazioni seguenti:

-   Un'esecuzione del metodo esecuzione Batch  
-   Un'esecuzione del metodo richiesta risposta  
-   Inizializzazione di un contenitore di richiesta risposta
  
Il nome di ogni blob ha un prefisso nel formato seguente: 

{Id dell'area di lavoro}-{al servizio Web Id}-{Id dell'Endpoint} / {registra tipo}  

In tipo di Log è uno dei seguenti valori:  

- batch  
- punteggio/richieste  
- punteggio/inizializzazione  