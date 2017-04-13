<properties
    pageTitle="Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi | Microsoft Azure"
    description="Come gestire le impostazioni per i gruppi di uso dei cmdlet di Azure Active Directory."
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
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi

Le seguenti per i gruppi di unificata possono essere configurate nella directory:

1.  Classificazioni: l'elenco delimitato da virgole di classificazioni che gli utenti possono impostare un gruppo. Esempi sono "Classificato", "Segreto" e "Segreto superiore".

2.  URL di linee guida per l'utilizzo: un URL che punta agli utenti le condizioni di utilizzo per l'utilizzo di gruppi unificata, come definito dall'organizzazione. Questo URL verrà visualizzato nell'interfaccia utente in cui gli utenti utilizzano i gruppi.

3.  Raggruppare Creazione abilitata: se nessuna, alcuni o tutti gli utenti possono creare gruppi unificata. Se è impostato su on, tutti gli utenti possono creare gruppi. Se è impostata su No, non sarà possibile creare gruppi. Quando è disattivato, è inoltre possibile specificare un gruppo di sicurezza cui gli utenti che ancora possono creare gruppi.

Queste impostazioni sono configurate con le impostazioni e oggetti SettingsTemplate. Inizialmente, non si vedranno tutti gli oggetti impostazioni nella directory. Questo errore indica che la directory è stata configurata con le impostazioni predefinite. Per modificare le impostazioni predefinite, si creerà un nuovo oggetto impostazioni utilizzando un modello di impostazioni. Impostazioni modelli sono definiti da Microsoft.

È possibile scaricare il modulo contenente i cmdlet usati per queste operazioni dal [sito Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Creare impostazioni di livello di directory

Questi passaggi creano impostazioni a livello di directory, che si applicano a tutti i gruppi di Office nella directory.

1. Se non si conosce quali SettingTemplate da utilizzare, questo cmdlet restituisce l'elenco dei modelli di impostazioni:

    `Get-MsolAllSettingTemplate`

    ![Elenco dei modelli di impostazioni](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Per aggiungere un URL delle linee guida l'uso, è necessario ottenere l'oggetto SettingsTemplate che definisce il valore di URL l'utilizzo di riferimento; vale a dire il modello Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Creare un nuovo oggetto impostazioni basato su tale modello:

    `$setting = $template.CreateSettingsObject()`

4. Quindi aggiornare il valore di indirizzo di utilizzo:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Infine, applicare le impostazioni:

    `New-MsolSettings –SettingsObject $setting`

    ![Aggiungere un URL delle linee guida sull'utilizzo](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Ecco le impostazioni definite in Group.Unified SettingsTemplate.

 **Impostazione**                          | **Descrizione**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Tipo: stringa<li>Predefinito: ""                  | Elenco delimitato da virgole dei valori di classificazione valido che può essere applicato ai gruppi unificata.                
 <ul><li>EnableGroupCreation<li>Tipo: booleano<li>Predefinito: vero              | Contrassegno che indica se la creazione di gruppi unificato è consentita nella directory.                               
 <ul><li>GroupCreationAllowedGroupId<li>Tipo: stringa<li>Predefinito: ""         | GUID del gruppo di sicurezza che è possibile creare gruppi unificata anche quando EnableGroupCreation = = false.
 <ul><li>UsageGuidelinesUrl<li>Tipo: stringa<li>Predefinito: ""                  | Collegamento alle linee guida sull'utilizzo di gruppo.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Leggere le impostazioni a livello di directory

Questa procedura, leggere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office nella directory.

1. Leggere tutte le impostazioni directory esistente:

    `Get-MsolAllSettings`

2. Leggere tutte le impostazioni per un gruppo specifico:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Leggere le impostazioni directory specifica, utilizzando SettingId GUID:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![Impostazioni ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Aggiornare le impostazioni di livello di directory

Questa procedura Aggiorna le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office nella directory.

1. Ottenere l'oggetto impostazioni esistente:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Ottenere il valore che si desidera aggiornare:

    `$value = $Setting.GetSettingsValue()`

3. Aggiornare il valore:

    `$value["AllowToAddGuests"] = "false"`

4. Aggiornare le impostazioni seguenti:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Rimuovere le impostazioni a livello di directory

Questo passaggio consente di rimuovere le impostazioni a livello di directory, che si applicano a tutti i gruppi di Office nella directory.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Riferimento sui cmdlet di sintassi

È possibile trovare altre documentazione di Azure Active Directory PowerShell al [Cmdlet di Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Riferimenti a oggetti di SettingsTemplate (Group.Unified SettingsTemplate oggetto)

- "nome": "EnableGroupCreation", "tipo": "Boolean", "defaultValue": "true", "descrizione": "Flag di boolean che indica se la funzionalità di creazione gruppo unificato è attivata."

- "nome": "GroupCreationAllowedGroupId", "tipo": "GUID", "defaultValue": "", "descrizione": "GUID del gruppo di sicurezza è whitelisted per creare gruppi unificato".

- "nome": "ClassificationList", "tipo": "String", "defaultValue": "", "descrizione": "Delimitato elenco di valori di classificazione valida che possono essere applicati ai gruppi unificato."

- "nome": "UsageGuidelinesUrl", "tipo": "String", "defaultValue": "", "descrizione": "Un collegamento alle linee guida sull'utilizzo di gruppo".

nome | tipo | defaultValue | Descrizione
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | "Boolean"  | "true"  | "Contrassegno di boolean che indica se la funzionalità di creazione gruppo unificato è attivata."
"GroupCreationAllowedGroupId"  | "GUID"  | ""  | "GUID del gruppo di sicurezza è whitelisted per creare gruppi unificata".
"ClassificationList"  | "String"  | ""  | "Delimitato elenco di valori di classificazione valido che può essere applicato ai gruppi unificata."
"UsageGuidelinesUrl"  | "String"  | ""  | "Un collegamento alle linee guida sull'utilizzo di gruppo".

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare altre documentazione di Azure Active Directory PowerShell al [Cmdlet di Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Ulteriori istruzioni da program manager Microsoft Rob de Jong sono disponibile [di Gerardino gruppi di](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)blog.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
