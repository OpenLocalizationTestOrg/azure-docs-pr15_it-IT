<properties
    pageTitle="Modificare l'ID di tenant archivio chiave dopo lo spostamento di un abbonamento | Microsoft Azure"
    description="Informazioni su come cambiare l'ID tenant di un archivio chiave dopo lo spostamento di una sottoscrizione a un altro tenant"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modificare un ID tenant archivio chiave dopo lo spostamento di una sottoscrizione
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q: l'abbonamento è stato spostato da tenant A tenant B. Come modificare l'ID tenant per la chiave archivio esistente e impostazione degli ACL corretti per le identità nel tenant B

Quando si crea un nuovo archivio di chiave in una sottoscrizione, è automaticamente collegata all'ID tenant di Azure Active Directory predefinito per la sottoscrizione. Tutte le voci di criteri di accesso sono inoltre collegate a questo ID tenant. Quando si sposta l'abbonamento Azure da tenant A tenant B, il chiavi archivi esistenti non sono accessibili da entità (utenti e applicazioni) nel tenant B. Per risolvere il problema, è necessario:

- Modificare l'ID tenant associato tutti archivi chiavi esistenti nella sottoscrizione al tenant B.
- Rimuovere tutte le voci di criteri di accesso.
- Aggiungere nuove voci dei criteri di accesso che sono associate a tenant B.

Ad esempio, se si dispone di archivio chiave 'myvault' in una sottoscrizione che è stata spostata dal tenant A B, ecco del tenant come modificare l'ID tenant per questo archivio chiave e rimuovere i criteri di accesso precedente.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). ResourceId $vault = AzureRmResource Get-ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() AzureRmResource Set - ResourceId $vaultResourceId-proprietà $vault. Proprietà
</pre>

Perché questo archivio è stato nel tenant A prima dello spostamento, il valore originale del **$vault. Properties.TenantId** è a tenant, mentre si **(Get-AzureRmContext). Tenant.TenantId** tenant B.

Ora che l'archivio è associato all'ID tenant corretto e vengono rimosse le voci dei criteri di accesso precedente, impostare voci dei criteri con [AzureRmKeyVaultAccessPolicy Set di](https://msdn.microsoft.com/library/mt603625.aspx)nuovo l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Nel caso di domande su Azure chiave archivio, visitare i [Forum di archivio di Azure chiave](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
