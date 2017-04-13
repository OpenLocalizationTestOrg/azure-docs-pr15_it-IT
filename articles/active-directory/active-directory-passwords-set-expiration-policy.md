<properties
    pageTitle="Impostare criteri di scadenza della password di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come controllare i criteri di scadenza e modificare la scadenza delle password utente singolarmente o in blocco per le password di Azure Active directory"
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
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Impostare i criteri di scadenza della password in Azure Active Directory

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Come amministratore globale per un servizio cloud di Microsoft, è possibile utilizzare il Microsoft Azure Active Directory modulo per Windows PowerShell per impostare le password degli utenti non scada. Utilizzare i cmdlet di Windows PowerShell per rimuovere il-scadenza configurazione o per vedere quale utente password siano configurate non a scadenza. In questo articolo viene fornita assistenza per i servizi cloud, ad esempio Microsoft Intune e Office 365, si basano su Microsoft Azure Active Directory per i servizi di identità e directory.

  > [AZURE.NOTE] Solo la password dell'account utente che non vengono sincronizzati tramite la sincronizzazione delle directory può essere configurata per non scada. Per ulteriori informazioni sulla sincronizzazione della directory, vedere l'elenco degli argomenti in [roadmap sulla sincronizzazione della Directory](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Per utilizzare i cmdlet di Windows PowerShell, è innanzitutto necessario installarli.

## <a name="what-do-you-want-to-do"></a>Cosa si desidera eseguire?

- [Come si verifica criteri di scadenza della password](#how-to-check-expiration-policy-for-a-password)

- [Impostare una password con scadenza](#set-a-password-to-expire)

- [Impostare una password in modo che non scadrà](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Come si verifica criteri di scadenza della password

1.  Connettersi a Windows PowerShell usando le credenziali di amministratore della società.

2.  Eseguire una delle operazioni seguenti:

    - Per vedere se la password di un singolo utente senza scadenza, eseguire il seguente cmdlet usando il nome dell'entità utente (UPN) (ad esempio aprilr@contoso.onmicrosoft.com) o l'ID dell'utente che si desidera selezionare:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - Per visualizzare l'impostazione "Nessuna scadenza Password" per tutti gli utenti, eseguire il cmdlet seguente:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Impostare una password con scadenza

1.  Connettersi a Windows PowerShell usando le credenziali di amministratore della società.

2.  Eseguire una delle operazioni seguenti:

    - Per impostare la password di un utente in modo che la password scadrà, eseguire il seguente cmdlet usando il nome dell'entità utente (UPN) o l'ID utente dell'utente:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`

    - Per impostare le password di tutti gli utenti dell'organizzazione in modo che essi scadrà, utilizzare il cmdlet seguente:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Connettersi a Windows PowerShell usando le credenziali di amministratore della società.

2.  Eseguire una delle operazioni seguenti:

    - Per impostare la password di un utente senza scadenza, eseguire il seguente cmdlet usando il nome dell'entità utente (UPN) o l'ID utente dell'utente:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`

    - Per impostare le password di tutti gli utenti di un'organizzazione scada mai, eseguire il cmdlet seguente:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Passaggi successivi

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
