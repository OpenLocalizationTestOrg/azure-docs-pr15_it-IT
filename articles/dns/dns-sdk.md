<properties 
   pageTitle="Creare zone DNS e set di record in DNS Azure utilizzando .NET SDK | Microsoft Azure" 
   description="Come creare zone DNS e set di record DNS Azure utilizzando .NET SDK." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Creare DNS zone e set di record utilizzando .NET SDK

È possibile automatizzare operazioni per creare, eliminare o aggiornare le aree, set di record e record DNS utilizzando DNS SDK con la raccolta di gestione del DNS .NET. È disponibile un progetto di Visual Studio completo [qui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Creare un account di servizio principale

In genere, l'accesso a risorse Azure viene concesso tramite un account dedicato anziché le proprie credenziali utente. Questi account dedicati sono denominati account "servizio principale". Per utilizzare il progetto di esempio Azure DNS SDK, è innanzitutto necessario creare un account di servizio principale e assegnare le autorizzazioni corrette.

1. Seguire [queste istruzioni](../resource-group-authenticate-service-principal.md) per creare un account di servizio principale (il progetto di esempio Azure DNS SDK presuppone l'autenticazione basata su password).

2. Creare un gruppo di risorse ([Ecco come](../azure-portal/resource-group-portal.md)).

3. Consente di disporre delle autorizzazioni dell'account del servizio principale 'DNS Zone collaboratori' al gruppo di risorse Azure RBAC ([Ecco come](../active-directory/role-based-access-control-configure.md).)

4. Se utilizzano il progetto di esempio Azure DNS SDK, modificare il file "program.cs" come indicato di seguito:
    * Inserire i valori corretti per il tenantId, clientId (noto anche come ID account), segreto (password dell'account principale) e subscriptionId usato nel passaggio 1.
    * Immettere il nome del gruppo di risorse scelta nel passaggio 2.
    * Immettere un nome di zona DNS preferito.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet pacchetti e le dichiarazioni di nomi

Per utilizzare Azure DNS .NET SDK, è necessario installare il pacchetto NuGet **Raccolta gestione DNS Azure** e altri pacchetti di Azure necessari.
 
1. In **Visual Studio**, aprire un progetto o un nuovo progetto. 

2. Passare a **Strumenti** **>** **Manager pacchetto NuGet** **>** **Gestire pacchetti NuGet per soluzione...**. 

3. Fare clic su **Sfoglia**, attivare la casella di controllo **Includi preliminare** e digitare **Microsoft.Azure.Management.Dns** nella casella di ricerca.

4. Selezionare il pacchetto e fare clic su **Installa** per aggiungerlo al progetto di Visual Studio.
 
5. Ripetere la procedura indicata sopra per installare anche i seguenti pacchetti: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Aggiungere le dichiarazioni di nomi

Aggiungere le seguenti dichiarazioni di spazio dei nomi

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Inizializzare il client di gestione DNS

*DnsManagementClient* contiene i metodi e proprietà necessarie per la gestione dei set di record e zone DNS.  Il codice seguente esegue l'accesso all'account principale del servizio e crea un oggetto DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Creare o aggiornare una zona DNS

Per creare una zona DNS, prima di tutto "Area" viene creato un oggetto per contenere i parametri di zona DNS. Poiché le aree DNS non è collegate a un'area specifica, il percorso è impostato su 'global'. In questo esempio, un [Manager delle risorse di Azure 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) viene anche aggiunto all'area.

Per effettivamente creare o aggiornare la zona DNS Azure, l'oggetto zona che contiene i parametri di zona viene passato al metodo *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient supporta tre modalità di funzionamento: icona del ('CreateOrUpdate'), asincrono ('CreateOrUpdateAsync'), o asincrono con l'accesso alla risposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  È possibile scegliere una delle tre modalità seguenti, a seconda delle esigenze dell'applicazione.

Azure DNS supporta ottimistica, denominata [ETag](dns-getstarted-create-dnszone.md). In questo esempio, specificando "*" per il 'If-None-Match' intestazione indica DNS Azure per creare una zona DNS se non ne esiste già.  La chiamata non riesce se esiste già una zona con il nome specificato nel gruppo di risorse specificato.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Creazione di record e set di record DNS

I record DNS vengono gestiti come un set di record. Un set di record è un set di record con lo stesso nome e tipo di record in un'area.  Il nome del set di record è relativo al nome zona, non il nome completo del DNS.

Per creare o aggiornare un record impostare un "RecordSet" parametri oggetto viene creato e passato a *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Come con zone DNS, sono disponibili tre modalità di funzionamento: icona del ('CreateOrUpdate'), asincrono ('CreateOrUpdateAsync'), o asincrono con l'accesso alla risposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Come con DNS zones operazioni sui set di record includono il supporto per ottimistica.  In questo esempio, poiché "If-Match" né 'If-None-Match' sono specificate, viene sempre creato il set di record.  Questa chiamata sovrascrive qualsiasi record esistente set con lo stesso nome e tipo di record nell'area DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Ottenere le aree e set di record

I metodi *DnsManagementClient.Zones.Get* e *DnsManagementClient.RecordSets.Get* recuperano singole zone e set di record. Recordset sono identificati dal relativo tipo, nome e il gruppo di zone e risorse in che esistenti. Le aree identificate, il nome della persona e il gruppo di risorse in che esistenti.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Aggiornare un set di record esistente

Per aggiornare un set di record DNS esistente, prima di tutto recuperare il set di record e quindi aggiornare il contenuto del set di record, quindi inviare la modifica.  In questo esempio abbiamo specificare Etag relativi al set di record recuperati nel parametro 'If-Match'. La chiamata non riesce se un'operazione simultanea ha modificato il record impostato nel frattempo.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Le aree di elenco e set di record

Per visualizzare le aree, utilizzare i metodi *DnsManagementClient.Zones.List...* , che supporta l'elenco di tutte le aree in un determinato gruppo di risorse o tutte le aree di una sottoscrizione di Azure specificata (all'interno dei gruppi di risorse.) Per elencare i set di record, utilizzare i metodi di *DnsManagementClient.RecordSets.List...* , che supporta un elenco di tutti i set di record in una determinata zona o solo i set di record di un tipo specifico.

Nota Quando si elencano le aree e set di record risultante potrebbe impaginazione.  Nell'esempio seguente viene illustrato come scorrere le pagine dei risultati. (Una dimensione pagina artificialmente ridotte di "2" viene utilizzata per imporre suddivisione in pagine; in pratica questo parametro deve essere omesso e le dimensioni di pagina predefinito utilizzato.)

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Passaggi successivi

Scaricare il [progetto di esempio Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), che include altri esempi di utilizzo .NET SDK Azure DNS, inclusi esempi per altri tipi di record DNS.
