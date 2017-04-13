
<properties
    pageTitle="Gestire le risorse con CLI Azure | Microsoft Azure"
    description="Usare l'interfaccia della riga di Azure (CLI) per gestire i gruppi e risorse Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Consente di gestire i gruppi di risorse e risorse Azure CLI Azure


> [AZURE.SELECTOR]
- [Portale](azure-portal/resource-group-portal.md) 
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


L'interfaccia della riga di comando di Azure (Azure CLI) è uno dei diversi strumenti per distribuire e gestire le risorse con Gestione risorse. In questo articolo vengono presentati comuni modi per gestire le risorse Azure e gruppi di risorse utilizzando CLI Azure in modalità di gestione risorse. Per informazioni sull'utilizzo CLI per distribuire le risorse, vedere [risorse di distribuzione con i modelli di Manager delle risorse e Azure CLI](resource-group-template-deploy-cli.md). Per informazioni generali sulle risorse Azure e Manager delle risorse, vedere [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Per gestire le risorse Azure con CLI Azure, è necessario [installare CLI Azure](xplat-cli-install.md)e [accedere al Azure](xplat-cli-connect.md) utilizzando il `azure login` comando. Assicurarsi che la CLI è in modalità di gestione risorse (eseguire `azure config mode arm`). Se si esegue questa operazione queste operazioni, si è pronto per l'invio.



## <a name="get-resource-groups-and-resources"></a>Ottenere risorse e gruppi di risorse

### <a name="resource-groups"></a>Gruppi di risorse

Per ottenere un elenco di tutti i gruppi di risorse in abbonamento e le relative posizioni, eseguire il comando.

    azure group list
    

### <a name="resources"></a>Risorse
 Per elencare tutte le risorse in un gruppo, ad esempio uno con nome *testRG*, utilizzare il comando seguente.

    azure resource list testRG

Per visualizzare una singola risorsa all'interno del gruppo, ad esempio una macchina virtuale denominata *MyUbuntuVM*, utilizzare un comando simile al seguente.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Si noti il parametro **Microsoft.Compute/virtualMachines** . Questo parametro indica il tipo di risorsa su che richiesta informazioni.
    
>[AZURE.NOTE]Quando si utilizzano i comandi di **risorsa azure** ad eccezione del comando **elenco** , è necessario specificare la versione dell'API della risorsa con il parametro **-o** . In caso di dubbi sulla versione dell'API, consultare il file del modello e trovare il campo apiVersion per la risorsa. Per ulteriori informazioni sulle versioni delle API in Gestione risorse, vedere [provider di gestione delle risorse, aree, versioni API e gli schemi](resource-manager-supported-services.md).

Quando si visualizzano i dettagli su una risorsa, è spesso utile usare la `--json` parametro. Questo parametro consente di output più leggibile sono strutture nidificate o le raccolte di alcuni valori. Nell'esempio seguente viene mostrato come restituire i risultati del comando **Mostra** come documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Se si desidera salvare i dati JSON in file utilizzando il &gt; carattere per indirizzare l'output in un file. Per esempio:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Tag

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gestire le risorse


Per aggiungere una risorsa, ad esempio un account di archiviazione a un gruppo di risorse, eseguire un comando simile a:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Oltre a specificare la versione dell'API della risorsa con il parametro **-o** , utilizzare il parametro **-p** per passare una stringa di formato JSON con le proprietà necessarie o altri.
    
    
Per eliminare una risorsa esistente, ad esempio una risorsa macchina virtuale, utilizzare un comando simile al seguente.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Per spostare le risorse esistenti in un altro gruppo di risorse o l'abbonamento, usare il comando **Sposta azure delle risorse** . Nell'esempio seguente viene illustrato come spostare una Cache Redis in un nuovo gruppo di risorse. Nel parametro **-i** specificare che un elenco delimitato da virgole di id risorsa 's spostare.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controllare l'accesso alle risorse

È possibile utilizzare CLI Azure per creare e gestire i criteri per controllare l'accesso alle risorse di Azure. Per informazioni generali sulla definizione dei criteri e sull'assegnazione di criteri alle risorse, vedere [utilizzare criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

Ad esempio, definire i criteri seguenti per rifiutare tutte le richieste in cui deve trovarsi non usa ovest o North Central US e salvare la policy.json di file di definizione dei criteri:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Eseguire il comando **Creazione definizione dei criteri** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Questo comando Visualizza output simile al seguente.

    + Creazione di definizione dei criteri MyPolicy dati: PolicyName: MyPolicy dati: PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    dati: PolicyType: dati personalizzata: DisplayName: non definito dati: descrizione: non definito dati: PolicyRule: campo = posizione, in = [westus, northcentralus], effetto = negare

 Per assegnare un criterio nell'ambito desiderato, usare **PolicyDefinitionId** restituito rispetto al comando precedente. Nell'esempio seguente l'ambito è la sottoscrizione, ma è possibile definire l'ambito per i gruppi di risorse o delle risorse individuali:

    assegnazione criteri Azure creare MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

È possibile ottenere, modificare o rimuovere definizioni di criteri, utilizzare i comandi **Mostra definizione dei criteri**, **impostare criteri definition**ed **eliminare definizione dei criteri** .

Analogamente, è possibile ottenere, modificare o rimuovere le assegnazioni dei criteri, utilizzare i comandi **Mostra l'assegnazione dei criteri**, **Imposta l'assegnazione dei criteri**ed **eliminare l'assegnazione dei criteri** .


## <a name="export-a-resource-group-as-a-template"></a>Esportare un gruppo di risorse come modello

Per un gruppo di risorse esistente, è possibile visualizzare il modello di Manager delle risorse per il gruppo di risorse. Esportazione del modello offre due vantaggi:

1. È possibile automatizzare facilmente distribuzioni future della soluzione perché tutti l'infrastruttura è definita nel modello.

2. È possibile acquisire familiarità con la sintassi dei modelli esaminando JSON che rappresenta la soluzione.

Usa CLI Azure, è possibile esportare un modello che rappresenta lo stato corrente del gruppo di risorse o scaricare il modello è stato usato per una distribuzione particolare.

* **Esportare il modello per un gruppo di risorse** : questa funzionalità è utile quando si apportato modifiche a un gruppo di risorse ed è necessario recuperare la rappresentazione JSON dello stato corrente. Tuttavia, il modello generato contiene solo un numero minimo di parametri e non le variabili. La maggior parte dei valori nel modello siano hardcoded. Prima di distribuire il modello generato, si desidera convertire più valori nei parametri in modo che è possibile personalizzare la distribuzione per ambienti diversi.

    Per esportare il modello per un gruppo di risorse in una directory locale, eseguire la `azure group export` comando come illustrato nell'esempio seguente. (Sostituire una directory locale appropriata per l'ambiente del sistema operativo).

        azure group export testRG ~/azure/templates/

* **Scaricare il modello per una specifica distribuzione** : questa funzionalità è utile quando si desidera visualizzare il modello effettivo utilizzato per la distribuzione di risorse. Il modello include tutti i parametri e variabili definite per la distribuzione originale. Tuttavia, se un utente dell'organizzazione apportato modifiche al gruppo di risorse di fuori di definizione del modello, questo modello non rappresenta lo stato corrente del gruppo di risorse.

    Per scaricare il modello usato per una specifica distribuzione di una directory locale, eseguire la `azure group deployment template download` comando. Per esempio:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Esporta modello è in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si tenta di esportare un modello, venga visualizzato un messaggio di errore indicante che alcune risorse non sono stati esportati. Se è necessario definire manualmente queste risorse nel modello scelto dopo il download di esso.



## <a name="next-steps"></a>Passaggi successivi

* Per ottenere dettagli delle operazioni di distribuzione e risolvere i problemi di distribuzione con CLI Azure, vedere [le operazioni di distribuzione di visualizzazione con Azure CLI](resource-manager-troubleshoot-deployments-cli.md).
* Se si desidera usare CLI per configurare un'applicazione o script per accedere alle risorse, vedere [Utilizzare Azure CLI per creare un servizio principale per accedere alle risorse](resource-group-authenticate-service-principal-cli.md).


