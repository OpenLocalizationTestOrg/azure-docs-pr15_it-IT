<properties
    pageTitle="Gestione di controllo dell'accesso basato sui ruoli con l'API REST"
    description="Gestione di controllo dell'accesso basato sui ruoli con l'API REST"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Gestione di controllo dell'accesso basato sui ruoli con l'API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

Basato sui ruoli accesso controllo (RBAC) nel portale di Azure e Azure Manager delle risorse API consente di gestire l'accesso alle risorse a livello diffusamente abbonamento. Con questa caratteristica, è possibile concedere l'accesso per gli utenti, gruppi o identità di servizio Active Directory mediante l'assegnazione di alcuni ruoli ad essi in un particolare ambito.

## <a name="list-all-role-assignments"></a>Elenco di tutte le assegnazioni di ruolo

Elenca tutte le assegnazioni di ruolo all'ambito specificato e subscopes.

Per le assegnazioni di ruolo di elenco, è necessario avere accesso a `Microsoft.Authorization/roleAssignments/read` operazione nell'ambito. Tutti i ruoli predefiniti ad accedere a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **GET** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito per i quali si desidera elencare le assegnazioni di ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{versione api}* con 2015-07-01.

3. Sostituire *{filtro}* con la condizione che si desidera applicare per filtrare l'elenco di assegnazione di ruolo:

  - Elencare i ruoli assegnati a solo l'ambito specificato, escluse le assegnazioni di ruolo in subscopes:`atScope()`    
  - Elencare i ruoli assegnati a un utente specifico, gruppo o dell'applicazione:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Elencare i ruoli assegnati a un utente specifico, inclusi quelli ereditata dallo gruppi |`assignedTo('{objectId of user}')`

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Ottenere informazioni su un'assegnazione di ruolo

Ottiene informazioni su un'assegnazione di ruolo singola specificata dall'identificatore di assegnazione di ruolo.

Per informazioni su un'assegnazione di ruolo, è necessario avere accesso a `Microsoft.Authorization/roleAssignments/read` operazione. Tutti i ruoli predefiniti ad accedere a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **GET** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito per i quali si desidera elencare le assegnazioni di ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di assegnazione ruolo}* con l'identificatore GUID l'assegnazione di ruolo.

3. Sostituire *{versione api}* con 2015-07-01.

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Creare un'assegnazione di ruolo

Creare un'assegnazione di ruolo nell'ambito specificato per l'entità specificata la concessione di ruolo specificato.

Per creare un'assegnazione di ruolo, è necessario avere accesso a `Microsoft.Authorization/roleAssignments/write` operazione. Ruoli predefiniti solo *proprietario* e *l'Amministratore di accesso utente* consentito l'accesso a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **inserire** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito in cui si desidera creare le assegnazioni di ruolo. Quando si crea un'assegnazione di ruolo in un ambito padre, tutti gli ambiti figlio ereditano la stessa assegnazione di ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1   
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di assegnazione ruolo}* con un nuovo GUID, diventa l'identificatore GUID della nuova assegnazione di ruolo.

3. Sostituire *{versione api}* con 2015-07-01.

Per il corpo della richiesta, fornire i valori nel formato seguente:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome dell'elemento     | Obbligatorio | Tipo   | Descrizione |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sì      | Stringa | Identificatore del ruolo. Il formato dell'identificatore è:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Sì      | Stringa | objectId dell'identità di Azure Active Directory (utente, gruppo o principale del servizio) a cui è assegnato il ruolo. |

### <a name="response"></a>Risposta

Codice di stato: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Eliminare un'assegnazione di ruolo

Eliminare un'assegnazione di ruolo nell'ambito specificato.

Per eliminare un'assegnazione di ruolo, è necessario avere accesso per la `Microsoft.Authorization/roleAssignments/delete` operazione. Ruoli predefiniti solo *proprietario* e *l'Amministratore di accesso utente* consentito l'accesso a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **DELETE** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito in cui si desidera creare le assegnazioni di ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di assegnazione ruolo}* con l'id di assegnazione di ruolo GUID.

3. Sostituire *{versione api}* con 2015-07-01.

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Elenco tutti i ruoli

Elenca tutti i ruoli che sono disponibili per l'assegnazione all'ambito specificato.

Ai ruoli di elenco, è necessario avere accesso a `Microsoft.Authorization/roleDefinitions/read` operazione nell'ambito. Tutti i ruoli predefiniti ad accedere a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **GET** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito per i quali si desidera elencare i ruoli. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - /Subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 delle risorse  

2. Sostituire *{versione api}* con 2015-07-01.

3. Sostituire *{filtro}* con la condizione che si desidera applicare per filtrare l'elenco dei ruoli:

  - Elenco ruoli disponibili per l'assegnazione all'ambito specificato e qualsiasi degli ambiti figlio:`atScopeAndBelow()`
  - Ricerca per un ruolo con nome visualizzato: `roleName%20eq%20'{role-display-name}'`. Utilizzare la forma di codifica URL del nome visualizzato del ruolo. Ad esempio,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Ottenere informazioni su un ruolo

Ottiene informazioni su un singolo ruolo specificato dall'identificatore di definizione ruolo. Per informazioni su un singolo ruolo utilizzando il relativo nome visualizzato, vedere [elenco di tutti i ruoli](role-based-access-control-manage-access-rest.md#list-all-roles).

Per informazioni su un ruolo, è necessario avere accesso a `Microsoft.Authorization/roleDefinitions/read` operazione. Tutti i ruoli predefiniti ad accedere a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **GET** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito per i quali si desidera elencare le assegnazioni di ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di definizione ruolo}* con l'identificatore GUID della definizione di ruolo.

3. Sostituire *{versione api}* con 2015-07-01.

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato
Creare un ruolo personalizzato.

Per creare un ruolo personalizzato, è necessario avere accesso a `Microsoft.Authorization/roleDefinitions/write` operazione su tutti i `AssignableScopes`. Ruoli predefiniti solo *proprietario* e *l'Amministratore di accesso utente* consentito l'accesso a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **inserire** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con la prima *AssignableScope* del ruolo personalizzato. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli.

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di definizione ruolo}* con un nuovo GUID, diventa l'identificatore GUID del nuovo ruolo personalizzato.

3. Sostituire *{versione api}* con 2015-07-01.

Per il corpo della richiesta, fornire i valori nel formato seguente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome dell'elemento | Obbligatorio | Tipo | Descrizione |
|--------------|----------|------|-------------|
| nome         | Sì | Stringa   | Identificatore GUID del ruolo personalizzato.    |
| properties.roleName               | Sì | Stringa   | Nome visualizzato del ruolo personalizzato. Caratteri di dimensioni massime 128.                        |
| Properties.Description            | No  | Stringa   | Descrizione del ruolo personalizzato. Caratteri di dimensioni massime 1024.                                               |
| Properties.Type                   | Sì | Stringa   | Impostare su "CustomRole".                                         |
| Properties.Permissions.Actions    | Sì | String] | Matrice di stringhe di azione che specifica le operazioni concesse dal ruolo personalizzato.             |
| properties.permissions.notActions | No  | String] | Matrice di stringhe di azione che specifica le operazioni da escludere dall'operazioni concesse dal ruolo personalizzato. |
| properties.assignableScopes       | Sì | String] | Matrice di ambiti in cui è possibile utilizzare il ruolo personalizzato.   |

### <a name="response"></a>Risposta

Codice di stato: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Modificare un ruolo personalizzato.

Per modificare un ruolo personalizzato, è necessario avere accesso a `Microsoft.Authorization/roleDefinitions/write` operazione su tutti i `AssignableScopes`. Ruoli predefiniti solo *proprietario* e *l'Amministratore di accesso utente* consentito l'accesso a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **inserire** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con la prima *AssignableScope* del ruolo personalizzato. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di definizione ruolo}* con l'identificatore GUID del ruolo personalizzato.

3. Sostituire *{versione api}* con 2015-07-01.

Per il corpo della richiesta, fornire i valori nel formato seguente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome dell'elemento | Obbligatorio | Tipo | Descrizione |
|--------------|----------|------|-------------|
| nome         | Sì      | Stringa | Identificatore GUID del ruolo personalizzato. |
| properties.roleName | Sì | Stringa | Nome visualizzato del ruolo personalizzato aggiornato. |
| Properties.Description | No | Stringa | Descrizione del ruolo personalizzato aggiornato. |
| Properties.Type | Sì | Stringa | Impostare su "CustomRole". |
| Properties.Permissions.Actions | Sì | String] | Matrice di stringhe di azione che specifica le operazioni a cui il ruolo personalizzato aggiornato concede l'accesso. |
| properties.permissions.notActions | No | String] | Matrice di stringhe di azione che specifica le operazioni da escludere dalle operazioni che concede il ruolo personalizzato aggiornato. |
| properties.assignableScopes | Sì | String] | Matrice di ambiti in cui può essere utilizzato il ruolo personalizzato aggiornato. |

### <a name="response"></a>Risposta

Codice di stato: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

Eliminare un ruolo personalizzato.

Per eliminare un ruolo personalizzato, è necessario avere accesso a `Microsoft.Authorization/roleDefinitions/delete` operazione su tutti i `AssignableScopes`. Ruoli predefiniti solo *proprietario* e *l'Amministratore di accesso utente* consentito l'accesso a questa operazione. Per ulteriori informazioni sulle assegnazioni di ruolo e per le risorse Azure che gestisce l'accesso, vedere [Il controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Richiesta

Utilizzare il metodo **DELETE** con il seguente URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

All'interno di URI, effettuare le seguenti sostituzioni per personalizzare la richiesta:

1. Sostituire *{ambito}* con l'ambito in cui si desidera eliminare la definizione del ruolo. Negli esempi seguenti viene illustrato come specificare l'ambito per i diversi livelli:

  - Abbonamento: /subscriptions/ {id abbonamento}  
  - Gruppo risorse: /subscriptions/ {id abbonamento} / resourceGroups/myresourcegroup1  
  - Risorsa: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Sostituire *{id di definizione ruolo}* con l'id di definizione di ruolo GUID del ruolo personalizzato.

3. Sostituire *{versione api}* con 2015-07-01.

### <a name="response"></a>Risposta

Codice di stato: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
