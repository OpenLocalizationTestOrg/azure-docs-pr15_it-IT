
<properties
    pageTitle="Utilizzo degli attributi per creare regole avanzate per l'appartenenza ai gruppi in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come creare regole avanzate per l'inclusione di appartenenza al gruppo dinamico supportate espressione regola operatori e parametri."
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>Utilizzo degli attributi per creare regole avanzate per l'appartenenza ai gruppi in anteprima di Azure Active Directory

Portale di Azure offre la possibilità di creare regole avanzate per abilitare più complesse basate su attributo dinamiche appartenenze per i gruppi di anteprima di Azure Active Directory (Azure Active Directory). [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) In questo articolo viene descritta gli attributi delle regole di sintassi per creare le regole avanzate.

## <a name="to-create-the-advanced-rule"></a>Per creare la regola avanzata

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **gli utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

  ![Gestione degli utenti di apertura](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  Scegliere **tutti i gruppi**e **l'utenti e gruppi** .

  ![Apertura e il gruppi](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. Scegliere il comando **Aggiungi** e **l'utenti e gruppi: tutti i gruppi** .

  ![Aggiungi nuovo gruppo](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. Nella e **gruppo** , immettere un nome e una descrizione per il nuovo gruppo. Selezionare un **digitare l'appartenenza** di **Utente dinamica** o **Dispositivo dinamico**, a seconda che si vuole creare una regola per gli utenti o dispositivi e quindi selezionare **Aggiungi query dinamico**. Per gli attributi utilizzati per le regole di dispositivi, vedere [attributi di utilizzo per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

  ![Aggiungi regola di appartenenza dinamico](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. Scegliere e il **regole di appartenenza dinamica** , immettere la regola nella casella **Aggiungi regola avanzata dell'appartenenza dinamica** , premere INVIO e quindi selezionare **Crea** nella parte inferiore della stessa e.

7. Selezionare **Crea** e il **gruppo** per creare il gruppo.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Costruzione di corpo di una regola avanzata

La regola avanzata è possibile creare per le appartenenze dinamiche per i gruppi di è essenzialmente un'espressione binaria che è costituito da tre parti e genera un risultato true o FALSO. Le tre parti sono:

- Parametro a sinistra
- Operatore binario
- Costante di destra

Una regola avanzata completa sarà simile alla seguente: (leftParameter binaryOperator "RightConstant"), dove l'apertura e una parentesi di chiusura sono necessari per l'intera espressione binario, virgolette doppie sono necessari per la costante destra e la sintassi per il parametro sinistro è user.property. Una regola avanzata può essere composti più espressioni binarie separate da- e, - o e - gli operatori logici non.

Di seguito sono esempi di una regola avanzata correttamente strutturata:

- (user.department - eq "Vendite")- o (user.department - eq "Marketing")
- (user.department - eq "Vendite")- e - non (user.jobTitle-contiene "SDE")

Per l'elenco completo dei parametri supportati e gli operatori di regole di espressioni, vedere sezioni che seguono. Per gli attributi utilizzati per le regole di dispositivi, vedere [attributi di utilizzo per creare regole per gli oggetti dispositivo](#using-attributes-to-create-rules-for-device-objects).

La lunghezza totale del corpo della regola avanzata non può superare 2048 caratteri.

> [AZURE.NOTE]
>Funzioni stringa e regex sono maiuscole e minuscole. È anche possibile eseguire controlli Null, utilizzando $null come una costante, ad esempio, user.department - eq $null.
Stringhe contenenti le offerte "deve essere di escape utilizzando ' carattere, ad esempio user.department - eq \`"Vendite".

## <a name="supported-expression-rule-operators"></a>Operatori di regola espressioni supportate
Nella tabella seguente elenca tutti gli operatori di regole di espressione supportati e della relativa sintassi da utilizzare nel corpo della regola avanzata:

| Operatore        | Sintassi         |
|-----------------|----------------|
| Non è uguale al      | -ne            |
| È uguale a          | -eq            |
| Non inizia con | -notStartsWith |
| Inizia con     | -startsWith    |
| Non contiene    | -notContains   |
| Contiene        | -contiene      |
| Non corrispondenza       | -notMatch      |
| Confronta           | -corrispondono         |


## <a name="query-error-remediation"></a>Risoluzione di errori di query
La tabella seguente elenca i potenziali errori e su come correggerli, se si sono verificati

| Errore di analisi di query     | Uso di errore       | Utilizzo corretto             |
|-----------------------|-------------------|-----------------------------|
| Errore: Attributo non è supportato.                                      | (user.invalidProperty - eq "Valore")       | (user.department - eq "valore")<br/>Proprietà deve corrispondere uno dall' [elenco proprietà è supportata](#supported-properties).                          |
| Errore: Operatore non è supportato sull'attributo.                       | (user.accountEnabled-contiene VERO)                                                                               | (user.accountEnabled - eq vero)<br/>Proprietà è di tipo boolean. Utilizzare gli operatori supportati (-eq o - ne) tipo booleana nell'elenco precedente.                                                                                                                                   |
| Errore: Errore di compilazione di Query.                                      | (user.department - eq "Vendite")- e (user.department - eq "Marketing")(user.userPrincipalName-match"*@domain.ext") | (user.department - eq "Vendite")- e (user.department - eq "Marketing")<br/>Operatore logico deve corrispondere a uno dall'elenco proprietà supportate. (user.userPrincipalName-corrispondono ".*@domain.ext")or(user.userPrincipalName -corrispondono "@domain.ext$")Error nelle espressioni regolari. |
| Errore: Espressione binaria non è incluso nel formato corretto.                     | (user.department – eq "Vendite") (user.department - eq "Vendite") ("Vendite" user.department eq)                             | (user.accountEnabled - eq vero)- e (user.userPrincipalName-contiene"alias@domain")<br/>Query sono presenti più errori. Parentesi non nel posto giusto.                                                                                                                            |
| : Errore Errore sconosciuto durante la configurazione appartenenze dinamiche. | (user.accountEnabled - eq "True" e user.userPrincipalName-contiene"alias@domain")                               | (user.accountEnabled - eq vero)- e (user.userPrincipalName-contiene"alias@domain")<br/>Query sono presenti più errori. Parentesi non nel posto giusto.                                                                                                                            |

## <a name="supported-properties"></a>Proprietà supportate
Di seguito sono tutte le proprietà utente che è possibile utilizzare le regole avanzate:

### <a name="properties-of-type-boolean"></a>Proprietà di tipo boolean

Operatori consentiti

* -eq


* -ne


| Proprietà     | Valori consentiti  | Uso                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | vero falso      | user.accountEnabled - eq vero)  |
| dirSyncEnabled | vero null false | (user.dirSyncEnabled - eq vero) |

### <a name="properties-of-type-string"></a>Proprietà di tipo stringa

Operatori consentiti

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contiene


* -notContains


* -corrispondono


* -notMatch

| Proprietà                 | Valori consentiti                                                                                        | Uso                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Città                       | Qualsiasi valore stringa o $null                                                                           | (user.city - eq "valore")                                   |
| paese                    | Qualsiasi valore stringa o $null                                                                            | (user.country - eq "valore")                                |
| reparto                 | Qualsiasi valore stringa o $null                                                                          | (user.department - eq "valore")                             |
| displayName                | Qualsiasi valore stringa                                                                                 | (user.displayName - eq "valore")                            |
| facsimileTelephoneNumber   | Qualsiasi valore stringa o $null                                                                           | (user.facsimileTelephoneNumber - eq "valore")               |
| givenName                  | Qualsiasi valore stringa o $null                                                                           | (user.givenName - eq "valore")                              |
| jobTitle                   | Qualsiasi valore stringa o $null                                                                           | (user.jobTitle - eq "valore")                               |
| posta elettronica                       | Qualsiasi valore stringa o $null (indirizzo SMTP dell'utente)                                                  | (user.mail - eq "valore")                                   |
| mailNickName               | Qualsiasi valore stringa (alias di posta elettronica dell'utente)                                                            | (user.mailNickName - eq "valore")                           |
| dispositivi mobili                     | Qualsiasi valore stringa o $null                                                                           | (user.mobile - eq "valore")                                 |
| objectId                   | GUID dell'oggetto utente                                                                            | (user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | Nessuna DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - eq "DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | Qualsiasi valore stringa o $null                                                                            | (user.physicalDeliveryOfficeName - eq "valore")             |
| CAP                 | Qualsiasi valore stringa o $null                                                                            | (user.postalCode - eq "valore")                             |
| preferredLanguage          | Codice ISO 639-1                                                                                        | (user.preferredLanguage - eq "en-US")                      |
| sipProxyAddress            | Qualsiasi valore stringa o $null                                                                            | (user.sipProxyAddress - eq "valore")                        |
| stato                      | Qualsiasi valore stringa o $null                                                                            | (user.state - eq "valore")                                  |
| streetAddress              | Qualsiasi valore stringa o $null                                                                            | (user.streetAddress - eq "valore")                          |
| Cognome                    | Qualsiasi valore stringa o $null                                                                            | (user.surname - eq "valore")                                |
| telephoneNumber            | Qualsiasi valore stringa o $null                                                                            | (user.telephoneNumber - eq "valore")                        |
| usageLocation              | Codice paese lettere due                                                                           | (user.usageLocation - eq "Usa")                             |
| userPrincipalName          | Qualsiasi valore stringa                                                                                     | (user.userPrincipalName - eq"alias@domain")               |
| userType                   | guest membro $null                                                                                    | (user.userType - eq "Membro")                              |

### <a name="properties-of-type-string-collection"></a>Proprietà della raccolta di tipo stringa

Operatori consentiti

* -contiene


* -notContains

| Poperties      | Valori consentiti                        | Uso                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Qualsiasi valore stringa                      | (user.otherMails-contiene"alias@domain")           |
| proxyAddresses | SMTP: alias@domain SMTP, vedere:alias@domain | (user.proxyAddresses-contiene "SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Estensione attributi e attributi personalizzati
Estensione attributi e attributi personalizzati sono supportati nelle regole di appartenenza dinamico.

Gli attributi di estensione vengono sincronizzati dalla finestra Server Active Directory locale e avere il formato di "ExtensionAttributeX", dove X è uguale a 1-15.
Un esempio di una regola che utilizza un attributo di estensione

(user.extensionAttribute15 - eq "Marketing")

Attributi personalizzati vengono sincronizzati da Windows Server Active Directory locale o da un'applicazione di SaaS connessa e il formato di "user.extension_[GUID]\__ [attributo]", dove [GUID] è l'identificatore univoco in AAD per l'applicazione creata con l'attributo AAD e [attributo] è il nome dell'attributo è stato creato.
Esempio di una regola che utilizza un attributo personalizzato

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Il nome dell'attributo personalizzato può trovarsi nella directory richiedendo un utente dell'attributo usando Esplora Graph e cercare il nome dell'attributo.

## <a name="direct-reports-rule"></a>Regola dipendenti diretti dell'utente
A questo punto è possibile inserire i membri di un gruppo in base all'attributo di gestione di un utente.

**Per configurare un gruppo a livello di gruppo "Manager"**

1. Seguire i passaggi da 1 a 5 per [creare la regola avanzata](#to-create-the-advanced-rule)e selezionare un **tipo di appartenenza** **dell'Utente dinamico**.

2. Nella e **regole di appartenenza dinamica** , immettere la regola con la sintassi seguente:

    Diretta report per i *dipendenti diretti dell'utente per {obectID_of_manager}*. Esempio di una regola valida per i dipendenti diretti dell'utente

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    dove "62e19b97-8b3d-4d4a-a106-4ce66896a863" è objectID del manager. L'ID di oggetto sono disponibili in Azure Active Directory nella **scheda profilo** della pagina dell'utente per l'utente responsabile.

3. Quando si salva la regola, tutti gli utenti che soddisfano la regola verranno aggiunti come membri del gruppo. Può richiedere alcuni minuti per il gruppo popolare inizialmente.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Utilizzo degli attributi per creare regole per gli oggetti dispositivo

È anche possibile creare una regola che consente di selezionare gli oggetti di dispositivo per l'appartenenza in un gruppo. È possono utilizzare gli attributi di dispositivo seguenti:

| Proprietà           | Valori consentiti                  | Uso                                                |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName          | qualsiasi valore stringa                | (device.displayName - eq "Gerardino Iphone")                 |
| deviceOSType         | qualsiasi valore stringa                | (device.deviceOSType - eq "IOS")                      |
| deviceOSVersion      | qualsiasi valore stringa                | (dispositivo. OSVersion - eq "9.1")                         |
| isDirSynced          | vero null false                 | (device.isDirSynced - eq "true")                      |
| isManaged            | vero null false                 | (device.isManaged - eq "false")                       |
| isCompliant          | vero null false                 | (device.isCompliant - eq "true")                      |


## <a name="additional-information"></a>Ulteriori informazioni
In questi articoli sono ulteriori informazioni sui gruppi di Azure Active Directory.

* [Visualizzare gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
