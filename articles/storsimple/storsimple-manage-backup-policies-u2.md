<properties 
   pageTitle="Gestire i criteri di backup StorSimple | Microsoft Azure"
   description="Spiega come è possibile utilizzare il servizio di gestione di StorSimple per creare e gestire backup manuali, pianificazioni di backup e conservazione backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Utilizzare il servizio di gestione StorSimple per gestire i criteri di backup (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare la pagina di **Criteri di Backup** del servizio di gestione di StorSimple per controllare processi di backup e la conservazione dei backup per i volumi StorSimple. Viene inoltre descritto come eseguire un backup manuale.

Quando si esegue il backup di un volume, è possibile creare uno snapshot locale o uno snapshot cloud. Se esegue il backup di un volume bloccato in locale, è consigliabile specificare uno snapshot cloud. Creazione di un numero elevato di istantanee locale di un volume locale bloccata associati a un set di dati che sono presenti molte varianza del genereranno situazioni in cui è possibile eseguire rapidamente fuori area locale. Se si sceglie di istantanee locale, è consigliabile istantanee meno giornaliera per eseguire il backup di stato più recente mantenerle per un giorno e quindi eliminarle.

Quando uno snapshot cloud di un volume locale bloccato, solo i dati modificati vengono copiate nel cloud, in cui è deduplicated e compressa. 

## <a name="the-backup-policies-page"></a>La pagina Criteri di Backup

La pagina di **Criteri di Backup** consente di gestire i criteri di backup e pianificare locali e cloud istantanee. (Criteri di backup vengono utilizzati per configurare le pianificazioni di backup e la conservazione dei backup per un insieme di volumi). Criteri di backup consentono di creare uno snapshot della più volumi contemporaneamente. Questo significa che il backup creato da un criterio di backup sarà copie coerenti arresto anomalo. La pagina di **Criteri di Backup** sono elencati i criteri di backup, i tipi di, volumi associati, il numero di backup mantenuti e l'opzione per abilitare questi criteri.

La pagina di **Criteri di Backup** consente anche filtrare i criteri di backup da uno o più dei campi seguenti:

- **Nome del criterio** : il nome associato al criterio. I diversi tipi di criteri includono:

   - Criteri programmati, in modo esplicito creati dall'utente.
   - Criteri automatici, che vengono creati quando è stato attivato il backup predefinito per questa opzione consente al momento della creazione del volume. Questi criteri sono denominati come default *NomeVolume* *NomeVolume* nel punto in cui si intende il nome del volume StorSimple configurato dall'utente nel portale di classica Azure. I criteri automatici come risultato giornaliera snapshot cloud iniziando dal tempo dispositivo 22:30.
   - Criteri importati, che sono stati creati originariamente in Gestione Snapshot StorSimple. Questi contiene un tag che descrive l'host StorSimple Snapshot Manager importati i criteri da.

- **Volumi** – volumi associati al criterio. Tutti i volumi associati a un criterio di backup sono raggruppati quando vengono creati i backup.

- **Ultimo backup completato** – la data e l'ora dell'ultimo backup completato eseguite con questo criterio.

- **Backup successivo** – la data e l'ora del backup pianificato successivo che verrà avviato da questo criterio.

- **Le pianificazioni** : il numero delle pianificazioni associato a criteri di backup.

Le operazioni comuni che è possibile eseguire da questa pagina sono:

- Aggiungere un criterio di backup 
- Aggiungere o modificare una pianificazione 
- Eliminare un criterio di backup 
- Eseguire un backup manuale 
- Creare un criterio di backup personalizzato con più volumi e pianificazioni 

## <a name="add-a-backup-policy"></a>Aggiungere un criterio di backup

Aggiungere un criterio di backup per la pianificazione automatica di backup. Nel portale di classica Azure per aggiungere un criterio di backup del dispositivo StorSimple, procedere come segue. Dopo aver aggiunto il criterio, è possibile definire una pianificazione (vedere [aggiungere o modificare una pianificazione](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Video disponibile](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come creare una locale o nel cloud dei criteri di backup, fare clic [qui](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Aggiungere o modificare una pianificazione

È possibile aggiungere o modificare una pianificazione è collegata a un criterio di backup esistente nel dispositivo StorSimple. Nel portale di classica Azure per aggiungere o modificare una pianificazione, procedere come segue.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Eliminare un criterio di backup

Nel portale di classica Azure per eliminare un criterio di backup in un dispositivo StorSimple, procedere come segue.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Eseguire un backup manuale

Nel portale di classica Azure per creare un backup (manuale) su richiesta per un singolo volume, procedere come segue.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Creare un criterio di backup personalizzato con più volumi e pianificazioni

Nel portale di classica Azure per creare un criterio di backup personalizzato contenente più volumi e le pianificazioni, procedere come segue.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
