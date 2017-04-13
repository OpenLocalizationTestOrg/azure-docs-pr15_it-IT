<properties 
   pageTitle="Amministrazione dell'Array virtuale Manager StorSimple | Microsoft Azure"
   description="Informazioni su come gestire la matrice virtuale locale StorSimple tramite il servizio di gestione StorSimple nel portale di classica Azure."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Usare il servizio di gestione di StorSimple per amministrare la matrice virtuale StorSimple

![flusso del processo di configurazione](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Panoramica

In questo articolo descrive l'interfaccia di servizio StorSimple Manager, ad esempio per la connessione e le varie opzioni disponibili e vengono forniti collegamenti ai flussi di lavoro specifici che possono essere eseguite tramite questa interfaccia utente. 

Dopo aver letto in questo articolo, si saprà come:

- La connessione al servizio di gestione StorSimple
- Esplorare l'interfaccia utente di gestione StorSimple
- Amministrare la matrice virtuale StorSimple tramite il servizio di gestione StorSimple

> [AZURE.NOTE] Per visualizzare le opzioni di gestione disponibili per il dispositivo di serie 8000 StorSimple, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>La connessione al servizio di gestione StorSimple

Il servizio di gestione StorSimple viene eseguito in Microsoft Azure e si connette a più matrici virtuale StorSimple. Utilizzare un portale classico centrale di Microsoft Azure in esecuzione in un browser per gestire i dispositivi. Per connettersi al servizio di gestione StorSimple, eseguire le operazioni seguenti.

#### <a name="to-connect-to-the-service"></a>Per connettersi al servizio

1. Passare a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Usa le credenziali dell'account Microsoft, accedere al portale classico di Microsoft Azure (disponibile nella parte superiore destra del riquadro).

3. Scorrere verso il basso il riquadro di spostamento sinistro per accedere al servizio Gestione StorSimple.

    ![scorrere fino al servizio](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Passare il servizio di gestione StorSimple dell'interfaccia utente

Nella tabella seguente è illustrata la gerarchia di navigazione per il servizio di gestione StorSimple dell'interfaccia utente.

- La pagina di destinazione **StorSimple Manager** consente di accedere alle pagine di servizio a livello dell'interfaccia utente applicabili a tutte le matrici virtuale all'interno di un servizio.

- La pagina **dispositivi** consente di accedere alle pagine dell'interfaccia utente a livello di dispositivo e applicabili a una matrice virtuale specifico.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Gerarchia di navigazione del servizio di gestione StorSimple

|Pagina di destinazione|Pagine di livello di servizio|Pagine di livello di dispositivo|
|---|---|---|
|Servizio di gestione StorSimple|Dashboard (servizio)|Dashboard (dispositivo)|
||Dispositivi →|Monitor|
||Catalogo di backup|Condivisioni (file server) o </br>Volumi (iSCSI server)|
||Configurare (servizio)|Configurare (dispositivo)|
||Processi|Manutenzione dei servizi|
||Avvisi|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Utilizzare il servizio di gestione StorSimple per eseguire attività di gestione

Nella tabella seguente mostra un riepilogo di tutte le attività di gestione comuni e i flussi di lavoro complessi che possono essere eseguite all'interno del servizio di gestione StorSimple dell'interfaccia utente. Queste attività sono organizzate in base alle pagine dell'interfaccia utente in cui vengono avviate.

Per ulteriori informazioni su ogni flusso di lavoro, fare clic su alla procedura nella tabella.

#### <a name="storsimple-manager-workflows"></a>Flussi di lavoro StorSimple Manager

|Se si desidera procedere come segue...|Passare alla pagina dell'interfaccia utente...|Utilizzare questa procedura|
|---|---|---|
|Creare un servizio</br>Eliminare un servizio</br>Ottenere la chiave di registrazione del servizio</br>Rigenerare la chiave di registrazione del servizio|Servizio di gestione StorSimple|[Distribuire il servizio di gestione StorSimple](storsimple-ova-manage-service.md)|
|Modificare la chiave di crittografia di servizio dati</br>Visualizzare i registri di operazioni|Dashboard StorSimple → service Manager|[Utilizzare il dashboard di servizio StorSimple](storsimple-ova-service-dashboard.md)|
|Disattivare una matrice virtuale</br>Eliminare una matrice virtuale|Gestione StorSimple servizio → dispositivi|[Disattivare o eliminare una matrice virtuale](storsimple-ova-deactivate-and-delete-device.md)|
|Failover dispositivo e ripristino di emergenza</br>Prerequisiti failover</br>Il controllo a un dispositivo virtuale</br>Ripristino di emergenza continuità aziendale (BCDR)</br>Errori durante il ripristino di emergenza|Gestione StorSimple servizio → dispositivi|[Failover dispositivo e ripristino di emergenza della matrice virtuale StorSimple](storsimple-ova-failover-dr.md)|
|Eseguire il backup condivisioni e volumi</br>Eseguire un backup manuale</br>Modificare la pianificazione di backup</br>Visualizzare i backup esistenti|Catalogo di Backup → service Manager StorSimple|[Eseguire il backup della matrice virtuale StorSimple](storsimple-ova-backup.md)|
|Ripristino delle condivisioni da un set di backup</br>Ripristinare volumi da un set di backup</br>Livello di elemento ripristino (solo server file)|Catalogo di Backup StorSimple → service Manager|[Ripristinare da un backup della matrice virtuale StorSimple](storsimple-ova-restore.md)|
|Informazioni sugli account di archiviazione</br>Aggiungere un account di archiviazione</br>Modificare un account di archiviazione</br>Eliminare un account di archiviazione|Configurare la gestione StorSimple servizio →|[Gestire gli account di archiviazione per la matrice virtuale StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Informazioni sui record di controllo di accesso</br>Aggiungere o modificare un record di controllo di accesso </br>Eliminare un record di controllo di accesso|Configurare la gestione StorSimple servizio →|[Gestire i record di controllo di accesso per la matrice virtuale StorSimple](storsimple-ova-manage-acrs.md)|
|Visualizzare i dettagli del processo|Gestione StorSimple servizio → processi| [Gestire i processi di matrice virtuale StorSimple](storsimple-ova-manage-jobs.md)|
|Configurare le impostazioni degli avvisi</br>Ricevere notifiche di avviso</br>Gestire gli avvisi</br>Rivedere gli avvisi|Gestione StorSimple servizio → avvisi|[Visualizzare e gestire avvisi per la matrice virtuale StorSimple](storsimple-ova-manage-alerts.md)|
|Modificare la password di amministratore del dispositivo|Gestione StorSimple servizio → dispositivi → configurare|[Modificare la password di amministratore del dispositivo matrice virtuale StorSimple](storsimple-ova-change-device-admin-password.md)|
|Installare gli aggiornamenti software|Gestione StorSimple servizio → dispositivi → manutenzione|[Aggiornare la matrice virtuale](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] È necessario utilizzare [web locale dell'interfaccia utente](storsimple-ova-web-ui-admin.md) per le attività seguenti:
>
>- [Recuperare la chiave di crittografia di servizio dati](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Creare un pacchetto di supporto](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Arrestare e riavviare una matrice virtuale](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Passaggi successivi
Per informazioni su web dell'interfaccia utente e come utilizzarlo, passare a [utilizzare l'interfaccia per amministrare la matrice virtuale StorSimple web StorSimple](storsimple-ova-web-ui-admin.md).
