<properties
   pageTitle="Sincronizzazione di Azure AD Connect: utilità di pianificazione | Microsoft Azure"
   description="In questo argomento viene descritta la funzionalità di utilità di pianificazione integrata di sincronizzazione di Azure AD Connect."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Sincronizzazione di Azure AD Connect: utilità di pianificazione
In questo argomento descrive l'utilità di pianificazione predefiniti di sincronizzazione di Azure AD Connect (noto anche motore di sincronizzazione).

Questa caratteristica è stata introdotta con build 1.1.105.0 (rilasciato febbraio 2016).

## <a name="overview"></a>Panoramica
Sincronizzazione di Azure AD Connect verrà sincronizzare le modifiche in corso nella directory locale con un'utilità di pianificazione. Sono disponibili due processi di pianificazione, uno per la sincronizzazione delle password e un altro oggetto/attributo sincronizzazione e attività di manutenzione. In questo argomento verrà illustrate le caratteristiche quest'ultima.

Nelle versioni precedenti l'utilità di pianificazione per gli oggetti e attributi è esterno al motore di sincronizzazione e l'utilità di pianificazione di Windows o un servizio Windows diverso è stato utilizzato per avviare il processo di sincronizzazione. L'utilità di pianificazione è con incorporati 1.1 versioni per la sincronizzazione del motore e consentire alcune personalizzazioni. La nuova frequenza di sincronizzazione predefinito è 30 minuti.

L'utilità di pianificazione è responsabile di due attività:

- **Ciclo di sincronizzazione**. Il processo di importazione, sincronizzare ed esportare le modifiche.
- **Attività di manutenzione**. Rinnovare chiavi e certificati per la reimpostazione della Password e dispositivo registrazione servizio (DRS). Eliminare voci precedente nel log delle operazioni.

L'utilità di pianificazione è sempre in esecuzione, ma può essere configurato per eseguire solo una o nessuna di queste attività. Ad esempio se è necessario che il proprio processo del ciclo di sincronizzazione, è possibile disattivare questa attività, l'utilità di pianificazione ma comunque eseguire l'attività di manutenzione.

## <a name="scheduler-configuration"></a>Configurazione di utilità di pianificazione
Per visualizzare le impostazioni di configurazione corrente, passare a PowerShell ed eseguire `Get-ADSyncScheduler`. Verrà visualizzato è simile al seguente:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Se viene visualizzato **il comando per la sincronizzazione o cmdlet è non disponibile** quando si esegue questo cmdlet, del modulo PowerShell non viene caricato. Può accadere se si esegue Azure AD Connect sul controller di dominio o su un server con livelli di restrizione PowerShell superiori rispetto a quelle predefinite. Se viene visualizzato questo errore, eseguire `Import-Module ADSync` per rendere disponibili i cmdlet.

- **AllowedSyncCycleInterval**. Più di frequente Azure Active Directory consente la sincronizzazione dei venga eseguita. È possibile sincronizzare più frequentemente e continueranno a essere supportate.
- **CurrentlyEffectiveSyncCycleInterval**. Pianificazione attualmente in vigore. Avrà lo stesso valore di CustomizedSyncInterval (se set) se non è più frequente AllowedSyncInterval. Se si cambia CustomizedSyncCycleInterval, si avranno effetto dopo il ciclo di sincronizzazione successivo.
- **CustomizedSyncCycleInterval**. Se si desidera l'utilità di pianificazione per eseguire altre frequenza da quello predefinito 30 minuti, configurare questa impostazione. Nell'immagine sopra l'utilità di pianificazione è stata impostata per l'esecuzione invece ogni ora. Se si imposta questo a un valore inferiore a AllowedSyncInterval, quest'ultimo verrà utilizzato.
- **NextSyncCyclePolicyType**. Delta o iniziale. Consente di definire se l'esecuzione successiva deve solo le modifiche delta processo, oppure se l'esecuzione successiva deve eseguire un full importare e sincronizzare, che sarà rielaborare anche tutte le regole nuove o modificate.
- **NextSyncCycleStartTimeInUTC**. Nuovamente l'utilità di pianificazione verrà avviato il ciclo di sincronizzazione successivo.
- **PurgeRunHistoryInterval**. Il tempo di mantenere registri operazioni. Questi possono essere esaminati in Gestore servizio di sincronizzazione. Il valore predefinito è mantenere le per 7 giorni.
- **SyncCycleEnabled**. Indica se l'utilità di pianificazione è in esecuzione processi di esportazione, importazione e sincronizzazione durante l'operazione.
- **MaintenanceEnabled**. Indica se è attivato il processo di manutenzione. Verrà aggiornare i certificati/tasti e pulizia log operazioni.
- **IsStagingModeEnabled**. Indica se è attiva [la modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode) .

È possibile modificare alcune di queste impostazioni con `Set-ADSyncScheduler`. È possibile modificare i parametri seguenti:

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

La configurazione di utilità di pianificazione è archiviata in Azure Active Directory. Se si dispone di un server di gestione temporanea, qualsiasi modifica nel server primario avranno effetto anche su server di gestione temporanea (ad eccezione delle IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintassi:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d: giorni, HH - ore, mm - minuti, ss - secondi

Esempio:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Cambierà l'utilità di pianificazione per eseguire ogni tre ore.

Esempio:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Cambierà utilità di pianificazione eseguiti con cadenza giornaliera.

## <a name="start-the-scheduler"></a>Avviare l'utilità di pianificazione
L'utilità di pianificazione verrà eseguita ogni 30 minuti per impostazione predefinita. In alcuni casi è consigliabile eseguire un ciclo di sincronizzazione tra i cicli di pianificata oppure è necessario eseguire un tipo diverso.

**Ciclo di sincronizzazione delta**  
Un ciclo di sincronizzazione delta include i passaggi seguenti:

- Importazione delta su tutti i connettori
- Sincronizzazione delta su tutti i connettori
- Esportare in tutti i connettori

È possibile che si dispone di un urgente modificare che deve essere sincronizzato immediatamente motivo per cui è necessario eseguire manualmente un ciclo. Se è necessario eseguire manualmente un ciclo, quindi da PowerShell eseguire `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo di sincronizzazione completa**  
Se è stata effettuata una delle seguenti modifiche di configurazione, è necessario eseguire un ciclo di sincronizzazione completa (noto anche Iniziali):

- Aggiunta di più oggetti o gli attributi di una directory di origine da importare
- Modifiche apportate le regole di sincronizzazione
- Modificare [il filtro](active-directory-aadconnectsync-configure-filtering.md) in modo da incluso un diverso numero di oggetti

Se è stata effettuata una di queste modifiche, è necessario eseguire un ciclo di sincronizzazione completa in modo che il motore di sincronizzazione ha la possibilità di riconsolidare spazi connettore. Un ciclo di sincronizzazione completa include i passaggi seguenti:

- Importazione completa su tutti i connettori
- Sincronizzazione completa su tutti i connettori
- Esportare in tutti i connettori

Per avviare un ciclo di sincronizzazione completa, eseguire `Start-ADSyncSyncCycle -PolicyType Initial` da un prompt dei comandi PowerShell. Verrà creato un ciclo di sincronizzazione completa.

## <a name="stop-the-scheduler"></a>Interrompere l'utilità di pianificazione
Se l'utilità di pianificazione è in esecuzione un ciclo di sincronizzazione che è possibile interromperlo. Se, ad esempio si avvia l'installazione guidata e viene visualizzato questo messaggio di errore:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Durante l'esecuzione di un ciclo di sincronizzazione, è possibile apportare modifiche alla configurazione. È possibile attendere che l'utilità di pianificazione è stato completato il processo, ma è possibile interrompere anche per poter apportare immediatamente le modifiche. Interruzione della corrente circolare non dannoso e le modifiche non ancora elaborate verranno elaborate con esecuzione successiva.

1. Prima di tutto che indica l'utilità di pianificazione per interrompere il ciclo corrente con il cmdlet di PowerShell `Stop-ADSyncSyncCycle`.
2. Interrompere l'utilità di pianificazione non impedirà il connettore corrente dall'attività corrente. Per forzare il connettore per interrompere, eseguire le operazioni seguenti: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Avviare il **Servizio di sincronizzazione** dal menu start. Passare ai **connettori**, evidenziare il connettore con lo stato **in esecuzione** e selezionare **Interrompi** delle azioni.

L'utilità di pianificazione è ancora in corso e inizierà nuovamente in opportunità successivo.

## <a name="custom-scheduler"></a>Utilità di pianificazione personalizzata
I cmdlet descritti in questa sezione sono disponibili solo nella build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e versioni successive.

Se l'utilità di pianificazione predefinita non soddisfa esigenze, è possibile pianificare i connettori tramite PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Richiamare ADSyncRunProfile
È possibile avviare un profilo di un connettore in questo modo:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

I nomi da utilizzare per [i nomi di connettore](active-directory-aadconnectsync-service-manager-ui-connectors.md) ed [eseguire profilo](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) sono disponibili nell' [Interfaccia utente di gestione di servizi di sincronizzazione](active-directory-aadconnectsync-service-manager-ui.md).

![Richiamare profilo di esecuzione](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

Il `Invoke-ADSyncRunProfile` icona del cmdlet, vale a dire non restituirà controllo fino al completamento dell'operazione, il connettore correttamente o con un errore.

Quando si pianificano i connettori, il suggerimento consiste nel pianificarne nell'ordine seguente:

1. (Intero/Delta) Importare da directory locale, ad esempio Active Directory
2. (Intero/Delta) Importazione da Azure Active Directory
3. (Intero/Delta) Sincronizzazione dalla directory locale, ad esempio Active Directory
4. (Full/Delta) Sincronizzazione di Azure Active Directory
5. Esportare in Azure Active Directory
6. Esportare in directory locale, ad esempio Active Directory

Se osserva l'utilità di pianificazione predefinita, questa è l'ordine che eseguiranno i connettori.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
È inoltre possibile monitorare il motore di sincronizzazione per verificare se è occupato o inattivo. Se il motore di sincronizzazione è inattivo e non è in esecuzione un connettore, questo cmdlet restituisce un risultato vuoto. Se si esegue un connettore, verrà restituito il nome del connettore.

```
Get-ADSyncConnectorRunStatus
```

![Connettore eseguire stato](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Nella figura riportata sopra, la prima riga è stata inviata da uno stato in cui il motore di sincronizzazione è inattivo. La seconda riga da quando è in esecuzione il connettore di Azure Active Directory.

## <a name="scheduler-and-installation-wizard"></a>Utilità di pianificazione e l'installazione guidata
Se viene avviata la procedura guidata installazione, quindi l'utilità di pianificazione verrà temporaneamente sospeso. In questo modo si presuppone si effettuano modifiche alla configurazione e non può essere applicati se il motore di sincronizzazione è in esecuzione. Per questo motivo, non lasciare l'installazione guidata aperta poiché il test verrà interrotto il motore di sincronizzazione di eseguire le azioni di sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
