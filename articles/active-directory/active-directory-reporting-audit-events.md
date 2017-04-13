<properties
   pageTitle="Azure Active Directory report eventi | Microsoft Azure"
   description="Controllare gli eventi disponibili per la visualizzazione e il download da Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Azure Active Directory report eventi

*Questa documentazione fa parte di [Azure Active Directory Reporting Guide] (attivo-directory-creazione di report-guide.md).*

Report di verifica Azure Active Directory aiuta i clienti identificare privilegi le azioni che si sono verificati in loro Azure Active Directory. Azioni privilegi includono modifiche elevazione (ad esempio, creazione di ruoli o la reimpostazione della password), la modifica delle configurazioni di criteri (ad esempio i criteri di password) o le modifiche alla configurazione di directory (ad esempio, le modifiche alle impostazioni di federazione dominio). I report offrono il record di controllo per il nome dell'evento, attore che ha eseguito l'azione, la risorsa di destinazione incidono la modifica e la data e ora (UTC). I clienti in grado di recuperare l'elenco degli eventi di controllo per loro Azure Active Directory tramite il [Portale di Azure](https://portal.azure.com/), come descritto nella [visualizzazione dei registri di controllo](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Elenco di eventi del Report di controllo
<!--- audit event descriptions should be in the past tense --->

Eventi                               | Descrizione dell'evento
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventi generati dall'utente**                      |
Aggiunta utente                             | Aggiungere un utente all'elenco.
Elimina utente                          | Eliminare un utente dalla directory.
Impostare le proprietà di licenza               | Impostare le proprietà di licenza per un utente nella directory.
Reimpostare la password utente                  | Reimpostare la password per un utente nella directory.
Modifica della password utente                 | Modificare la password per un utente nella directory.
Licenza utente di modifica                  | Modificare la licenza assegnata a un utente nella directory. Per vedere quali licenze sono state aggiornate, esaminare proprietà [utente di aggiornamento](#update-user-attributes)
Aggiornare l'utente                          | Aggiornare un utente nella directory. [Vedere di seguito](#update-user-attributes) per gli attributi che possono essere aggiornati.
Forza set di modifica della password utente       | Impostare la proprietà che consente all'utente di modificare la password di accesso.
Aggiornare le credenziali utente        | Utente cambiato la password  
**Eventi del gruppo**                     |
Aggiunta di gruppo                            | Creare un gruppo nella directory.
Gruppo di aggiornamento                         | Aggiornare un gruppo nella directory. Per visualizzare le proprietà di gruppo sono state aggiornate, fare riferimento alla [Proprietà o meno controllato di gruppo](#update-group-attributes) nella sezione seguente
Elimina gruppo                         | Eliminare un gruppo dalla directory.
Aggiungere membri al gruppo            | Aggiungere un membro a un gruppo nella directory.
Rimuovi membro dal gruppo         | Rimuovere un membro da un gruppo nella directory.
CreateGroupSettings              | Impostazioni di gruppo creato
UpdateGroupSettings          | Aggiornare le impostazioni di gruppo. Per visualizzare le impostazioni dei gruppi sono stati aggiornati, fare riferimento alla [Proprietà o meno controllato di gruppo](#update-group-attributes) nella sezione seguente
DeleteGroupSettings            | Impostazioni gruppo eliminato
SetGroupLicense                  | Impostare licenza di gruppo.
SetGroupManagedBy              | Gruppo di set di essere gestiti dall'utente
AddGroupMember               | Membro aggiunto al gruppo
RemoveGroupMember            | Rimuovi membro dal gruppo
AddGroupOwner                | Proprietario aggiunto al gruppo
RemoveGroupOwner                 | Proprietario rimosso dal gruppo
**Eventi dell'applicazione**               |
Aggiungere principale del servizio                | Aggiungere un'entità servizio alla directory.
Rimuovere principale del servizio             | Rimuovere un'entità servizio dalla directory.
Aggiungere le credenziali dell'entità di servizio    | Credenziali aggiunte a un'identità di servizio.
Rimuovere le credenziali dell'entità di servizio | Rimuovere le credenziali da un servizio principale.
Aggiungere la delega voce                 | Creare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory.
Impostare la delega voce                 | Aggiornare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory.
Rimuovere la delega voce              | Eliminare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) nella directory.
**Eventi ruolo**                      |
Aggiungi membro del ruolo al ruolo              | Aggiungere un utente a un ruolo di directory.
Rimuovi membro del ruolo dal ruolo         | Rimuovere un utente da un ruolo di directory.
Impostare le informazioni di contatto della società      | Impostare le preferenze di contatto di livello aziendale. Sono inclusi gli indirizzi di posta elettronica per le notifiche di marketing, nonché tecniche sui servizi Online Microsoft.
Aggiungere la delega voce                 | Creare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) nella directory.
Impostare la delega voce                 | Aggiornare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) nella directory.
Rimuovere la delega voce              | Eliminare un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) nella directory.
AddSevicePrincipalOwner          | Proprietario aggiunto a principale del servizio.
RemoveSevicePrincipalOwner   | Rimosse proprietario principale del servizio.
AddApplication  | Aggiunta dell'applicazione.
UpdateApplication | Aggiornamento dell'applicazione. Per visualizzare le impostazioni di app sono state aggiornate, fare riferimento all' [Applicazione proprietà controllati](#update-application-attributes) nella sezione seguente
DeleteApplication | Eliminare l'applicazione.
RestoreApplication|Ripristinare le applicazioni.
AddApplicationOwner   |     Proprietario aggiunto all'applicazione.
RemoveApplicationOwner    |     Rimuovere proprietario dall'applicazione.
**Eventi ruolo**                         |
Aggiungi membro del ruolo al ruolo                 | Aggiungere un utente a un ruolo di directory.
Rimuovi membro del ruolo dal ruolo            | Rimuovere un utente da un ruolo di directory.
AddRoleDefinition               | Definizione di ruolo aggiunto.
UpdateRoleDefinition                | Definizione di ruolo aggiornato. Per visualizzare le impostazioni di ruolo sono state aggiornate, fare riferimento al [Ruolo ricoperto dai partecipanti definizione proprietà controllati](#update-role-definition-attributes) nella sezione seguente
DeleteRoleDefinition                | Definizione di ruolo eliminato.
AddRoleAssignmentToRoleDefinition | Assegnazione di ruolo aggiunto alla definizione del ruolo.
RemoveRoleAssignmentFromRoleDefinition | Assegnazione di ruolo rimosso dalla definizione ruolo.
AddRoleFromTemplate     | Aggiunto ruolo da modello.
UpdateRole  | Ruolo aggiornato.
AddRoleScopeMemberToRole    | Membro dell'ambito aggiunto al ruolo.
RemoveRoleScopedMemberFromRole  | Rimuovere un membro dal ruolo.
**Dispositivo eventi (tutti i nuovi eventi)**                    |
AddDevice               | Dispositivo aggiunto.
UpdateDevice            | Dispositivo aggiornati. Per visualizzare le proprietà dei dispositivi sono state aggiornate, fare riferimento alle [proprietà dei dispositivi Audited](#update-device-attributes) nella sezione seguente
DeleteDevice            | Dispositivo eliminato.
AddDeviceConfiguration      | Configurazione dei dispositivi aggiunte.
UpdateDeviceConfiguration   | Configurazione di dispositivo aggiornati. Per visualizzare le proprietà di configurazione del dispositivo sono state aggiornate, fare riferimento alle [proprietà dei dispositivi configurazione Audited](#update-device-configuration-attributes) nella sezione seguente
DeleteDeviceConfiguration   | Configurazione dei dispositivi eliminati.
AddRegisteredOwner     | Proprietario registrato aggiunto al dispositivo.
AddRegisteredUsers     | Utenti registrati aggiunti al dispositivo.
RemoveRegisteredOwner    | Rimuovere il proprietario registrato dal dispositivo.
RemoveRegisteredUsers    | Rimuovere utenti registrati dal dispositivo.
RemoveDeviceCredentials  | Rimuovere le eventuali credenziali dispositivo.
**Eventi B2B**                       |
Gli inviti batch caricati.              | Un amministratore ha caricato un file contenente gli inviti inviati agli utenti di partner.
Inviti batch di elaborazione.             | Un file contenente gli inviti degli utenti di partner è stato elaborato.
Invitare utenti esterni.                | Un utente esterno è stato invitato alla directory.
Riscattare Invita utenti esterni.         | Un utente esterno è stato usato loro invito alla directory.
Aggiungere utenti esterni al gruppo.          | Un utente esterno sia assegnato l'appartenenza a un gruppo nella directory.
Assegnare utenti esterni all'applicazione. | Un utente esterno è stato assegnato accesso diretto a un'applicazione.
Creazione di virus tenant.               | Un nuovo tenant è stato creato in Azure Active Directory da prezzo_rimb invito.
Creazione dell'utente sono.                 | Un utente è stato creato in un tenant esistente in Azure Active Directory da prezzo_rimb invito.
**Unità amministrative (tutti i nuovi eventi)**             |
AddAdministrativeUnit   |   Aggiungere unità amministrativa.
UpdateAdministrativeUnit|   Aggiornare unità amministrativa. Per visualizzare le proprietà di unità amministrativa sono state aggiornate, fare riferimento alla [proprietà dell'unità amministrativa controllati](#update-administrative-unit-attributes) nella sezione seguente
DeleteAdministrativeUnit|   Eliminare unità amministrativa.
AddMemberToAdministrativeUnit|  Aggiungere membri a unità amministrativa.
RemoveMemberFromAdministrativeUnit|     Rimuovere membri da unità amministrativa.
**Eventi di directory**                 |
Aggiungere partner società               | Aggiungere un partner alla directory.
Rimuovere Partner della società          | Rimuovere un partner dalla directory.
DemotePartner   |   Abbassare di livello partner.
Aggiungere il dominio a società                | Aggiungere un dominio alla directory.
Rimuovere un dominio della società           | Rimuovere un dominio dalla directory.
Dominio di aggiornamento                        | Aggiornare un dominio nella directory. Per visualizzare le proprietà del dominio sono state aggiornate, fare riferimento a [controllare le proprietà del dominio](#update-domain-attributes) nella sezione seguente
Impostare l'autenticazione dominio            | Modificare l'impostazione di dominio predefinito per la società.
Impostare le informazioni di contatto della società      | Impostare le preferenze di contatto di livello aziendale. Sono inclusi gli indirizzi di posta elettronica per le notifiche di marketing, nonché tecniche sui servizi Online Microsoft.
Configurare le impostazioni di federazione nel dominio    | Aggiornare le impostazioni della federazione per un dominio.
Verificare il dominio                        | Verificare un dominio nella directory.
Verificare il dominio verificato di posta elettronica         | Verificare un dominio nella directory utilizzando la verifica di posta elettronica.
Impostare il contrassegno DirSyncEnabled della società   | Impostare la proprietà che può essere una directory di sincronizzazione di Azure Active Directory.
Impostare i criteri Password                  | Impostare i vincoli di lunghezza e caratteri per le password degli utenti.
Informazioni sulla società set              | Aggiornare le informazioni di livello aziendale. Cmdlet di PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) per altri dettagli, vedere.
SetCompanyAllowedDataLocation  |    Impostare società consentito posizione dei dati.
SetCompanyDirSyncEnabled    |   Impostare il contrassegno DirSyncEnabled.
SetCompanyDirSyncFeature |  Funzionalità di DirSync set.
SetCompanyInformation |   Informazioni sulla società set.
SetCompanyMultiNationalEnabled    |     Impostare società multinazionali abilitata.
SetDirectoryFeatureOnTenant   |     Set di funzionalità di directory nel tenant.
SetTenantLicenseProperties  |   Impostare le proprietà di licenza tenant.
CreateCompanySettings   |   Creare le impostazioni della società
UpdateCompanySettings   |   Aggiornare le impostazioni della società. Per visualizzare le proprietà della società sono state aggiornate, fare riferimento alla [proprietà società o meno controllato](#update-company-attributes) nella sezione seguente
DeleteCompanySettings   |   Eliminare le impostazioni della società
SetAccidentalDeletionThreshold   |  Impostare il limite di eliminazioni accidentali.
SetRightsManagementProperties   |   Impostare rights management.
PurgeRightsManagementProperties     |   Eliminare le proprietà di rights management.
UpdateExternalSecrets   |   Aggiornare informazioni riservate esterni
**Eventi di criteri (tutti i nuovi eventi)**           |
AddPolicy   |   Aggiungere criteri.
UpdatePolicy    |   Aggiornare i criteri.
DeletePolicy    |   Eliminare criterio.
AddDefaultPolicyApplication     |   Aggiungere criteri all'applicazione.
AddDefaultPolicyServicePrincipal    |   Aggiungere criteri a principale del servizio.
RemoveDefaultPolicyApplication  |   Rimuovere i criteri dall'applicazione.
RemoveDefaultPolicyServicePrincipal     |   Rimuovere i criteri da principale del servizio.
RemovePolicyCredentials     |   Rimuovere le eventuali credenziali criteri.

## <a name="audit-report-retention"></a>Conservazione dei report di controllo
Eventi del report di controllo di Azure Active Directory vengono conservati per 180 giorni. Per ulteriori informazioni sui criteri di conservazione report, vedere [Azure Active Directory Report i criteri di conservazione](active-directory-reporting-retention.md).

Per i clienti di memorizzare i relativi eventi di controllo per più periodi di conservazione, è possibile utilizzare l'API dei report in modo da estrarre regolarmente gli eventi di controllo in un archivio di dati separato. Per informazioni dettagliate, vedere [Introduzione all'API dei report](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Proprietà inclusa in ogni evento di controllo

Proprietà      | Descrizione
------------- | --------------------------------------------------------------
Data e ora | La data e l'ora in cui si è verificato l'evento di controllo
Attore         | L'utente o il capitale servizio che hanno effettuato l'operazione
Azione        | Azione eseguita
Destinazione        | L'utente o il capitale servizio che è stata eseguita l'azione su


## <a name="update-user-attributes"></a>Attributi "Aggiornare utente"
L'evento di controllo "Utente aggiornamento" include informazioni aggiuntive sui quali attributi utente sono stati aggiornati. Per ogni attributo è incluso il valore precedente e il nuovo valore.

Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | L'utente può accedere.
AssignedLicense                     | Tutte le licenze assegnate all'utente.
AssignedPlan                        | Piani di servizio risultante dalla licenze assegnate all'utente.
LicenseAssignmentDetail             | Informazioni dettagliate sulle licenze assegnate all'utente. Ad esempio, se le licenze basate su gruppo era coinvolto, trattarsi il gruppo che abbia concesso o meno la licenza.
Dispositivi mobili                              | Telefono cellulare dell'utente.
OtherMail                           | Indirizzo di posta elettronica alternativo dell'utente.
OtherMobile                         | Telefono cellulare alternativo dell'utente.
StrongAuthenticationMethod          | Elenco dei metodi di verifica configurato dall'utente per l'autenticazione a più fattori, ad esempio il codice di chiamata VoIP, SMS o verifica da un'app per dispositivi mobili.
StrongAuthenticationRequirement     | Se viene applicata l'autenticazione a più fattori, abilitata o disabilitata per questo utente.
StrongAuthenticationUserDetails     | Numero di telefono dell'utente, alternativo telefono indirizzo di posta elettronica e numero utilizzato per la verifica di reimpostare l'autenticazione a più fattori e la password.
StrongAuthenticationPhoneAppDetail  | App del telefono forof dettaglio registrati per eseguire l'accesso 2FA
TelephoneNumber                     | Numero di telefono dell'utente.
AlternativeSecurityId               | Un ID di sicurezza alternativi per l'oggetto.
CreationType                        |Metodo di creazione dell'utente (o invito da virus).
InviteTicket                        |Elenco di biglietti di invito per l'utente.
InviteReplyUrl                      |Elenco degli URL per rispondere all'accettazione di invito.
InviteResources                     |Elenco delle risorse a cui l'utente è stato invitato.
LastDirSyncTime                     |Ultimo aggiornamento dell'oggetto a causa di sincronizzazione dal autorevoli directory (cliente, locale).
MSExchRemoteRecipientType           |Esegue il mapping a tipi di destinatari MSO. Fare riferimento a https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx [tipi di destinatari MSO] per i tipi di destinatari
PreferredDataLocation               |La posizione preferita per l'utente, del gruppo, del contatto, cartelle pubbliche o dati del dispositivo.
ProxyAddresses                      |L'indirizzo per il quale in un sistema di posta elettronica esterna viene riconosciuto un oggetto di destinatari di Exchange Server.
StsRefreshTokensValidFrom           |Esegue aggiornare le informazioni di revoca token - token di aggiornamento qualsiasi servizio token di sicurezza rilasciati prima dell'ora sono considerate scaduto.
UserPrincipalName                   |L'UPN che è un nome di accesso di tipo Internet per un utente.
UserState                           |Stato dell'utente in attesa di approvazione/PendingAcceptance/accettato/PendingVerification.
UserStateChangedOn                  |TimeStamp dell'ultima modifica all'UserState. Consente di impostare un trigger ciclo di vita dei flussi di lavoro.
UserType                            |Tipo di utente. Membro (0), Guest (1), Viral(2).

## <a name="update-group-attributes"></a>Attributi "Aggiorna gruppo"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classificazione            | Classificazione per un gruppo di unificato (alto impatto aziendale, impatto e così via).
Descrizione               | Frasi descrittive leggibili sull'oggetto.  
DisplayName               |Il nome visualizzato per un oggetto
DirSyncEnabled            |Indica se la sincronizzazione ha luogo da autorevole directory (cliente, locale).
GroupLicenseAssignment    |Assegnazione di licenze di un gruppo
GroupType                 |Tipo di gruppo, unificata (0)
IsMembershipRuleLocked    |Indica che la proprietà MembershipRule è impostata per il servizio di gestione dei gruppi modalità self-service e non possa essere modificata dagli utenti. Applicabile solo ai gruppi in cui GroupType include GroupType.DynamicMembership
IsPublic                  |Contrassegno per indicare se il gruppo è pubblico/privato.
LastDirSyncTime           |Ultimo aggiornamento dell'oggetto in seguito a sincronizzazione dal autorevoli directory (cliente, distribuzione locale).
Posta elettronica                      |Indirizzo di posta elettronica principale
MailEnabled               |Indica se il gruppo ha la funzionalità di posta elettronica.
MailNickname              |Moniker per un oggetto della Rubrica, in genere la parte del nome posta elettronica precedente la "@" simbolo.   
MembershipRule            |Una stringa di esprimere i criteri utilizzati dal servizio di gestione gruppo modalità self-service per determinare quali membri devono appartenere al gruppo. Vedere anche IsMembershipRuleLocked. Applicabile solo ai gruppi in cui GroupType include GroupType.DynamicMembership.
MembershipRuleProcessingState| Un valore di enumerazione definito dal servizio di gestione delle modalità self-service gruppo definizione dello stato di appartenenza l'elaborazione di questo gruppo. Applicabile solo ai gruppi in cui GroupType include GroupType.DynamicMembership.
ProxyAddresses            |L'indirizzo per il quale in un sistema di posta elettronica esterna viene riconosciuto un oggetto di destinatari di Exchange Server.
RenewedDateTime           |Record timestamp quando un gruppo è stato rinnovato più di recente.   
SecurityEnabled           |Indica se l'appartenenza al gruppo può influire decisioni relative alle autorizzazioni.
WellKnownObject           |Un oggetto directory, come parte di un gruppo predefinito delle etichette.

## <a name="update-device-attributes"></a>Attributi "Aggiornare dispositivo"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indica se un'identità di protezione può eseguire l'autenticazione.
CloudAccountEnabled | Indica se un'identità di protezione può eseguire l'autenticazione. Scritto da InTune quando il dispositivo è applicato a locale.
CloudDeviceOSType | Tipo del dispositivo basato sul sistema operativo, ad esempio Windows RT, iOS. Quando è impostata da un servizio cloud (ad esempio Intune), questo attributo diventa rilevanti nella directory per DeviceOSType.
CloudDeviceOSVersion | Versione del sistema operativo. Quando è impostata da un servizio cloud (ad esempio Intune), questo attributo diventa rilevanti nella directory per DeviceOSVersion.
CloudDisplayName | Valore dell'attributo displayName LDAP. Quando è impostata da un servizio cloud (ad esempio Intune), questo attributo diventa rilevanti nella directory displayName.
CloudCreated |Indica se l'oggetto è stata creata da servizi cloud.
CompliantUntil | Fino a che ora dispositivo è considerato conforme.
DeviceMetadata |Metadati personalizzati per il dispositivo
DeviceObjectVersion | Questo attributo viene utilizzato per identificare la versione di schema del dispositivo.
DeviceOSType |Tipo del dispositivo basato sul sistema operativo, ad esempio Windows RT, iOS. Scritto dal servizio di registrazione e deve essere aggiornati tramite il MDM servizio di gestione o servizio token di sicurezza chiaro servizio di gestione.
DeviceOSVersion |Versione del sistema operativo. Scritto dal servizio di registrazione e deve essere aggiornati tramite il MDM servizio di gestione o servizio token di sicurezza chiaro servizio di gestione.
DevicePhysicalIds |Attributo multivalore destinato a memorizzare gli identificatori del dispositivo fisico. Può trattarsi di ID BIOS, identificazioni personali TPM, hardware ID specifici e così via.
DirSyncEnabled | Indica se da un rilevanti (cliente, distribuzione locale) viene eseguita la sincronizzazione directory.
DisplayName |Il nome visualizzato per un oggetto
IsCompliant | Questo attributo viene utilizzato per gestire lo stato di gestione di dispositivi mobili del dispositivo.
IsManaged |Questo attributo viene utilizzato per indicare che il dispositivo viene gestito da un'area MDM.
LastDirSyncTime |Ultimo aggiornamento dell'oggetto a causa di sincronizzazione dal autorevoli directory (cliente, distribuzione locale).

## <a name="update-device-configuration-attributes"></a>Attributi "Aggiornare configurazione del dispositivo"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Il numero massimo di giorni che vanno che un dispositivo può essere inattivo prima che viene considerato per la rimozione.
RegistrationQuota   |Criteri utilizzati per limitare il numero delle registrazioni di dispositivo consentiti per un singolo utente.

## <a name="update-service-principal-configuration-attributes"></a>Attributi "Aggiornare la configurazione dell'entità del servizio"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indica se un'identità di protezione può eseguire l'autenticazione.
AppPrincipalId | Identità esterni, definiti dall'applicazione per un'identità di protezione.
DisplayName |Il nome visualizzato per un oggetto
ServicePrincipalName    | Un nome dell'entità di servizio che contiene "nome/autorità" in nome specifica un valore di classe dell'applicazione e autorità contiene almeno hostname [: porta] o "nome" che specifica un identificatore per principale del servizio.

## <a name="update-app-attributes"></a>Attributi "Aggiornare App"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Insieme di indirizzi (reindirizzamento URL) che vengono assegnate a un'identità di servizio.
AppId                               |ID applicazione dell'App   
AppIdentifierUri | URI applicazione che identifica l'applicazione.  È in genere l'URL di accesso dell'applicazione.
AppLogoUrl |L'url per l'immagine del logo di applicazione archiviata in una rete CDN.
AvailableToOtherTenants | True è multi-tenant applicazione (ad esempio, può essere utilizzato da altri tenant).
DisplayName | Il nome visualizzato per nome di un'applicazione
Diritto |Elenco dei diritti di applicazione.
ExternalUserAccountDelegationsAllowed |Indica se applicazione risorsa sia attendibile e può creare le voci di delega per gli account utente esterni.
GroupMembershipClaims |Domande di appartenenza a un gruppo.
PublicClient | True se il client non è possibile mantenere segreto (ovvero confidenziali client in OAuth2.0)
RecordConsentConditions | Tipi di condizioni di consenso dell'utente, come definito da termini del contratto: Nessuno (0), SilentConsentForPartnerManagedApp(1). Questo valore verrà esposta nello schema di API di grafico e può essere solo set/modificate dagli amministratori tenant.
RequiredResourceAccess |Contenuto XML di un valore della proprietà RequiredResourceAccess.
Web App |Se true, indica che l'applicazione è un'app web.
WwwHomepage |Pagina Web principale.

## <a name="update-role-attributes"></a>Attributi "Aggiorna ruolo"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Insieme di indirizzi (reindirizzamento URL) che vengono assegnate a un'identità di servizio.
BelongsToFirstLoginObjectSet |Se true, indica che l'oggetto appartiene all'insieme di oggetti necessari per consentire l'accesso dell'amministratore prima di un nuovo tenant.
Incorporati |Indica se la durata di un oggetto è proprietà del sistema.
Descrizione | Frasi descrittive leggibili sull'oggetto.  
DisplayName |Il nome visualizzato per un oggetto
MailNickname | Moniker per un oggetto della Rubrica, in genere la parte del nome posta elettronica precedente la "@" simbolo.
RoleDisabled |Indica se il ruolo deve essere ignorato ai fini dei controlli di access.
RoleTemplateId | Identità del modello di ruolo.
ServiceInfo |Provisioning informazioni che possono essere utilizzate per MOAC e/o altre istanze di servizio (stessa o di altro tipo di servizio).
TaskSetScopeReference |Identifica un TaskSet e un set di ambiti associati a un ruolo o i modelli di ruolo.
ValidationError |Informazioni pubblicate da un servizio federato che descrive un errore non temporanei specifiche del servizio riguardo alle proprietà o creare un collegamento dalla azione oggetto amministratore per risolvere.
WellKnownObject |Un oggetto directory, come parte di un gruppo predefinito delle etichette.

## <a name="update-role-definition-attributes"></a>Attributi "Aggiornare la definizione di ruolo"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Insieme di ambiti di autorizzazione che è possibile fare riferimento quando si assegna questo RoleDefinition a un'identità di protezione.
DisplayName     |Il nome visualizzato per un oggetto
GrantedPermissions  |Autorizzazioni concesse da questo RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>Attributi "Aggiornare unità amministrativa"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Descrizione |   Questa proprietà viene aggiornata quando si modifica la descrizione di un'unità amministrativa.
DisplayName |   Questa proprietà viene aggiornata quando si modifica il nome di un'unità amministrativa.

## <a name="update-company-attributes"></a>Attributi "Aggiornamento Company"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Posizione in cui gli utenti della società consentiti effettuare il provisioning.
AuthorizedServiceInstance   | Nomi delle istanze del servizio a cui è possibile distribuire un piano.
DirSyncEnabled |Indica se da un rilevanti (cliente, distribuzione locale) viene eseguita la sincronizzazione directory.
DirSyncStatus |Indica se la sincronizzazione degli indirizzi della Rubrica oggetti in questo contesto tenant da un rilevanti (cliente, distribuzione locale) directory; espansione della proprietà DirSyncEnabled sugli oggetti della società.
DirSyncFeatures |Contrassegno di bit per tenere traccia dei set di caratteristiche dirsync attivato e disattivato per il tenant.
DirectoryFeatures |Funzionalità di attivazione/disattivazione directory.
DirSyncConfiguration |Contiene tutti DirSync configurazione specifiche per il tenant corrente.
DisplayName |Il nome visualizzato per un oggetto
IsMnc |Flag booleano impostata su "true" iff che della società è abilitata per la caratteristica società multinazionali.
ObjectSettings | Insieme di impostazioni applicabile all'ambito dell'oggetto.
PartnerCommerceUrl |URL del sito e-commerce del Partner.
PartnerHelpUrl |URL del sito della Guida del Partner.
PartnerSupportEmail | URL di posta elettronica di supporto del Partner.
PartnerSupportTelephone |URL di telefono supporto del Partner.
PartnerSupportUrl |URL del sito di supporto del Partner.
StrongAuthenticationDetails |Dettagli relativi alle StrongAuthentication.
StrongAuthenticationPolicy |Criteri di autenticazione per la società.
TechnicalNotificationMail |Indirizzo di posta elettronica per segnalare problemi tecnici relativi a una società.
TelephoneNumber |Numeri di telefono che si attenga a E.123 di Recommendation ITU.
TenantType |Il tipo di un tenant. Se questo valore non viene specificato, quest'ultimo è una società. In caso contrario, i valori possibili sono: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Un insieme di nomi di dominio DNS associato a una società.

## <a name="update-domain-attributes"></a>Attributi "Aggiornare dominio"
Attributo                           | Descrizione
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Funzionalità    |Bit contrassegni che descrive le funzionalità del dominio, se presenti.
Impostazione predefinita |Indica se il dominio è il valore predefinito. ad esempio, il UserPrincipalName suffisso predefinito quando un amministratore crea un nuovo utente in MOAC.
Iniziale |Indica se il dominio è il dominio iniziale per la società, come ricevuto dall'OCP. Il dominio iniziale è un sottodominio univoco del dominio di Microsoft Online. e.g.contoso3.microsoftonline.com.
LiveType    |Tipo del Windows Live spazio dei nomi corrispondente, se presenti.
Nome    | Identificatore per l'endpoint.
PasswordNotificationWindowDays  |Il numero di giorni prima della scadenza di una password l'utente riceve una notifica.
PasswordValidityPeriodDays  | Il numero di giorni che vanno che una password è utile per prima devono essere modificato.

Record di controllo sono i controlli obbligatori per molti normative di conformità. Se si utilizza il Report di controllo Directory Azure Active per soddisfare le loro regole di conformità, è consigliabile che il cliente inviare una copia di questo argomento della Guida con la copia del report di controllo esportato del cliente per spiegare i dettagli del report. Se il revisore desidera comprendere le regole di conformità affine attualmente Azure, indicare il revisore alla [pagina di conformità](https://azure.microsoft.com/support/trust-center/compliance/) di Microsoft Azure Trust Center.
