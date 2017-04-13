<properties 
    pageTitle="Bloccare le risorse con Gestione risorse | Microsoft Azure" 
    description="Impedire agli utenti di aggiornamento o l'eliminazione di alcune risorse applicando una restrizione a tutti gli utenti e ruoli." 
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
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Bloccare le risorse con Gestione risorse di Azure

Un amministratore, potrebbe essere necessario bloccare un abbonamento, gruppo di risorse o delle risorse per impedire ad altri utenti dell'organizzazione da un'accidentale eliminazione o la modifica delle risorse critiche. È possibile impostare il livello di blocco per **CanNotDelete** o di **sola lettura**. 

- **CanNotDelete** indica che gli utenti autorizzati possano comunque leggere e modificare una risorsa, ma non è possibile eliminarlo. 
- **Sola lettura** indica che gli utenti autorizzati possano leggere da una risorsa, ma non è possibile eliminarlo o eseguire azioni su di esso. L'autorizzazione per la risorsa è limitato al ruolo **lettore** . 

L'applicazione di **sola lettura** può generare risultati imprevisti perché alcune operazioni sembrano leggere operazioni richiedono effettivamente altre azioni. Ad esempio l'immissione di un blocco di **sola lettura** in un account di archiviazione impedisce tutti gli utenti elencati i tasti. Nell'elenco operazione di tasti viene gestita tramite una richiesta POST perché le chiavi restituite sono disponibili per le operazioni di scrittura. Per un altro esempio immissione di un blocco di **sola lettura** in risorsa servizio App impedisce Visual Studio Server Explorer la visualizzazione di file per la risorsa poiché che l'interazione richiede l'accesso di scrittura.

A differenza di controllo dell'accesso basato sui ruoli, utilizzare blocchi di gestione di applicare una restrizione in tutti gli utenti e ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo dell'accesso basato sui ruoli di Azure](./active-directory/role-based-access-control-configure.md).

Quando si applica un blocco in un ambito padre, tutte le risorse figlio ereditano lo stesso blocco. Risorse pari che aggiungere in un secondo momento ereditano il blocco dall'elemento padre. Il blocco più restrittivo nell'ereditarietà avrà la precedenza.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Chi può creare o eliminare blocchi all'interno dell'organizzazione

Per creare o eliminare blocchi management, è necessario avere accesso a **Microsoft.Authorization/\* ** o **Microsoft.Authorization/locks/\* ** azioni. Ruoli predefiniti solo **proprietario** e **l'Amministratore di accesso utente** disponga queste azioni.

## <a name="creating-a-lock-through-the-portal"></a>Creazione di un blocco tramite il portale

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Creazione di un blocco di un modello

Nell'esempio seguente viene illustrato un modello che crea un blocco su un account di archiviazione. Account di archiviazione su cui si desidera applicare il blocco viene fornito come parametro. Il nome del blocco viene creato concatenando il nome della risorsa con **/Microsoft.Authorization/** e il nome del blocco, in questo caso **myLock**.

Il tipo fornito è specifico per il tipo di risorsa. Per l'archiviazione, impostare il tipo "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Creazione di un blocco con API REST

È possibile bloccare le risorse distribuite con l' [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx). API REST consente di creare ed eliminare blocchi e recuperare le informazioni relative ai blocchi esistenti.

Per creare un blocco, eseguire:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

L'ambito potrebbe essere un abbonamento, gruppo di risorse o delle risorse. Il nome di blocco è qualsiasi elemento che si desidera chiamare il blocco. Per la versione di api, utilizzare **2015 01 / 01**.

Nella convocazione, includere un oggetto JSON che specifica le proprietà per il blocco.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Per esempi, vedere [API REST per i blocchi di gestione](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Creazione di un blocco con PowerShell Azure

È possibile bloccare risorse distribuite con PowerShell Azure utilizzando **Nuovo AzureRmResourceLock** come illustrato nell'esempio seguente.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell sono disponibili altri comandi per l'utilizzo di blocchi, ad esempio **Set AzureRmResourceLock** per aggiornare un lucchetto e **AzureRmResourceLock Rimuovi** per eliminare un blocco.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'uso dei blocchi di risorse, vedere [Verso il basso il Azure risorse di blocco](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Per altre informazioni sull'organizzazione in modo logico le risorse, vedere [usare i contrassegni per organizzare le risorse](resource-group-using-tags.md)
- Per modificare il gruppo di risorse in cui si trova una risorsa, vedere [spostare le risorse al nuovo gruppo di risorse](resource-group-move-resources.md)
- È possibile applicare restrizioni e convenzioni attraverso l'abbonamento con criteri personalizzati. Per ulteriori informazioni, vedere [Utilizzare criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
