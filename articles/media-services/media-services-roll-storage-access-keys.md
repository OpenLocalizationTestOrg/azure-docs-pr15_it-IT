<properties 
    pageTitle="Aggiornare i servizi di supporto dopo l'operazione tasti di scelta dello spazio di archiviazione | Microsoft Azure" 
    description="In questo articolo per ottenere istruzioni su come aggiornare servizi multimediali di dopo l'operazione tasti di scelta dello spazio di archiviazione." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Servizi multimediali di aggiornamento dopo l'operazione tasti di scelta dello spazio di archiviazione

Quando si crea un nuovo account di servizi multimediali di Windows Azure, anche viene richiesto di selezionare un account di archiviazione Azure utilizzato per l'archiviazione del contenuto multimediale. Si noti che è possibile [aggiungere più di un account di archiviazione](meda-services-managing-multiple-storage-accounts.md) al proprio account di servizi di supporto.

Quando viene creato un nuovo account di archiviazione, Azure genera due lo spazio di archiviazione di 512 bit tasti di scelta, che consentono di eseguire l'autenticazione di accesso al proprio account di archiviazione. Per mantenere le connessioni di spazio di archiviazione sicura, è consigliabile periodicamente rigenerare e ruotare il tasto di scelta di spazio di archiviazione. Due tasti di scelta (primari e secondari) sono disponibili per consentono di gestire le connessioni all'account di archiviazione utilizzando un tasto di scelta rapida mentre si rigenera il tasto di scelta. Questa procedura è l'acronimo di "tasti in sequenza".

Servizi multimediali dipende da una chiave di spazio di archiviazione fornita. In particolare, Locator che consentono di flusso o il download dei propri beni dipendono dal tasto di scelta rapida archiviazione specificato. Quando viene creato un account AMS assume una dipendenza tasto di scelta rapida principale dello spazio di archiviazione per impostazione predefinita, ma come utente è possibile aggiornare la chiave di archiviazione contenente AMS. È necessario assicurarsi di comunicare servizi multimediali la chiave da utilizzare eseguendo i passaggi descritti in questo argomento. Inoltre, quando in sequenza tasti di scelta dello spazio di archiviazione, è necessario assicurarsi di aggiornare il Locator in modo non ci sarà alcuna interruzione del servizio di trasmissione (questo passaggio anche descritte nell'argomento).

>[AZURE.NOTE]Se si hanno più account di archiviazione, eseguire questa procedura con ogni account di archiviazione.
>
>Prima di eseguire i passaggi descritti in questo argomento per un account di produzione, assicurarsi di testarli in un account di pre-produzione.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Passaggio 1: Rigenerare tasto archiviazione secondaria

Iniziare con la rigenerazione chiave di archiviazione secondario. Per impostazione predefinita, la chiave secondaria non è utilizzata da servizi di supporto.  Per informazioni su come ripristinare chiavi per l'archiviazione, vedere [come: visualizzazione, copia e Rigenera lo spazio di archiviazione tasti](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Passaggio 2: Aggiornare servizi multimediali di Microsoft per usare la nuova chiave di archiviazione secondaria

Aggiornare i servizi di supporto per usare il tasto di scelta di archiviazione secondario. È possibile usare uno dei due metodi seguenti per sincronizzare la chiave di archiviazione rigenerazione con i servizi di supporto.

- Usare il portale di Azure: per trovare il nome e la chiave, accedere al portale di Azure e selezionare il proprio account. Sul lato destro viene visualizzata la finestra di impostazioni. Nella finestra Impostazioni selezionare chiavi. A seconda di quale tasto lo spazio di archiviazione desiderata per i servizi di supporto Sincronizza con, selezionare la chiave primaria Sincronizza o sincronizzare pulsante chiave secondario. In questo caso, utilizzare la chiave secondaria.

- Usare la gestione dei servizi multimediali API REST.

Nell'esempio seguente viene illustrato come creare il https://endpoint/***subscriptionId/services/mediaservices/account/nome account*/StorageAccounts/*storageAccountName*richiesta /Key per sincronizzare la chiave di archiviazione specificato con i servizi di supporto. In questo caso, viene utilizzato il valore di chiave di archiviazione secondario. Per ulteriori informazioni, vedere [come: API REST di usare supporti i servizi di gestione](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Dopo questo passaggio, aggiornare Locator esistente (che hanno dipendenza da chiave di archiviazione precedente), come illustrato nel passaggio successivo.

>[AZURE.NOTE]Attendere 30 minuti prima di eseguire le operazioni con i servizi multimediali (ad esempio creazione nuovo Locator) al fine di evitare qualsiasi impatto su processi in sospeso.

##<a name="step-3-update-locators"></a>Passaggio 3: Indicatori di aggiornamento

>[AZURE.NOTE]Implementazione di tasti di scelta dello spazio di archiviazione, è necessario assicurarsi di aggiornare il Locator esistente, quindi non esiste alcuna interruzione del servizio di trasmissione.

Attendere almeno 30 minuti dopo la sincronizzazione la nuova chiave di spazio di archiviazione con AMS. È quindi possibile ricreare il Locator su richiesta in modo che definiscono dipendenza su chiave di archiviazione specificato e mantenere l'URL esistente.

Si noti che, quando si aggiorna o ricreare un locator SA, verrà modificati sempre l'URL.

>[AZURE.NOTE] Per assicurarsi che consente di mantenere gli URL esistenti del Locator su richiesta, è necessario eliminare locator esistente e crearne uno nuovo con lo stesso ID.

Nell'esempio di .NET seguente viene illustrato come ricreare un indicatore di posizione con lo stesso ID.

privato ILocator RecreateLocator(CloudMediaContext context, ILocator locator) statica {/ / salvare le proprietà di locator esistente.
var bene = locator. Risorse; var accessPolicy = locator. AccessPolicy; var locatorId = locator. ID. var startDate = locator. Ora di inizio; var locatorType = locator. Digitare; var locatorName = locator. Nome.

Eliminare locator precedente.
indicatore di posizione. Delete ();

Se (locator. ExpirationDateTime < = DateTime.UtcNow) {eccezione nuovo (Format ("non è possibile ricreare locator Id = {0} poiché è la data di scadenza locator in passato", locator. ID)); }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Passaggio 5: Rigenerare tasto principale dello spazio di archiviazione

Rigenerare il tasto di scelta di archiviazione principale. Per informazioni su come ripristinare chiavi per l'archiviazione, vedere [come: visualizzazione, copia e Rigenera lo spazio di archiviazione tasti](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Passaggio 6: Aggiornamento servizi multimediali di Microsoft per usare la nuova chiave primaria dello spazio di archiviazione
    
Utilizzare la stessa procedura come descritto nel [passaggio 2](media-services-roll-storage-access-keys.md#step2) , ma questa volta sincronizzare la nuova chiave di accesso principale dello spazio di archiviazione con l'account di servizi di supporto.

>[AZURE.NOTE]Attendere 30 minuti prima di eseguire le operazioni con i servizi multimediali (ad esempio creazione nuovo Locator) al fine di evitare qualsiasi impatto su processi in sospeso.

##<a name="step-7-update-locators"></a>Passaggio 7: Indicatori di aggiornamento  

Dopo 30 minuti è possibile ricreare il Locator su richiesta in modo che definiscono dipendenza sulla nuova chiave primaria dello spazio di archiviazione e mantenere l'URL esistente.

Utilizzare la stessa procedura descritta nel [passaggio 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Riconoscimenti 

Vogliamo confermare seguendo altre persone che hanno contribuito per creare il documento: Cenk Dingiloglu, Gada Milano, Seva Titov.
