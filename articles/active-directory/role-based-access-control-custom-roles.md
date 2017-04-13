<properties
    pageTitle="Ruoli personalizzati in RBAC Azure | Microsoft Azure"
    description="Informazioni su come definire ruoli personalizzati con il controllo dell'accesso Azure Role-Based per la gestione delle identità più preciso nell'abbonamento Azure."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Ruoli personalizzati in RBAC Azure


Creare un ruolo personalizzato nel controllo di accesso Azure Role-Based (RBAC) se nessuno dei ruoli predefiniti soddisfa esigenze specifiche di access. Ruoli personalizzati possono essere creati in [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [interfaccia riga di comando Azure](role-based-access-control-manage-access-azure-cli.md) (CLI) e le [API REST](role-based-access-control-manage-access-rest.md). Come ruoli incorporati, è possono assegnare ruoli personalizzati per gli utenti, gruppi e le applicazioni abbonamento, gruppo di risorse e ambiti per le risorse. Ruoli personalizzati vengono memorizzati in un tenant di Azure Active Directory e possono essere condiviso tra tutte le sottoscrizioni che utilizzano tenant come directory di Azure Active Directory per il subsciption.

Di seguito è illustrato un esempio di un ruolo personalizzato per il monitoraggio e il riavvio di macchine virtuali di:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Azioni
Proprietà **Azioni** di un ruolo personalizzato specifica le operazioni di Azure a cui concede l'accesso. Si tratta di un insieme di stringhe di operazione che identificano le operazioni che è possibile proteggere del provider di risorse Azure. Stringhe di operazione che contengono i caratteri jolly (\*) concedere l'accesso a tutte le operazioni che corrispondono alla stringa operazione. Ad esempio:

-   `*/read`concede l'accesso alle operazioni di lettura per tutti i tipi di risorse di tutti i provider di risorse Azure.
-   `Microsoft.Network/*/read`concede l'accesso alle operazioni di lettura per tutti i tipi di risorse del provider di risorse Microsoft.Network di Azure.
-   `Microsoft.Compute/virtualMachines/*`concede l'accesso a tutte le operazioni di macchine virtuali e figlio tipi di risorse.
-   `Microsoft.Web/sites/restart/Action`concede l'accesso a riavviare siti Web.

Usare `Get-AzureRmProviderOperation` (in PowerShell) o `azure provider operations show` (in Azure CLI) per elencare le operazioni di provider di risorse Azure. È anche possibile utilizzare questi comandi per verificare la validità una stringa di operazione e per espandere le stringhe di operazione con caratteri jolly.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Immagine di PowerShell - Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | Operazione FT, invece](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Mostra le operazioni di provider schermata - azure di CLI Azure "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Utilizzare la proprietà **NotActions** se le operazioni che si desidera consentire più facilmente sono stata definita escludendo operazioni con restrizioni. Accesso da un ruolo personalizzato viene calcolato sottraendo le operazioni di **NotActions** da operazioni di **Azioni** .

> [AZURE.NOTE] Se un utente viene assegnato un ruolo che esclude un'operazione di **NotActions**e viene assegnato un ruolo secondo che concede l'accesso per la stessa operazione, l'utente potrà eseguire tale operazione. **NotActions** non è una regola, è sufficiente utile per creare un set di operazioni consentite quando operazioni specifiche è opportuno escludere.

## <a name="assignablescopes"></a>AssignableScopes
La proprietà **AssignableScopes** del ruolo personalizzato specifica gli ambiti (abbonamenti, gruppi di risorse o risorse) in cui è disponibile per l'assegnazione ruolo personalizzato. È possibile rendere disponibili per l'assegnazione ruolo personalizzato in solo gli abbonamenti o i gruppi di risorse in cui è necessaria e non creano confusione esperienza utente per il resto degli abbonamenti o dei gruppi di risorse.

Esempi di ambiti di assegnare validi:

-   "/ abbonamenti/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ abbonamenti/e91d47c4-76f3-4271-a796-21b4ecfe3624" - rende disponibili per l'assegnazione al ruolo negli abbonamenti a due.
-   "abbonamenti/c276fc76-9cd4-44c9-99a7-4fd71546436e" - rende disponibili per l'assegnazione del ruolo in una singola sottoscrizione.
-  "/ abbonamenti/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/rete" - rende disponibili per l'assegnazione del ruolo solo nel gruppo di risorse di rete.

> [AZURE.NOTE] È necessario utilizzare almeno un abbonamento, gruppo di risorse o ID risorsa.

## <a name="custom-roles-access-control"></a>Ruoli personalizzati accedere controllo
La proprietà **AssignableScopes** del ruolo personalizzato controlla anche chi può visualizzare, modificare ed eliminare il ruolo.

- Utenti autorizzati a creare un ruolo personalizzato?
    I proprietari (e gli amministratori di accesso utente) degli abbonamenti, gruppi di risorse e le risorse possono creare ruoli personalizzati per l'utilizzo in tali ambiti.
    Utente che crea il ruolo deve essere in grado di eseguire `Microsoft.Authorization/roleDefinition/write` operazione su tutte le **AssignableScopes** del ruolo.

- Chi può modificare un ruolo personalizzato?
    I proprietari (e gli amministratori di accesso utente) degli abbonamenti, gruppi di risorse e le risorse possono modificare ruoli personalizzati in tali ambiti. Gli utenti devono essere in grado di eseguire il `Microsoft.Authorization/roleDefinition/write` operazione su tutti i **AssignableScopes** di un ruolo personalizzato.

- Chi può visualizzare ruoli personalizzati?
    Tutti i ruoli predefiniti di Azure RBAC consentono la visualizzazione dei ruoli disponibili per un'assegnazione. Gli utenti che possono essere eseguite le `Microsoft.Authorization/roleDefinition/read` operazione in un ambito può visualizzare i ruoli RBAC che sono disponibili per l'assegnazione in tale ambito.

## <a name="see-also"></a>Vedere anche
- [Controllo dell'accesso basato su ruoli](role-based-access-control-configure.md): Guida introduttiva a RBAC nel portale di Azure.
- Informazioni su come gestire l'accesso con:
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
    - [API REST](role-based-access-control-manage-access-rest.md)
- [Ruoli predefiniti](role-based-access-built-in-roles.md): informazioni sui ruoli disponibili standard in RBAC.
