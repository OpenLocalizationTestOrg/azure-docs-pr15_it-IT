<properties
    pageTitle="Gruppi di Azure Active Directory dedicato | Microsoft Azure"
    description="Panoramica dei gruppi come dedicati funzionano in Azure Active Directory e modalità di creazione."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Gruppi dedicati in Azure Active Directory

In Azure Active Directory (Azure Active Directory), la funzione gruppi dedicato automaticamente crea e popola appartenenza per i gruppi di Azure Active Directory predefinite. Membri dei gruppi dedicati non possono essere aggiunti o rimossi tramite il portale classica Azure, cmdlet di Windows PowerShell, o a livello di programmazione.

>[AZURE.NOTE] Gruppi dedicati richiedono l'assegnazione di una licenza di Azure Active Directory Premium a
>- l'amministratore che gestisce la regola su un gruppo
>- tutti gli utenti sono selezionati per la regola dovrà essere membro del gruppo

**Per abilitare i gruppi dedicati**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi aprire la directory dell'organizzazione.

2. Selezionare la scheda **gruppi** e quindi aprire il gruppo che si desidera modificare.

3. Selezionare la scheda **Configura** e quindi impostare **Abilitare gruppi dedicato** su **Sì**.

Dopo l'opzione Abilita dedicato gruppi è impostata su **Sì**, è possibile abilitare ulteriormente la directory creare automaticamente il gruppo dedicato tutti gli utenti mediante l'impostazione di **abilitare "Tutti gli utenti" gruppo** passare su **Sì**. Quindi è possibile inoltre modificare il nome del gruppo dedicato digitandola nella **nome visualizzato per "Tutti gli utenti" gruppo** campo.

Il gruppo tutti gli utenti può essere utilizzato per assegnare le stesse autorizzazioni a tutti gli utenti nella directory. Ad esempio, è possibile concedere tutti gli utenti dell'accesso directory a un'applicazione di SaaS mediante l'assegnazione di accesso per il gruppo dedicato tutti gli utenti a questa applicazione.

Il gruppo tutti gli utenti dedicato include tutti gli utenti nella directory, inclusi i guest e gli utenti esterni. Se è necessario un gruppo che esclude gli utenti esterni, quindi possono essere svolte mediante la creazione di un gruppo con una regola basata su attributi dinamica, ad esempio le operazioni seguenti:

                (user.userPrincipalName -notContains "#EXT#@")

Per un gruppo che esclude tutti gli utenti Guest, usare una regola, ad esempio le operazioni seguenti:

                (user.userType -ne "Guest")

Per informazioni su come creare regole *Avanzate* (regole che possono contenere più confronti) per l'appartenenza ai gruppi dinamici, vedere [attributi di utilizzo per creare regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md).


Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
* [Che cos'è Azure Active Directory?](active-directory-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
