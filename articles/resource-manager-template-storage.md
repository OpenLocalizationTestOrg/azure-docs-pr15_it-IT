<properties
   pageTitle="Modello di Manager delle risorse per lo spazio di archiviazione | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di account di archiviazione tramite un modello."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Schema del modello di archiviazione account

Crea un account di archiviazione.

## <a name="schema-format"></a>Formato di schema

Per creare un account di archiviazione, aggiungere lo schema di seguito nella sezione risorse del modello.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- |
| tipo | Enumerazione<br />Obbligatorio<br />**Microsoft.Storage/storageAccounts**<br /><br />Tipo di risorsa da creare. |
| apiVersion | Enumerazione<br />Obbligatorio<br />**2015-06-15** o **Anteprima 01 di 05 2015**<br /><br />La versione dell'API da utilizzare per la creazione della risorsa. | 
| nome | Stringa<br />Obbligatorio<br />Tra i 3 e 24 caratteri, solo numeri e lettere minuscole.<br /><br />Il nome dell'account di archiviazione per creare. Il nome deve essere univoco in tutti Azure. È possibile utilizzare la funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione, come illustrato nell'esempio riportato di seguito. |
| posizione | Stringa<br />Obbligatorio<br />Area geografica che supporta gli account di archiviazione. Per determinare aree valide, vedere [supportati aree geografiche](resource-manager-supported-services.md#supported-regions).<br /><br />Area di ospitare account di archiviazione. |
| proprietà | Oggetto<br />Obbligatorio<br />[oggetto di proprietà](#properties)<br /><br />Oggetto che specifica il tipo di account di archiviazione da creare. |

<a id="properties" />
### <a name="properties-object"></a>oggetto di proprietà

| Nome | Valore |
| ---- | ---- | 
| accountType | Stringa<br />Obbligatorio<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**o **Premium_LRS**<br /><br />Il tipo di account di archiviazione. I valori consentiti corrispondono a Standard localmente ridondanti, ridondanti zona Standard, ridondante geografico Standard, Standard i geografico ridondante accesso in lettura e Premium localmente ridondanti. Per informazioni su questi tipi di account, vedere [replica di archiviazione Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Esempi

Nell'esempio seguente viene distribuito un account di archiviazione Standard localmente ridondanti con un nome univoco in base all'id di gruppo di risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Guida introduttiva modelli

Sono disponibili numerosi modelli di Guida introduttiva che includono un account di archiviazione. I modelli seguenti illustrano alcuni scenari comuni:

- [Creare un Account di archiviazione Standard](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Distribuzione semplice di una macchina virtuale di Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Distribuzione semplice di un VM Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Creare un profilo di rete CDN, un Endpoint CDN con un Account di archiviazione come origine](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Creare una Farm di SharePoint Availabilty alta con 9 macchine virtuali utilizzando l'estensione DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Distribuzione semplice di 5 nodo sicura servizio tessuti Cluster con tampone abilitata](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Creare una macchina virtuale da un'immagine di Windows con 4 dischi dati vuota](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali sull'archiviazione, vedere [Introduzione a Microsoft Azure archiviazione](./storage/storage-introduction.md).
- Ad esempio modelli che utilizzano un nuovo account di archiviazione con una macchina virtuale, vedere [distribuire un semplice macchine Virtuali Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) o [distribuire una macchina virtuale Windows semplice](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
