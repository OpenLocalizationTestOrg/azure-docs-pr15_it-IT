<properties 
   pageTitle="Amministrazione del servizio di gestione StorSimple | Microsoft Azure"
   description="Informazioni su come gestire il dispositivo StorSimple tramite il servizio di gestione StorSimple nel portale di classica Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple

## <a name="overview"></a>Panoramica

Questo articolo descrive l'interfaccia di servizio StorSimple Manager, inclusa la modalità di connessione, le varie opzioni disponibili e i collegamenti Indietro per i flussi di lavoro specifici che possono essere eseguite tramite questa interfaccia utente. Questa guida è applicabile al entrambi; fisica StorSimple e il dispositivo virtuale.

Dopo aver letto in questo articolo, si apprenderà come:

- La connessione al servizio Gestione StorSimple
- Esplorare l'interfaccia utente di gestione StorSimple
- Amministrare il dispositivo StorSimple tramite il servizio di gestione StorSimple


## <a name="connect-to-storsimple-manager-service"></a>La connessione al servizio Gestione StorSimple

Il servizio di gestione StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Utilizzare un portale classico centrale di Microsoft Azure in esecuzione in un browser per gestire i dispositivi. Per connettersi al servizio di gestione StorSimple, eseguire le operazioni seguenti.

#### <a name="to-connect-to-the-service"></a>Per connettersi al servizio

1. Passare a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Usa le credenziali dell'account Microsoft, accedere al portale classico di Microsoft Azure (disponibile nella parte superiore destra del riquadro).

1. Scorrere verso il basso il riquadro di spostamento sinistro per accedere al servizio Gestione StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Passare il servizio di gestione StorSimple dell'interfaccia utente

Nella tabella seguente è illustrata la gerarchia di navigazione per il servizio di gestione StorSimple dell'interfaccia utente.

- Pagina di destinazione **StorSimple Manager** consente di accedere alle pagine di servizio a livello dell'interfaccia utente applicabili a tutti i dispositivi all'interno di un servizio.

- Pagina **dispositivi** consente di accedere alle pagine dell'interfaccia utente a livello di dispositivo e applicabili a un dispositivo specifico.

- Pagina di **Contenitori volume** consente di accedere alla pagina volume che mostra tutti i volumi associati a un dispositivo.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Gerarchia di navigazione del servizio di gestione StorSimple

|Pagina di destinazione|Pagine di livello di servizio|Pagine di livello di dispositivo|Pagine di livello di dispositivo|
|---|---|---|---|
|Servizio di gestione StorSimple|Dashboard del servizio|Dashboard di dispositivo||
||Dispositivi →|Monitor|
||Catalogo di backup|Volume containers→|Volumi|
||Configurare (servizio)|Criteri di backup||
||Processi|Configurare (dispositivo)|
||Avvisi|Manutenzione dei servizi|

![Video disponibile](./media/storsimple-manager-service-administration/Video_icon.png) **Video disponibile**

Per guardare un video che illustra l'interfaccia utente del servizio di StorSimple Manager, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Amministrare dispositivo StorSimple utilizzo del servizio di gestione StorSimple

Nella tabella seguente mostra un riepilogo di tutte le attività di gestione comuni e i flussi di lavoro complessi che possono essere eseguite all'interno del servizio di gestione StorSimple dell'interfaccia utente. Queste attività sono organizzate in base alle pagine dell'interfaccia utente in cui vengono avviate.

Per ulteriori informazioni su ogni flusso di lavoro, fare clic su alla procedura nella tabella.

#### <a name="storsimple-manager-workflows"></a>Flussi di lavoro StorSimple Manager

|Se si desidera procedere come segue...|Passare alla pagina dell'interfaccia utente...|Utilizzare questa procedura.|
|---|---|---|
|Creare un servizio</br>Eliminare un servizio</br>Ottenere il codice di registrazione di servizio</br>Rigenerare il codice di registrazione di servizio|Servizio di gestione StorSimple|[Distribuire un servizio di gestione StorSimple](storsimple-manage-service.md)
|Modificare la chiave di crittografia di servizio dati</br>Visualizzare i registri di operazione|Dashboard StorSimple → service Manager|[Utilizzare il dashboard di servizio StorSimple Manager](storsimple-service-dashboard.md)|
|Disattivare un dispositivo</br>Eliminare un dispositivo|Gestione StorSimple servizio → dispositivi|[Disattivare o eliminare un dispositivo](storsimple-deactivate-and-delete-device.md)|
|Informazioni sui failover dispositivo e ripristino di emergenza</br>Il controllo a un dispositivo fisico</br>Il controllo a un dispositivo virtuale</br>Ripristino di emergenza continuità aziendale (BCDR)|Gestione StorSimple servizio → dispositivi|[Failover e ripristino di emergenza del dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Elenco di backup per un volume</br>Selezionare un set di backup</br>Eliminare un set di backup|Catalogo di Backup StorSimple → service Manager|[Gestire i backup](storsimple-manage-backup-catalog.md)|
|Duplicare un volume|Catalogo di Backup StorSimple → service Manager|[Duplicare un volume](storsimple-clone-volume.md)|
|Ripristinare un set di backup|Catalogo di Backup StorSimple → service Manager|[Ripristinare un set di backup](storsimple-restore-from-backup-set.md)|
|Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione</br>Rotazione delle chiavi degli account di archiviazione|Configurare la gestione StorSimple servizio →|[Gestire gli account di archiviazione](storsimple-manage-storage-accounts.md)|
|Informazioni sui modelli di larghezza di banda</br>Aggiungere un modello di larghezza di banda</br>Modificare un modello di larghezza di banda</br>Eliminare un modello di larghezza di banda</br>Utilizzare un modello di larghezza di banda predefinito</br>Creare un modello della larghezza di banda giornata intera che inizia in un momento specificato|Configurare la gestione StorSimple servizio →|[Gestire modelli di larghezza di banda](storsimple-manage-bandwidth-templates.md)|
|Informazioni sui record di controllo di accesso</br>Creare un record di controllo di accesso</br>Modificare un record di controllo di accesso</br>Eliminare un record di controllo di accesso|Configurare la gestione StorSimple servizio →|[Gestire i record di controllo di accesso](storsimple-manage-acrs.md)|
|Visualizzare i dettagli del processo</br>Annullare un processo|Gestione StorSimple servizio → processi|[Gestione dei processi](storsimple-manage-jobs.md)
|Ricevere notifiche di avviso</br>Gestire gli avvisi</br>Rivedere gli avvisi|Gestione StorSimple servizio → avvisi|[Visualizzare e gestire gli avvisi StorSimple](storsimple-manage-alerts.md)
|Visualizzare iniziatori connessi</br>Calcolare il numero seriale di dispositivo</br>Trovare la destinazione IQN|Gestione StorSimple servizio → dispositivi → Dashboard|[Utilizzare il dashboard di dispositivo StorSimple](storsimple-device-dashboard.md)|
|Creare grafici monitoraggio|Gestione StorSimple servizio → dispositivi → monitorare|[Monitorare il dispositivo StorSimple](storsimple-monitor-device.md)|
|Aggiungere un contenitore di volume</br>Modificare un contenitore di volume</br>Eliminare un contenitore di volume|StorSimple → servizio di gestione dispositivi → Volume contenitori|[Gestire i contenitori volume](storsimple-manage-volume-containers.md)|
|Aggiungere un volume</br>Modificare un volume</br>Disconnettere un volume</br>Eliminare un volume</br>Monitorare un volume|StorSimple Manager servizio → dispositivi → Volume contenitori → volumi|[Gestire i volumi](storsimple-manage-volumes.md)|
|Modificare le impostazioni del dispositivo</br>Modificare le impostazioni di tempo</br>Modificare le impostazioni di DNS.md</br>Configurare le interfacce di rete|Gestione StorSimple servizio → dispositivi → configurare|[Modificare la configurazione di dispositivo per il dispositivo StorSimple](storsimple-modify-device-config.md)|
|Impostazioni del proxy di visualizzazione web|Gestione StorSimple servizio → dispositivi → configurare|[Configurare un proxy web per il dispositivo](storsimple-configure-web-proxy.md)|
|Modificare la password di amministratore di dispositivo</br>Modificare la password StorSimple Snapshot Manager|Gestione StorSimple servizio → dispositivi → configurare|[Cambiare le password StorSimple](storsimple-change-passwords.md)|
|Configurare la gestione remota|Gestione StorSimple servizio → dispositivi → configurare|[La connessione remota al dispositivo StorSimple](storsimple-remote-connect.md)|
|Configurare le impostazioni degli avvisi|Gestione StorSimple servizio → dispositivi → configurare|[Visualizzare e gestire gli avvisi StorSimple](storsimple-manage-alerts.md)|
|Configurare CHAP del dispositivo StorSimple|Gestione StorSimple servizio → dispositivi → configurare|[Configurare CHAP del dispositivo StorSimple](storsimple-configure-chap.md)|
|Aggiungere un criterio di backup</br>Aggiungere o modificare una pianificazione</br>Eliminare un criterio di backup</br>Eseguire un backup manuale</br>Creare un criterio di backup personalizzato con più volumi e pianificazioni|StorSimple servizio → dispositivi → Backup criteri di gestione|[Gestire i criteri di backup](storsimple-manage-backup-policies.md)|
|Interrompere controller dispositivo</br>Riavviare dispositivo controller</br>Chiudere controller dispositivo</br>Riavviare il dispositivo predefinite</br>(In precedenza sono per solo un dispositivo locale)|Gestione StorSimple servizio → dispositivi → manutenzione|[Gestire StorSimple dispositivo controller](storsimple-manage-device-controller.md)|
|Informazioni sui componenti hardware StorSimple</br>Monitorare lo stato di hardware</br>(In precedenza sono per solo un dispositivo locale)|Gestione StorSimple servizio → dispositivi → manutenzione|[Componenti hardware monitor](storsimple-monitor-hardware-status.md)|
|Creare un pacchetto di supporto|Gestione StorSimple servizio → dispositivi → manutenzione|[Creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md)|
|Installare gli aggiornamenti software|Gestione StorSimple servizio → dispositivi → manutenzione|[Aggiornare il dispositivo](storsimple-update-device.md)|


##<a name="next-steps"></a>Passaggi successivi
Se si verificano problemi con le operazioni giornaliere di dispositivo StorSimple o con uno dei relativi componenti hardware, vedere:

- [Risolvere i problemi di dispositivi operativi](storsimple-troubleshoot-operational-device.md)
- [Utilizzare StorSimple monitoraggio LED](storsimple-monitoring-indicators.md)

Se non è possibile risolvere i problemi ed è necessario creare una richiesta di assistenza, vedere [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).
