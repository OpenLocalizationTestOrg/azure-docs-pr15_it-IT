<properties
   pageTitle="Azure AD Connect: Cronologia delle versioni versione | Microsoft Azure"
   description="In questo argomento sono elencate tutte le versioni di Azure AD Connect e sincronizzazione di Azure Active Directory"
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
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Cronologia delle versioni versione

Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect con nuove funzionalità e funzionalità. Non tutte le aggiunte sono applicabili a tutti i gruppi di destinatari.

In questo articolo è progettato per tenere traccia delle versioni che sono state rilasciate e da comprendere se è necessario aggiornare la versione più recente o meno.

Si tratta di elenco di argomenti correlati:

Argomento |  
--------- | --------- |
Passaggi da eseguire l'aggiornamento di Azure AD Connect | Diversi metodi per [l'aggiornamento da una versione precedente per le ultime](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect release.
Autorizzazioni necessarie | Per le autorizzazioni necessarie per applicare un aggiornamento, vedere [account e autorizzazioni](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Download| [Connettere download Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Rilasciato: agosto 2016

**Problemi risolti:**

- Le modifiche apportate a sincronizzare intervallo non viene eseguita fino al termine successivo ciclo di sincronizzazione.
- Procedura guidata di Azure AD Connect non accetta account Azure Active Directory il cui nome utente inizia con un carattere di sottolineatura (\_).
- Procedura guidata di Azure AD Connect non riesce a eseguire l'autenticazione account Azure Active Directory fornito se password dell'account contiene troppi caratteri speciali. Messaggio di errore "Impossibile convalidare le credenziali. Un errore imprevisto si è verificato." viene restituito.
- Disinstallazione di server di gestione temporanea disattiva la sincronizzazione delle password nel tenant di Azure Active Directory e la password non consente la sincronizzazione con active server.
- La sincronizzazione delle password non funziona in casi rari quando non c'è alcun hash password memorizzati all'utente.
- Quando il server Azure AD Connect è abilitato per la modalità di gestione temporanea, writeback di password non è temporaneamente disabilitato.
- Azure AD Connect guidata non viene visualizzata la sincronizzazione delle password effettivo e la configurazione di writeback di password quando server è in modalità di gestione temporanea. Mostra sempre loro come disattivato.
- Modifiche alla configurazione per la sincronizzazione delle password e la password writeback non vengono mantenute dalla procedura guidata di Azure AD Connect quando server è in modalità di gestione temporanea.

**Miglioramenti:**

- Cmdlet di inizio ADSyncSyncCycle aggiornata per indicare se è in grado di avviare un nuovo ciclo di sincronizzazione o meno.
- Cmdlet di tabulazione ADSyncSyncCycle aggiunto per terminare il ciclo di sincronizzazione e operazione in corso.
- Cmdlet di tabulazione ADSyncScheduler aggiornati per terminare il ciclo di sincronizzazione e operazione in corso.
- Quando si configura [Estensioni Directory](active-directory-aadconnectsync-feature-directory-extensions.md) nella creazione guidata Azure AD Connect, è possibile selezionare ora attributo di Active Directory di tipo "Teletex stringa".

## <a name="111890"></a>1.1.189.0
Rilasciato: 2016 giugno

**Problemi risolti e miglioramenti:**

- Azure AD Connect può ora installato in un server compatibile con FIPS.
    - Per la sincronizzazione delle password, vedere [la sincronizzazione delle Password e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- È stato risolto un problema nel punto in cui un nome NetBIOS non è possibile risolvere il nome FQDN in Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Rilascio: 2016 maggio

**Nuove caratteristiche:**

- Visualizza un avviso e consente di verificare domini se non è stata svolta prima di eseguire Azure AD Connect.
- Aggiunta del supporto per [la Germania Cloud Microsoft](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Aggiunta del supporto per l'infrastruttura [cloud di Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) più recente con nuovi requisiti URL.

**Problemi risolti e miglioramenti:**

- Aggiungere il filtro all'Editor di regola di sincronizzazione per renderlo più facilmente le regole di sincronizzazione.
- Miglioramento delle prestazioni quando si eliminano uno spazio connettore.
- Fissa un problemi quando lo stesso oggetto è stato eliminato e aggiunto nella stessa eseguire (denominati Elimina/Aggiungi).
- Una regola di sincronizzazione disabilitata non verrà più riattivare inclusi oggetti e attributi sullo schema di aggiornamento o di una directory aggiornare.

## <a name="111300"></a>1.1.130.0
Rilascio: 2016 aprile

**Nuove caratteristiche:**

- Aggiunta del supporto per gli attributi multivalore a [Estensioni Directory](active-directory-aadconnectsync-feature-directory-extensions.md).
- Aggiunta del supporto per più varianti di configurazione per l' [aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) da prendere in considerazione idoneo per l'aggiornamento.
- Aggiungere alcuni cmdlet per [l'utilità di pianificazione personalizzata](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Rilascio: 2016 marzo

**Problemi risolti:**

- Apportate che Express installazione non può essere utilizzato in Windows Server 2008 (pre-R2) dopo la sincronizzazione delle password non è supportata in questo sistema operativo.
- Aggiornamento da DirSync con la configurazione di un filtro personalizzato non risolvono come previsto.
- Durante l'aggiornamento a una versione più recente e sono state apportate modifiche alla configurazione, importazione/sincronizzazione completa non deve essere pianificata.

## <a name="111100"></a>1.1.110.0
Rilascio: febbraio 2016

**Problemi risolti:**

- Aggiornamento da versioni precedenti non funziona se l'installazione non è presente nella cartella **C:\Programmi\Microsoft file** predefinito.
- Se si installa e deselezionare l'opzione **Avvia il processo di sincronizzazione.** al termine dell'installazione guidata, eseguire nuovamente l'installazione guidata non attivare l'utilità di pianificazione.
- L'utilità di pianificazione non funzionano come previsto su server in cui il formato di data/ora non IT-IT. Verrà inoltre bloccare `Get-ADSyncScheduler` per restituire ore corrette.
- Se è installata una versione precedente di Azure AD Connect con ADFS come opzione di accesso e l'aggiornamento, è possibile eseguire l'installazione guidata di nuovo.

## <a name="111050"></a>1.1.105.0
Rilascio: febbraio 2016

**Nuove caratteristiche:**

- Funzionalità di [aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) per i clienti di impostazioni Express.
- Supporto per l'amministratore globale utilizzando MFA e PIM nell'installazione guidata.
    - È necessario consentire il proxy consentire anche il traffico https://secure.aadcdn.microsoftonline-p.com se si utilizza MFA.
    - È necessario aggiungere https://secure.aadcdn.microsoftonline-p.com all'elenco siti attendibili per MFA di funzionare correttamente.
- Consenti modifica metodo di accesso dell'utente dopo l'installazione iniziale.
- Consenti [dominio e unità Organizzativa filtro](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) nell'installazione guidata. In questo modo anche la connessione a insiemi di strutture in cui non tutti i domini sono disponibili.
- [Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è integrato al motore di sincronizzazione.

**Caratteristiche alzati di livello dalla versione di anteprima per GA:**

- [Writeback di dispositivo](active-directory-aadconnect-feature-device-writeback.md).
- [Estensioni directory](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nuove funzionalità di anteprima:**

- Il nuovo formato sincronizzare ciclo intervallo è 30 minuti. Usato per 3 ore per tutte le versioni precedenti. Aggiunge il supporto per modificare il comportamento di [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemi risolti:**

- Nella pagina domini di verifica DNS non è riuscita riconoscere sempre i domini.
- Richiede le credenziali di amministratore di dominio durante la configurazione di ADFS.
- I locale account Active Directory non vengono riconosciuti dall'installazione guidata se si trova in un dominio con una struttura DNS differente il dominio radice.

## <a name="1091310"></a>1.0.9131.0
Rilasciato: dicembre 2015

**Problemi risolti:**

- La sincronizzazione delle password potrebbe non funzionare quando si modificano le password in Active Directory, ma funziona quando si imposta password.
- Quando si dispone di un server proxy, l'autenticazione di Azure Active Directory può avere esito negativo durante l'installazione o annullare l'aggiornamento della pagina di configurazione.
- Aggiornamento da una versione precedente di Azure AD Connect completo SQL Server non riuscirà se non si SA in SQL.
- Aggiornamento da una versione precedente di Azure AD Connect con un telecomando SQL Server verrà visualizzato l'errore "Impossibile accedere al database SQL ADSync".

## <a name="1091250"></a>1.0.9125.0
Rilasciato: novembre 2015

**Nuove caratteristiche:**

- Possibile riconfigurare ADFS all'attendibilità di Azure Active Directory.
- Consente di aggiornare lo schema di Active Directory e rigenerare le regole di sincronizzazione.
- Disattivare una regola di sincronizzazione.
- Definire "AuthoritativeNull" come nuovo valore letterale in una regola di sincronizzazione.

**Nuove funzionalità di anteprima:**

- [Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md).
- Supporto per la sincronizzazione delle password [dei servizi di dominio Active Directory Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nuovo scenario supportato:**

- Supporta più organizzazioni di Exchange locale. Per ulteriori informazioni, vedere [distribuzioni ibride con più insiemi di strutture di Active Directory](https://technet.microsoft.com/library/jj873754.aspx) .

**Problemi risolti:**

- Problemi di sincronizzazione password:
    - Un oggetto spostato dall'esterno dell'ambito nell'ambito non avranno la relativa password sincronizzati. Questo incudes unità Organizzativa e il filtro degli attributi.
    - Selezione di una nuova unità Organizzativa da includere aggiornati non richiede una sincronizzazione completa password.
    - Quando un utente disabilitato è attivato la password non vengono sincronizzati.
    - Coda di tentativi password è impostata su infinito e il limite precedente di 5.000 oggetti che si desidera è stata rimossa.
    - [Risoluzione dei problemi migliorate](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Non è possibile connettersi a Active Directory con livello di foresta funzionalità di Windows Server 2016.
- Non è possibile modificare il gruppo utilizzato per il filtro del gruppo dopo l'installazione iniziale.
- Non è più creerà un nuovo profilo utente nel server Azure AD Connect per ogni utente che effettua la modifica della password con writeback di password abilitato.
- Non è possibile usare intero lungo valori aggiornati ambiti di regole.
- La casella di controllo "dispositivo writeback" rimane disattivato se sono presenti controller di dominio non è raggiungibile.

## <a name="1086670"></a>1.0.8667.0
Rilasciato: agosto 2015

**Nuove caratteristiche:**

- Installazione guidata di Azure AD Connect è ora localizzato a tutte le lingue di Windows Server.
- Aggiunta del supporto per conto sbloccare quando si utilizza la gestione delle password Azure Active Directory.

**Problemi risolti:**

- Installazione guidata di Azure AD Connect determina l'arresto anomalo se un altro utente continua installazione piuttosto che la persona che ha iniziato l'installazione.
- Se una precedente disinstallazione di Azure AD Connect non riesce a disinstallare sincronizzazione di Azure AD Connect correttamente, non è possibile reinstallare.
- Non è possibile installare Azure AD Connect tramite installazione rapida se l'utente non è presente nel dominio radice della foresta oppure se viene utilizzata una versione localizzata di Active Directory.
- Se non è possibile risolvere il FQDN dell'account utente Active Directory, viene visualizzato un messaggio di errore "Impossibile eseguire il commit lo schema" fuorvianti.
- Se l'account usato in Active Directory Connector viene modificata la creazione guidata, la procedura guidata avrà esito negativo nelle esecuzioni successive.
- Azure AD Connect a volte non è possibile installare in un controller di dominio.
- Non è possibile attivare e disattivare "Modalità di gestione temporanea" se sono stati aggiunti attributi di estensione.
- Password writeback ha esito negativo in alcune attività di configurazione a causa di una password errata in Active Directory Connector.
- Non è possibile aggiornare DirSync se nome distinto viene usato nell'attributo filtro.
- Utilizzo della CPU eccessivo quando si usa la reimpostazione della password.

**Caratteristiche rimosse preview:**

- La caratteristica Anteprima [che writeback utente](active-directory-aadconnect-feature-preview.md#user-writeback) è stato rimosso temporaneamente in base a commenti e suggerimenti dai clienti di anteprima. Verrà nuovamente aggiunto in un secondo momento quando è stato risolto il feedback fornito.

## <a name="1086410"></a>1.0.8641.0
Rilasciato: 2015 giugno

**Versione iniziale di Azure AD Connect.**

Nome è cambiato da sincronizzazione di Azure Active Directory per Azure AD Connect.

**Nuove caratteristiche:**

- [Esprimere le impostazioni di](./connect/active-directory-aadconnect-get-started-express.md) installazione
- Possibile [configurare ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Può [eseguire l'aggiornamento da DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Evitare che le eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introdotta [la modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuove funzionalità di anteprima:**

- [Writeback di utente](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Writeback di gruppo](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Writeback di dispositivo](active-directory-aadconnect-feature-device-writeback.md)
- [Estensioni directory](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Rilascio: 2015 maggio

**Nuovo requisito:**

- Sincronizzazione di Azure Active Directory richiede la versione di .net framework 4.5.1 siano installati.

**Problemi risolti:**

- Writeback di password da Azure Active Directory non riesce con un errore di connettività servicebus.

## <a name="104910413"></a>1.0.491.0413
Rilascio: 2015 aprile

**Problemi risolti e miglioramenti:**

- Active Directory Connector non elabora correttamente le eliminazioni se è attivato il Cestino e sono presenti più domini della foresta.
- Le prestazioni di operazioni di importazione sono stata migliorata per Azure Active Directory Connector.
- Quando un gruppo ha superato il limite di appartenenza (per impostazione predefinita, il limite è impostato su 50k oggetti), il gruppo è stato eliminato in Azure Active Directory. Il nuovo comportamento è che il gruppo rimarrà, viene generato un errore e non verrà esportata Nessuna modifica l'appartenenza.
- Un nuovo oggetto non è possibile eseguire il provisioning se un'operazione di eliminazione a fasi con il nome distinto stessa è già presente nell'area connettore.
- Alcuni oggetti sono contrassegnati per la sincronizzazione durante una sincronizzazione delta anche se non viene modificata in più fasi sull'oggetto.
- Forzare una sincronizzazione delle password rimuove anche all'elenco di controller di dominio preferito.
- CSExportAnalyzer con problemi a livello con alcuni stati di oggetti.

**Nuove caratteristiche:**

- Un join può ora connettersi al tipo di oggetto "ANY" nel MV.

## <a name="104850222"></a>1.0.485.0222
Rilascio: febbraio 2015

**Miglioramenti:**

- Prestazioni migliorate importazione.

**Problemi risolti:**

- La sincronizzazione delle password rispetti l'attributo cloudFiltered utilizzata dagli attributi di filtro. Oggetti filtrati non risulteranno nell'ambito per la sincronizzazione delle password.
- In determinate situazioni in cui la topologia era molto numerosi controller di dominio, la sincronizzazione delle password non funziona.
- "Arrestato server" quando si importano dal connettore Azure Active Directory dopo la gestione di dispositivi è stato attivato in Azure Active Directory/Intune.
- Partecipazione alle identità di protezione esterna (FSP) da più domini nella stessa foresta genera un errore di join ambigui.

## <a name="104751202"></a>1.0.475.1202
Rilasciato: dicembre 2014

**Nuove caratteristiche:**

- È ora supportato per eseguire la sincronizzazione delle password con il filtro attributo basata. Per ulteriori informazioni, vedere [la sincronizzazione delle Password con il filtro](active-directory-aadconnectsync-configure-filtering.md).
- Attributo msDS-ExternalDirectoryObjectID scritte in Active Directory. Verrà aggiunto il supporto per le applicazioni di Office 365 usando OAuth2 per accedere a entrambi, Online e cassette postali in una distribuzione ibrida di Exchange locale.

**Problemi di aggiornamento fisse:**

- Una versione più recente dell'Assistente per l'accesso è disponibile nel server.
- Un percorso di installazione personalizzata è stato usato per installare sincronizzazione di Azure Active Directory.
- Un criterio di join personalizzate non validi blocca l'aggiornamento.

**Altre correzioni:**

- Fissa i modelli per Office Professional Plus.
- Problemi di installazione fissi dovuti nomi utente che iniziano con un trattino.
- Fisso perdere l'impostazione sourceAnchor durante l'esecuzione dell'installazione guidata di una seconda volta.
- Fisso ETW tracing per la sincronizzazione delle password

## <a name="104701023"></a>1.0.470.1023
Rilasciato: ottobre 2014

**Nuove caratteristiche:**

- Sincronizzazione delle password da più locale Active Directory di Azure Active Directory.
- Installazione dell'interfaccia utente localizzata a tutte le lingue di Windows Server.

**L'aggiornamento da GA AADSync 1.0**

Se si dispone già installato sincronizzazione di Azure Active Directory, esiste un'ulteriore operazione di che è necessario eseguire nel caso in cui sono state modificate le regole di sincronizzazione della casella. Dopo l'aggiornamento per la 1.0.470.1023 rilascia, la sincronizzazione vengono duplicate regole è stato modificato. Per ogni regola sincronizzazione modificate eseguire le operazioni seguenti:

- Individuare la regola di sincronizzazione sono state modificate e prendere nota delle modifiche apportate.
- Eliminare la regola di sincronizzazione.
- Individuare la nuova regola di sincronizzazione autore sincronizzazione di Azure Active Directory e riapplicare le modifiche.

**Autorizzazioni per l'account Active Directory**

L'account Active Directory deve disporre di autorizzazioni aggiuntive per poter leggere i valori di password da Active Directory. Le autorizzazioni da concedere sono denominate "Replica modifiche Directory" e "Replica Directory modifiche tutti". Entrambe le autorizzazioni necessarie per poter leggere i valori di password

## <a name="104190911"></a>1.0.419.0911
Rilasciato: settembre 2014

**Versione iniziale di sincronizzazione di Azure Active Directory.**

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
