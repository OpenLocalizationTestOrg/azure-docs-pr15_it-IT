<properties
   pageTitle="Azure AD Connect: Account e le autorizzazioni | Microsoft Azure"
   description="In questo argomento descrive l'account utilizzato e creato e le autorizzazioni necessarie."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Account e autorizzazioni
Installazione guidata di Azure AD Connect offre due diversi percorsi:

- In Impostazioni rapide, la procedura guidata richiede più privilegi, in modo che è possibile impostare la configurazione facilmente, senza che sia necessario creare gli utenti o configurare le autorizzazioni separatamente.

- In impostazioni personalizzate, la procedura guidata sono disponibili più opzioni e opzioni, ma vi sono alcune situazioni in cui è necessario avere a disposizione delle autorizzazioni corrette se stessi.

## <a name="related-documentation"></a>Documentazione correlata
Se non è stato letto la documentazione [sull'integrazione le identità locali con Azure Active Directory](../active-directory-aadconnect.md), nella tabella seguente vengono forniti collegamenti ad argomenti correlati.

Argomento |  
--------- | ---------
Installare con le impostazioni Express | [Installazione di Azure AD Connect rapida](active-directory-aadconnect-get-started-express.md)
Installare con le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Eseguire l'aggiornamento da DirSync | [Eseguire l'aggiornamento da strumento di sincronizzazione di Azure Active Directory (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## <a name="express-settings-installation"></a>Impostazioni installazione rapida
Nelle impostazioni di Express la procedura guidata installazione richiede le credenziali di amministratore di Active Directory dell'organizzazione in modo di Active Directory locale possono essere configurate con le autorizzazioni necessarie per Azure AD Connect. Se si sta aggiornando DirSync, credenziali Active Directory Enterprise Admins vengono utilizzate per reimpostare la password per l'account utilizzato da DirSync. È inoltre necessario le credenziali di amministratore globale di Azure Active Directory.

Schermata della procedura guidata  | Credenziali ottenute | Autorizzazioni necessarie| Usato per
------------- | ------------- |------------- |-------------
N/D|Utente che esegue l'installazione guidata| Amministratore del server locale| <li>Crea l'account locale che viene utilizzato come [account del servizio motore di sincronizzazione](#azure-ad-connect-sync-service-account).
Connettersi a Azure Active Directory| Credenziali di Azure Active directory | Ruolo di amministratore globale in Azure Active Directory | <li>Abilitazione della sincronizzazione della directory di Azure Active Directory.</li>  <li>Creazione di [account Azure Active Directory](#azure-ad-service-account) che viene utilizzato per le operazioni di sincronizzazione in corso in Azure Active Directory.</li>
Connettersi a Active Directory | Credenziali Active Directory locale | Membro del gruppo amministratori Enterprise (EA) in Active Directory.| <li>Crea un [account](#active-directory-account) in Active Directory e concede autorizzazioni. Creazione di account viene utilizzato per leggere e scrivere le informazioni di directory durante la sincronizzazione.</li>

### <a name="enterprise-admin-credentials"></a>Credenziali di amministratore dell'organizzazione
Queste credenziali vengono utilizzate solo durante l'installazione e vengono utilizzate dopo l'installazione è stata completata. È amministratore dell'organizzazione e non Domain Admin, per assicurarsi che le autorizzazioni in Active Directory è possibile impostare in tutti i domini.

### <a name="global-admin-credentials"></a>Credenziali di amministratore globale
Queste credenziali vengono utilizzate solo durante l'installazione e non vengono utilizzate dopo l'installazione. Viene utilizzato per creare l' [account Azure Active Directory](#azure-ad-service-account) utilizzato per sincronizzare le modifiche apportate ai Azure Active Directory. L'account consente inoltre sincronizzazione come una caratteristica in Azure Active Directory.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Le autorizzazioni per la creazione di dominio Active Directory account per accedere a impostazioni express
L' [account](#active-directory-account) creato per la lettura e scrittura in Active Directory dispone delle autorizzazioni seguenti quando creato express impostazioni:

Autorizzazione | Usato per
---- | ----
<li>Replica modifiche Directory</li><li>Directory replica Cambia tutto | Sincronizzazione delle password
Lettura/scrittura tutte le proprietà utente | Importazione ed Exchange ibrido
Lettura/scrittura tutte le proprietà iNetOrgPerson | Importazione ed Exchange ibrido
Raggruppare tutte le proprietà di lettura/scrittura | Importazione ed Exchange ibrido
Contattare tutte le proprietà di lettura/scrittura | Importazione ed Exchange ibrido
Reimpostazione password | Preparazione per l'attivazione writeback di password

## <a name="custom-settings-installation"></a>Installazione di impostazioni personalizzate
Quando si utilizza impostazioni personalizzate, è necessario creare l'account utilizzato per connettersi a Active Directory prima dell'installazione. Le autorizzazioni da assegnare questo account sono disponibili in [creare l'account di dominio Active Directory](#create-the-ad-ds-account).

Schermata della procedura guidata  | Credenziali ottenute | Autorizzazioni necessarie| Usato per
------------- | ------------- |------------- |-------------
N/D | Utente che esegue l'installazione guidata|<li>Amministratore del server locale</li><li>Se a un Server SQL completo, l'utente deve essere amministratore di sistema (SA) in SQL</li>| Per impostazione predefinita, crea l'account locale che viene utilizzato come [account del servizio motore di sincronizzazione](#azure-ad-connect-sync-service-account). L'account viene creato solo quando l'amministratore non specifica un determinato account.
Installazione di servizi di sincronizzazione, opzione account del servizio | Active Directory o le credenziali dell'account utente locali | Gli utenti, autorizzazioni dall'installazione guidata | Se l'amministratore specifica un account, l'account viene utilizzato come account del servizio per il servizio di sincronizzazione.
Connettersi a Azure Active Directory | Credenziali di Azure Active directory| Ruolo di amministratore globale in Azure Active Directory| <li>Abilitazione della sincronizzazione della directory di Azure Active Directory.</li>  <li>Creazione di [account Azure Active Directory](#azure-ad-service-account) che viene utilizzato per le operazioni di sincronizzazione in corso in Azure Active Directory.</li>
Connettere la directory | Credenziali Active Directory locale per ogni foresta sia connesso a Azure Active Directory | Le autorizzazioni dipendono alle funzionalità abilitare e sono disponibili [nell'account DS crea Active Directory](#create-the-ad-ds-account) |L'account viene utilizzato per leggere e scrivere informazioni directory durante la sincronizzazione.
Server ADFS Active Directory | Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione | Amministratore di dominio | Installazione e configurazione del ruolo del server ADFS.
Server proxy di applicazioni Web |Per ogni server nell'elenco, la procedura guidata raccoglie le credenziali quando le credenziali di accesso dell'utente che esegue la procedura guidata non sono sufficienti per la connessione | Amministratore locale nel computer di destinazione | Installazione e configurazione di ruolo del server WAP.
Credenziali di protezione del proxy |Credenziali di protezione del servizio di federazione (credenziali il proxy utilizza per registrare un certificato di protezione da di ADFS |Account di dominio è un amministratore del server ADFS | Registrazione iniziale del certificato di protezione WAP ADFS.
Pagina Account del servizio ADFS Active Directory "Utilizzare un'opzione di account utente di dominio" | Credenziali dell'account utente di Active Directory | Utente del dominio | L'account utente di Active Directory vengono fornite le cui credenziali viene utilizzato come account di accesso del servizio di ADFS.

### <a name="create-the-ad-ds-account"></a>Creare l'account di dominio Active Directory
Durante l'installazione di Azure AD Connect, l'account specificato nella pagina **connettere le directory** devono essere presente in Active Directory e sono necessarie le autorizzazioni concesse. L'installazione guidata non verificare che le autorizzazioni e gli eventuali problemi sono presenti solo durante la sincronizzazione.

Le autorizzazioni necessarie dipende dalle funzionalità facoltativa abilitare. Se si hanno più domini, è necessario assegnare le autorizzazioni per tutti i domini della foresta. Se non si attiva una di queste funzionalità, sono sufficienti **Dominio le** autorizzazioni predefinite.

Caratteristica | Autorizzazioni
------ | ------
Sincronizzazione delle password | <li>Replica modifiche Directory</li>  <li>Directory replica Cambia tutto
Distribuzione ibrida di Exchange | Autorizzazioni di scrittura per gli attributi descritti in [writeback ibrida di Exchange](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) per gli utenti, gruppi e contatti.
Writeback di password | Autorizzazioni di scrittura per gli attributi descritti nella [Guida introduttiva alla gestione delle password](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) per gli utenti.
Writeback di dispositivo | Autorizzazioni concesse con uno script di PowerShell come descritto nel [dispositivo writeback](../active-directory-aadconnect-feature-device-writeback.md).
Writeback di gruppo | Leggere, creare, aggiornare ed eliminare oggetti gruppo nell'unità Organizzativa in cui i gruppi di distribuzione devono trovarsi.

## <a name="upgrade"></a>Eseguire l'aggiornamento
Quando si aggiorna da una versione di Azure AD Connect a una nuova versione, sono necessarie le autorizzazioni seguenti:

Principali | Autorizzazioni necessarie | Usato per
---- | ---- | ----
Utente che esegue l'installazione guidata | Amministratore del server locale | Aggiornare i file binari.
Utente che esegue l'installazione guidata | Membro del ADSyncAdmins | Apportare modifiche alle regole di sincronizzazione e altre operazioni di configurazione.
Utente che esegue l'installazione guidata | Se si usa un server SQL completo: DBO (o simili) del database motore di sincronizzazione | Apportare modifiche a livello di database, ad esempio l'aggiornamento di tabelle con le nuove colonne.

## <a name="more-about-the-created-accounts"></a>Altre informazioni, vedere account creati

### <a name="active-directory-account"></a>Account Active Directory
Se si Usa impostazioni express, viene creato un account di Active Directory non vengono usate per la sincronizzazione. Account creato si trova nel dominio radice della foresta nel contenitore di utenti e ha il relativo nome il prefisso **MSOL_**. L'account viene creato con una password complessa lunghe che non scada. Se si dispone di un criterio di password del proprio dominio, assicurarsi che lungo e sarebbe consentite password complesse per l'account.

![Account Active Directory](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Account del servizio di sincronizzazione Azure AD Connect
Viene creato un account di servizio locale dall'installazione guidata (a meno che l'account da utilizzare nelle impostazioni personalizzate). L'account è il prefisso **AAD_** e utilizzato per il servizio di sincronizzazione effettivo per l'esecuzione come. Se si installa Azure AD Connect sul Controller di dominio, viene creato l'account del dominio. Se si usa un server remoto che esegue SQL server o se si utilizza un proxy che richiede l'autenticazione, l'account di servizio **AAD_** deve trovarsi nel dominio.

![Account del servizio di sincronizzazione](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

L'account viene creato con una password complessa lunghe che non scada.

L'account viene utilizzato per archiviare le password per gli altri account in modo sicuro. Questi altri account vengono crittografati nel database. Le chiavi private per le chiavi di crittografia sono protetti con la crittografia di chiave privata di servizi di crittografia utilizzando API di protezione dei dati di Windows (DPAPI). È necessario reimpostare la password dell'account del servizio non poiché Windows comporta l'eliminazione quindi le chiavi di crittografia per motivi di sicurezza.

Se si usa un Server SQL completo, l'account del servizio è il proprietario del database creato per il motore di sincronizzazione. Il servizio non funzionerà correttamente con qualsiasi altra autorizzazione. Viene creato anche un accesso SQL.

L'account viene anche concesso le autorizzazioni per i file, le chiavi del Registro di sistema e altri oggetti correlati al motore di sincronizzazione.

### <a name="azure-ad-service-account"></a>Account del servizio Azure Active Directory
Viene creato un account in Azure Active Directory per l'utilizzo del servizio di sincronizzazione. Questo account può essere identificato con il nome visualizzato.

![Account Active Directory](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Nella seconda parte del nome utente, è possibile identificare il nome del server in che l'account viene utilizzato. Nella figura, il nome del server è FABRIKAMCON. Se si dispone di server di gestione temporanea, ogni server è il proprio account. È previsto un limite di 10 account del servizio di sincronizzazione di Azure Active Directory.

L'account di servizio viene creato con una password complessa lunghe che non scada. Dispone un ruolo speciale **Account di sincronizzazione della Directory** che contiene solo le autorizzazioni per eseguire attività di sincronizzazione della directory. Non è possibile concedere questo ruolo incorporato speciale la creazione guidata Azure AD Connect e il portale di Azure Mostra questo account con il ruolo **utente**.

![Account Active Directory ruolo](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](../active-directory-aadconnect.md).
