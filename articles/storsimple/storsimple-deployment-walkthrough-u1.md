<properties 
   pageTitle="Distribuire il dispositivo di StorSimple (1 aggiornamento) | Microsoft Azure"
   description="Vengono illustrati i passaggi e procedure consigliate per la distribuzione il dispositivo StorSimple aggiornamento 1 e il servizio."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device-update-1"></a>Distribuire il dispositivo di StorSimple locale (aggiornamento 1)

> [AZURE.SELECTOR]
- [Aggiornamento 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Aggiornamento 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Versione GA](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Panoramica

Introduzione a Microsoft Azure StorSimple distribuzione del dispositivo. Queste esercitazioni per la distribuzione si applica a StorSimple 8000 serie Update 1.0. Questa serie di esercitazioni viene descritto come configurare il dispositivo StorSimple e include un elenco di controllo di configurazione, i prerequisiti di configurazione e i dettagli della configurazione passaggi.

Le informazioni in queste esercitazioni presuppone che si hanno rivisto precauzioni di sicurezza e decompresso, centralizzato in remoto e cavi dispositivo StorSimple. Se occorre eseguire queste operazioni, iniziare da rivedere [le misure di sicurezza](storsimple-safety.md). A seconda del modello di dispositivo, quindi sarà possibile decomprimere, installazione a rack e cavo seguendo le istruzioni disponibili in:

- [Decomprimere, rack montaggio e cavo il 8100](storsimple-8100-hardware-installation.md)
- [Decomprimere, rack montaggio e cavo il 8600](storsimple-8600-hardware-installation.md)

Sono necessari privilegi di amministratore per completare il processo di installazione e configurazione. È consigliabile esaminare l'elenco di controllo di configurazione prima di iniziare. Il processo di configurazione e distribuzione può richiedere del tempo per completare.

> [AZURE.NOTE] Le informazioni di distribuzione StorSimple pubblicate nel sito Web di Microsoft Azure applicano ai dispositivi solo di 8000 StorSimple serie. Per informazioni complete sui dispositivi serie 5000 e 7000, passare a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Per informazioni di distribuzione serie 5000 e 7000, vedere [StorSimple sistema Quick Start Guide](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Passaggi di distribuzione

Eseguire queste operazioni necessarie per configurare il dispositivo StorSimple e connetterlo al servizio di gestione StorSimple. Oltre i passaggi necessari, sono disponibili procedure che potrebbe essere necessario durante la distribuzione e passaggi facoltativi. Le istruzioni dettagliate distribuzione indicano quando è necessario eseguire i passaggi facoltativi.


| Passaggio                                                                                   | Descrizione                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PREREQUISITI**                                                                      | Questi da portare a termine in preparazione per la distribuzione in scadenza.                                                                                        |
| Elenco di controllo per la configurazione di distribuzione.                                                     | Usare questo elenco di controllo per raccogliere e registrare le informazioni nelle versioni precedenti a e la distribuzione.                                                                       |
| Prerequisiti per la distribuzione.                                                               | Questi convalidare l'ambiente sia pronto per la distribuzione.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DISTRIBUZIONE DETTAGLIATA**                                                                   | Questi passaggi per distribuire il dispositivo StorSimple nell'ambiente di produzione.                                                                                      |
| Passaggio 1: Creare un nuovo servizio.                                                         | Configurare la gestione di cloud e l'archiviazione del dispositivo StorSimple. Se si dispone di un servizio esistente per altri dispositivi StorSimple, ignorare questo passaggio.                |
| Passaggio 2: Ottenere il codice di registrazione di servizio.                                               | Utilizzare questo tasto per eseguire la registrazione e connettere il dispositivo StorSimple con il servizio di gestione.                                                                         |
| Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple.    | Connettere il dispositivo alla rete e registrare con Azure per completare la configurazione utilizzando il servizio di gestione.                                            |
| Passaggio 4: Completare la configurazione di periferica minimi</br>Facoltativo: Aggiornare il dispositivo StorSimple.      | Utilizzare il servizio di gestione per completare la configurazione di dispositivi e abilitarla fornire l'archiviazione.                                                                      |
| Passaggio 5: Creare un contenitore di volume.                                                      | Creare un contenitore a disposizione volumi. Account di archiviazione, della larghezza di banda e le impostazioni di crittografia per tutti i volumi in essa contenuti un contenitore di volume.    |
| Passaggio 6: Creare un volume.                                                                | Effettuare il provisioning di volumi di archiviazione sul dispositivo StorSimple per i server.                                                                                        |
| Passaggio 7: Installare, inizializzare e formattare un volume.</br>Facoltativo: Configurare MPIO.            | Collegare i server di archiviazione iSCSI fornita dal dispositivo. Se si desidera configurare MPIO per assicurarsi che il server tollerabile collegamento, di rete e di errore dell'interfaccia.                                                                                                                                                              |
| Passaggio 8: Eseguire un backup.                                                                  | Configurare i criteri di backup per proteggere i dati                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **ALTRE PROCEDURE**                                                                   | Potrebbe essere necessario fare riferimento a queste procedure quando si distribuisce la soluzione.                                                                                        |
| Configurare un nuovo account di archiviazione per il servizio.                                      |                                                                                                                                                               |
| Utilizzare PuTTY per connettersi alla console seriale dispositivo.                                    |                                                                                                                                                               |
| È possibile ottenere IQN di un host Windows Server.                                                   |                                                                                                                                                               |
| Creare un backup manuale.                                                                 | 


## <a name="deployment-configuration-checklist"></a>Elenco di controllo configurazione distribuzione

L'elenco di controllo di configurazione della distribuzione seguente descrive le informazioni da raccogliere prima e come si configura il software in un dispositivo StorSimple. Preparazione alcune di queste informazioni anticipo consente di semplificare il processo di distribuzione dispositivo StorSimple nel proprio ambiente. Usare questo elenco di controllo per si noti inoltre verso il basso i dettagli di configurazione come si distribuisce il dispositivo.

| Finestra di condivisione                                  | Parametro                                         | Dettagli                                                                                                                                                                | Valori |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Cavo dispositivo**                      | Accesso seriale                                     | Configurazione del dispositivo iniziale                                                                  | Sì/No |
|   |   |  |  |
| **Configurare e registrare dispositivo**          | Impostazioni di rete dati 0                           | Indirizzo IP dati 0:</br>Maschera subnet:</br>Gateway:</br>Server DNS primario:</br>Server NTP primario:</br>Server proxy Web IP/FQDN (facoltativo):</br>Porta proxy Web:|        |
|                                        | Password di amministratore di dispositivo                     | Password deve essere compreso tra 8 e 15 caratteri contenenti lettere minuscole, caratteri maiuscoli, numerici e speciali. |        |
|                                        | StorSimple Snapshot Gestione password              | Password deve essere 14 o 15 caratteri contenenti lettere minuscole, caratteri maiuscoli, numerici e speciali.|        |
|                                        | Chiave di registrazione del servizio                          | Questo tasto viene generato dal portale di classica Azure.    |        |
|                                        | Chiave di crittografia di servizio dati                       | Questo tasto viene creato quando il dispositivo è registrato con il servizio di gestione tramite Windows PowerShell per StorSimple. Copiare questa chiave e salvarlo in un percorso sicuro.|  |
|   |   |  |  |
| **Configurazione di dispositivi minima completata**          | Nome descrittivo per il dispositivo                     | Si tratta di un nome descrittivo per il dispositivo. |        |
|                                        | Fuso orario                                          | Il dispositivo utilizzerà il fuso orario per tutte le operazioni pianificate.  |        |
|                                        | Server DNS secondario                              | Si tratta di una configurazione necessaria.                                  |        |
|                                        | Interfaccia di rete: controller dati 0 fissa IP                                     | Questi indirizzi IP dovrebbe essere routing a Internet.</br>Controller 0 fissa IP indirizzo:</br>Controller 1 fissa IP indirizzo:|
|   |   |  |  |
| **Impostazioni dell'interfaccia di rete aggiuntive**  | Interfaccia di rete: dati 1</br>Se abilitata iSCSI, non si configura il Gateway.      | Scopo: Cloud/iSCSI/non utilizzati</br>Indirizzo IP:</br>Maschera subnet:</br>Gateway:|
|                                        | Interfaccia di rete: dati 2</br>Se iSCSI abilitato, non si configura il Gateway.      | Scopo: Cloud/iSCSI/non utilizzati</br>Indirizzo IP:</br>Maschera subnet:</br>Gateway:|
|                                        | Interfaccia di rete: dati 3</br>Se abilitata iSCSI, non si configura il Gateway.      | Scopo: Cloud/iSCSI/non utilizzati</br>Indirizzo IP:</br>Maschera subnet:</br>Gateway:|
|                                        | Interfaccia di rete: dati 4</br>Se abilitata iSCSI, non si configura il Gateway.      | Scopo: Cloud/iSCSI/non utilizzati</br>Indirizzo IP:</br>Maschera subnet:</br>Gateway:|
|                                        | Interfaccia di rete: dati 5</br>Se abilitata iSCSI, non si configura il Gateway.      | Scopo: Cloud/iSCSI/non utilizzati</br>Indirizzo IP:</br>Maschera subnet:</br>Gateway:|
|   |   |  |  |
| **Creare un contenitore di volume**                      | Nome del contenitore di volume:                            | Nome del contenitore                                                                                                                                                 |        |
|                                        | Account di archiviazione Azure:                            | Tasto archiviazione account nome & da associare il contenitore volume                                                                                              |        |
|                                        | Chiave di crittografia di spazio di archiviazione cloud:                     | Chiave di crittografia per l'archiviazione in ogni contenitore                                                                                                                           |        |
|   |   |  |  |
| **Creare un volume**                        | Dettagli per ogni volume                           | Nome del volume:                                                                                                                                                           |        |
|                                        |                                                   | Dimensione:                                                                                                                                                                  |        |
|                                        |                                                   | Tipo di utilizzo:                                                                                                                                                            |        |
|                                        |                                                   | Nome ACR:                                                                                                                                                              |        |
|                                        |                                                   | Criteri di backup predefinito:                                                                                                                                                 |        |
|   |   |  |  |
| **Montaggio, inizializzare e formattare un volume** | Dettagli per ogni server host connessione per l'archiviazione | Nome del Server di Windows:                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Nome del volume di Windows Server:                                                                                                                                                   |        |
|                                        |                                                   | NTFS montaggio punto/lettera:                                                                                                                                      |        |

## <a name="deployment-prerequisites"></a>Prerequisiti per la distribuzione

Nelle sezioni seguenti illustrano i prerequisiti di configurazione per il servizio di gestione StorSimple e il dispositivo StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Per il servizio di gestione StorSimple

Prima di iniziare, verificare che:

- È l'account Microsoft con le credenziali di accesso.

- È l'account di archiviazione di Microsoft Azure con credenziali di accesso.

- L'abbonamento a Microsoft Azure è abilitato per il servizio di gestione StorSimple. Acquistare l'abbonamento tramite il [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- È possibile accedere a software di emulazione terminale, ad esempio PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Per il dispositivo nel centro dati

Prima di configurare il dispositivo, verificare quanto segue:

- Il dispositivo è completamente decompresso, installato su un rack e completamente cavi per power, rete e accesso seriale come descritto in:

    -  [Decomprimere, rack montaggio e cavo dispositivo 8100](storsimple-8100-hardware-installation.md)
    -  [Decomprimere, rack montaggio e cavo dispositivo 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel centro dati

Prima di iniziare, verificare che:

- Per consentire l'iSCSI e cloud il traffico come descritto nella [rete requisiti per il dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)vengono aperte le porte nel firewall Data Center.


## <a name="step-by-step-deployment"></a>Distribuzione dettagliata

Utilizzare le seguenti istruzioni dettagliate per distribuire il dispositivo StorSimple nel centro dati.

## <a name="step-1-create-a-new-service"></a>Passaggio 1: Creare un nuovo servizio

Un servizio di gestione StorSimple può gestire più dispositivi StorSimple. Per creare una nuova istanza del servizio di gestione StorSimple, procedere come segue.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Se non è stato attivato la creazione di un account di archiviazione automatica con il servizio, sarà necessario creare almeno un account di archiviazione dopo aver creato un servizio. Questo account di archiviazione verrà utilizzato quando si crea un contenitore di volume. 
>
> * Se non è stato creato automaticamente un account di archiviazione, passare a [Configura un nuovo account di archiviazione per il servizio](#configure-a-new-storage-account-for-the-service) per istruzioni dettagliate. 
> * Se è abilitata la creazione automatica di un account di archiviazione, passare a [passaggio 2: ottenere il codice di registrazione servizio](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Passaggio 2: Ottenere il codice di registrazione di servizio

Dopo il servizio di gestione StorSimple sia in esecuzione, sarà necessario ottenere la chiave di registrazione del servizio. Questo tasto viene utilizzato per eseguire la registrazione e connettere il dispositivo StorSimple con il servizio.

Nel portale di classica Azure, procedere come segue.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passaggio 3: Configurare e registrare il dispositivo di Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per completare la configurazione iniziale del dispositivo StorSimple come descritto nella procedura seguente. È necessario utilizzare software di emulazione terminale per completare questo passaggio. Per ulteriori informazioni, vedere [Utilizzare PuTTY per connettersi alla console seriale dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passaggio 4: Completare la configurazione di periferica minimi

Per la configurazione di dispositivi minima del dispositivo StorSimple, verrà chiesto di: 

- Configurare i server DNS secondario.
- Abilitare iSCSI sull'interfaccia di almeno una rete.
- Assegnare indirizzi IP fissi a entrambi i controller.

Nel portale di classica Azure per completare la configurazione di dispositivi minima, procedere come segue.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Passaggio 5: Creare un contenitore di volume

Account di archiviazione, della larghezza di banda e le impostazioni di crittografia per tutti i volumi in essa contenuti un contenitore di volume. È necessario creare un contenitore volume prima di iniziare il provisioning di volumi nel dispositivo StorSimple. 

Eseguire la procedura seguente nel portale di classica Azure per creare un contenitore di volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passaggio 6: Creare un volume

Dopo aver creato un contenitore di volume, è possibile eseguire il provisioning di una quantità di spazio di archiviazione sul dispositivo StorSimple per i server. Nel portale di classica Azure per creare un volume, procedere come segue.

> [AZURE.IMPORTANT] StorSimple Manager può creare solo ai volumi eseguono provisioning. Non è possibile creare provisioning interamente o parzialmente provisioning volumi. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passaggio 7: Installare, inizializzare e formattare un volume

La procedura seguente vengono eseguita sull'host Windows Server. 


> [AZURE.IMPORTANT]

> - Per la disponibilità della soluzione StorSimple, è consigliabile configurare MPIO sui server dell'host (facoltativo) prima di configurare iSCSI. Configurazione di MPIO sui server host garantisce che il server tollerabile un collegamento, reti o errore dell'interfaccia.

> - Per MPIO e iSCSI installazione e configurazione istruzioni sull'host Windows Server, vedere [Configurare MPIO del dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Questi includerà anche la procedura per installare, inizializzare e formattare i volumi StorSimple.

> - Per MPIO e iSCSI installazione e configurazione istruzioni su un host Linux, passare alla [Configurazione MPIO per l'host StorSimple Linux](storsimple-configure-mpio-on-linux.md)

Se si decide di non configurare MPIO, eseguire la procedura seguente per installare, inizializzare e formattare i volumi StorSimple su un host Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passaggio 8: Eseguire un backup

Backup proteggere in un momento di volumi e migliorare recupero riducendo tempi di ripristino. È possibile eseguire due tipi di backup in un dispositivo StorSimple: istantanee locali e cloud snapshot. Ognuno di questi tipi di backup può essere **pianificata** o **manuale**. 

Nel portale Azure classico per creare un backup pianificato, procedere come segue.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

È possibile scrivere un manuale di backup in qualsiasi momento. Per procedure, vedere [creare un backup manuale](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio facoltativo che è necessario eseguire solo se non è stato attivato la creazione di un account di archiviazione automatica con il servizio. Per creare un contenitore di volume StorSimple è necessario un account di archiviazione di Microsoft Azure.

Se è necessario creare un account di archiviazione Azure in un'area diversa, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md) per istruzioni dettagliate.

Nel portale di classica Azure, nella pagina **Gestione StorSimple servizi** , procedere come segue.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizzare PuTTY per connettersi alla console seriale dispositivo

Per connettersi a Windows PowerShell per StorSimple, è necessario utilizzare software di emulazione terminale, ad esempio PuTTY. È possibile utilizzare PuTTY quando si accede al dispositivo direttamente tramite la console seriale o aprire una sessione telnet da un computer remoto.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## <a name="scan-for-and-apply-updates"></a>Consente di cercare e applicare gli aggiornamenti

Il dispositivo l'aggiornamento può richiedere alcune ore. Eseguire i passaggi seguenti per la ricerca e applicare gli aggiornamenti nel dispositivo.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Per aggiornare il dispositivo

1.  Nella pagina **Guida introduttiva** periferica, fare clic su **dispositivi**. Selezionare il dispositivo fisico, fare clic su **manutenzione** e quindi fare clic su **Analisi aggiornamenti**.  

2.  Viene creato un processo di cercare gli aggiornamenti disponibili. Se sono disponibili aggiornamenti, **Installare gli aggiornamenti**a **Esaminare gli aggiornamenti** . Fare clic su **Installa aggiornamenti**. 

3.  Verrà creato un processo di aggiornamento. Controllare lo stato dell'aggiornamento, passare a **processi**.

    > [AZURE.NOTE] Quando si avvia il processo di aggiornamento, viene visualizzato immediatamente lo stato del 50%. Lo stato di modifica al 100% solo dopo che il processo di aggiornamento è stato completato. Non esiste alcun in tempo reale sullo stato del processo di aggiornamento.

4.  Dopo il dispositivo viene aggiornato correttamente, abilitare le interfacce di rete dati 2 e 3 dati se questi sono stati disattivati.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Ottenere IQN di un host Windows Server

Eseguire la procedura seguente per ottenere il iSCSI completo nome iSCSI di un host di Windows che esegue Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Creare un backup manuale

Nel portale di classica Azure per creare un backup manuale su richiesta per un singolo volume sul dispositivo StorSimple, procedere come segue.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="configure-mpio"></a>Configurare MPIO

Più percorsi i/o (MPIO) è una funzionalità facoltativa e non è installato in Windows Server per impostazione predefinita. Deve essere installato come una caratteristica tramite Server Manager. Per istruzioni sull'installazione MPIO, vedere [Configurare MPIO del dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

MPIO istruzioni di installazione per un dispositivo StorSimple connesso a un host Linux, passare alla [Configurazione MPIO per l'host Linux](storsimple-configure-mpio-on-linux.md).


> [AZURE.NOTE] MPIO non è supportata in un dispositivo virtuale StorSimple. 



## <a name="next-steps"></a>Passaggi successivi

- Configurare un [dispositivo virtuale](storsimple-virtual-device-u2.md).

- Utilizzare il [servizio di gestione di StorSimple](storsimple-manager-service-administration.md) per gestire il dispositivo StorSimple.
 
