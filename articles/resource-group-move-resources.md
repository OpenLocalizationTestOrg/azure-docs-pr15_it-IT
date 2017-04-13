<properties 
    pageTitle="Spostare le risorse al nuovo gruppo di risorse | Microsoft Azure" 
    description="Gestione delle risorse Azure consente di spostare le risorse per il nuovo gruppo di risorse o l'abbonamento." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Spostare le risorse al nuovo gruppo di risorse o l'abbonamento

In questo argomento viene illustrato come spostare le risorse per un nuovo abbonamento o un nuovo gruppo di risorse nello stesso abbonamento. È possibile usare il portale, PowerShell, CLI Azure o API REST per spostare risorsa. Le operazioni di spostamento in questo argomento sono disponibili per l'utente senza assistenza dal supporto Azure.

In genere, spostare le risorse se si decide che:

- Ai fini della fatturazione, una risorsa deve dinamica in una sottoscrizione diversa.
- Una risorsa non è più condivide il ciclo di vita stesso come risorse che precedentemente raggruppato con. Si desidera spostarlo in un nuovo gruppo di risorse in modo che è possibile gestire la risorsa separatamente da altre risorse.

Quando si spostano le risorse, gruppo di origine e il gruppo di destinazione sono bloccate durante l'operazione. Scrivere e le operazioni di eliminazione vengono bloccati per i gruppi fino al completamento dello spostamento.

Non è possibile modificare la posizione della risorsa. Spostamento di una risorsa spostarlo solo in un nuovo gruppo di risorse. Il nuovo gruppo di risorse abbia un percorso diverso, ma che non modificare la posizione della risorsa.

> [AZURE.NOTE] In questo articolo viene descritto come spostare risorse all'interno di un Azure esistente account offerta. Se si desidera effettivamente modificare account Azure che offre (ad esempio, l'aggiornamento da uso prepagato pre-pagare) pur continuando a lavorare con le risorse esistenti, vedere [modificare l'abbonamento Azure a un'altra proposta](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Elenco di controllo prima di spostamento di risorse

Esistono alcuni passaggi importanti da eseguire prima di spostare una risorsa. Verificando queste condizioni, è possibile evitare errori.

1. Il servizio necessario abilitare la possibilità di spostare le risorse. Vedere l'elenco seguente per informazioni su quali [servizi consentono di spostamento delle risorse](#services-that-enable-move).
1. Le sottoscrizioni di origine e destinazione devono trovarsi all'interno di stesso [tenant di Active Directory](./active-directory/active-directory-howto-tenant.md). Per spostare in un nuovo tenant, contattare il supporto.
2. Sottoscrizione di destinazione deve essere registrata per il provider di risorse della risorsa viene spostato. In caso contrario, viene visualizzato un messaggio di errore indicante che l' **abbonamento non è registrato per un tipo di risorsa**. Questo problema possono verificarsi quando lo spostamento di una risorsa in una nuova sottoscrizione, ma che l'abbonamento non sia mai stato usato con il tipo di risorsa. Per informazioni su come controllare lo stato di registrazione e registrare i provider di risorse, vedere [tipi e provider di risorse](../resource-manager-supported-services.md#resource-providers-and-types).
4. Se si spostano servizio App app, si avranno terminato di rivedere [limitazioni del servizio di App](#app-service-limitations).
4. Se si spostano le risorse associate con i servizi di recupero, aver esaminato le [limitazioni dei servizi di recupero](#recovery-services-limitations)
5. Se si spostano le risorse distribuite tramite modello classico, aver esaminato [le limitazioni di distribuzione classica](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Quando contattare il supporto

È possibile spostare la maggior parte delle risorse tramite le operazioni modalità self-service illustrate in questo argomento. Utilizzare le operazioni in modalità self-service:

- Spostare le risorse di Manager delle risorse.
- Spostare le risorse classiche in base alle [limitazioni della distribuzione classica](#classic-deployment-limitations). 

Contattare il supporto quando è necessario:

- Spostare le risorse in un nuovo account Azure (e tenant di Active Directory).
- Spostare le risorse classiche ma si verificano problemi con le limitazioni.

## <a name="services-that-enable-move"></a>Servizi che consentono di spostamento

Per ora, i servizi che consentono di spostare a un nuovo gruppo di risorse e di abbonamento sono:

- Gestione dell'API
- Le applicazioni di servizio App (applicazioni web), vedere [limitazioni del servizio di App](#app-service-limitations)
- Automazione
- Batch
- RETE CDN
- Servizi cloud, vedere [limitazioni della distribuzione classica](#classic-deployment-limitations)
- Dati Factory
- DNS
- DocumentDB
- HDInsight cluster
- Hub IoT
- Archivio di chiave 
- Servizi multimediali di Microsoft
- Impegno per dispositivi mobili
- Hub di notifica
- Approfondimenti operativi
- Redis Cache
- Utilità di pianificazione
- Ricerca
- Bus di servizio
- Spazio di archiviazione
- Spazio di archiviazione (classico), vedere [limitazioni di distribuzione classica](#classic-deployment-limitations)
- Database SQL server - il database e il server deve trovarsi nello stesso gruppo di risorse. Quando si sposta un server SQL, vengono spostati anche tutti i database.
- Macchine virtuali - tuttavia funziona non supporta spostata in una nuova sottoscrizione quando i certificati sono archiviati in un archivio di chiave
- Macchine virtuali (classico), vedere [limitazioni di distribuzione classica](#classic-deployment-limitations)
- Reti virtuali

## <a name="services-that-do-not-enable-move"></a>Servizi che non consentono di spostamento

I servizi che attualmente non consentono di spostare una risorsa sono:

- Gateway di applicazioni
- Informazioni dettagliate sui applicazione
- Strada
- Archivio di servizi di recupero - anche non spostare le risorse di elaborazione, di rete e di archiviazione associate all'archivio di servizi di recupero, vedere [limitazioni dei servizi di recupero](#recovery-services-limitations).
- Macchine virtuali con certificato archiviato nella chiave archivio
- Set di scala macchine virtuali
- Reti virtuali (classico), vedere [limitazioni di distribuzione classica](#classic-deployment-limitations)
- Gateway VPN

## <a name="app-service-limitations"></a>Limitazioni del servizio di App

Quando si utilizzano le applicazioni di servizio App, non è possibile spostare un piano di servizio di App. Per spostare le applicazioni di servizio App, le opzioni disponibili sono:

- Spostare il piano di servizio di App e tutte le altre App servizio risorse di tale gruppo di risorse in un nuovo gruppo di risorse che non dispone già di risorse del servizio di App. Questo requisito indica che è necessario spostare anche l'App servizio alle risorse che non sono associato al piano di servizio di App. 
- Spostare le App in un gruppo di risorse diversi, ma mantenere tutti i piani di servizio App nel gruppo di risorse originale.

Se il gruppo di risorse originale include anche una risorsa approfondimenti applicazione, è possibile spostare tale risorsa poiché approfondimenti applicazione non è attualmente attiva l'operazione di spostamento. Se si include la risorsa applicazione approfondimenti quando si spostano le applicazioni di servizio App, l'operazione di spostamento intera. Tuttavia, l'applicazione approfondimenti e piano di servizio App non devono trovarsi nello stesso gruppo di risorse con l'app per l'app per il corretto funzionamento.

Ad esempio, se il gruppo di risorse contiene:

- **Web a** cui è associato **piano-a** e **approfondimenti un'app**
- **web-b** è associata al **piano b** e **app-approfondimenti-b**

Le opzioni disponibili sono:

- Spostare **web-a**, **piano-a**, **b web**e **piano-b**
- Spostare **web-a** e **b web**
- Spostare **un oggetto di web**
- Spostare **web-b**

Tutte le altre combinazioni implicano l'invio di spostamento di un tipo di risorsa che non è possibile spostare (applicazione approfondimenti) o uscire da dietro a un tipo di risorsa che non può essere lasciato dietro quando si sposta un piano di servizio App (qualsiasi tipo di risorsa servizio App).

Se un'app web risiede in un gruppo di risorse diversi rispetto a un piano di servizio di App, ma si desidera spostare entrambe a un nuovo gruppo di risorse, è necessario eseguire lo spostamento in due passaggi. Per esempio:

- **Web a** cui si trova nel **gruppo web**
- **pianificare un** si trova nel **gruppo piano**
- Si desidera **web-a** e **piano-a** inserire nel **gruppo combinati**

Per completare lo spostamento, eseguire due operazioni di spostamento separata nella sequenza delle operazioni seguente:

1. Spostare il **web** al **piano di gruppo**
2. Spostare **web-a** e **piano di un** gruppo **combinate**.

È possibile spostare un certificato di servizio App per il nuovo gruppo di risorse o l'abbonamento senza problemi. Tuttavia, se l'app web include un certificato SSL acquistata esternamente e caricare l'app, è necessario eliminare il certificato prima di spostare l'applicazione web. Ad esempio, è possibile eseguire la procedura seguente:

1. Eliminare il certificato caricato da web app
2. Spostare il web app
3. Caricare il certificato nell'app web

## <a name="recovery-services-limitations"></a>Limitazioni di servizi di recupero

Spostare non è abilitata per l'archiviazione, rete, o calcolare le risorse utilizzate per configurare il ripristino di emergenza con il ripristino del sito di Azure. 

Si supponga ad esempio, si dispone di impostare la replica del computer locale a un account di archiviazione (Storage1) e si vuole computer protetto a sorgere dopo il failover in Azure come macchina virtuale (VM1) collegata a una rete virtuale (rete domestica1). Non è possibile spostare qualsiasi di queste risorse Azure - Storage1, VM1 e rete domestica1 - attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni.

## <a name="classic-deployment-limitations"></a>Limitazioni della distribuzione classica

Le opzioni per lo spostamento risorse distribuite tramite il modello classico variano in base se si spostano le risorse all'interno di un abbonamento o in una nuova sottoscrizione. 

### <a name="same-subscription"></a>Stesso abbonamento

Quando si spostano le risorse da un gruppo di risorse in un altro gruppo di risorse all'interno della stessa sottoscrizione, le restrizioni seguenti:

- Reti virtuali (classiche) non possono essere spostate.
- Con il servizio cloud, è necessario spostare macchine virtuali (classico). 
- Servizio cloud può essere spostata solo quando lo spostamento include tutte le macchine virtuali.
- Servizio sola cloud può essere spostata contemporaneamente.
- Un solo account di archiviazione (classico) possono essere spostate contemporaneamente.
- Account di archiviazione (classica) può essere spostato nella stessa operazione con una macchina virtuale o un servizio cloud.

Per spostare le risorse classiche in un nuovo gruppo di risorse all'interno della stessa sottoscrizione, utilizzare le operazioni di spostamento standard tramite il [portale](#use-portal), [PowerShell di Azure](#use-powershell), [CLI Azure](#use-azure-cli)oppure [API REST](#use-rest-api). Utilizzare le stesse operazioni quando si usa per spostare le risorse di Manager delle risorse.

### <a name="new-subscription"></a>Nuova sottoscrizione

Quando si spostano le risorse in una nuova sottoscrizione, le restrizioni seguenti:

- Nella stessa operazione, è necessario spostare tutte le risorse classiche nella sottoscrizione.
- La sottoscrizione di destinazione non può contenere qualsiasi altra risorsa classica.
- Lo spostamento può essere richiesti solo tramite un API REST separata per gli spostamenti classici. I comandi di spostamento standard di gestione risorse non funzionano quando si spostano le risorse classiche a un nuovo abbonamento.

Per spostare le risorse classiche in una nuova sottoscrizione, è necessario utilizzare altre operazioni di specifiche alle risorse classiche. Eseguire i passaggi seguenti per spostare le risorse classiche in una nuova sottoscrizione.

1. Verificare se la sottoscrizione di origine partecipare spostamento di una croce abbonamento. Utilizzare la seguente operazione:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     Nel corpo della richiesta includono:

         {
           "role": "source"
         }

     La risposta per l'operazione di convalida è nel formato seguente:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Verificare se l'abbonamento di destinazione faranno spostamento di una croce abbonamento. Utilizzare la seguente operazione:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     Nel corpo della richiesta includono:

         {
           "role": "target"
         }

     La risposta è lo stesso formato la convalida dell'abbonamento di origine.

3. Se entrambe le sottoscrizioni superano la convalida, spostare tutte le risorse classiche da una sottoscrizione a un'altra sottoscrizione con la seguente operazione:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    Nel corpo della richiesta includono:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

L'operazione è possibile eseguire per alcuni minuti. 

## <a name="use-portal"></a>Utilizzare portale

Per spostare le risorse in un nuovo gruppo di risorse nella **stessa sottoscrizione**, selezionare il gruppo di risorse contenente tali risorse e quindi selezionare il pulsante **Sposta** .

![spostare le risorse](./media/resource-group-move-resources/edit-rg-icon.png)

In alternativa, per spostare le risorse in un **nuovo abbonamento**, selezionare il gruppo di risorse contenente tali risorse e quindi selezionare l'icona di sottoscrizione di modifica.

![spostare le risorse](./media/resource-group-move-resources/change-subscription.png)

Selezionare le risorse da spostare e il gruppo di risorse di destinazione. Confermare che è necessario aggiornare gli script per queste risorse e fare clic su **OK**. Se si seleziona l'icona di sottoscrizione di modifica nel passaggio precedente, è necessario selezionare anche la sottoscrizione di destinazione.

![Seleziona destinazione](./media/resource-group-move-resources/select-destination.png)

In **notifiche**, viene visualizzato che l'operazione di spostamento sia in esecuzione.

![visualizzare lo stato di spostamento](./media/resource-group-move-resources/show-status.png)

Quando è stata completata, verrà notificata la presenza del risultato.

![Mostra il risultato di spostamento](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Usare PowerShell

Per spostare le risorse esistenti in un altro gruppo di risorse o l'abbonamento, usare il comando **Sposta AzureRmResource** .

Nel primo esempio viene illustrato come spostare una risorsa in un nuovo gruppo di risorse.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Il secondo esempio viene illustrato come spostare più risorse in un nuovo gruppo di risorse.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Per spostare in una nuova sottoscrizione, includere un valore per il parametro **DestinationSubscriptionId** .

Viene chiesto di confermare che si desidera spostare le risorse specificate.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Utilizzare CLI Azure

Per spostare le risorse esistenti in un altro gruppo di risorse o l'abbonamento, usare il comando **Sposta azure delle risorse** . È necessario specificare l'ID risorsa delle risorse da spostare. È possibile ottenere gli ID risorsa con il comando seguente:

    azure resource list -g sourceGroup --json

Che restituisce nel formato seguente:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

Nell'esempio seguente viene illustrato come spostare un account di archiviazione in un nuovo gruppo di risorse. Nel parametro **-i** specificare che un elenco delimitato da virgole di id risorsa 's spostare.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Viene chiesto di confermare che si desidera spostare la risorsa specificata.

## <a name="use-rest-api"></a>Utilizzare API REST

Per spostare le risorse esistenti in un altro gruppo di risorse o l'abbonamento, eseguire:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Nel corpo della richiesta, specificare il gruppo di risorse di destinazione e le risorse per spostarsi. Per ulteriori informazioni sull'operazione resto sposta, vedere [spostare le risorse](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sui cmdlet di PowerShell per gestire l'abbonamento, vedere [Uso di PowerShell Azure con Gestione risorse](powershell-azure-resource-manager.md).
- Per informazioni sui comandi CLI Azure per la gestione dell'abbonamento, vedere [utilizzo di Azure CLI Gestione risorse](xplat-cli-azure-resource-manager.md).
- Per informazioni sulle funzionalità del portale di gestione dell'abbonamento, vedere [tramite il portale di Azure per gestire le risorse](./azure-portal/resource-group-portal.md).
- Per informazioni sull'applicazione di un'organizzazione logica alle risorse, vedere [tramite tag per organizzare le risorse](resource-group-using-tags.md).
