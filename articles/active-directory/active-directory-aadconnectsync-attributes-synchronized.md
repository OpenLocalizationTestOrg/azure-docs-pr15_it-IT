<properties
    pageTitle="Sincronizzazione di Azure AD Connect: gli attributi sincronizzati con Azure Active Directory | Microsoft Azure"
    description="Elenca gli attributi che vengono sincronizzati con Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronizzazione di Azure AD Connect: gli attributi sincronizzati con Azure Active Directory
In questo argomento sono elencati gli attributi sincronizzati dal servizio di sincronizzazione Azure AD Connect.  
Gli attributi sono raggruppati correlati Azure Active Directory app.

## <a name="attributes-to-synchronize"></a>Attributi da sincronizzare
Domande frequenti sono *che cos'è l'elenco degli attributi minimi per la sincronizzazione*. Il valore predefinito e un approccio consigliato è per mantenere gli attributi predefiniti per un elenco indirizzi globale completo (elenco indirizzi globale) può essere creato nel cloud e ottenere tutte le caratteristiche in Office 365 carichi di lavoro. In alcuni casi, esistono alcuni attributi per l'organizzazione non essere sincronizzati nel cloud dal momento che contengono tali attributi sensibile o personali (informazioni personali) dati, come in questo esempio:  
![attributi errati](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

In questo caso, iniziare con l'elenco degli attributi nel corso dell'argomento e identificare gli attributi che contengano dati sensibili o personali e non è possibile sincronizzare. Deselezionare quindi gli attributi durante l'installazione mediante [app Azure Active Directory e il filtro degli attributi](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Quando deselezionando attributi, è necessario prestare attenzione e deselezionare solo gli attributi assolutamente non è possibili sincronizzare. Deselezionando altri attributi potrebbe avere un impatto negativo sulle funzionalità.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Nome attributo| Utente| Commento |
| --- | :-: | --- |
| accountEnabled| X| Consente di definire se un account è abilitato.|
| CN| X|  |
| displayName| X|  |
| objectSID| X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| pwdLastSet| X| proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| sourceAnchor| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| usageLocation| X| proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X| UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|

## <a name="exchange-online"></a>Exchange Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| Assistente| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| società| X| X|  |  |
| codice paese| X| X|  |  |
| reparto| X| X|  |  |
| Descrizione| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| Telefono| X| X|  |  |
| Info| X| X| X| Attualmente questo attributo non è utilizzato per i gruppi.|
| Iniziali| X| X|  |  |
| g| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Manager| X| X|  |  |
| membro|  |  | X|  |
| dispositivi mobili| X| X|  |  |
| msDS HABSeniorityIndex| X| X| X|  |
| msDS PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Questo attributo non è attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Questo attributo non è attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Questo attributo non è attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Questo attributo non è attualmente utilizzato da Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Questo attributo non è attualmente utilizzato da Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg IsOrganizational|  |  | X|  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| spostamento| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CAP| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivato dalla groupType|
| NS| X| X|  |  |
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titolo| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| società| X| X|  |  |
| codice paese| X| X|  |  |
| reparto| X| X|  |  |
| Descrizione| X| X| X|  |
| displayName| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| telefono| X| X|  |  |
| Info| X| X| X|  |
| iniziali| X| X|  |  |
| ipPhone| X| X|  |  |
| g| X| X|  |  |
| posta elettronica| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| membro|  |  | X|  |
| middleName| X| X|  |  |
| dispositivi mobili| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| spostamento| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CAP| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivato dalla groupType|
| NS| X| X|  |  |
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titolo| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL| X| X|  |  |
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| società| X| X|  |  |
| reparto| X| X|  |  |
| Descrizione| X| X| X|  |
| displayName| X| X| X|  |
| facsimileTelephoneNumber| X| X| X|  |
| givenName| X| X|  |  |
| telefono| X| X|  |  |
| ipPhone| X| X|  |  |
| g| X| X|  |  |
| posta elettronica| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| membro|  |  | X|  |
| dispositivi mobili| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| attributo msRTCSIP-ApplicationOptions| X|  |  |  |
| attributo msRTCSIP-DeploymentLocator| X| X|  |  |
| attributo msRTCSIP-linea| X| X|  |  |
| attributo msRTCSIP-OptionFlags| X| X|  |  |
| attributo msRTCSIP-OwnerUrn| X|  |  |  |
| attributo msRTCSIP-PrimaryUserAddress| X| X|  |  |
| attributo msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| CAP| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| securityEnabled|  |  | X| Derivato dalla groupType|
| NS| X| X|  |  |
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| ThumbNailPhoto| X| X|  |  |
| titolo| X| X|  |  |
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| CN| X|  | X| Comuni nome o l'alias. In genere il prefisso del valore [stampa].|
| displayName| X| X| X| Stringa che rappresenta il nome spesso visualizzato come il nome descrittivo (cognome).|
| posta elettronica| X| X| X| indirizzo di posta elettronica completo.|
| membro|  |  | X|  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| proxyAddresses| X| X| X| proprietà meccanico. Utilizzato da Azure Active Directory. Contiene tutti gli indirizzi di posta elettronica secondari per l'utente.|
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati.|
| securityEnabled|  |  | X| Derivate dai groupType.|
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | Questo UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|

## <a name="intune"></a>Intune

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| c| X| X|  |  |
| CN| X|  | X|  |
| Descrizione| X| X| X|  |
| displayName| X| X| X|  |
| posta elettronica| X| X| X|  |
| mailNickname| X| X| X|  |
| membro|  |  | X|  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| securityEnabled|  |  | X| Derivato dalla groupType|
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| c| X| X|  |  |
| CN| X|  | X|  |
| CO| X| X|  |  |
| società| X| X|  |  |
| codice paese| X| X|  |  |
| Descrizione| X| X| X|  |
| displayName| X| X| X|  |
| facsimileTelephoneNumber| X| X|  |  |
| givenName| X| X|  |  |
| g| X| X|  |  |
| managedBy|  |  | X|  |
| Manager| X| X|  |  |
| membro|  |  | X|  |
| dispositivi mobili| X| X|  |  |
| objectSID| X|  | X| proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| physicalDeliveryOfficeName| X| X|  |  |
| CAP| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| securityEnabled|  |  | X| Derivato dalla groupType|
| NS| X| X|  |  |
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| ST| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| titolo| X| X|  |  |
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|

## <a name="3rd-party-applications"></a>3 ° applicazioni di terze parti
In questo gruppo è un insieme di attributi utilizzate come attributi minimi necessari per un'applicazione o un carico di lavoro generico. Può essere utilizzato per un carico di lavoro non presente nell'elenco in un'altra sezione o su un'applicazione non Microsoft. In modo esplicito viene utilizzato per le operazioni seguenti:

- Yammer (solo per un utente utilizzato)
- [Scenari di ibrido Business-to-Business (B2B) tra org collaborazione offerti da risorse come SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

In questo gruppo è un insieme di attributi che può essere utilizzato se la directory di Azure Active Directory non viene utilizzata per il supporto di Office 365, Dynamics o Intune. Ha un piccolo gruppo di attributi principali.

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Consente di definire se un account è abilitato.|
| CN| X|  | X|  |
| displayName| X| X| X|  |
| givenName| X| X|  |  |
| posta elettronica| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| membro|  |  | X|  |
| objectSID| X|  |  | proprietà meccanico. ID utente di Active Directory utilizzato per la gestione di sincronizzazione tra Azure Active Directory e Active Directory.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | proprietà meccanico. Consente di sapere quando invalidate token già rilasciati. Usato per la sincronizzazione delle password e federazione.|
| NS| X| X|  |  |
| sourceAnchor| X| X| X| proprietà meccanico. Non modificabile identificatore per la gestione di relazione tra aggiunge e Azure Active Directory.|
| usageLocation| X|  |  | proprietà meccanico. Paese dell'utente. Usato per l'assegnazione di licenze.|
| userPrincipalName| X|  |  | UPN è l'ID di accesso dell'utente. Valore più spesso come [stampa].|

## <a name="windows-10"></a>Windows 10
Un computer(device) dominio Windows 10 Sincronizza alcuni attributi di Azure Active Directory. Per ulteriori informazioni sugli scenari, vedere [Connetti dominio ai dispositivi di Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md). Questi attributi sempre la sincronizzazione e Windows 10 non viene visualizzato come un'app che è possibile deselezionare. Un computer Windows 10 dominio viene identificato mediante la ricezione di userCertificate attributo popolato.

| Nome attributo| Dispositivo| Commento |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valore hardcoded per i computer di dominio. |
| displayName | X| |
| ms-DS-CreatorSID | X| L'acronimo registeredOwnerReference.|
| objectGUID | X| L'acronimo deviceID.|
| objectSID | X| L'acronimo onPremisesSecurityIdentifier.|
| sistema operativo | X| L'acronimo deviceOSType.|
| operatingSystemVersion | X| L'acronimo deviceOSVersion.|
| userCertificate | X| |

Questi attributi per **utente** sono oltre alle altre app di che aver selezionato.  

| Nome attributo| Utente| Commento |
| --- | :-: | --- |
| domainFQDN| X| L'acronimo NomeDominioDns. Ad esempio contoso.com.|
| domainNetBios| X| L'acronimo netBiosName. Ad esempio CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Writeback ibrida di Exchange
Questi attributi vengono scritte da Azure Active Directory in Active Directory locale quando si sceglie di abilitare **distribuzione ibrida di Exchange**. A seconda della versione di Exchange, è possibile che sincronizzati meno attributi.

| Nome attributo| Utente| Contatto| Gruppo| Commento |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| X|  |  | Derivate dai cloudAnchor in Azure Active Directory. L'attributo è nuovo in Exchange 2016.|
| msExchArchiveStatus| X|  |  | Archivio online: Consente di archiviare la posta.|
| msExchBlockedSendersHash| X|  |  | Filtro: Scriva filtro in locale e online mittente sicuri e bloccati dati dai client.|
| msExchSafeRecipientsHash| X|  |  | Filtro: Scriva filtro in locale e online mittente sicuri e bloccati dati dai client.|
| msExchSafeSendersHash| X|  |  | Filtro: Scriva filtro in locale e online mittente sicuri e bloccati dati dai client.|
| msExchUCVoiceMailSettings| X|  |  | Attivare la messaggistica unificata (messaggistica unificata) - Online segreteria telefonica: utilizzato da Microsoft Lync Server integration per indicare a Lync Server in locale che gli utenti abbiano segreteria telefonica in servizi online.|
| msExchUserHoldPolicies| X|  |  | Un'esenzione controversia legale: Servizi cloud consente di determinare quali utenti sono in attesa controversia legale.|
| proxyAddresses| X| X| X| Solo il x500 viene inserito l'indirizzo da Exchange Online.|

## <a name="device-writeback"></a>Writeback di dispositivo
Gli oggetti dispositivo vengono creati in Active Directory. Gli oggetti possono essere dispositivi fa parte di Azure Active Directory o dominio computer Windows 10.

| Nome attributo| Dispositivo| Commento |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| nome distinto | X| |
| msDS CloudAnchor | X| |
| msDS DeviceID | X| |
| msDS DeviceObjectVersion | X| |
| msDS DeviceOSType | X| |
| msDS DeviceOSVersion | X| |
| msDS DevicePhysicalIDs | X| |
| msDS KeyCredentialLink | X| Solo con schema di Active Directory di Windows Server 2016 |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>Note

- Quando si utilizza un ID alternativo, in locale attributo userPrincipalName viene sincronizzato con onPremisesUserPrincipalName di attributo Azure Active Directory. L'attributo ID alternativo, solo per i messaggi di esempio, viene sincronizzata con Azure Active Directory attributo userPrincipalName.
- Negli elenchi in precedenza, il tipo di oggetto **utente** riguarda anche il tipo di oggetto **iNetOrgPerson**.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
