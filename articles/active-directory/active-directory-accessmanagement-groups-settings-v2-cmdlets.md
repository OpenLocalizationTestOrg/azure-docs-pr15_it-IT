<properties
    pageTitle="Azure Active Directory anteprima i cmdlet di PowerShell per la gestione del gruppo in Active Directory Azure | Microsoft Azure"
    description="Questa pagina vengono forniti esempi di PowerShell per gestire i gruppi di Azure Active Directory"
    keywords="Azure Active Directory, Azure Active Directory, PowerShell, gestione dei gruppi, gruppo"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Azure Active Directory anteprima cmdlet per la gestione del gruppo

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-groups-create-azure-portal.md)
- [Portale classica Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Il documento seguente fornirà con esempi su come usare PowerShell per gestire i gruppi di Azure Active Directory (Azure Active Directory).  Vengono inoltre fornite informazioni su come impostare se stessi con il modulo di Azure Active Directory PowerShell anteprima. Prima di tutto, è necessario [scaricare il modulo di Azure Active Directory PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Installare il modulo di Azure Active Directory PowerShell

Per installare il modulo di anteprima AzureAD PowerShell, utilizzare i comandi seguenti:

    PS C:\Windows\system32> install-module azureadpreview

Per verificare che sia stato installato il modulo di anteprima, usare il comando seguente:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa di cmdlet nel modulo AzureAD anteprima, fare riferimento alla [documentazione online](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Connessione alla directory
Prima di iniziare la gestione dei gruppi uso dei cmdlet di Azure Active Directory PowerShell preview, è necessario connettere la sessione di PowerShell alla directory che si desidera gestire. Per eseguire questa operazione, utilizzare il comando seguente:

    PS C:\Windows\system32> Connect-AzureAD -Force

Il cmdlet verrà chiesto di credenziali che si desidera utilizzare per accedere a directory. In questo esempio, si sta utilizzando karen@drumkit.onmicrosoft.com per accedere alla directory dimostrazione. Il cmdlet restituirà un messaggio di conferma per visualizzare che la sessione è stata stabilita alla directory:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

A questo punto è possibile iniziare a usare i cmdlet di anteprima AzureAD per gestire i gruppi nella directory.

## <a name="retrieving-groups"></a>Recupero di gruppi
Per recuperare i gruppi esistenti dalla directory è possibile utilizzare il cmdlet Get-AzureADGroups. Per recuperare tutti i gruppi nella directory, utilizzare il cmdlet senza parametri:

    PS C:\Windows\system32> get-azureadgroup

Il cmdlet restituirà tutti i gruppi nella directory connesso.

È possibile utilizzare il parametro - objectID per recuperare un gruppo specifico per i quali si specificare objectID del gruppo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Il cmdlet restituirà il gruppo di cui objectID associa il valore del parametro immesso:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

È possibile cercare un gruppo specifico utilizzando il filtro parametro-. Questo parametro utilizza una clausola di filtro ODATA e restituisce tutti i gruppi che corrispondono al filtro, come illustrato nell'esempio seguente:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Si noti che i cmdlet di AzureAD PowerShell implementano lo standard query OData, sono disponibili ulteriori informazioni [qui](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Creazione di gruppi
Per creare un nuovo gruppo nella directory, utilizzare il cmdlet New-AzureADGroup. Questo cmdlet consente di creare un nuovo gruppo di sicurezza denominato "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Aggiornamento di gruppi
Per aggiornare un gruppo esistente, utilizzare il cmdlet Set-AzureADGroup. In questo esempio, da modificare la proprietà DisplayName del gruppo "Amministratori Intune." Prima di tutto, si sta individuando il gruppo utilizzando il cmdlet Get-AzureADGroup e applicare un filtro tramite l'attributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Successivamente, stiamo cambiando la proprietà descrizione per il nuovo valore "Intune dispositivo amministratori":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Ora se è individuato il gruppo nuovo, è possibile vedere la proprietà Description viene aggiornata in modo da rispecchiare il nuovo valore:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Eliminazione di gruppi
Per eliminare gruppi dalla directory, utilizzare il cmdlet Rimuovi AzureADGroup come segue:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gestione dei membri dei gruppi
Se è necessario aggiungere nuovi membri a un gruppo, utilizzare il cmdlet AzureADGroupMember Aggiungi. Questo comando consente di aggiungere un membro al gruppo Administrators Intune che è stato usato nell'esempio precedente:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Il parametro - ObjectId è ObjectID del gruppo a cui si desidera aggiungere un membro e RefObjectId - è ObjectID dell'utente che si desidera aggiungere un membro al gruppo.

Per ottenere i membri di un gruppo esistenti, utilizzare il cmdlet Get-AzureADGroupMember, come in questo esempio:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Per rimuovere il membro che abbiamo aggiunto in precedenza al gruppo, utilizzare il cmdlet Rimuovi AzureADGroupMember, come illustrato di seguito:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Per verificare membership(s) gruppo di un utente, utilizzare il cmdlet selezionare AzureADGroupIdsUserIsMemberOf. Questo cmdlet consente di accedere come parametri ObjectId dell'utente per cui si desidera verificare l'appartenenza e un elenco di gruppi di cui si desidera controllare le appartenenze. L'elenco dei gruppi deve essere forniti sotto forma di una variabile complesso di tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", quindi è innanzitutto necessario creare una variabile con questo tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Successivamente, sono disponibili i valori per ID dei gruppi controllare l'attributo "ID dei gruppi" di questa variabile complesso:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

A questo punto, se si desidera controllare le appartenenze ai gruppi di un utente con 72cd4bbd-2594-40a2-935c-016f3cfeeeea ObjectID in base a gruppi in $g, bisogna serve:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Valore restituito è un elenco di gruppi a cui appartiene l'utente. È inoltre possibile applicare questo metodo per controllare l'appartenenza ai contatti, gruppi o identità di servizio per l'intero elenco dei gruppi, tramite selezionare AzureADGroupIdsContactIsMemberOf, selezionare AzureADGroupIdsGroupIsMemberOf o selezionare AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Gestione dei proprietari di gruppi
Per aggiungere un gruppo di proprietari, utilizzare il cmdlet AzureADGroupOwner Aggiungi:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Il parametro - ObjectId è ObjectID del gruppo a cui si desidera aggiungere un proprietario e RefObjectId - è ObjectID dell'utente che si desidera aggiungere come proprietario del gruppo.

Per recuperare i proprietari di un gruppo, utilizzare Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Il cmdlet restituirà l'elenco dei proprietari per il gruppo specificato:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se si desidera rimuovere un proprietario da un gruppo, utilizzare Rimuovi AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare altre documentazione di Azure Active Directory PowerShell al [Cmdlet di Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
