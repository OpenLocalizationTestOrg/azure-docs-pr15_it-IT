<properties
    pageTitle="Concedere autorizzazioni agli utenti per i criteri specifici laboratorio | Microsoft Azure"
    description="Informazioni su come concedere autorizzazioni agli utenti ai criteri di laboratorio specifiche nei DevTest in base alle esigenze di ogni utente"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Concedere autorizzazioni agli utenti ai criteri di laboratorio specifico

## <a name="overview"></a>Panoramica

In questo articolo viene illustrato come usare PowerShell per concedere autorizzazioni agli utenti a un criterio di laboratorio specifico. In questo modo, le autorizzazioni possono essere applicate in base alle esigenze di ogni utente. È consigliabile, ad esempio, consentire a un utente specifico di modificare le impostazioni dei criteri di macchine Virtuali, ma non i criteri di costo.

## <a name="policies-as-resources"></a>Criteri come risorse

Come illustrato nell'articolo [Controllo dell'accesso basato sui ruoli di Azure](../active-directory/role-based-access-control-configure.md) , RBAC consente la gestione di accesso diffusamente delle risorse per Azure. Usa RBAC, è possibile separare i compiti all'interno del team di attrezzi e concedere solo la quantità di accesso per gli utenti che hanno bisogno per svolgere il proprio lavoro.

In serie di esercitazioni DevTest, un criterio è un tipo di risorsa che consente l'azione RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Ogni criterio laboratorio è una risorsa in tipo di risorsa criteri e può essere assegnata come ambito per un ruolo RBAC.

Ad esempio, in modo da concedere agli utenti lettura/scrittura dell'autorizzazione per i criteri di **Dimensioni macchine Virtuali consentite** , è necessario creare un ruolo personalizzato che funziona con **Microsoft.DevTestLab/labs/policySets/policies/** * azione e quindi assegnare gli utenti a questo ruolo personalizzato nell'ambito della * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Per ulteriori informazioni sui ruoli personalizzati in RBAC, vedere il [controllo dell'accesso ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Creazione di un ruolo personalizzato laboratorio tramite PowerShell
Per iniziare, è necessario leggere l'articolo seguente, che spiega come installare e configurare i cmdlet di PowerShell Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Dopo aver impostato i cmdlet di PowerShell di Azure, è possibile eseguire le operazioni seguenti:

- Elenco di tutte le operazioni/azioni per un provider di risorse
- Azioni elenco in un particolare ruolo:
- Creare un ruolo personalizzato

Lo script di PowerShell seguente vengono illustrati esempi su come eseguire le operazioni seguenti:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Assegnazione di autorizzazioni a un utente per un determinato criterio utilizzando ruoli personalizzati
Dopo avere definito i ruoli personalizzati, è possibile assegnare agli utenti. Per assegnare un ruolo personalizzato a un utente, è innanzitutto necessario ottenere **ObjectId** che rappresenta l'utente. A tale scopo, utilizzare il cmdlet **Get-AzureRmADUser** .

Nell'esempio seguente, **ObjectId** dell'utente *SomeUser* è 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Dopo avere inserito **ObjectId** per l'utente e un nome di ruolo personalizzato, è possibile assegnare tale ruolo all'utente con il cmdlet **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Nell'esempio precedente, viene utilizzato il criterio **AllowedVmSizesInLab** . È possibile utilizzare i seguenti criteri:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi

Una volta sono state concesse le autorizzazioni degli utenti ai criteri di laboratorio specifico, ecco alcuni passaggi successivi da considerare:

- [Proteggere l'accesso a un ambiente di lavoro](devtest-lab-add-devtest-user.md).

- [Impostare i criteri di laboratorio](devtest-lab-set-lab-policy.md).

- [Creare un modello di laboratorio](devtest-lab-create-template.md).

- [Creare elementi personalizzati per le macchine virtuali](devtest-lab-artifact-author.md).

- [Aggiungi una macchina virtuale con gli elementi da un ambiente di lavoro](devtest-lab-add-vm-with-artifacts.md).
