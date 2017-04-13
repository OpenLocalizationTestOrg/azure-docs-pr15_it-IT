<properties
    pageTitle="Criteri password e le restrizioni di Azure Active Directory | Microsoft Azure"
    description="Vengono descritti i criteri che si applicano alle password di Azure Active Directory, inclusi i caratteri consentiti, lunghezza e scadenza"
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


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Criteri password e le restrizioni di Azure Active Directory

In questo articolo vengono illustrati i requisiti di complessità associati agli account utente memorizzati nella directory di Azure Active Directory e criteri password.

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente

Ogni account utente che è necessario eseguire l'accesso al sistema di autenticazione di Azure Active Directory deve essere presente un valore di attributo di nome principale (UPN) utente univoco associato a quell'account. Descritti nella tabella seguente i criteri che riguardano entrambi locale gli account utente di origine di Active Directory (sincronizzati nel cloud) e agli account utente basata solo su cloud.

|   Proprietà           |     Requisiti di UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Caratteri consentiti    |  <ul> <li>A-Z</li> <li>+ z </li><li>0-9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Caratteri non consentiti  | <ul> <li>Qualsiasi '@' carattere che non sia l'estrazione del nome utente da quello del dominio.</li> <li>Non può contenere un carattere punto '.' che precede il '@' simbolo</li></ul> |
| Vincoli di lunghezza  |       <ul> <li>Lunghezza totale non deve superare 113 caratteri</li><li>64 caratteri prima di ‘@’ simbolo</li><li>48 caratteri dopo il ‘@’ simbolo</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente cloud

Nella tabella seguente descrive le impostazioni dei criteri password disponibili che possono essere applicate agli account utente che vengono creati e gestiti in Azure Active Directory.

|  Proprietà       |    Requisiti          |
|   ----------------------- |   ----------------------- |
|  Caratteri consentiti   |   <ul><li>A-Z</li><li>+ z </li><li>0-9</li> <li>@# $ % ^ & * - _ ! + = [] {} & #124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Caratteri non consentiti   |       <ul><li>Caratteri Unicode</li><li>Spazi</li><li> **Solo password complesse**: non può contenere un carattere punto '.' che precede il '@' simbolo</li></ul> |
|   Restrizioni password | <ul><li>8 caratteri minimi e massimo 16 caratteri</li><li>**Solo password complesse**: richiede 3 fuori 4 delle operazioni seguenti:<ul><li>Caratteri minuscoli</li><li>Caratteri maiuscoli</li><li>Numeri (0-9)</li><li>Simboli (vedere restrizioni della password sopra)</li></ul></li></ul> |
| Durata scadenza password      | <ul><li>Il valore predefinito: **90** giorni </li><li>Valore può essere configurato tramite il cmdlet Set-MsolPasswordPolicy da di Azure Active Directory modulo per Windows PowerShell.</li></ul> |
| Notifica della scadenza password |  <ul><li>Il valore predefinito: **14** giorni (prima della scadenza della password)</li><li>Valore può essere configurato tramite il cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Scadenza password |  <ul><li>Il valore predefinito: giorni **false** (indica che la scadenza password è attivata) </li><li>Valore può essere configurato per gli account utente utilizzando il cmdlet Set-MsolUser. </li></ul> |
|  Cronologia delle password  | Ultima password non può essere riutilizzato. |
|  Durata cronologia delle password | Per sempre |
|  Blocco dell'account | Dopo 10 accesso tentativi (password errata), verrà bloccato l'utente per un minuto. Ulteriori tentativi di accesso corretti verranno bloccato l'utente per aumentare la durata. |


## <a name="next-steps"></a>Passaggi successivi

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [Gestire le password da qualsiasi luogo](active-directory-passwords.md)
* [Funzionamento della gestione delle Password](active-directory-passwords-how-it-works.md)
* [Guida introduttiva di gestione del Password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle Password](active-directory-passwords-customize.md)
* [Procedure consigliate per Gestione password](active-directory-passwords-best-practices.md)
* [Come ottenere informazioni dettagliate sui operativi con i report di gestione delle Password](active-directory-passwords-get-insights.md)
* [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md)
* [Risolvere i problemi di gestione delle Password](active-directory-passwords-troubleshoot.md)
* [Ulteriori informazioni](active-directory-passwords-learn-more.md)
