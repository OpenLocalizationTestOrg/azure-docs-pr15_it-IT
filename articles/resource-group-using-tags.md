<properties
    pageTitle="Uso dei contrassegni per organizzare le risorse Azure | Microsoft Azure"
    description="Viene illustrato come applicare contrassegni per organizzare le risorse per la fatturazione e la gestione."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Uso dei contrassegni per organizzare le risorse Azure

Manager delle risorse consente di organizzare in modo logico risorse per l'applicazione di contrassegni. I contrassegni che è costituito da coppie chiave/valore che identificano le risorse con le proprietà definite dall'utente. Per contrassegnare risorse come appartenenti alla stessa categoria, applicare lo stesso tag alle risorse.

Quando si visualizzano le risorse con un determinato tag, viene visualizzato risorse da tutti i gruppi di risorse. Non si è limitato alle risorse stesso gruppo di risorse, che consente di organizzare le risorse in modo indipendente le relazioni di distribuzione. Tag può essere utile quando si desidera organizzare le risorse per la fatturazione o la gestione.

Ogni tag che si aggiunge a una risorsa o il gruppo viene aggiunto automaticamente alla tassonomia a livello di sottoscrizione. Immissione della tassonomia per l'abbonamento con i nomi dei tag e i valori che si desidera utilizzare come risorse vengono contrassegnate in futuro.

Ogni risorsa o il gruppo può avere un massimo di 15 tag. Il nome di tag è limitato ai 512 caratteri e il valore di tag è limitato a 256 caratteri.

> [AZURE.NOTE] È possibile applicare contrassegni solo alle risorse che supportano le operazioni di gestione risorse. Se è stato creato una macchina virtuale, virtuali o lo spazio di archiviazione tramite il modello di distribuzione classico (ad esempio tramite il portale classico), non è possibile applicare un contrassegno a quella risorsa. Per supportare tagging, ridistribuire queste risorse tramite Gestione risorse. Tutte le altre risorse di supporto tagging.

## <a name="templates"></a>Modelli

Per contrassegnare una risorsa durante la distribuzione, è sufficiente aggiungere l'elemento **tag** per la risorsa che si desidera distribuire e specificare il nome di tag e il valore. Il nome di tag e il valore non è necessario già presenti nell'abbonamento. È possibile fornire fino a 15 tag per ogni risorsa.

Nell'esempio seguente mostra un account di archiviazione con un tag.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Manager delle risorse attualmente non supporta l'elaborazione di un oggetto per i nomi dei tag e i valori. Se, tuttavia, passare un oggetto per i valori di tag, ma è comunque necessario specificare i nomi dei tag, come illustrato nell'esempio seguente.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portale

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST

Il portale e PowerShell entrambi utilizzare l' [REST API Manager delle risorse](https://msdn.microsoft.com/library/azure/dn848368.aspx) in background. Se è necessario integrare tagging in un ambiente diverso, è possibile ottenere tag con un GET sull'id di risorsa e aggiornare il set di tag con una chiamata di PATCH.


## <a name="tags-and-billing"></a>Contrassegni e fatturazione

Per i servizi supportati, è possibile utilizzare tag per raggruppare i dati di fatturazione. Ad esempio [macchine virtuali integrata con Gestione risorse di Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) consentono di definire e applicare contrassegni per organizzare l'utilizzo di fatturazione per macchine virtuali. Se si esegue più macchine virtuali per le organizzazioni diverse, è possibile usare i contrassegni per l'utilizzo di gruppo per centro di costo.  
Utilizzare i contrassegni per categorizzare i costi dall'ambiente di runtime; ad esempio, di fatturazione per l'utilizzo macchine virtuali in esecuzione nell'ambiente di produzione.

È possibile recuperare informazioni tag mediante [API RateCard e l'utilizzo delle risorse di Azure](billing-usage-rate-card-overview.md) o il file con valori delimitati da virgole (CSV) l'uso. Scaricare il file l'uso dal [portale di account Azure](https://account.windowsazure.com/) o [portale EA](https://ea.azure.com). Per ulteriori informazioni sull'accesso alle informazioni di fatturazione, vedere [ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md). Per le operazioni di API REST, vedere [Riferimenti alle API REST di fatturazione Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando si scarica l'utilizzo CSV per i servizi che supportano tag con la fatturazione, i contrassegni che vengono visualizzati nella colonna **categorie** . Per ulteriori informazioni, vedere [informazioni sulla fattura per Microsoft Azure](billing/billing-understand-your-bill.md).

![Visualizzare tag di fatturazione](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Passaggi successivi

- È possibile applicare restrizioni e convenzioni attraverso l'abbonamento con criteri personalizzati. I criteri impostati potrebbero richiedere che tutte le risorse presenta un valore per un determinato tag. Per ulteriori informazioni, vedere [Utilizzare criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
- Per un'introduzione all'utilizzo di PowerShell Azure quando la distribuzione delle risorse, vedere [Uso di PowerShell Azure Gestione risorse Azure](./powershell-azure-resource-manager.md).
- Per un'introduzione all'utilizzo di Azure CLI quando la distribuzione delle risorse, vedere [utilizzo CLI Azure per Mac, Linux e Windows Azure gestione delle risorse](./xplat-cli-azure-resource-manager.md).
- Per un'introduzione all'utilizzo del portale, vedere [tramite il portale di Azure per gestire le risorse Azure](./azure-portal/resource-group-portal.md)  
