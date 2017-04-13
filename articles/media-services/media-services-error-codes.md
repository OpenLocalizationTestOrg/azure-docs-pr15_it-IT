<properties
    pageTitle="Codici di errore servizi multimediali Azure | Microsoft Azure"
    description="L'argomento viene fornita una panoramica dei codici di errore servizi multimediali di Windows Azure."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Codici di errore servizi multimediali Azure

Quando tramite servizi multimediali di Microsoft Azure, potrebbe essere visualizzato codici di errore HTTP dal servizio a seconda di problemi, ad esempio scadenza alle azioni che non sono supportate in servizi multimediali di token di autenticazione. Di seguito è un elenco dei **codici di errore HTTP** che può essere restituito da servizi di supporto e le possibili cause per loro.  
  
## <a name="400-bad-request"></a>400 richiesta non valida

La richiesta contiene informazioni non valide e rifiutata a causa di uno dei motivi seguenti:

- Una versione non supportata di API viene specificata. Per la versione più recente, vedere [il programma di installazione per lo sviluppo di API REST servizi multimediali](media-services-rest-how-to-use.md).
- La versione dell'API dei servizi di supporto non è specificata. Per informazioni su come specificare la versione dell'API, vedere [connessione a servizi multimediali con all'API REST di servizi di supporto](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Se si utilizza .NET o Java SDK per connettersi ai servizi di supporto, la versione dell'API specificata automaticamente ogni volta che provare ed eseguire un'azione dai servizi di supporto.
- È stata specificata una proprietà non definita. Il nome della proprietà è nel messaggio di errore. È possibile specificare solo le proprietà che appartengono a una determinata entità. Vedere [Riferimenti di API REST di servizi di Azure Media](http://msdn.microsoft.com/library/azure/hh973617.aspx) per un elenco di entità e le relative proprietà.
- È stato specificato un valore di proprietà non valide. Il nome della proprietà è nel messaggio di errore. Fare clic sul collegamento precedente per i tipi di proprietà valido e i rispettivi valori.
- Un valore di proprietà non è presente ed è necessario.
- Parte dell'URL specificato contiene un valore non valido.
- Tentativo di aggiornamento di una proprietà WriteOnce.
- Un tentativo di creare un processo che ha un input bene con un AssetFile principale che non è stata specificata o non è possibile determinare.
- Tentativo di aggiornamento di un indicatore di posizione SA. Indicatori di SA solo creati o eliminati. Indicatori di flusso può essere aggiornato. Per ulteriori informazioni, vedere [indicatori di posizione](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Un'operazione non supportata o la query è stato inviato. 

## <a name="401-unauthorized"></a>401 non autorizzato

La richiesta non può essere autenticata (prima che possono essere autorizzata) a causa di uno dei motivi seguenti:

- Intestazione di autenticazione mancante.
- Valore dell'intestazione di autenticazione non corretta.
    - Il token è scaduto. Se utilizza direttamente le API REST, vedere [connessione a servizi multimediali con all'API REST di servizi di supporto](media-services-rest-connect_programmatically.md) per imparare a generare un nuovo token di autenticazione. Se si utilizza .NET o Java SDK, creare un oggetto CloudMediaContext o MediaContract per generare il token. Per ulteriori informazioni su come eseguire questa operazione, vedere [connessione a servizi multimediali con Media Services SDK per .NET](media-services-dotnet-connect-programmatically.md).
    - Il token contiene una firma non valida.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 accesso negato

La richiesta non è consentita a causa di uno dei motivi seguenti:

- L'account di servizi di supporto non è possibile trovare o è stato eliminato.
- È disabilitato l'account di servizi di supporto e il tipo di richiesta non HTTP GET. Operazioni di servizio restituirà anche una risposta 403.
- Il token di autenticazione non contiene informazioni sulle credenziali dell'utente: nome account e/o SubscriptionId. Sono disponibili le informazioni dell'estensione interfaccia utente di servizi di supporto per l'account di servizi multimediali nel portale di gestione di Azure.
- Non è possibile accedere alla risorsa.
    - Tentativo di usare un MediaProcessor che non è disponibile per l'account di servizi di supporto.
    - Tentativo di aggiornare un JobTemplate definito dai servizi di supporto.
    - Tentativo di sovrascrivere Locator alcuni altri servizi di supporto dell'account.
    - Tentativo di sovrascrivere ContentKey alcuni altri servizi di supporto dell'account.

- Impossibile creare la risorsa a causa di una quota di servizio che è stato raggiunto per l'account di servizi di supporto. Per ulteriori informazioni su quote del servizio, vedere [le quote e limitazioni](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 pagina non trovata

La richiesta non è consentita in una risorsa a causa di uno dei motivi seguenti:

- È stato effettuato un tentativo di aggiornare un'entità che non esiste.
- Un tentativo di eliminazione di un'entità che non esiste.
- Un tentativo di creare un'entità collegato a un'entità che non esiste.
- Un tentativo di ottenere un'entità che non esiste.
- Tentativo di specificare un account di archiviazione che non è associato all'account di servizi di supporto.  

## <a name="409-conflict"></a>409 conflitto

La richiesta non è consentita a causa di uno dei motivi seguenti:

- Più AssetFile con il nome specificato all'interno bene.
- Un tentativo di creare un secondo AssetFile primaria all'interno bene.
- Un tentativo di creare un ContentKey con l'Id specificato già in uso.
- Un tentativo di creare un indicatore di posizione con l'Id specificato già in uso.
- Più IngestManifestFile con il nome specificato all'interno di IngestManifest.
- Tentativo di collegare una seconda crittografia di spazio di archiviazione ContentKey bene crittografati lo spazio di archiviazione.
- Tentativo di collegare la stessa ContentKey per la risorsa.
- Un tentativo di creare un indicatore di posizione di un bene il cui contenitore di spazio di archiviazione è vuoto o non è più associato all'attività.
- Un tentativo di creare un indicatore di posizione per una risorsa che contiene già 5 Locator in uso. (Spazio di archiviazione azure vengono applicate al limite di cinque criteri di accesso condiviso in un contenitore di spazio di archiviazione).
- Il collegamento di account di archiviazione di un bene per un IngestManifestAsset non è uguale all'account di archiviazione utilizzato dall'elemento padre IngestManifest.  

## <a name="500-internal-server-error"></a>500 Errore interno del Server

Durante l'elaborazione della richiesta, servizi multimediali si verifica un errore che impedisce l'elaborazione di continuare. Questo potrebbe essere causato da uno dei motivi seguenti:

- Creazione di un'attività o un processo non riesce perché le informazioni di quota servizio dell'account servizi multimediali sono temporaneamente non disponibile.
- Creazione di un contenitore di spazio di archiviazione blob bene o IngestManifest non riesce perché informazioni sull'account di archiviazione dell'account è temporaneamente non disponibile.
- Altro errore imprevisto. 

## <a name="503-service-unavailable"></a>503 Servizio non disponibile

Il server è attualmente in grado di ricevere richieste. Questo errore può essere causato da eccessive richieste al servizio. Servizi multimediali di limitazione meccanismo limita l'utilizzo delle risorse per le applicazioni che rendono eccessiva richiesta al servizio.

>[AZURE.NOTE]Controllare il messaggio di errore e la stringa di codice di errore per ottenere informazioni più dettagliate sul motivo ottenuto l'errore 503. Questo messaggio di errore non significa sempre limitazione.

Descrizioni possibili sono:

- "Server è occupato. Esecuzioni precedenti di questo tipo di richiesta ha più di {0} secondi."
- "Server è occupato. Più di {0} richieste al secondo può essere limitata."
- "Server è occupato. Più di {0} richieste all'interno di {1} secondi può essere limitata."

Per gestire l'errore, è consigliabile usare esponenziale back-off ritentare. Ciò significa che utilizzando gradualmente più attesa tra tentativi per le risposte di errori consecutivi.  Per ulteriori informazioni, vedere [Blocco dell'applicazione di gestione temporaneo Fault](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Se si utilizza [Azure Media Services SDK per .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), la logica di ripetizione relativa all'errore 503 è stata implementata da SDK.  
  
## <a name="see-also"></a>Vedere anche  

[Codici di errore di gestione dei servizi multimediali di Microsoft](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
