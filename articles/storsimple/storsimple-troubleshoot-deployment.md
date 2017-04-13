<properties 
   pageTitle="Risoluzione dei problemi di distribuzione StorSimple | Microsoft Azure"
   description="Descrive come individuare e correggere gli errori che si verificano quando si distribuisce prima StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Risoluzione dei problemi di distribuzione StorSimple dispositivo

## <a name="overview"></a>Panoramica

Questo articolo fornisce indicazioni per la risoluzione dei problemi utile per la distribuzione di Microsoft Azure StorSimple. Descrive i problemi più comuni, le possibili cause e procedure consigliate per risolvere problemi che potrebbero verificarsi quando si configura StorSimple. Queste informazioni si applicano al dispositivo fisico StorSimple locale e il dispositivo virtuale StorSimple.

> [AZURE.NOTE] Problemi relativi alla configurazione di dispositivi potrebbero verificarsi possono verificarsi quando si distribuisce il dispositivo per la prima volta o può verificarsi in un secondo momento, quando il dispositivo è operativo. In questo articolo è incentrato sulla risoluzione dei problemi prima distribuzione. Per risolvere un dispositivo operativo, passare alla [risoluzione dei problemi operativi dispositivo](storsimple-troubleshoot-operational-device.md).

In questo articolo vengono illustrati gli strumenti per la risoluzione dei problemi StorSimple distribuzioni anche e viene fornito un esempio dettagliato sulla risoluzione dei problemi.

## <a name="first-time-deployment-issues"></a>Problemi di distribuzione la prima volta

Se si verificano problemi durante la distribuzione di dispositivo per la prima volta, tenere presente quanto segue:

- Se si sta risolvendo un dispositivo fisico, assicurarsi che l'hardware è stato installato e configurato come descritto in [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).
- Controllare i prerequisiti per la distribuzione. Assicurarsi di avere tutte le informazioni descritte nell' [elenco di controllo configurazione distribuzione](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Esaminare le note sulla versione StorSimple per verificare se il problema viene descritta. Note sulla versione includono soluzioni alternative per i problemi noti di installazione. 

Durante la distribuzione di dispositivo, più comuni problemi che gli utenti possono verificarsi nominale all'esecuzione della configurazione guidata e quando si registra il dispositivo tramite Windows PowerShell al StorSimple. (Utilizzare Windows PowerShell per StorSimple per registrare e configurare il dispositivo StorSimple. Per ulteriori informazioni sulla registrazione di dispositivi, vedere [passaggio 3: configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Nelle sezioni seguenti consentono di risolvere i problemi che si verificano quando si configura il dispositivo StorSimple per la prima volta.

## <a name="first-time-setup-wizard-process"></a>Procedura guidata configurazione la prima volta

La procedura seguente riepiloga il processo di configurazione guidata. Per informazioni sull'installazione dettagliate, vedere [distribuire il dispositivo di StorSimple locale](storsimple-deployment-walkthrough.md).

1. Eseguire il cmdlet [HcsSetupWizard richiama](https://technet.microsoft.com/library/dn688135.aspx) per avviare la configurazione guidata che mostrerà tutti i passaggi rimanenti. 
2. Configurare la rete: la configurazione guidata consente di configurare le impostazioni di rete per l'interfaccia di rete 0 dati in un dispositivo StorSimple. Queste impostazioni includono le seguenti:
  - Virtuale indirizzi IP, maschera subnet e gateway: il cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) viene eseguito in background. Viene configurato l'indirizzo IP, maschera subnet e gateway per l'interfaccia di rete 0 dati in un dispositivo StorSimple.
  - Server DNS primario: il cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) viene eseguito in background. Configura le impostazioni di DNS per la soluzione StorSimple.
  - Server NTP – il cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) viene eseguita in background. Configura le impostazioni del server NTP per la soluzione StorSimple.
  - Proxy web facoltativo: il cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) viene eseguita in background. Imposta, quindi consente la configurazione del proxy web per la soluzione StorSimple.
3. Impostare le password: il passaggio successivo consiste nel configurare dell'amministratore del dispositivo e la password StorSimple Snapshot Manager. Se si esegue l'aggiornamento 1, quindi sarà non richiesto per impostare la password StorSimple Snapshot Manager.
  - La password di amministratore del dispositivo usata per accedere al dispositivo. La password di dispositivo predefinito è **Password1**.
  - La password StorSimple Snapshot Manager è necessaria quando si configura un dispositivo da usare StorSimple Snapshot Manager. È necessario prima impostare la password della configurazione guidata e quindi è possibile impostare e modificare dal servizio di gestione StorSimple. Questa password autentica la periferica con StorSimple Snapshot Manager.
 
    > [AZURE.IMPORTANT] Le password sono raccolti prima dell'iscrizione, ma applicate solo dopo aver registrato correttamente il dispositivo. Se si verifica un errore per applicare una password, verrà richiesto di immettere nuovamente la password fino a quando non sono stati raccolti password (che soddisfano i requisiti di complessità).

4. Registrare il dispositivo: l'ultimo passaggio consiste nel registrare il dispositivo con il servizio di gestione di StorSimple in esecuzione in Microsoft Azure. La registrazione è necessario per [ottenere la chiave di registrazione del servizio](storsimple-manage-service.md#get-the-service-registration-key) dal portale di classica Azure e fornire della configurazione guidata. Dopo il dispositivo è registrato correttamente, è disponibile una chiave di crittografia di servizio dati. Assicurarsi di mantenere la chiave di crittografia in un percorso sicuro perché sarà richiesto di registrare tutti i dispositivi successivi con il servizio.

## <a name="common-errors-during-device-deployment"></a>Errori comuni durante la distribuzione di dispositivo

Le tabelle seguenti elencano gli errori comuni che possono verificarsi quando si:

- Configurare le impostazioni di rete necessari.
- Configurare le impostazioni del proxy web facoltativo.
- Configurare l'amministratore del dispositivo e la password StorSimple Snapshot Manager. 
- Registrare il dispositivo. 

## <a name="errors-during-the-required-network-settings"></a>Errori durante le impostazioni di rete necessari

| No.| Messaggio di errore | Le possibili cause | Azione consigliata |
| ---| ------------- | --------------- | ------------------ |
| 1  | Richiamare HcsSetupWizard: È possibile eseguire il comando solo sul controller attivo. | È stato in corso la configurazione sul controller passivo.| Eseguire il comando da controller attivo. Per ulteriori informazioni, vedere [identificare un controller active nel dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Richiamare HcsSetupWizard: Dispositivo non pronto. | Esistono problemi di connettività di rete dati 0.| Verificare la connettività di rete fisica dati 0.|
| 3 | Richiamare HcsSetupWizard: Non esiste un conflitto di indirizzi IP con un altro sistema sulla rete (eccezione da HRESULT: 0x80070263). | IP fornito per dati 0 era già in uso da un altro sistema. | Specificare un nuovo indirizzo IP che non sia in uso.|
| 4 | Richiamare HcsSetupWizard: Non è possibile una risorsa. (Eccezione da HRESULT: 0x800713AE). | Duplicare VIP. IP specificato è già in uso.| Specificare un nuovo indirizzo IP che non sia in uso.|
| 5 | Richiamare HcsSetupWizard: L'indirizzo IPv4 non validi. | L'indirizzo IP è fornito in un formato non corretto.| Verificare il formato e fornire nuovamente l'indirizzo IP. Per ulteriori informazioni, vedere [Gli indirizzi Ipv4 di][1]. |
| 6 | Richiamare HcsSetupWizard: L'indirizzo IPv6 non valido. | L'indirizzo IP è fornito in un formato non corretto.| Verificare il formato e fornire nuovamente l'indirizzo IP. Per ulteriori informazioni, vedere [Indirizzi Ipv6][2].|
| 7 | Richiamare HcsSetupWizard: Sono non disponibili Nessun endpoint nel mapping degli endpoint. (Eccezione da HRESULT: 0x800706D9) | La funzionalità di cluster non funziona. | [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per la procedura successiva.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Errori durante le impostazioni del proxy web facoltativo

| No.| Messaggio di errore | Le possibili cause | Azione consigliata |
| ---| ------------- | --------------- | ------------------ |
| 1  | Richiamare HcsSetupWizard: Parametro non valido (eccezione da HRESULT: 0x80070057) | Uno dei parametri disponibili per le impostazioni del proxy non è valido.| URI non è disponibile nel formato corretto. Utilizzare il formato seguente: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Richiamare HcsSetupWizard: Server RPC non disponibile (eccezione da HRESULT: 0x800706ba) | La causa è una delle operazioni seguenti:<ol><li>Il cluster non è massimo.</li><li>Controller passivo non è possibile comunicare con il controller attivo e viene eseguito il comando da controller passiva.</li></ol> | A seconda della causa principale:<ol><li>[Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assicurarsi che il cluster è attivo.</li><li>Eseguire il comando da controller attivo. Se si desidera eseguire il comando da controller passivo, sarà necessario assicurarsi che il controller passivo possa comunicare con controller attivo. Se la connettività viene interrotta, sarà necessario contattare [Il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) .</li></ol> |
| 3 | Richiamare HcsSetupWizard: Non è possibile chiamata RPC (eccezione da HRESULT: 0x800706be) | Cluster è verso il basso. | [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assicurarsi che il cluster è attivo.|
| 4 | Richiamare HcsSetupWizard: Risorsa non trovata Cluster (eccezione da HRESULT: 0x8007138f) | La risorsa non viene trovata. Questo problema può verificarsi durante l'installazione non è corretta. | Potrebbe essere necessario riavviare il dispositivo per le impostazioni predefinite. [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per creare una risorsa.|
| 5 | Richiamare HcsSetupWizard: Cluster risorsa non in linea (eccezione da HRESULT: 0x8007138c)| Risorse cluster non sono online. | [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per la procedura successiva.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Errori relativi all'amministratore del dispositivo e la password StorSimple Snapshot Manager

La password di amministratore del dispositivo predefinito è **Password1**. Questa password scade dopo il primo accesso; di conseguenza, sarà necessario usare la configurazione guidata per modificarlo. Quando si registra il dispositivo per la prima volta, è necessario specificare una nuova password di amministratore di dispositivo. 

Se si usa il software di StorSimple Snapshot Manager in esecuzione nell'host dei Server di Windows per gestire il dispositivo, è anche necessario fornire una password StorSimple Snapshot Manager durante la registrazione del primo. 

Assicurarsi che le password soddisfano i requisiti seguenti:

- Password di amministratore per il dispositivo deve essere compreso tra 8 e 15 caratteri.
- La password di gestione di Snapshot StorSimple dovrebbe essere 14 o 15 caratteri.
- Le password devono contenere 3 dei seguenti tipi di 4 carattere: lettere minuscole, maiuscole, numerici e speciali. 
- La password non può essere uguale ultime 24 password.

Inoltre, tenere presente che le password scadono di ogni anno e possono essere modificate solo dopo aver registrato correttamente il dispositivo. Se la registrazione avrà esito negativo per qualsiasi motivo, le password non essere modificate. Per ulteriori informazioni su dell'amministratore del dispositivo e la password StorSimple Snapshot Manager, passare a [usare il servizio di gestione di StorSimple per cambiare le password StorSimple](storsimple-change-passwords.md).

È possibile riscontrare uno o più degli errori seguenti quando si configura l'amministratore del dispositivo e la password StorSimple Snapshot Manager.

| No.| Messaggio di errore | Azione consigliata |
| ---| ------------- | ------------------ | 
| 1  | La password supera la lunghezza massima. |Utilizzare una password che soddisfi questi requisiti:<ul><li>Password di amministratore per il dispositivo deve essere compreso tra 8 e 15 caratteri.</li><li>La password di gestione di Snapshot StorSimple deve essere 14 o 15 caratteri.</li></ul> | 
| 2 | La password non soddisfa la lunghezza desiderata. | Utilizzare una password che soddisfi questi requisiti:<ul><li>Password di amministratore per il dispositivo deve essere compreso tra 8 e 15 caratteri.</li><li>La password di gestione di Snapshot StorSimple deve essere 14 o 15 caratteri.</lu></ul> |
| 3 | La password deve contenere caratteri minuscoli. | Le password devono contenere 3 dei seguenti tipi di 4 carattere: lettere minuscole, maiuscole, numerici e speciali. Assicurarsi che la password soddisfi questi requisiti. |
| 4 | La password deve contenere caratteri numerici. | Le password devono contenere 3 dei seguenti tipi di 4 carattere: lettere minuscole, maiuscole, numerici e speciali. Assicurarsi che la password soddisfi questi requisiti. |
| 5 | La password deve contenere caratteri speciali. | Le password devono contenere 3 dei seguenti tipi di 4 carattere: lettere minuscole, maiuscole, numerici e speciali. Assicurarsi che la password soddisfi questi requisiti. |
| 6 | La password deve contenere 3 dei seguenti tipi di 4 carattere: caratteri maiuscoli, minuscoli, numerici e speciali. | La password non contiene i tipi di caratteri richiesti. Assicurarsi che la password soddisfi questi requisiti. |
| 7 | Parametro non corrisponde a conferma. | Assicurarsi che la password soddisfi i requisiti tutti e che è stata immessa correttamente. |
| 8 | La password non corrispondenza a quello predefinito. | La password predefinita è *Password1*. È necessario modificare la password dopo l'accesso per la prima volta. |
| 9 | Password immessa non corrisponde la password per il dispositivo. Immettere la password. | Controllare la password e digitarla di nuovo. |

Le password sono stati raccolti prima che il dispositivo è stato registrato, ma vengono applicate solo dopo la registrazione ha esito positivo. Il flusso di lavoro di ripristino password richiede il dispositivo da registrare. 

> [AZURE.IMPORTANT] In generale, se non riesce un tentativo di applicare una password, quindi il software ripetutamente tenta di raccogliere la password fino a quando non viene eseguito correttamente. In alcuni casi il la password non possa essere applicata. In questo caso, è possibile registrare il dispositivo e continuare, tuttavia le password non verranno modificate. Non si riceverà alcuna indicazione su quali non è stata modificata la password, ovvero la password di amministratore del dispositivo oppure la password StorSimple Snapshot Manager. Se si verifica questa situazione, è consigliabile modificare entrambe le password.

È possibile reimpostare le password nel portale di classica Azure tramite il servizio di gestione StorSimple. Per ulteriori informazioni, vedere: 

- [Modificare la password di amministratore del dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Modificare la password StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Errori durante la registrazione del dispositivo

Utilizzare il servizio di gestione di StorSimple in esecuzione in Microsoft Azure per registrare il dispositivo. Si potrebbero essere visualizzati uno o più dei seguenti problemi durante la registrazione del dispositivo.

| No.| Messaggio di errore | Le possibili cause | Azione consigliata |
| ---| ------------- | --------------- | ------------------ |
| 1  | Errore 350027: Impossibile registrare il dispositivo con StorSimple Manager. |  | Attendere alcuni minuti e quindi ripetere l'operazione. Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Errore 350013: Verificato un errore nella registrazione del dispositivo. Ciò potrebbe essere dovuto chiave di registrazione del servizio non corretti. | | Registrare nuovamente il dispositivo con la chiave di registrazione servizio corretto. Per ulteriori informazioni, vedere [ottenere la chiave di registrazione del servizio.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Errore 350063: Autenticazione del servizio di gestione StorSimple passato ma la registrazione non riuscita. Ripetere l'operazione in un secondo momento. | Questo errore indica che l'autenticazione con ACS è già trascorsa ma chiamata registro effettuata il servizio non è riuscita. Può trattarsi di un risultato di un errore di rete sporadiche. | Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Errore 350049: Non raggiungere il servizio durante la registrazione. | Se la chiamata al servizio, alla ricezione di un'eccezione web. In alcuni casi, il problema potrebbe richiedere risolto con la ripetizione dell'operazione in un secondo momento. | Controllare l'indirizzo IP e nome DNS e quindi ripetere l'operazione. Se il problema persiste, [contattare il supporto Microsoft.](storsimple-contact-microsoft-support.md) | 
| 5 | Errore 350031: Il dispositivo è già stato registrato. | | Nessuna azione necessaria. |
| 6 | Errore 350016: Registrazione dispositivo non riuscita. | |Verificare che la chiave di registrazione sia corretta. |
| 7 | Richiamare HcsSetupWizard: Verificato un errore durante la registrazione del dispositivo; Questa operazione può essere dovuta indirizzo IP errato o il nome DNS. Controllare le impostazioni di rete e riprovare. Se il problema persiste, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md). (Errore 350050) | Assicurarsi che il dispositivo può effettuare il ping di rete esterna. Se non si dispone di connettività di rete esterna, la registrazione potrebbe non riuscire con questo errore. Questo errore può essere una combinazione di uno o più delle operazioni seguenti:<ul><li>Indirizzi IP non corretti</li><li>Subnet non corretti</li><li>Gateway non corretti</li><li>Impostazioni DNS non corrette</li></ul> | Vedere i passaggi indicati nella [procedura di esempio sulla risoluzione dei problemi](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Richiamare HcsSetupWizard: L'operazione corrente non è riuscita a causa di un errore interno del servizio [0x1FBE2]. Ripetere l'operazione dopo un intervallo di tempo. Se il problema persiste, contattare il supporto Microsoft. | Si tratta di un errore generico generato per tutti gli errori invisibili utente dal servizio o agente. La causa più comune potrebbe essere che l'autenticazione di ACS non è riuscita. Possibili cause dell'errore è che ci sono problemi con la configurazione del server NTP e ora del dispositivo non è stata impostata correttamente. | Correggere l'ora (se sono presenti problemi) e quindi ripetere l'operazione di registrazione. Se si utilizza il comando Set HcsSystem - fuso orario per regolare il fuso orario, tutte iniziali maiuscole nel fuso orario (ad esempio "Standard del Pacifico").  Se il problema persiste, [contatto del supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per passaggi successivi. |
| 9 | Avviso: Impossibile attivare il dispositivo. All'amministratore del dispositivo e la password StorSimple Snapshot Manager non è stata modificata. | Se la registrazione avrà esito negativo, l'amministratore del dispositivo e la password StorSimple Snapshot Manager non vengono modificata. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Strumenti per la risoluzione StorSimple distribuzioni

StorSimple include numerosi strumenti che è possibile utilizzare per la risoluzione soluzione StorSimple. Sono inclusi:

- Pacchetti di supporto e registri dispositivo 
- Cmdlet progettati specificamente per la risoluzione dei problemi 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Supporto pacchetti e registri dispositivo disponibili per la risoluzione dei problemi

Un pacchetto di supporto contiene tutti i log pertinenti che possono aiutare il team di supporto Microsoft sulla risoluzione dei problemi di dispositivi. È possibile utilizzare Windows PowerShell per StorSimple per generare un pacchetto di supporto crittografato che è quindi possibile condividere con agli addetti al supporto.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Per visualizzare i log o il contenuto del pacchetto di supporto

1. Utilizzare Windows PowerShell per StorSimple per generare un pacchetto di supporto, come descritto in [creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md).

2. Scaricare lo [script decrittografia](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) in locale nel computer client.

3. Utilizzare questa [procedura dettagliata](storsimple-create-manage-support-package.md#edit-a-support-package) per aprire e decrittografare il pacchetto di supporto.

4. I log del pacchetto di supporto decrittografare sono in formato etw/etvx. È possibile eseguire la procedura seguente per visualizzare questi file nel Visualizzatore eventi di Windows:
  1. Eseguire il comando **eventvwr** nel client di Windows. Verrà avviato il Visualizzatore eventi.
  2. Nel riquadro **Azioni** , fare clic su **Apri registro salvato** e scegliere i file di log in formato etvx/etw (il pacchetto di supporto). È ora possibile visualizzare il file. Dopo avere aperto il file, è possibile rapida e salvare il file come testo.
   
    > [AZURE.IMPORTANT] È anche possibile utilizzare il cmdlet **Get-WinEvent** aprire questi file in Windows PowerShell. Per ulteriori informazioni, vedere [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) nella documentazione di riferimento cmdlet di Windows PowerShell.

5. Quando i registri apre nel Visualizzatore eventi, cercare i registri seguenti che contengono i problemi relativi alla configurazione del dispositivo:

  - hcs_pfconfig/operativo Log
  - hcs_pfconfig/configurazione

6. Nei file di log, cercare stringhe correlate al cmdlet chiamate nella procedura guidata configurazione. Per un elenco di questi cmdlet, vedere [processo di configurazione guidata del primo](#first-time-setup-wizard-process) . 

7. Se non si riesce a individuare la causa del problema, è possibile [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per la procedura successiva. Utilizzare la procedura descritta in [creare una richiesta di assistenza](storsimple-contact-microsoft-support.md#create-a-support-request) quando si contattare il supporto Microsoft per assistenza.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet disponibili per la risoluzione dei problemi

Usare il seguente cmdlet di Windows PowerShell per trovare gli errori di integrazione applicativa.

- `Get-NetAdapter`: Utilizzare questo cmdlet per rilevare lo stato delle interfacce di rete. 

- `Test-Connection`: Utilizzare questo cmdlet per controllare la connettività di rete all'interno e all'esterno della rete.

- `Test-HcsmConnection`: Utilizzare questo cmdlet per verificare la connessione di un dispositivo correttamente registrato.

Se si esegue l'aggiornamento 1 nel dispositivo StorSimple, sono disponibili anche i cmdlet di diagnostici seguenti.

- `Sync-HcsTime`: Utilizzare questo cmdlet per visualizzare l'ora di dispositivo e forzare la sincronizzazione dell'ora con il server NTP.

- `Enable-HcsPing`e `Disable-HcsPing`: utilizzare i cmdlet per consentire agli effettuare il ping le interfacce di rete in un dispositivo StorSimple host. Per impostazione predefinita, le interfacce di rete StorSimple non risponde alle richieste di ping.

- `Trace-HcsRoute`: Utilizzare questo cmdlet come strumento di analisi route. Invia pacchetti a ogni router lungo il tragitto per una destinazione finale per un periodo di tempo e quindi calcola i risultati in base ai pacchetti restituiti da ogni passaggio. Poiché `Trace-HcsRoute` indica il grado di perdita di pacchetti in un dato router o il collegamento, è possibile individuare il router o collegamenti potrebbero causare problemi di rete. 

- `Get-HcsRoutingTable`: Utilizzare questo cmdlet per visualizzare la tabella di routing IP locale.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Risolvere i problemi con il cmdlet Get-NetAdapter

Quando si configurano le interfacce di rete per una distribuzione primo dispositivo, lo stato di hardware non è disponibile nel servizio di gestione StorSimple dell'interfaccia utente perché il dispositivo non è ancora registrato con il servizio. Inoltre, la pagina stato Hardware potrebbe non sempre rispecchia correttamente lo stato del dispositivo, soprattutto se sono presenti problemi che influiscono sulla sincronizzazione del servizio. In questi casi, è possibile utilizzare il `Get-NetAdapter` cmdlet per determinare l'integrità e stato delle interfacce di rete.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Per visualizzare un elenco di tutte le schede di rete nel dispositivo

1. Avviare Windows PowerShell per StorSimple e quindi digitare `Get-NetAdapter`. 

2. Utilizzare l'output del `Get-NetAdapter` cmdlet e le seguenti linee guida per conoscere lo stato dell'interfaccia della rete.
  - Se l'interfaccia corretti e abilitato, verrà visualizzato lo stato di **IndiceIf** come **backup**.
  - Se l'interfaccia sia integro ma non è connessa (tramite un cavo di rete), **IndiceIf** viene visualizzato come **disabilitato**.
  - Se l'interfaccia integro ma non abilitato, viene visualizzato lo stato di **IndiceIf** come **NotPresent**.
  - Se l'interfaccia non esiste, non viene visualizzato nell'elenco. Il servizio di gestione StorSimple dell'interfaccia utente verrà comunque visualizzato questa interfaccia in stato di errore.

Per ulteriori informazioni su come utilizzare questo cmdlet, passare a [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) nel riferimento sui cmdlet di Windows PowerShell. 

Nelle sezioni seguenti vengono visualizzare esempi di output di `Get-NetAdapter` cmdlet. 

 In questi esempi, controller 0 era controller passivo ed è stata configurata come indicato di seguito:

- DATI 0, 1 dati, dati 2 e 3 dati rete interfacce esistevano nel dispositivo.
- DATI 4 e 5 dati dell'interfaccia di rete non sono presenti; di conseguenza, non sono elencate nell'output.
- DATI 0 è stati attivati.

Controller 1 era controller attivo ed è stata configurata come indicato di seguito:

- DATI 0, 1 dati, dati 2, 3 di dati, dati 4 e 5 dati rete interfacce esistevano nel dispositivo.
- DATI 0 è stati attivati.

**Esempio output – controller 0**

Di seguito viene emesso dal controller 0 (controller passivo). DATI 1, dati 2 e 3 di dati non si è connessi. DATI 4 e 5 di dati non sono elencate perché non sono presenti nel dispositivo. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Esempio output – controller 1**

Di seguito viene emesso dal controller di 1 (controller attivo). Solo i dati 0 è configurato l'interfaccia di rete sul dispositivo e lavoro.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Risolvere i problemi con i cmdlet di connessione di prova

È possibile utilizzare il `Test-Connection` cmdlet per determinare se il dispositivo StorSimple può connettersi alla rete esterna. Se tutti i parametri di rete, inclusi il DNS siano configurati correttamente della configurazione guidata, è possibile utilizzare il `Test-Connection` cmdlet per effettuare il ping indirizzo noto all'esterno della rete, ad esempio outlook.com. 

È consigliabile attivare ping per la risoluzione dei problemi di connettività con questo cmdlet disattivando ping.

Vedere i seguenti esempi l'output di `Test-Connection` cmdlet. 

> [AZURE.NOTE] Nel primo esempio, il dispositivo è configurato con un DNS non corretto. Nel secondo esempio, il DNS è corretto.
 
**Esempio output – DNS non corretti**

Nell'esempio riportato di seguito non esiste alcun output per gli indirizzi IPV4 e IPV6, che indica che il DNS non è stato risolto. Ciò significa che non è presente alcuna connettività di rete esterna e DNS corretto deve essere fornito. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Esempio di output: correggere DNS**

Nell'esempio seguente il DNS restituisce indirizzi IPV4 che indica che il DNS è configurato correttamente. Ciò significa che c'è connettività di rete esterna. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Risolvere i problemi con il cmdlet Test HcsmConnection

Utilizzare la `Test-HcsmConnection` cmdlet per un dispositivo che è già connessi a e registrato con il servizio di gestione StorSimple. Questo cmdlet consente di verificare la connessione tra un dispositivo registrato e il servizio di gestione StorSimple corrispondente. È possibile eseguire il comando su Windows PowerShell per StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Per eseguire il cmdlet Test HcsmConnection

1. Assicurarsi che sia registrato il dispositivo.

2. Controllare lo stato dispositivo. Se il dispositivo è disattivato, in modalità di manutenzione o non in linea, potrebbe essere visualizzato errori seguenti: 

   - ErrorCode.CiSDeviceDecommissioned: indica che il dispositivo è disattivato.
   - ErrorCode.DeviceNotReady: indica che il dispositivo è in modalità di manutenzione.
   - ErrorCode.DeviceNotReady: indica che il dispositivo non è in linea.

3. Verificare che il servizio di gestione StorSimple sia in esecuzione (utilizzare il cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Se il servizio non è in esecuzione, potrebbe essere visualizzato errori seguenti:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError: indica che si è verificato un'eccezione quando si è eseguito Get-ClusterResource.

4. Controllare il token di servizio controllo di accesso (ACS). Se viene generata un'eccezione web, è possibile il risultato di un problema di gateway, un mancanti autenticazione del proxy, un DNS non corretto o un errore di autenticazione. Potrebbe essere visualizzato errori seguenti:

   - ErrorCode.CiSApplianceGateway – indica un'eccezione HttpStatusCode.BadGateway: il servizio di sistema di risoluzione dei nomi non consente di risolvere il nome host. 
   - ErrorCode.CiSApplianceProxy – indica un'eccezione HttpStatusCode.ProxyAuthenticationRequired (codice di stato HTTP 407): il client non può eseguire l'autenticazione con il server proxy. 
   - ErrorCode.CiSApplianceDNSError – indica un'eccezione WebExceptionStatus.NameResolutionFailure: il servizio di sistema di risoluzione dei nomi non consente di risolvere il nome host.
   - ErrorCode.CiSApplianceACSError: indica che il servizio ha restituito un errore di autenticazione, ma è disponibile la connettività.
   
    Se non viene generato un'eccezione web, cercare ErrorCode.CiSApplianceFailure. Indica che il dispositivo non è riuscita.

5. Verificare la connessione di servizio cloud. Se il servizio genera un'eccezione web, potrebbe essere visualizzato errori seguenti:

  - ErrorCode.CiSApplianceGateway – indica un'eccezione HttpStatusCode.BadGateway: un server proxy intermedio ricevuto una richiesta non valida da un altro proxy o dal server originale.
  - ErrorCode.CiSApplianceProxy – indica un'eccezione HttpStatusCode.ProxyAuthenticationRequired (codice di stato HTTP 407): il client non può eseguire l'autenticazione con il server proxy. 
  - ErrorCode.CiSApplianceDNSError – indica un'eccezione WebExceptionStatus.NameResolutionFailure: il servizio di sistema di risoluzione dei nomi non consente di risolvere il nome host.
  - ErrorCode.CiSApplianceACSError: indica che il servizio ha restituito un errore di autenticazione, ma è disponibile la connettività.
  
    Se non viene generato un'eccezione web, cercare ErrorCode.CiSApplianceSaasServiceError. Questo indica un problema con il servizio di gestione StorSimple.
 
6. Controllare la connettività Bus di servizio Azure. ErrorCode.CiSApplianceServiceBusError indica che il dispositivo non riesce a connettersi al Bus di servizio.
 
File di log CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog avrà ulteriori informazioni, ad esempio dettagli sulle eccezioni. 

Per ulteriori informazioni su come utilizzare il cmdlet, passare a [HcsmConnection Test](https://technet.microsoft.com/library/dn715782.aspx) in Windows PowerShell fare riferimento alla documentazione.

> [AZURE.IMPORTANT] È possibile eseguire questo cmdlet per attivo e controller passivo. 
 
Vedere i seguenti esempi l'output di `Test-HcsmConnection` cmdlet. 

**Esempio di output: dispositivo correttamente registrato esegue una versione StorSimple (luglio 2014)**

Il primo campione è stata inviata da un dispositivo che correttamente registrato con il servizio di gestione StorSimple e non è presenti problemi di connettività. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Esempio di output: dispositivo correttamente registrato che esegue StorSimple aggiornamento 1**

Se si esegue l'aggiornamento 1 nel dispositivo StorSimple, non è necessario eseguire con l'opzione dettagliato.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Esempio di output: dispositivo non in linea esegue una versione StorSimple (luglio 2014)**

In questo esempio è stata inviata da un dispositivo che lo stato **Offline** nel portale di classica Azure.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Il dispositivo non è riuscito a connettersi tramite la configurazione del proxy web corrente. Può trattarsi di un problema con la configurazione del proxy web o un problema di connettività di rete. In questo caso, assicurarsi che le impostazioni del proxy web siano corrette e i server di proxy web online e raggiungibile. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Risolvere i problemi con i cmdlet di sincronizzazione HcsTime

Questo cmdlet consente di visualizzare l'ora del dispositivo. Se il tempo di dispositivo ha un offset con il server NTP, quindi utilizzare questo cmdlet per la sincronizzazione per forzare il tempo con il server NTP. Se la distanza tra il dispositivo e server NTP è maggiore di 5 minuti, verrà visualizzato un avviso. Se l'offset supera 15 minuti, quindi passa il dispositivo non in linea. È comunque possibile usare questo cmdlet per forzare la sincronizzazione dell'ora. Tuttavia, se l'offset supera 15 ore, quindi non sarà possibile in vigore-sincronizzazione che verrà visualizzati un messaggio di errore e l'ora.

**Esempio di output: sincronizzazione dell'ora imposto tramite sincronizzazione HcsTime**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Risolvere i problemi con i cmdlet Enable-HcsPing e disabilita HcsPing

Utilizzare i cmdlet per garantire che le interfacce di rete in un dispositivo rispondono alle richieste di ping ICMP. Per impostazione predefinita le interfacce di rete StorSimple non risponde alle richieste di ping. Questo cmdlet è il modo migliore per sapere se il dispositivo è online e raggiungibile.  

**Esempio di output: HcsPing abilitare e disabilitare HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Risolvere i problemi con il cmdlet traccia HcsRoute

Utilizzare questo cmdlet come strumento di analisi route. Invia pacchetti a ogni router lungo il tragitto per una destinazione finale per un periodo di tempo e quindi calcola i risultati in base ai pacchetti restituiti da ogni passaggio. Poiché il cmdlet seguente il grado di perdita di pacchetti per ciascun router o collegamento, è possibile individuare il router o collegamenti potrebbero causare problemi di rete.

**Output di esempio che mostra come tracciare la route di un pacchetto con traccia HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Risolvere i problemi con il cmdlet Get-HcsRoutingTable

Questo cmdlet consente di visualizzare la tabella di routing del dispositivo StorSimple. Una tabella di routing è un set di regole che consentono di determinare dove verranno indirizzati pacchetti di dati in viaggio su una rete IP (Internet Protocol). 

La tabella routing Mostra le interfacce e gateway che indirizza i dati per le reti specificate. Inoltre, offre la metrica di routing che i dirigenti per il percorso per raggiungere la destinazione particolare. Il valore più basso la metrica di routing, maggiore sarà la preferenza. 

Ad esempio, se si dispongono di 2 interfacce di rete, dati 2 e 3 dati connessi a Internet. Se le metriche di routing per dati 2 e 3 dati sono 15 e 261, 2 di dati con la metrica di routing inferiore è l'interfaccia preferita usato per accedere a Internet.

Se si esegue l'aggiornamento 1 nel dispositivo StorSimple, l'interfaccia di rete 0 dati include la preferenza più elevata per il traffico cloud. Questo significa che, anche se sono presenti altre interfacce basate su cloud, il traffico cloud sarà indirizzato tramite dati 0. 

Se si esegue il `Get-HcsRoutingTable` cmdlet senza specificare tutti i parametri (come nell'esempio seguente), il cmdlet fornirà le tabelle di routing IPv4 e IPv6. In alternativa, è possibile specificare `Get-HcsRoutingTable -IPv4` o `Get-HcsRoutingTable -IPv6` per ottenere una tabella di routing pertinenti.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Esempio di risoluzione dei problemi StorSimple dettagliato

Nell'esempio seguente viene dettagliate risoluzione dei problemi di una distribuzione StorSimple. Nello scenario di esempio dispositivo registrazione avrà esito negativo con un messaggio di errore che indica che le impostazioni di rete o il nome DNS non è corretto.

Il messaggio di errore restituito è:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

L'errore può essere dovuto a una delle operazioni seguenti:

- Installazione dell'hardware non corretti
- Interfacce di rete difettoso
- Indirizzo IP errato, maschera subnet, gateway, server DNS primario o proxy web
- Chiave di registrazione non corretti
- Impostazioni del firewall non corretti

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Per individuare e risolvere il problema di registrazione del dispositivo

1. Controllare la configurazione del dispositivo: sul controller attivi eseguire `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] La configurazione guidata è necessario eseguire sul controller attivo. Per verificare che si è connessi a controller attivo, esaminare l'intestazione visualizzata nella console di sequenza. L'intestazione indica se si è connessi a controller di 0 o 1, e se il controller è attivo o passivo. Per ulteriori informazioni, passare a [identificare un controller active nel dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Assicurarsi che il dispositivo sia collegato correttamente: controllare i cavi sul dispositivo nuovo piano di rete. Il cavo è specifico per il modello. Per ulteriori informazioni, passare a [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Se si utilizza 10 porte di rete GbE, sarà necessario utilizzare le schede di funzione QSFP fornite e i cavi di funzione. Per ulteriori informazioni, vedere l' [elenco di cavi, opzioni e ricetrasmettitori consigliati dal fornitore OEM per le porte Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Verificare lo stato dell'interfaccia di rete:

   - Utilizzare il cmdlet Get-NetAdapter per rilevare lo stato delle interfacce di rete per i dati 0. 
   - Se il collegamento non funziona, lo stato di **IndiceIf** indica che l'interfaccia sia verso il basso. È quindi necessario verificare la connessione di rete della porta per il dispositivo e il parametro. Sarà anche necessario escludere cavi errati. 
   - Se si sospetta che i dati 0 porta sul controller attivo non è riuscita, è possibile verificarlo mediante la connessione ai dati porta 0 sul controller 1. Per verificarlo, scollegare il cavo di rete dal retro del dispositivo da controller di 0, collegare il cavo al controller 1 e quindi eseguire di nuovo il cmdlet Get-NetAdapter. 
   Se i dati 0 porta per un controller non riesce, [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per passaggi successivi. Potrebbe essere necessario sostituire il controller nel sistema.
 
4. Verificare la connessione all'opzione:
   - Assicurarsi che le interfacce di rete 0 dati sul controller 0 e 1 nel simbolo principale siano nella stessa subnet. 
   - Controllare l'hub o router. In genere, è consigliabile connettersi entrambi i controller allo stesso hub o router. 
   - Assicurarsi che le opzioni che utilizzare per la connessione che i dati siano 0 per entrambi i controller nella stessa vLAN.
   
5. Eliminare eventuali errori dell'utente:

  - Eseguire l'installazione guidata nuovo (Esegui **Richiama HcsSetupWizard**) e immettere i valori per assicurarsi che non siano presenti errori. 
  - Controllare la registrazione chiave utilizzata. Per collegare più dispositivi in un servizio di gestione StorSimple, è possibile utilizzare la stessa chiave di registrazione. Utilizzare la procedura di [ottenere la chiave di registrazione del servizio](storsimple-manage-service.md#get-the-service-registration-key) per garantire che si sta utilizzando la chiave di registrazione corretto.

    > [AZURE.IMPORTANT] Se si dispone di più servizi in esecuzione, è necessario assicurarsi che la chiave di registrazione per il servizio appropriato venga utilizzata per registrare il dispositivo. Se è stato registrato un dispositivo con il servizio di gestione StorSimple errato, sarà necessario [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per la procedura successiva. È necessario eseguire un ripristino produttore del dispositivo (questo può comportare la perdita di dati) a quindi connettersi al servizio desiderato.

6. Utilizzare il cmdlet Test connessione per verificare di essere connessi alla rete esterna. Per ulteriori informazioni, vedere [risoluzione dei problemi con il cmdlet Test connessione](#troubleshoot-with-the-test-connection-cmdlet).

7. Cercare un'interferenza firewall. Se dopo aver verificato che le impostazioni di indirizzi IP, subnet, gateway e DNS virtuale sono tutte corrette, risulta ancora problemi di connettività, quindi è possibile che il firewall blocca le comunicazioni tra il dispositivo e di rete diversa. È necessario assicurarsi che le porte 80 e 443 siano disponibili in un dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [requisiti per il dispositivo di StorSimple di rete](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Esaminare i log. Passare a [pacchetti di supporto e registri dispositivo disponibili per la risoluzione dei problemi](#support-packages-and-device-logs-available-for-troubleshooting).

9. Se i passaggi precedenti non risolvono il problema, [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per assistenza.

## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come risolvere i problemi di dispositivi operativi](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
