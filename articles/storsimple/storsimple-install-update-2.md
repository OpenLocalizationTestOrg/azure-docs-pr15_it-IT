<properties
   pageTitle="Installare l'aggiornamento 2 sul dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come installare StorSimple 8000 serie aggiornamento 2 in un dispositivo di serie 8000 StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Installare l'aggiornamento 2 sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come installare l'aggiornamento 2 in un dispositivo StorSimple in esecuzione una versione precedente di software tramite il portale classico Azure. L'esercitazione copre anche i passaggi necessari per l'aggiornamento quando è configurato un gateway nell'interfaccia di rete diversa da quella dati 0 del dispositivo StorSimple e si sta tentando di aggiornamento da una versione del software 1 pre-aggiornamento.

Aggiornamento 2 include gli aggiornamenti software di dispositivo, gli aggiornamenti dei driver LSI e aggiornamenti firmware disco. Il dispositivo aggiornamenti software e LSI gli aggiornamenti non dannosi e possono essere applicati tramite il portale classico Azure. Gli aggiornamenti di firmware disco la disponibilità di aggiornamenti e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]

> -  Aggiornamento 2 potrebbe non essere visualizzato immediatamente perché facciamo graduale degli aggiornamenti. Cercare gli aggiornamenti in pochi giorni nuovamente come questo aggiornamento sarà disponibile presto.
> - Una serie di pre-controlli manuali e automatiche vengono eseguite prima dell'installazione per determinare lo stato di dispositivo in termini di connettività di rete e stato hardware. Questi controlli non vengono eseguiti solo se si applicano gli aggiornamenti dal portale di classica Azure.
> - È consigliabile installare gli aggiornamenti software e driver tramite il portale classico Azure. Devono essere inseriti solo all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti), se il controllo di pre-aggiornamento gateway non riesce nel portale. Gli aggiornamenti potrebbero richiedere ore 4-7 per installare (inclusi gli aggiornamenti di Windows). Tramite l'interfaccia di Windows PowerShell del dispositivo, è necessario installare gli aggiornamenti in modalità di manutenzione. Come gli aggiornamenti in modalità di manutenzione aggiornamenti dannosi, questi verrà generato un orario verso il basso per il dispositivo.
> - Se la gestione di Snapshot StorSimple facoltativi è in esecuzione, assicurarsi di aver aggiornato alla versione di gestione di Snapshot di aggiornamento 2 prima dell'aggiornamento il dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Installare l'aggiornamento 2 tramite il portale classico Azure

Per aggiornare il dispositivo di [aggiornamento 2](storsimple-update2-release-notes.md), procedere come segue.


> [AZURE.NOTE]
Aggiornamento 2 consente a Microsoft in modo da estrarre informazioni diagnostiche aggiuntive dal dispositivo. Di conseguenza, quando il nostro team operazioni identifica dispositivi che si verificano problemi, sono migliore per raccogliere informazioni dal dispositivo e diagnosticare i problemi. Accettando aggiornamento 2, si consentono di fornire questo supporto preventivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificare che il dispositivo sia in esecuzione **StorSimple 8000 serie aggiornamento 2 (6.3.9600.17673)**. **Ultimo aggiornamento data** devono essere modificati. Verrà visualizzata anche che sono disponibili aggiornamenti in modalità di manutenzione (questo messaggio potrebbe continuano a essere visualizzati fino a 24 ore dopo l'installazione degli aggiornamenti).

    Gli aggiornamenti in modalità di manutenzione sono dannosi aggiornamenti che risultato del tempo di inattività dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. In alcuni casi quando si esegue l'aggiornamento 1.2 del firmware disco potrebbe già essere aggiornato, nel qual caso non è necessario installare gli aggiornamenti in modalità di manutenzione.

13. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura descritta in [scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3121899, gli aggiornamenti di firmware disco installazioni (altri aggiornamenti devono essere già installati a questo punto).

13. Seguire i passaggi elencati in [installare e verificare gli aggiornamenti rapidi modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità di manutenzione.


## <a name="install-update-2-as-a-hotfix"></a>Installare l'aggiornamento 2 come aggiornamento rapido

Utilizzare questa procedura se non si riesce controllo gateway quando si tenta di installare gli aggiornamenti tramite il portale classico Azure. La verifica non riesce quando si dispone di un gateway assegnato a un'interfaccia di rete 0 non dati e il dispositivo è in esecuzione una versione del software prima dell'aggiornamento 1.

Le versioni di software che possono essere aggiornate utilizzando il metodo di correzione sono aggiornamento 0,1, 0,2, aggiornamento e aggiornamento 0,3, aggiornamento 1, 1.1 aggiornamento e aggiornamento 1.2. Il metodo di aggiornamento rapido prevede tre passaggi seguenti:

- Scaricare gli aggiornamenti rapidi dal catalogo di Microsoft Update.
- Installare e verificare gli aggiornamenti rapidi modalità normale.
- Installare e verificare che l'aggiornamento rapido modalità di manutenzione.

Per installare l'aggiornamento 2 come aggiornamento rapido, è necessario scaricare e installare gli aggiornamenti rapidi seguenti:

| Ordine  | KB        | Descrizione                    | Tipo di aggiornamento  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Aggiornamento del software         |  Regolare     |
| 2      | KB3121900 | Driver LSI              |  Regolare     |
| 3      | KB3080728 | Correzione Storport </br> Windows Server 2012 R2 |  Regolare     |
| 4      | KB3090322 | Correzione spaceport </br> Windows Server 2012 R2 |  Regolare     |
| 5      | KB3121899 | Firmware disco           | Manutenzione dei servizi  |


> [AZURE.IMPORTANT]
>
> - Se il dispositivo è in esecuzione la versione definitiva (GA), contattare il [Supporto tecnico clienti Microsoft](storsimple-contact-microsoft-support.md) per facilitare l'aggiornamento.
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 2. È possibile usare il portale classico Azure per applicare gli aggiornamenti successivi.
> - Ogni installazione può richiedere circa 20 minuti per completare. Il tempo totale installa è quasi due ore.
> - Prima di utilizzare questa procedura per applicare l'aggiornamento, verificare che entrambi i controller di dispositivo sono online e tutti i componenti hardware siano corretti.

Per applicare questo aggiornamento come aggiornamento rapido, procedere come segue.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [versione 2 di aggiornamento](storsimple-update2-release-notes.md).
