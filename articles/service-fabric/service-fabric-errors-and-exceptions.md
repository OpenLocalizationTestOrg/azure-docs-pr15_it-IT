<properties
   pageTitle="FabricClient comuni eccezioni | Microsoft Azure"
   description="Descrive le eccezioni e gli errori che possono essere generati da APIs FabricClient durante l'esecuzione dell'applicazione e le operazioni di Gestione cluster comuni."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Eccezioni ed errori quando si lavora con APIs FabricClient comuni
API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) consentono agli amministratori di cluster e applicazione di eseguire attività amministrative in un'applicazione servizio tessuti, servizio o cluster. Ad esempio, la distribuzione delle applicazioni, aggiornamento e rimozione, il controllo dell'integrità un cluster o il test di un servizio. Gli sviluppatori di applicazioni e cluster amministratori possono utilizzare APIs FabricClient per lo sviluppo di strumenti per la gestione del cluster di tessuti servizi e applicazioni.

Esistono diversi tipi di operazioni che possono essere eseguite tramite FabricClient.  Ogni metodo può generare eccezioni per errori a causa di input non corretti, gli errori di runtime o problemi temporanei dell'infrastruttura.  Vedere la documentazione di riferimento API per trovare le eccezioni vengono generate da un metodo specifico. Esistono alcune eccezioni, tuttavia, che possono essere generate da molte API [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diversi. La tabella seguente elenca le eccezioni sono comuni a APIs FabricClient.

|Eccezione| Generata quando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|L'oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) è stato chiuso. Eliminare l'oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) si sta usando e creare un'istanza di un nuovo oggetto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|Operazione esaurito. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) viene restituito quando l'operazione richiede più MaxOperationTimeout per completare.|
|[UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|Il controllo di accesso per l'operazione non è riuscita. Viene restituito E_ACCESSDENIED.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Si è verificato un errore di runtime durante l'operazione. Uno dei metodi FabricClient potenzialmente possibile che venga generata [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), la proprietà di [codice di errore](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica l'esatta causa dell'eccezione. Codici di errore sono definiti nell'enumerazione [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|L'operazione non è riuscita a causa di una condizione di errore temporaneo di qualche tipo. Ad esempio un'operazione potrebbe non riuscire perché una base di repliche temporaneamente non è raggiungibile. Eccezioni temporanee corrispondono alle operazioni non riuscite che possono essere ripetute.|

Errori [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) comuni che possono essere restituiti in una [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Errore| Condizione|
|---------|:-----------|
|CommunicationError|Un errore di comunicazione causato l'operazione avere esito negativo, ripetere l'operazione.|
|InvalidCredentialType|Il tipo di credenziali non è valido.|
|InvalidX509FindType|Il X509FindType non è valido.|
|InvalidX509StoreLocation|Il X509 percorso archivio non è valido.|
|InvalidX509StoreName|Il X509 rivenditori partner non è valido.|
|InvalidX509Thumbprint|Il X509 la stringa di identificazione personale del certificato non è valida.|
|InvalidProtectionLevel|Il livello di protezione non è valido.|
|InvalidX509Store|X509 archivio certificati non può essere aperti.|
|InvalidSubjectName|Il nome del soggetto non è valido.|
|InvalidAllowedCommonNameList|Il formato della stringa di elenco comuni nome non è valido. Deve trattarsi di un elenco delimitato da virgole.|
