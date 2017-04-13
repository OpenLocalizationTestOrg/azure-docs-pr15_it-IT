<properties 
   pageTitle="Dispositivo virtuale StorSimple aggiornamento 2 | Microsoft Azure"
   description="Informazioni su come creare, distribuire e gestire un dispositivo virtuale StorSimple in una rete virtuale di Microsoft Azure. (Si applica a StorSimple Update 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Distribuire e gestire un dispositivo virtuale StorSimple in Azure


##<a name="overview"></a>Panoramica
Il dispositivo virtuale serie 8000 StorSimple è una funzionalità aggiuntiva fornito con la soluzione Microsoft Azure StorSimple. Il dispositivo virtuale StorSimple viene eseguito su una macchina virtuale in una rete virtuale di Microsoft Azure ed è possibile utilizzare per eseguire il backup e duplicare i dati dall'host. In questa esercitazione viene descritto come distribuire e gestire un dispositivo virtuale in Azure ed è applicabile a tutti i dispositivi virtuali in esecuzione la versione del software aggiornamento 2 e inferiore.


#### <a name="virtual-device-model-comparison"></a>Confronto tra i modelli dispositivo virtuale

Il dispositivo virtuale StorSimple è disponibile in due modelli, un 8010 standard (in precedenza noto come 1100) e un premio 8020 (introdotta nell'aggiornamento 2). Un confronto tra i due modelli è inserito sotto.


| Modello di dispositivo          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacità massima**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Macchine Virtuali di Azure**              | Standard_A3 (4 core; 7 GB di memoria)                                                                      | Standard_DS3 (4 core; 14 GB di memoria)                                                                                                                          |
| **Compatibilità delle versioni** | Versioni in esecuzione pre-aggiornamento 2 o versioni successive                                             | Versioni aggiornamento 2 o versione successiva                                                                                                  |
| **Disponibilità per area**   | Tutte le aree di Azure                                                         | Azure aree geografiche che supportano l'archiviazione Premium<br></br>Per un elenco delle aree, vedere [supportati aree per 8020](#supported-regions-for-8020) |
| **Tipo di archiviazione**          | Viene utilizzata l'archiviazione Standard Azure per dischi locali<br></br> Informazioni su come [creare un account di archiviazione Standard]() | Utilizza lo spazio di archiviazione di Azure Premium per dischi locali<sup>2</sup> <br></br>Informazioni su come [creare un account di archiviazione Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Guida di carico di lavoro**     | Elemento livello recupero dei file da un backup                                              | Cloud dev e testare scenari, bassa latenza, superiore carichi di lavoro di prestazioni <br></br>Dispositivo secondario per il ripristino di emergenza                                                                                            |
 
<sup>1</sup> *precedentemente noto come 1100*.

<sup>2</sup> *il 8010 e 8020 utilizzare Azure Standard dello spazio di archiviazione per il livello di cloud. La differenza esiste solo nel livello locale nell'ambito del dispositivo*.

#### <a name="supported-regions-for-8020"></a>Aree supportate per 8020

Le aree di spazio di archiviazione Premium sono attualmente supportate per 8020 vengano inserite sotto. In questo elenco verrà aggiornato continuamente Premium archiviazione diventa disponibile in altre aree geografiche. 

| S. No.                                                  | Attualmente supportati in aree geografiche |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | Centrale USA                     |
| 2                                                       |  Stati Uniti orientali                       |
| 3                                                       |  Stati Uniti orientali 2                     |
| 4                                                       | Usa ovest                        |
| 5                                                       | Europa Nord America                   |
| 6                                                       | Europa occidentale                    |
| 7                                                       | Area Asia sudorientale                 |
| 8                                                       | Giappone est                     |
| 9                                                       | Giappone ovest                     |
| 10                                                      | Australia orientale                 |
| 11                                                      | Australia Sudest *           |
| 12                                                      | Asia orientale *                     |
| 13                                                      | Sud centrale degli Stati Uniti *              |

* Spazio di archiviazione premium è stata avviata recente in queste geos.

In questo articolo vengono illustrati al processo dettagliato riportato di distribuzione di un dispositivo virtuale StorSimple in Azure. Dopo aver letto in questo articolo, sarà necessario:

- Comprendere le differenze tra il dispositivo virtuale dal dispositivo fisico.

- Sarà possibile creare e configurare il dispositivo virtuale.

- Connettere il dispositivo virtuale.

- Informazioni su come lavorare con il dispositivo virtuale.

In questa esercitazione si applica a tutte le StorSimple virtuali dispositivi che eseguono aggiornamento 2 e versioni successive. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Come dispositivo virtuale è diverso dal dispositivo fisico

Il dispositivo virtuale StorSimple è una versione di StorSimple che viene eseguita su un singolo nodo in un computer virtuali di Microsoft Azure solo software. Il dispositivo virtuale supporta emergenza in cui non è disponibile il dispositivo fisico e appropriato per l'utilizzo di recupero a livello di elemento da backup, ripristino di emergenza locale e scenari di sviluppo e test basati su cloud.

#### <a name="differences-from-the-physical-device"></a>Differenze dal dispositivo fisico

Nella tabella seguente illustra le differenze tra il dispositivo virtuale StorSimple e il dispositivo fisico StorSimple.

|                             | Dispositivo fisico                                          | Dispositivo virtuale                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Posizione**                    | Si trova nel centro dati.                               | Viene eseguito in Azure.                                                                            |
| **Interfacce di rete**          | Sei interfacce di rete sono previsti: dati 0 a 5 di dati.                  | È solo un'interfaccia di rete: dati 0.                                        |
| **Registrazione**                | Registrati durante il passaggio di configurazione.                | La registrazione è un'attività separata.                                                          |
| **Chiave di crittografia di servizio dati** | Rigenerare il dispositivo fisico e quindi aggiornare il dispositivo virtuale con la nuova chiave.           | Non è possibile rigenerare dal dispositivo virtuale. |


## <a name="prerequisites-for-the-virtual-device"></a>Prerequisiti per il dispositivo virtuale

Nelle sezioni seguenti illustrano i prerequisiti di configurazione del dispositivo virtuale StorSimple. Prima di implementare un dispositivo virtuale, esaminare le [Considerazioni sulla protezione per l'uso di un dispositivo virtuale](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Requisiti di Azure

Il provisioning virtuale del dispositivo, è necessario effettuare le seguenti operazioni preparatorie nel proprio ambiente Azure:

- Per il dispositivo virtuale [configurare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Se utilizzano lo spazio di archiviazione Premium, è necessario creare una rete virtuale in un'area di Azure che supporta l'archiviazione Premium. Ulteriori informazioni sulle [aree geografiche sono attualmente supportate per 8020](#supported-regions-for-8020).
- È consigliabile utilizzare il server DNS predefinito fornito da Azure anziché specificando il proprio nome di server DNS. Se il nome del server DNS non è valido o se il server DNS non è in grado di risolvere gli indirizzi IP correttamente, la creazione del dispositivo virtuale avrà esito negativo.
- Punto al sito e da sito sono facoltativo, ma non necessari. Se si desidera, è possibile configurare queste opzioni per scenari più avanzati. 
- È possibile creare [macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-about.md) (server host) nella rete virtuale utilizzate volumi esposti dal dispositivo virtuale. Questi server devono soddisfare i requisiti seguenti:                            
    - Essere Windows o Linux macchine virtuali con iSCSI software Initiator installato.
    - Eseguire la stessa rete virtuale dispositivo virtuale.
    - In grado di connettersi alla destinazione iSCSI del dispositivo virtuale tramite l'indirizzo IP interno del dispositivo virtuale.

- Assicurarsi di che aver configurato il supporto per il traffico iSCSI e cloud nella stessa rete virtuale.


#### <a name="storsimple-requirements"></a>Requisiti di StorSimple

Effettuare gli aggiornamenti seguenti al servizio di Azure StorSimple prima di creare un dispositivo virtuale:


- Aggiungere [il record di controllo di accesso](storsimple-manage-acrs.md) per macchine virtuali che sta per essere server host del dispositivo virtuale.

- Utilizzare un [account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account) nella stessa regione come dispositivo virtuale. Gli account di archiviazione in diverse aree geografiche possono determinare un rallentamento delle prestazioni. È possibile usare un account Standard o lo spazio di archiviazione Premium con il dispositivo virtuale. Ulteriori informazioni su come creare un [account di archiviazione Standard]() o un [account di archiviazione Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Usare un account di archiviazione diversi per la creazione di dispositivo virtuale da quello utilizzato per i dati. Con lo stesso account di archiviazione può determinare un rallentamento delle prestazioni.

Verificare di avere le informazioni seguenti prima di iniziare:

- Account del portale classica Azure con credenziali di accesso.

- Una copia della chiave di crittografia servizio dati dal dispositivo fisico.


## <a name="create-and-configure-the-virtual-device"></a>Creare e configurare il dispositivo virtuale

Prima di eseguire queste procedure, assicurarsi che siano soddisfatti i [Prerequisiti per il dispositivo virtuale](#prerequisites-for-the-virtual-device). 

Dopo aver creato una rete virtuale, stato configurato un servizio di gestione StorSimple e registrare il dispositivo StorSimple fisico con il servizio, è possibile utilizzare la procedura seguente per creare e configurare un dispositivo virtuale StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Passaggio 1: Creare un dispositivo virtuale

Per creare il dispositivo virtuale StorSimple, procedere come segue.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Se la creazione del dispositivo virtuale non riesce in questo passaggio, potrebbe non essere connettività a Internet. Per altre informazioni, vedere Risoluzione dei [problemi di connettività Internet](#troubleshoot-internet-connectivity-errors) quando creatig un dispositivo virtuale.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Passaggio 2: Configurare e registrare il dispositivo virtuale

Prima di iniziare questa procedura, accertarsi di avere una copia della chiave di crittografia servizio dati. La chiave di crittografia dati del servizio è stata creata quando è stato configurato il primo dispositivo StorSimple ed è stato richiesto di salvare in un percorso sicuro. Se non si dispone di una copia della chiave di crittografia servizio dati, è necessario contattare il supporto Microsoft per assistenza.

Eseguire i passaggi seguenti per configurare e registrare il dispositivo virtuale StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passaggio 3: (Facoltativo) modifica le impostazioni di configurazione del dispositivo

La sezione seguente descrive le impostazioni di configurazione del dispositivo necessario per la StorSimple dispositivo virtuale se si desidera utilizzare CHAP, StorSimple Snapshot Manager o cambiare la password dell'amministratore del dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurare l'iniziatore CHAP

Questo parametro contiene le credenziali che il dispositivo virtuale (destinazione) prevede da iniziatori (server) che siano tentando di accedere i volumi. Gli iniziatori fornisce un nome utente CHAP e una password CHAP per identificarsi al dispositivo durante l'autenticazione. Per istruzioni dettagliate, vedere [Configurare CHAP del dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurare la destinazione CHAP

Questo parametro contiene le credenziali che il dispositivo virtuale utilizza quando un iniziatore abilitato CHAP richiede l'autenticazione reciproca o bidirezionale. Il dispositivo virtuale utilizzerà invertire CHAP nome utente e password invertire CHAP identificarsi al promotore durante il processo di autenticazione. Si noti che le impostazioni di destinazione CHAP impostazioni globali. Quando si sono applicati, tutti i volumi collegati al dispositivo virtuale di spazio di archiviazione utilizza l'autenticazione CHAP. Per istruzioni dettagliate, vedere [Configurare CHAP del dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurare la password StorSimple Snapshot Manager

Software StorSimple Snapshot Manager risiede l'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di locali e cloud istantanee.

>[AZURE.NOTE] Per il dispositivo virtuale, l'host di Windows è una macchina virtuale Azure.

Quando si configura un dispositivo in Gestione Snapshot StorSimple, verrà richiesto di specificare l'indirizzo IP del dispositivo StorSimple e la password per l'autenticazione il dispositivo di archiviazione. Per informazioni dettagliate, passare alla [configurazione StorSimple Snapshot Gestione password](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Cambiare la password di amministratore del dispositivo 

Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale, sarà necessario immettere una password di amministratore del dispositivo. Per la protezione dei dati, verrà chiesto di modificare la password prima di utilizzare il dispositivo virtuale. Per informazioni dettagliate, passare alla [password di amministratore configura dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>La connessione remota al dispositivo virtuale
Accesso remoto al dispositivo virtuale tramite l'interfaccia di Windows PowerShell non è abilitata per impostazione predefinita. È necessario abilitare la gestione remota sul dispositivo virtuale prima di tutto e quindi abilitarla sul client che verrà utilizzato per accedere a un dispositivo virtuale.

Di seguito è riportata la procedura in due passaggi per la connessione remota.

### <a name="step-1-configure-remote-management"></a>Passaggio 1: Configurare la gestione remota

Eseguire i passaggi seguenti per configurare la gestione remota del dispositivo virtuale StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Passaggio 2: Accesso remoto il dispositivo virtuale

Dopo aver attivato Gestione remota nella pagina Configurazione dispositivo StorSimple, è possibile utilizzare Windows PowerShell remota per connettersi al dispositivo virtuale da un altro computer virtuale all'interno della stessa rete virtuale; ad esempio, è possibile connettersi dall'host macchine Virtuali che è configurato e utilizzato per la connessione iSCSI. Nella maggior parte delle distribuzioni, si verrà è già aperto un endpoint pubblico per accedere all'host macchina virtuale che è possibile usare per accedere al dispositivo virtuale.

>[AZURE.WARNING] **Per una maggiore protezione, è consigliabile utilizzare HTTPS quando ci si connette ai punti finali e quindi eliminare i punti finali dopo aver completato la sessione remota di PowerShell.**

È necessario seguire le procedure descritte nella [connessione remota al dispositivo StorSimple](storsimple-remote-connect.md) per configurare i servizi remoti del dispositivo virtuale.

## <a name="connect-directly-to-the-virtual-device"></a>Connettersi direttamente al dispositivo virtuale

È anche possibile connettersi direttamente al dispositivo virtuale. Se si desidera connettersi direttamente al dispositivo virtuale da un altro computer all'esterno della rete virtuale o all'esterno dell'ambiente di Microsoft Azure, è necessario creare endpoint aggiuntivi, come descritto nella procedura seguente. 

Eseguire la procedura seguente per creare un endpoint pubblico su dispositivo virtuale.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

È consigliabile connettersi da un'altra macchina virtuale all'interno della stessa rete virtuale poiché questa esercitazione viene ridotto a icona il numero di endpoint pubblico sulla rete virtuale. Quando si usa questo metodo, è sufficiente connettersi tramite una sessione Desktop remoto al computer virtuale e configurare macchina virtuale per l'utilizzo come si farebbe con qualsiasi altro client di Windows in una rete locale. Non è necessario aggiungere il numero di porta pubblico perché la porta è già note.

## <a name="work-with-the-storsimple-virtual-device"></a>Lavorare con il dispositivo virtuale StorSimple

Ora che è stato creato e configurato il dispositivo virtuale StorSimple, si è pronti iniziare a utilizzarlo. Utilizzare volume contenitori, volumi e criteri di backup in un dispositivo virtuale esattamente come si farebbe in un dispositivo StorSimple fisico; l'unica differenza è che è necessario assicurarsi di selezionare il dispositivo virtuale dall'elenco di dispositivi. Fare riferimento a [usare il servizio di gestione di StorSimple per la gestione di un dispositivo virtuale](storsimple-manager-service-administration.md) per le procedure dettagliate delle varie attività di gestione per il dispositivo virtuale.

Nelle sezioni seguenti vengono descritte alcune delle differenze che si verificheranno quando si lavora con il dispositivo virtuale.

### <a name="maintain-a-storsimple-virtual-device"></a>Mantenere un dispositivo virtuale StorSimple

Poiché si tratta di un dispositivo solo software, manutenzione per il dispositivo virtuale è minima rispetto alla manutenzione di dispositivo fisico. Sono disponibili le opzioni seguenti:

- **Gli aggiornamenti software** – è possibile visualizzare la data in cui il software è stato aggiornato, insieme a qualsiasi aggiornare messaggi di stato. È possibile utilizzare il pulsante **analisi aggiornamenti** nella parte inferiore della pagina per eseguire un'analisi manuale se si desidera verificare la presenza di nuovi aggiornamenti. Se sono disponibili aggiornamenti, fare clic su **Installa aggiornamenti** da installare. Poiché c'è un'unica interfaccia del dispositivo virtuale, significa che vi sono un'interruzione del servizio lieve quando sono applicati gli aggiornamenti. Il dispositivo virtuale verrà arrestare e riavviare, se necessario, per applicare gli aggiornamenti che sono stati pubblicati. Per una procedura dettagliata, vedere [aggiornare il dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Pacchetto di supporto** , è possibile creare e caricare un pacchetto di supporto per risolvere i problemi con il dispositivo virtuale supporto Microsoft. Per una procedura dettagliata, vedere [creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Account di archiviazione per un dispositivo virtuale

Gli account di archiviazione vengono creati da usare per il servizio di gestione StorSimple, dal dispositivo virtuale e dal dispositivo fisico. Quando si crea l'account di archiviazione, è consigliabile utilizzare un identificatore di area geografica nella casella nome descrittivo per garantire che l'area geografica sia coerenza in tutti i componenti di sistema. Per un dispositivo virtuale, è importante che tutti i componenti sia nella stessa regione per evitare problemi di prestazioni.

Per una procedura dettagliata, vedere [aggiungere un account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Disattivare un dispositivo virtuale StorSimple

La disattivazione di un dispositivo virtuale elimina la macchina virtuale e le risorse create quando è stato eseguito il provisioning. Dopo che è stato disattivato il dispositivo virtuale, non possono essere ripristinato allo stato precedente. Prima di disattivare il dispositivo virtuale, assicurarsi di interrompere o eliminare i client e host che dipendono da essa.

La disattivazione di un dispositivo virtuale determina le azioni seguenti:

- Il dispositivo virtuale viene rimosso.

- Il disco rigido del sistema operativo e dischi di dati creati per il dispositivo virtuale vengono rimossi.

- Il servizio di hosting e virtuali creati durante il provisioning vengono mantenuti. Se non si usa loro, è necessario eliminarli manualmente.

- Istantanee cloud create per il dispositivo virtuale vengono mantenute.

Per una procedura dettagliata, passare a [Disattiva ed eliminare il dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Non appena il dispositivo virtuale viene visualizzato come disattivato nella pagina Gestione StorSimple il servizio, è possibile eliminare il dispositivo virtuale dall'elenco dei dispositivi nella pagina **dispositivi** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Iniziare, chiudere e riavviare un dispositivo virtuale
Diversamente da quanto succede periferica fisica StorSimple, non è alcun power attivato o spegnere pulsante da inserire in un dispositivo virtuale StorSimple. Tuttavia, potrebbero esserci occasioni in cui è necessario arrestare e riavviare il dispositivo virtuale. Ad esempio, alcune potrebbero richiedere riavviare la macchina virtuale per completare il processo di aggiornamento. Il modo più semplice per poter avviare, arrestare e riavviare un dispositivo virtuale consiste nell'utilizzare macchine virtuali di Management Console.

Quando osserva la Console di gestione, lo stato dispositivo virtuale è **in esecuzione** in quanto viene avviato per impostazione predefinita dopo averlo creato. È possibile avviare, arrestare e riavviare una macchina virtuale in qualsiasi momento.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Ripristina impostazioni predefinite

Se si decide che si vuole semplicemente ricominciare da capo con il dispositivo virtuale, è sufficiente disattivare eliminarla e quindi crearne uno nuovo. Proprio come quando si reimposta il dispositivo fisico, nuovi dispositivi virtuali non avranno tutti gli aggiornamenti installati; di conseguenza, assicurarsi di verificare la presenza di aggiornamenti prima di usarlo.


## <a name="fail-over-to-the-virtual-device"></a>Non riuscire sul dispositivo virtuale

Ripristino di emergenza (DR) corrisponde a uno degli scenari principali progettati per il dispositivo virtuale StorSimple. In questo scenario, il dispositivo StorSimple fisico o l'intero Data Center potrebbero non essere disponibili. Per fortuna, è possibile utilizzare un dispositivo virtuale per ripristinare le operazioni in un percorso alternativo. Durante il DR, i contenitori di volume dal dispositivo origine modificare il proprietario e vengono trasferiti al dispositivo virtuale. I prerequisiti per DR sono che il dispositivo virtuale sia stato creato e configurato, tutti i volumi all'interno del contenitore di volume sono stati offline e il contenitore volume è associato un snapshot cloud.

>[AZURE.NOTE] 
>
> - Quando si utilizza un dispositivo virtuale come dispositivo secondario per DR, tenere presente che il 8010 30 TB di spazio di archiviazione Standard, che 8020 ha 64 TB di spazio di archiviazione Premium. Il dispositivo virtuale 8020 di capacità superiore potrebbe essere più adatto per uno scenario di DR.
> - Non è possibile failover o duplicato da un dispositivo esegue l'aggiornamento 2 a un dispositivo che esegue pre-aggiornamento 1. È possibile tuttavia esito negativo su un dispositivo esegue l'aggiornamento 2 a un dispositivo che esegue aggiornamento 1 (1.1 o 1.2)

Per una procedura dettagliata, vedere [il controllo a un dispositivo virtuale](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Arrestare o eliminare il dispositivo virtuale

Se si già configurato e utilizzato un StorSimple dispositivo virtuale ma ora vuole interrompere attribuiti calcolo in base alle tariffe per l'uso, è possibile arrestare il dispositivo virtuale. Arrestare il dispositivo virtuale non si elimina il sistema operativo o dischi dati dell'archivio. Interrotta in base alle tariffe attribuiti nell'abbonamento a, ma le spese di spazio di archiviazione per il disco del sistema operativo e dati continueranno.

Se si elimina o arrestare il dispositivo virtuale, verrà visualizzato come **non in linea** nella pagina dispositivi del servizio di gestione StorSimple. È possibile scegliere di disattivare o eliminare il dispositivo se si desidera eliminare i backup creati dal dispositivo virtuale. Per ulteriori informazioni, vedere [Disattiva ed elimina un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Risolvere i problemi di connettività Internet 

Durante la creazione di un dispositivo virtuale, se è presente alcuna connettività a Internet, il passaggio della creazione avrà esito negativo. Per risolvere i problemi se l'errore è a causa di connettività Internet, eseguire la procedura seguente nel portale di classica Azure:

1. Creare una macchina virtuale di Windows server 2012 in Azure. Questa macchina virtuale devono utilizzare stesso account di archiviazione, VNet e subnet dal dispositivo virtuale. Se si dispone già di un host Windows Server esistente in Azure con lo stesso account di archiviazione, Vnet e subnet, è possibile usarlo per risolvere i problemi di connettività Internet.
2. Remoto al log in macchina virtuale creata nel passaggio precedente. 
3. Aprire una finestra di comando macchina virtuale (Windows + R, quindi digitare `cmd`).
4. Eseguire il seguente cmd quando viene richiesto.

    `nslookup windows.net`

5. Se `nslookup` non riesce, quindi errore di connettività Internet impedisce di registrazione al servizio di gestione StorSimple il dispositivo virtuale. 
6. Apportare le modifiche necessarie alla rete virtuale per assicurarsi che il dispositivo virtuale in grado di accedere ai siti Azure, ad esempio "windows.net".

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare il servizio di gestione di StorSimple per la gestione di un dispositivo virtuale](storsimple-manager-service-administration.md).
 
- Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set.md). 

