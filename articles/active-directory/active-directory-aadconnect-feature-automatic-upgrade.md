<properties
   pageTitle="Azure AD Connect: Aggiornamento automatico | Microsoft Azure"
   description="Questo argomento illustra la funzionalità incorporata di aggiornamento automatica di sincronizzazione di Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Aggiornamento automatico
Questa caratteristica è stata introdotta con build 1.1.105.0 (rilasciato febbraio 2016).

## <a name="overview"></a>Panoramica
Accertarsi che l'installazione di Azure AD Connect sia sempre aggiornato non sono mai state più facilmente con la funzionalità di **aggiornamento automatico** . Questa funzionalità è attivata per impostazione predefinita per le installazioni express e DirSync gli aggiornamenti. Quando viene rilasciata una nuova versione, l'installazione viene aggiornato automaticamente.

Aggiornamento automatico è attivata per impostazione predefinita per le operazioni seguenti:

- Esprimere impostazioni installazione e DirSync aggiornamenti.
- Uso di SQL Express LocalDB, che è cosa impostazioni Express utilizzano sempre. DirSync con SQL Express anche usare LocalDB.
- L'account Active Directory è l'account MSOL_ predefinito creato tramite le impostazioni di Express e DirSync.
- Contenere meno di 100.000 oggetti nel metaverse.

È possibile visualizzare lo stato corrente di aggiornamento automatico con i cmdlet di PowerShell `Get-ADSyncAutoUpgrade`. Sono stati seguenti:

Stato | Commento
---- | ----
Attivato | Aggiornamento automatico è attivato.
Sospesa | L'impostazione solo il sistema. Il sistema non è più idoneo per ricevere gli aggiornamenti automatici.
Disattivato | Aggiornamento automatico viene disabilitato.

È possibile modificare tra **attivato** e **disattivato** con `Set-ADSyncAutoUpgrade`. Solo il sistema è necessario impostare lo stato **sospeso**.

Aggiornamento automatico utilizza Azure Active Directory connettersi integrità per l'infrastruttura di aggiornamento. Per aggiornamento automatico per l'uso, verificare che è stato aperto gli URL nel server proxy per **La salute la connessione di Azure Active Directory** come descritto in [Office 365 URL e intervalli di indirizzi IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se la **Gestione del servizio di sincronizzazione** dell'interfaccia utente è in esecuzione nel server, l'aggiornamento verrà sospeso finché non viene chiusa l'interfaccia utente.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se l'installazione di connessione vengono aggiornati come previsto, attenersi alla seguente procedura per sapere cosa potrebbe contenere informazioni errate.

Prima di tutto, non è necessario attendere l'aggiornamento automatico per provare a eseguire il primo giorno che viene rilasciata una nuova versione. Esiste una casualità intenzionale prima tentativo di aggiornamento in modo che non avviso se l'installazione non è aggiornato immediatamente.

Se si ritiene che un elemento non è corretto, quindi eseguire prima `Get-ADSyncAutoUpgrade` per assicurarsi che l'aggiornamento automatico è attivato.

Assicurarsi che è stato aperto gli URL richiesti in un proxy o firewall. Aggiornamento automatico utilizza Azure Active Directory connettersi integrità come descritto nella [Panoramica](#overview). Se si utilizza un proxy, verificare che l'integrità è stato configurato per l'utilizzo di un [server proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Verificare anche la [connettività di integrità](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) di Azure Active Directory.

Con la connettività di Azure Active Directory verificato, è possibile esaminare i registri eventi. Avviare il Visualizzatore eventi e controllare il registro eventi **applicazione** . Aggiungere un filtro di registro eventi per l'origine di **Azure Active Directory connettere l'aggiornamento** e l' intervallo di id evento **399 300**.  
![Filtro registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

È ora possibile visualizzare i registri eventi associati con lo stato dell'aggiornamento automatico.  
![Filtro registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Il codice risultato presenta un prefisso con una panoramica dello stato.

Prefisso codice risultato | Descrizione
--- | ---
Successo | L'installazione è stato aggiornato.
UpgradeAborted | Una condizione temporanea arrestato l'aggiornamento. Verrà ripetuta nuovamente e la previsione è in un secondo momento stabilito.
UpgradeNotSupported | Il sistema ha una configurazione che blocca il sistema dall'aggiornamento automaticamente. Verrà ripetuta per vedere se lo stato in fase di modifica, ma non si prevede che il sistema deve essere aggiornato manualmente.

Di seguito è riportato un elenco dei messaggi più comuni di cui che si trova. Non sono elencati tutti, ma il messaggio del risultato dovrebbe essere deselezionare con quali il problema.

Messaggio di risultato | Descrizione
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | Impossibile scrivere nel Registro di sistema.
UpgradeAbortedInsufficientDatabasePermissions | Gruppo administrators predefinito non dispone delle autorizzazioni per il database. Aggiornare manualmente la versione più recente di Azure AD Connect per risolvere questo problema.
UpgradeAbortedInsufficientDiskSpace | Non c'è spazio su disco sufficiente per supportare un aggiornamento.
UpgradeAbortedSecurityGroupsNotPresent | Impossibile trovare e risolvere tutti i gruppi di sicurezza utilizzati dal motore di sincronizzazione.
UpgradeAbortedServiceCanNotBeStarted | Impossibile avviare il servizio NT **Sincronizzazione di Microsoft Azure Active Directory** .
UpgradeAbortedServiceCanNotBeStopped | Impossibile arrestare il servizio NT **Sincronizzazione di Microsoft Azure Active Directory** .
UpgradeAbortedServiceIsNotRunning | Il servizio NT **Sincronizzazione di Microsoft Azure Active Directory** non è in esecuzione.
UpgradeAbortedSyncCycleDisabled | L'opzione SyncCycle in [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è stata disattivata.
UpgradeAbortedSyncExeInUse | [Gestione di servizi di sincronizzazione dell'interfaccia utente](active-directory-aadconnectsync-service-manager-ui.md) è aperto nel server.
UpgradeAbortedSyncOrConfigurationInProgress | Esegue l'installazione guidata o una sincronizzazione è stata programmata di fuori l'utilità di pianificazione.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | È stato aggiunto regole personalizzate per la configurazione.
UpgradeNotSupportedDeviceWritebackEnabled | È stata attivata la caratteristica [writeback di dispositivo](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | È stata attivata la caratteristica [writeback di gruppo](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | L'installazione non è un impostazioni Express o un aggiornamento DirSync.
UpgradeNotSupportedMetaverseSizeExceeeded | Si dispone di più di 100.000 oggetti nel metaverse.
UpgradeNotSupportedMultiForestSetup | Si è connessi a più di un insieme di strutture. Installazione rapida solo si connette a un insieme di strutture.
UpgradeNotSupportedNonLocalDbInstall | Non si usa un database di SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | L' [account Active Directory Connector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) non è più l'account MSOL_ predefinito.
UpgradeNotSupportedStagingModeEnabled | Il server è impostato in [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | È stata attivata la caratteristica [writeback utente](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
