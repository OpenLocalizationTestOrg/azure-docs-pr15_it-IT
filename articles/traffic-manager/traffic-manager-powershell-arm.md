<properties
    pageTitle="Azure Manager delle risorse supporto per il traffico Manager | Microsoft Azure "
    description="Utilizzo di PowerShell per il traffico Manager Azure Gestione risorse"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Azure supporto Manager delle risorse per gestione di traffico di Azure

Gestione risorse di Azure è l'interfaccia di gestione preferito per i servizi di Azure. Azure profili di gestore del traffico possono essere gestiti usando API basate su Gestione risorse di Azure e strumenti.

## <a name="resource-model"></a>Modello di risorse

Gestore del traffico Azure viene configurato tramite un insieme di impostazioni definito un profilo di gestore del traffico. Il profilo contiene le impostazioni DNS, impostazioni instradare il traffico, impostazioni di monitoraggio endpoint e un elenco di endpoint del servizio in cui è indirizzato il traffico.

Ogni profilo di gestore del traffico è rappresentato da una risorsa di tipo 'TrafficManagerProfiles'. Livello API REST URI per ogni profilo è:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Confronto con l'API classica Azure il traffico Manager

La gestione di risorse Azure del supporto tecnico per il traffico Manager utilizza terminologia diversa rispetto al modello di distribuzione classica. Nella tabella seguente vengono illustrate le differenze tra i termini di Manager delle risorse e classica:

| Termini di Manager delle risorse | Termini classica |
|-----------------------|--------------|
| Metodo di routing del traffico | Metodo di bilanciamento del carico |
| Metodo di priorità | Metodo di failover |
| Metodo Media ponderata | Metodo circolari |
| Metodo di prestazioni | Metodo di prestazioni |

In base al feedback dei clienti, è stata modificata la terminologia per migliorare la nitidezza e ridurre malintesi comuni. Non vi è alcuna differenza funzionalità.

## <a name="limitations"></a>Limitazioni

Quando si fa riferimento un endpoint di tipo 'AzureEndpoints' per un'App Web, il traffico Manager endpoint possono fare riferimento solo il valore predefinito (produzione) [intervallo aperto Web App](../app-service-web/web-sites-staged-publishing.md). Bande orarie personalizzati non sono supportate. In alternativa, è possono configurare bande orarie personalizzati utilizzando il tipo di 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>La configurazione di PowerShell di Azure

Queste istruzioni utilizzano Microsoft Azure PowerShell. L'articolo seguente viene illustrato come installare e configurare Azure PowerShell.

- [Come installare e configurare PowerShell Azure](../powershell-install-configure.md)

Gli esempi riportati in questo articolo presuppongono che si dispone di un gruppo di risorse esistenti. È possibile creare un gruppo di risorse utilizzando il comando seguente:

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Gestione risorse di Azure richiede che tutti i gruppi di risorse hanno un percorso. Questo percorso viene utilizzato come predefinito per le risorse create in tale gruppo di risorse. Tuttavia, poiché il traffico Manager profilo risorse sono globale, non regionali, la scelta della posizione di gruppo di risorse non influisce su Azure il traffico Manager.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di gestore del traffico

Per creare un profilo di gestore del traffico, utilizzare il cmdlet New-AzureRmTrafficManagerProfile:

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

La tabella seguente descrive i parametri:

| Parametro | Descrizione |
|-----------|-------------|
| Nome | Nome di risorsa per la risorsa di profilo di gestore del traffico. I profili nello stesso gruppo di risorse devono avere nomi univoci. Questo nome è distinto dal nome DNS utilizzato per le query DNS.|
| ResourceGroupName | Il nome del gruppo di risorse contenente la risorsa profilo.|
| TrafficRoutingMethod | Specifica il metodo di routing del traffico utilizzato per determinare quali endpoint viene restituito in risposta a una query DNS. Valori possibili sono 'Prestazioni', 'Weighted' o 'Priority'.|
| RelativeDnsName | Specifica la parte di nome host del nome del DNS fornito dal profilo il traffico Manager. Questo valore viene combinato con il nome di dominio DNS utilizzato da Azure il traffico Manager in modo da formare il nome di dominio completo (FQDN) del profilo. Impostazione del valore di "contoso" diventa ad esempio 'contoso.trafficmanager.net'.|
| TTL | Specifica il DNS per durata (TTL), in secondi. Questo TTL informa i sistemi di risoluzione DNS locali e i client DNS per le risposte DNS cache per il profilo di gestore del traffico quanto tempo.|
| MonitorProtocol | Specifica il protocollo da utilizzare per monitorare l'integrità del punto finale. I valori possibili sono 'HTTP' e 'HTTPS'.|
| MonitorPort | Specifica la porta TCP utilizzata per monitorare l'integrità del punto finale.|
| MonitorPath | Specifica il percorso relativo al nome di dominio endpoint utilizzato per verificare la presenza per integrità endpoint.|

Il cmdlet crea un profilo di gestore del traffico in Azure e restituisce un oggetto profilo corrispondente a PowerShell. A questo punto, il profilo non contiene tutti i punti finali. Per ulteriori informazioni sull'aggiunta di endpoint per un profilo di traffico Manager, vedere [Aggiunta di gestore del traffico endpoint](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Ottenere un profilo di gestore del traffico

Per recuperare un oggetto di profilo di gestore del traffico esistente, utilizzare il cmdlet Get-AzureRmTrafficManagerProfle:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Restituisce un oggetto di profilo di gestore del traffico.

## <a name="update-a-traffic-manager-profile"></a>Aggiornare un profilo di gestore del traffico

La modifica dei profili di gestore del traffico segue un processo passaggio 3:

1. Recuperare il profilo utilizzando Get-AzureRmTrafficManagerProfile o usare il profilo restituito da AzureRmTrafficManagerProfile di nuovo.
2. Modificare il profilo. Aggiungere e rimuovere i punti finali o modificare i parametri del profilo o endpoint. Queste modifiche sono operazioni non in linea. Si sta modificando l'oggetto locale in memoria che rappresenta il profilo.
3. Salvare le modifiche utilizzando il cmdlet Set-AzureRmTrafficManagerProfile.

Tutte le proprietà del profilo possono essere modificate ad eccezione RelativeDnsName del profilo. Per modificare il RelativeDnsName, è necessario eliminare profilo e un nuovo profilo con un nuovo nome.

Nell'esempio seguente viene illustrato come modificare TTL del profilo:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Esistono tre tipi di traffico Manager endpoint:

1. **Endpoint Azure** sono servizi ospitati in Azure
2. **Endpoint esterni** sono servizi ospitati all'esterno di Azure
3. **I punti finali annidate** vengono utilizzati per creare gerarchie annidate di traffico Gestione profili. Endpoint annidato abilitare configurazioni di routing del traffico avanzate per applicazioni complesse.

In ogni caso, è possano aggiungere gli endpoint in due modi:

1. Utilizzo di un processo di 3-passaggio descritto in precedenza. Il vantaggio di questo metodo è che è possibile effettuare diverse endpoint modifiche in un unico aggiornamento.
2. Utilizzare il cmdlet New-AzureRmTrafficManagerEndpoint. Questo cmdlet consente di aggiungere un endpoint per un profilo di gestore del traffico esistente in un'unica operazione.

## <a name="adding-azure-endpoints"></a>Aggiunta di endpoint Azure

Endpoint Azure fare riferimento a servizi ospitati in Azure. Tre tipi di Azure endpoint sono supportati:

1. Azure Web App
2. 'Classica' cloud services (che può contenere un servizio PaaS o macchine virtuali IaaS)
3. Risorse PublicIpAddress Azure (che possono essere collegate a un bilanciamento del carico o una macchina virtuale NIC). Il PublicIpAddress deve avere un nome DNS assegnato da utilizzare in gestore del traffico.

In ogni caso:

- Il servizio è specificato utilizzando il parametro 'targetResourceId' di Aggiungi AzureRmTrafficManagerEndpointConfig o AzureRmTrafficManagerEndpoint di nuovo.
- La "Destinazione" e 'EndpointLocation' sono implicite il TargetResourceId.
- Se si specifica il 'peso' è facoltativo. Spessori vengono utilizzate solo se il profilo è configurato per utilizzare il metodo di routing del traffico 'Weighted'. In caso contrario, vengono ignorati. Se specificato, il valore deve essere un numero compreso tra 1 e 1000. Il valore predefinito è "1".
- Se si specifica il 'Priority' è facoltativo. Priorità vengono utilizzate solo se il profilo è configurato per utilizzare il metodo di routing del traffico 'Priority'. In caso contrario, vengono ignorati. Valori validi sono da 1 a 1000 con valori più bassi che indica una priorità più alta. Se per un endpoint specificato, deve essere specificati per tutti gli endpoint. Se omesso, i valori predefiniti partire da "1" vengono applicati nell'ordine in cui sono elencati i punti finali.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Esempio 1: Aggiunta di endpoint di Web App con Aggiungi AzureRmTrafficManagerEndpointConfig

In questo esempio abbiamo creare un profilo di gestore del traffico e aggiungere due punti finali Web App utilizzando il cmdlet AzureRmTrafficManagerEndpointConfig Aggiungi.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Esempio 2: Aggiunta di un endpoint del servizio cloud 'classica' utilizzando AzureRmTrafficManagerEndpoint nuovo

In questo esempio, un endpoint servizio Cloud 'classico' viene aggiunto a un profilo di gestore del traffico. In questo esempio viene specificato il profilo mediante i nomi dei gruppi di profili e risorse, anziché passare un oggetto profilo. Entrambi gli approcci sono supportati.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Esempio 3: Aggiunta di un endpoint publicIpAddress utilizzando AzureRmTrafficManagerEndpoint nuovo

In questo esempio, una risorsa indirizzo IP pubblica viene aggiunto al profilo gestore del traffico. L'indirizzo IP pubblico deve avere un nome DNS configurato e può essere associato alla scheda di una macchina virtuale o a un servizio di bilanciamento del carico.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Aggiunta di endpoint esterni

Il traffico Manager utilizza gli endpoint esterni per indirizzare il traffico ai servizi ospitato all'esterno di Azure. Come per gli endpoint Azure, gli endpoint esterni possono essere aggiunti tramite Aggiungi-AzureRmTrafficManagerEndpointConfig seguito da Set AzureRmTrafficManagerProfile o AzureRMTrafficManagerEndpoint di nuovo.

Quando si specificano gli endpoint esterni:

- È necessario specificare il nome di dominio dell'endpoint utilizzando il parametro di "Destinazione"
- Se si utilizza il metodo di routing del traffico 'Prestazioni', 'EndpointLocation' è necessario. In caso contrario è facoltativo. Il valore deve essere un [nome dell'area di Azure valido](https://azure.microsoft.com/regions/).
- La 'Peso' e 'Priority' sono facoltativi.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Esempio 1: Aggiunta di endpoint esterni utilizzando Aggiungi AzureRmTrafficManagerEndpointConfig e AzureRmTrafficManagerProfile Set

In questo esempio è creare un profilo di gestore del traffico, aggiungere due endpoint esterni e le modifiche.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Esempio 2: Aggiunta di endpoint esterni con nuovo AzureRmTrafficManagerEndpoint

In questo esempio, si aggiunge un endpoint esterno a un profilo esistente. Il profilo viene specificato utilizzando i nomi dei gruppi di profili e risorse.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Aggiunta di endpoint 'Annidate'

Ogni profilo di gestore del traffico consente di specificare un unico metodo di routing del traffico. Esistono tuttavia scenari che richiedono più sofisticati il traffico routing che il routing forniti da un singolo profilo di gestore del traffico. È possibile annidare i profili di gestore del traffico per combinare i vantaggi di più di un metodo di routing del traffico. Profili annidati consentono di ignorare il comportamento di gestore del traffico predefinito per supportare più grandi e installazioni applicazione più complesse. Per esempi più dettagliati, vedere [profili annidate il traffico Manager](traffic-manager-nested-profiles.md).

Endpoint annidate sono configurati in profilo padre, tramite un tipo di endpoint specifico, 'NestedEndpoints'. Quando si specificano i punti finali annidati:

- È necessario specificare l'endpoint utilizzando il parametro 'targetResourceId'
- Se si utilizza il metodo di routing del traffico 'Prestazioni', 'EndpointLocation' è necessario. In caso contrario è facoltativo. Il valore deve essere un [nome dell'area di Azure valido](http://azure.microsoft.com/regions/).
- La «Peso» e 'Priority' sono facoltativi, per gli endpoint Azure.
- Il parametro 'MinChildEndpoints' è facoltativo. Il valore predefinito è "1". Se il numero di endpoint disponibili supera questo limite, il profilo padre considera il profilo figlio 'ridotto' e trasferisce il traffico agli altri endpoint nel profilo padre.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Esempio 1: Aggiunta di endpoint annidate utilizzando Aggiungi AzureRmTrafficManagerEndpointConfig e AzureRmTrafficManagerProfile Set

In questo esempio abbiamo creare nuovo padre e figlio il traffico Gestione profili, aggiungere l'elemento figlio come endpoint annidate all'elemento padre e il commit delle modifiche.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Per brevità in questo esempio, non è stato aggiunto altri endpoint ai profili padre o figlio.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Esempio 2: Aggiunta di endpoint annidate utilizzando AzureRmTrafficManagerEndpoint nuovo

In questo esempio abbiamo aggiungere un profilo figlio esistente come endpoint annidate a un profilo padre esistente. Il profilo viene specificato utilizzando i nomi dei gruppi di profili e risorse.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Aggiornare un Endpoint di gestore del traffico

Esistono due modi per aggiornare un endpoint di gestore del traffico esistente:

1. Ottenere il profilo di gestore del traffico utilizzando Get-AzureRmTrafficManagerProfile, aggiornare le proprietà endpoint all'interno del profilo e il commit delle modifiche tramite Set AzureRmTrafficManagerProfile. Questo metodo ha il vantaggio di effettuare l'aggiornamento più endpoint in un'unica operazione.
2. Ottenere l'endpoint di gestore del traffico utilizzando Get-AzureRmTrafficManagerEndpoint, aggiornare le proprietà endpoint e il commit delle modifiche tramite Set AzureRmTrafficManagerEndpoint. Questo metodo è semplice, poiché non richiede l'indicizzazione nella matrice endpoint nel profilo.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Esempio 1: Aggiornamento endpoint utilizzando Get-AzureRmTrafficManagerProfile e AzureRmTrafficManagerProfile Set

In questo esempio viene modificata la priorità in due punti finali all'interno di un profilo esistente.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Esempio 2: Aggiornamento di un estremo utilizzando Get-AzureRmTrafficManagerEndpoint e AzureRmTrafficManagerEndpoint Set

In questo esempio abbiamo modificare lo spessore di un singolo endpoint di un profilo esistente.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Attivare e disattivare i punti finali e dei profili

Gestore del traffico consente endpoint singoli attivato e disattivato, e consentire l'attivazione e disattivazione dei profili interi.
Queste modifiche possono essere eseguite da Guida/aggiornamento/impostazione risorse endpoint o profilo. Per semplificare le operazioni comuni, sono supportati anche tramite i cmdlet dedicati.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Esempio 1: Attivazione e disattivazione di un profilo di gestore del traffico

Per abilitare un profilo di gestore del traffico, utilizzare AzureRmTrafficManagerProfile attiva. È possibile specificare il profilo utilizzando un oggetto profilo. L'oggetto profilo può essere passato attraverso la pipeline o usando il '-TrafficManagerProfile' parametro. In questo esempio abbiamo specificare il profilo dal nome del gruppo profilo e delle risorse.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Per disattivare un profilo di gestore del traffico:

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Il cmdlet Disabilita AzureRmTrafficManagerProfile chiede di confermare. Questo messaggio può essere eliminato utilizzando il "-forza ' parametro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Esempio 2: Attivazione e disattivazione di un endpoint di gestore del traffico

Per abilitare un endpoint di gestore del traffico, utilizzare AzureRmTrafficManagerEndpoint attiva. Esistono due modi per specificare il punto finale

1. Utilizzando un oggetto TrafficManagerEndpoint attraverso la pipeline o il '-TrafficManagerEndpoint' parametro
2. Con il nome dell'endpoint, tipo di endpoint, il nome del profilo e nome gruppo di risorse:

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Analogamente, per disattivare un endpoint di gestore del traffico:

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Come disabilitare AzureRmTrafficManagerProfile, il cmdlet Disabilita AzureRmTrafficManagerEndpoint chiede conferma. Questo messaggio può essere eliminato utilizzando il "-forza ' parametro.

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminazione di un estremo gestore del traffico

Per rimuovere singoli endpoint, utilizzare il cmdlet Rimuovi AzureRmTrafficManagerEndpoint:

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Questo cmdlet chiede di confermare l'operazione. Questo messaggio può essere eliminato utilizzando il "-forza ' parametro.

## <a name="delete-a-traffic-manager-profile"></a>Eliminare un profilo di gestore del traffico

Per eliminare un profilo di gestore del traffico, utilizzare il cmdlet Rimuovi AzureRmTrafficManagerProfile, specificando i nomi dei gruppi di profili e risorse:

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Questo cmdlet chiede di confermare l'operazione. Questo messaggio può essere eliminato utilizzando il "-forza ' parametro.

Il profilo per essere eliminato può essere specificato utilizzando un oggetto profilo:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Questa sequenza possa anche essere reindirizzata:

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passaggi successivi

[Gestore del traffico monitoraggio](traffic-manager-monitoring.md)

[Considerazioni sulle prestazioni di gestore del traffico](traffic-manager-performance-considerations.md)
