<properties
 pageTitle="Importare esportazione delle identità dispositivo IoT Hub | Microsoft Azure"
 description="Concetti e .NET frammenti per la gestione delle identità dispositivo IoT Hub in blocco di codice"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Gestione delle identità dispositivo IoT Hub di massa

Ogni hub IoT con un dispositivo identità del Registro di sistema che è possibile utilizzare per creare le risorse per dispositivo nel servizio, ad esempio coda che contiene i messaggi cloud al dispositivo in corso. Registro di sistema di identità dispositivo è inoltre possibile controllare l'accesso all'endpoint esposto dispositivo. In questo articolo viene descritto come importare ed esportare le identità di dispositivo in blocco da un registro di sistema di identità di dispositivo.

Importare ed esportare le operazioni effettuate nel contesto di *processi* che consentono di eseguire operazioni di servizio di massa rispetto a un hub IoT.

La classe **RegistryManager** include le **ExportDevicesAsync** e i metodi di **ImportDevicesAsync** che utilizzano il framework di **processo** . Questi metodi consentono di esportare, importare e sincronizzare l'intera durata di un registro di sistema IoT dispositivo hub.

## <a name="what-are-jobs"></a>Quali sono i processi?

Operazioni del Registro di sistema di identità dispositivo utilizzare il sistema di **processo** quando l'operazione:

*  Ha un tempo di esecuzione potrebbe richiedere alcuni minuto rispetto alle operazioni di runtime standard, o
*  Restituisce una grande quantità di dati all'utente.

In questi casi, anziché una singola chiamata API in attesa o il blocco sul risultato dell'operazione, l'operazione in modo asincrono crea un **processo** per tale hub IoT. L'operazione quindi restituisce immediatamente un oggetto **JobProperties** .

Frammento di codice c# riportato di seguito viene illustrato come creare un processo di esportazione:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

È quindi possibile utilizzare la classe **RegistryManager** per eseguire query lo stato del **processo** utilizzando i metadati **JobProperties** restituiti.

Frammento di codice c# riportato di seguito viene illustrato come sondaggio ogni cinque secondi per visualizzare se il processo di termine dell'esecuzione:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Esportare i dispositivi

Utilizzare il metodo **ExportDevicesAsync** per esportare l'intera durata di un registro di sistema IoT dispositivo hub in un contenitore di blob [Azure archiviazione](https://azure.microsoft.com/documentation/services/storage/) utilizzando una [Firma di Access condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

Questo metodo consente di creare backup affidabili le informazioni di dispositivo in un contenitore di blob che è possibile controllare.

Il metodo **ExportDevicesAsync** richiede due parametri:

*  *Stringa* contenente un URI di un contenitore di blob. Questo URI deve contenere un token di sa che concede l'accesso di scrittura per il contenitore. Il processo crea un blob di blocco in questo contenitore di memorizzazione dei dati di dispositivo serializzato Esporta. Token sa deve includere le seguenti autorizzazioni:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Valore boolean* che indica se si desidera escludere i tasti di autenticazione da esportare i dati. Se l'output **false**, l'autenticazione di chiavi sono incluse in esportazione; in caso contrario, le chiavi vengono esportate come **null**.

Il frammento di codice c# seguente viene illustrato come avviare un processo di esportazione che include chiavi di autenticazione dispositivo nei dati di esportazione e quindi sondaggio per il completamento:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Il processo archivia l'output nel contenitore di blob fornito come blob blocco con il nome **devices.txt**. I dati di output è costituito da dati di dispositivo JSON serializzato, con un dispositivo per riga.

Nell'esempio seguente mostra i dati di output:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se è necessario accedere ai dati nel codice, è possibile deserializzare facilmente questi dati utilizzando la classe **ExportImportDevice** . Frammento di codice c# riportato di seguito viene illustrato come leggere le informazioni di dispositivo precedentemente esportato in un blob blocco:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  È anche possibile utilizzare il metodo **GetDevicesAsync** della classe **RegistryManager** per recuperare un elenco di dispositivi. Tuttavia, questo approccio ha un disco rigido cap 1000 al numero di oggetti dispositivo restituiti. Il caso di utilizzo previsto per il metodo **GetDevicesAsync** per scenari di sviluppo agevolare il debug e non è consigliabile carichi di lavoro di produzione.

## <a name="import-devices"></a>Dispositivi di importazione

Il metodo **ImportDevicesAsync** nella classe **RegistryManager** consente di eseguire operazioni di importazione e la sincronizzazione di massa in un registro di sistema IoT dispositivo hub. Ad esempio il metodo **ExportDevicesAsync** , il metodo **ImportDevicesAsync** utilizza il framework di **processo** .

Prestare particolare attenzione utilizzando il metodo **ImportDevicesAsync** perché oltre il provisioning di nuovi dispositivi il Registro di sistema di identità di dispositivo, anche possibile aggiornare ed eliminare dispositivi esistenti.

> [AZURE.WARNING]  Un'operazione di importazione non può essere annullata. Eseguire il backup dei dati esistenti utilizzando il metodo **ExportDevicesAsync** in un altro contenitore blob prima di apportare modifiche in blocco per il Registro di sistema di identità di dispositivo.

Il metodo **ImportDevicesAsync** accetta due parametri:

*  *Stringa* contenente un URI di un contenitore di blob [Azure lo spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/) da utilizzare come *input* al processo. Questo URI deve contenere un token di sa che concede l'accesso in lettura al contenitore. In questo contenitore deve contenere un blob con il nome **devices.txt** che contiene i dati di dispositivo serializzato importare il Registro di sistema di identità di dispositivo. Importare dati devono contenere informazioni sul dispositivo nello stesso formato JSON che il processo di **ExportImportDevice** utilizza quando viene creato un blob **devices.txt** . Token sa deve includere le seguenti autorizzazioni:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  *Stringa* contenente un URI di un contenitore di blob [Azure lo spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/) da utilizzare come *output* dal processo. Il processo crea un blob di blocco in questo contenitore per memorizzare le informazioni sugli errori dal **processo**di importazione completata. Token sa deve includere le seguenti autorizzazioni:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  I due parametri possono puntare allo stesso contenitore di blob. I parametri separati abilitare semplicemente un maggiore controllo i dati come il contenitore di output richiede autorizzazioni aggiuntive.

Il frammento di codice c# seguente viene illustrato come avviare un processo di importazione:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportamento di importazione

È possibile utilizzare il metodo **ImportDevicesAsync** per eseguire le seguenti operazioni in blocco del Registro di identità dispositivo:

-   Registrazione di massa di nuovi dispositivi
-   Eliminazioni in blocco dei dispositivi esistente
-   Le modifiche dello stato di blocco (abilitare o disabilitare dispositivi)
-   Assegnazione collettiva di nuove chiavi di autenticazione dispositivo
-   Blocco automatico rigenerazione delle chiavi di autenticazione dispositivo

È possibile eseguire qualsiasi combinazione di operazioni precedente all'interno di una singola chiamata **ImportDevicesAsync** . Ad esempio possibile registrare nuovi dispositivi ed eliminare o aggiornare i dispositivi esistenti nello stesso momento. Se utilizzata insieme al metodo **ExportDevicesAsync** , è possibile migrare completamente tutti i dispositivi dall'hub IoT una a altra.

Utilizzare la proprietà facoltativo **importMode** nei dati di serializzazione Importa per tutti i dispositivi di controllare il processo di importazione al dispositivo. La proprietà **importMode** presenta le opzioni seguenti:

| importMode |  Descrizione |
| -------- | ----------- |
| **createOrUpdate** | Se non esiste un dispositivo con l' **id**specificato, viene appena registrata. <br/>Se il dispositivo esiste già, le informazioni esistenti viene sovrascritta con i dati di input forniti indipendentemente dal valore **ETag** . |
| **creare** | Se non esiste un dispositivo con l' **id**specificato, viene appena registrata. <br/>Se il dispositivo esiste già, il file di log è scritto un errore. |
| **aggiornamento** | Se esiste già un dispositivo con l' **id**specificato, informazioni esistenti vengono sovrascritte con i dati di input forniti indipendentemente dal valore **ETag** . <br/>Se il dispositivo non esiste, il file di log è scritto un errore. |
| **updateIfMatchETag** | Se esiste già un dispositivo con l' **id**specificato, informazioni esistenti vengono sovrascritte con i dati di input disponibili solo se c'è una corrispondenza **ETag** . <br/>Se il dispositivo non esiste, il file di log è scritto un errore. <br/>In caso di mancata corrispondenza **ETag** , il file di log è scritto un errore. |
| **createOrUpdateIfMatchETag** | Se non esiste un dispositivo con l' **id**specificato, viene appena registrata. <br/>Se il dispositivo esiste già, le informazioni esistenti viene sovrascritta con i dati di input forniti solo se c'è una corrispondenza **ETag** . <br/>In caso di mancata corrispondenza **ETag** , il file di log è scritto un errore. |
| **Elimina** | Se esiste già un dispositivo con l' **id**specificato, viene eliminato indipendentemente dal valore **ETag** . <br/>Se il dispositivo non esiste, il file di log è scritto un errore. |
| **deleteIfMatchETag** | Se esiste già un dispositivo con l' **id**specificato, viene eliminato solo se c'è una corrispondenza **ETag** . Se il dispositivo non esiste, il file di log è scritto un errore. <br/>In caso di mancata corrispondenza ETag, il file di log è scritto un errore. |

> [AZURE.NOTE] Se i dati di serializzazione non definiscono in modo esplicito un contrassegno **importMode** per un dispositivo, viene utilizzato il **createOrUpdate** durante l'operazione di importazione.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importare esempio dispositivi-blocco provisioning dispositivo 

Esempio di codice c# seguente viene illustrato come generare più identità di dispositivo che:

- Includere chiavi di autenticazione.
- Scrivere un blob blocco tali informazioni dispositivo.
- Importare i dispositivi del Registro di sistema di identità dispositivo.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Esempio di dispositivi importazione: eliminazione in blocco

Nell'esempio seguente viene illustrato come eliminare i dispositivi che aggiunti utilizzando l'esempio precedente:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Ottenere il contenitore SAS URI


Nell'esempio seguente viene illustrato come generare un [URI SA](../storage/storage-dotnet-shared-access-signature-part-2.md) con autorizzazioni di lettura, scrittura ed Elimina per un contenitore di blob:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come eseguire operazioni di massa con il Registro di sistema di identità di dispositivo in un hub IoT. Seguire questi collegamenti per ulteriori informazioni sulla gestione di Azure IoT Hub:

- [Metriche di utilizzo][lnk-metrics]
- [Monitoraggio delle operazioni][lnk-monitor]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md