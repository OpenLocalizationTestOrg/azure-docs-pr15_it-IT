<properties
   pageTitle="Installare l'aggiornamento 3 in un dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come installare l'aggiornamento StorSimple 8000 serie 3 in un dispositivo di serie 8000 StorSimple."
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>Installare l'aggiornamento 3 in un dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come installare l'aggiornamento 3 in un dispositivo StorSimple in esecuzione una versione precedente di software tramite il portale classico Azure e utilizzando il metodo di correzione. Il metodo di correzione viene utilizzato quando è configurato un gateway nell'interfaccia di rete diversa da quella dati 0 del dispositivo StorSimple e si sta tentando di aggiornamento da una versione del software 1 pre-aggiornamento.

Aggiornamento 3 include dispositivo software, driver LSI e firmware, Storport e Spaceport aggiornamenti. Se l'aggiornamento da aggiornamento 2 o una versione precedente, anche è necessario applicare iSCSI, WMI e in alcuni casi, su disco aggiornamenti del firmware. Il software di dispositivo WMI, iSCSI, LSI driver, Spaceport e Storport correzioni gli aggiornamenti non dannosi e possono essere applicate tramite il portale classico Azure. Gli aggiornamenti di firmware disco la disponibilità di aggiornamenti e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. 

> [AZURE.IMPORTANT]

> - Una serie di pre-controlli manuali e automatiche vengono eseguite prima dell'installazione per determinare lo stato di dispositivo in termini di connettività di rete e stato hardware. Questi controlli non vengono eseguiti solo se si applicano gli aggiornamenti dal portale di classica Azure.
> - È consigliabile installare gli aggiornamenti software e driver tramite il portale classico Azure. Devono essere inseriti solo all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti), se il controllo di pre-aggiornamento gateway non riesce nel portale. A seconda della versione che si sta aggiornando, gli aggiornamenti potrebbero richiedere ore 1,5-2,5 per l'installazione. Tramite l'interfaccia di Windows PowerShell del dispositivo, è necessario installare gli aggiornamenti in modalità di manutenzione. Come gli aggiornamenti in modalità di manutenzione aggiornamenti dannosi, questi verrà generato un orario verso il basso per il dispositivo.
> - Se la gestione di Snapshot StorSimple facoltativi è in esecuzione, assicurarsi di aver aggiornato alla versione di gestione di Snapshot di aggiornamento 2 prima dell'aggiornamento il dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Installare l'aggiornamento 3 tramite il portale classico Azure

Per aggiornare il dispositivo per [aggiornare i 3](storsimple-update3-release-notes.md), procedere come segue.


> [AZURE.NOTE]
Se si applicano aggiornamento 2 o versione successiva (comprese aggiornamento 2.1), Microsoft sarà possibile estrarre informazioni diagnostiche aggiuntive dal dispositivo. Di conseguenza, quando il nostro team operazioni identifica dispositivi che si verificano problemi, sono migliore per raccogliere informazioni dal dispositivo e diagnosticare i problemi. Accettando aggiornamento 2 o versione successiva, si consentono di fornire questo supporto preventivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificare che il dispositivo sia in esecuzione **StorSimple 8000 serie Update 3 (6.3.9600.17759)**. **Ultimo aggiornamento data** devono essere modificati. 

    Se si sta aggiornando da una versione precedente dell'aggiornamento 2, si vedrà anche che gli aggiornamenti in modalità di manutenzione sono disponibili (questo messaggio potrebbe continuano a essere visualizzati fino a 24 ore dopo l'installazione degli aggiornamenti).

    Gli aggiornamenti in modalità di manutenzione sono dannosi aggiornamenti che risultato del tempo di inattività dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo. In alcuni casi quando si esegue l'aggiornamento 1.2 del firmware disco potrebbe già essere aggiornato, nel qual caso non è necessario installare gli aggiornamenti in modalità di manutenzione.

    Se si è l'aggiornamento da aggiornamento 2 o versioni successive, il dispositivo dovrebbe ora essere aggiornato. È possibile ignorare i passaggi rimanenti.

13. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura descritta in [scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3121899, gli aggiornamenti di firmware disco installazioni (altri aggiornamenti devono essere già installati a questo punto).

13. Seguire i passaggi elencati in [installare e verificare gli aggiornamenti rapidi modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità di manutenzione. 

  

## <a name="install-update-3-as-a-hotfix"></a>Installare l'aggiornamento 3 come aggiornamento rapido

Utilizzare questa procedura se non si riesce controllo gateway quando si tenta di installare gli aggiornamenti tramite il portale classico Azure. La verifica non riesce quando si dispone di un gateway assegnato a un'interfaccia di rete 0 non dati e il dispositivo è in esecuzione una versione del software prima dell'aggiornamento 1.

Le versioni di software che possono essere aggiornate utilizzando il metodo di correzione sono:

- Aggiornare 0,1, 0,2, 0,3
- Aggiornare 1, 1.1, 1.2
- Aggiornare 2, 2.1, 2.2 

> [AZURE.IMPORTANT]
>
> - Se il dispositivo è in esecuzione la versione definitiva (GA), contattare il [Supporto tecnico clienti Microsoft](storsimple-contact-microsoft-support.md) per facilitare l'aggiornamento.

Il metodo di aggiornamento rapido prevede tre passaggi seguenti:

1.  Scaricare gli aggiornamenti rapidi dal catalogo di Microsoft Update.

2.  Installare e verificare gli aggiornamenti rapidi modalità normale.

3.  Installare e verificare che l'aggiornamento rapido modalità di manutenzione (solo durante l'aggiornamento da pre-aggiornamento software 2).


#### <a name="download-updates-for-your-device"></a>Scaricare gli aggiornamenti del dispositivo

**Se il dispositivo è in esecuzione aggiornamento 2.1 o 2.2**, è necessario scaricare e installare gli aggiornamenti rapidi seguenti nell'ordine indicato:

| Ordine  | KB        | Descrizione                    | Tipo di aggiornamento  | Fase di installazione |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3186843 | Aggiornamento software & #42;  |  Regolare <br></br>Senza interruzione     | ~ 45 min. |
| 2.      | KB3186859 | Firmware e driver LSI             |  Regolare <br></br>Senza interruzione      | ~ 20 min. |
| 3.      | KB3121261 | Correzione Storport e Spaceport </br> Windows Server 2012 R2 |  Regolare <br></br>Senza interruzione      | ~ 20 min. |

& #42;  Aggiornamento *Nota, software è costituito da due file binari: aggiornamento software dispositivo preceduto `all-hcsmdssoftwareupdate` e l'agente Cis e strategia di download minima preceduto `all-cismdsagentupdatebundle`. Prima dell'agente Cis e strategia di download minima, è necessario installare l'aggiornamento di software di dispositivo. È necessario riavviare il controller attivo tramite il `Restart-HcsController` cmdlet dopo l'applicazione agente Cis e strategia di download minima aggiornare (e prima che l'applicazione il rimanente aggiorni).* 


**Se il dispositivo è in esecuzione aggiornamento 0,1, 0,2, 0,3, 1.0, 1.1, 1.2 o 2.0**, è necessario scaricare e installare gli aggiornamenti rapidi seguenti oltre il, driver LSI aggiornamenti software e firmware (mostrati nella tabella precedente), nell'ordine indicato:

| Ordine  | KB        | Descrizione                    | Tipo di aggiornamento  | Fase di installazione |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3146621 | pacchetto iSCSI | Regolare <br></br>Senza interruzione  | ~ 20 min. |
| 5.      | KB3103616 | Pacchetto WMI |  Regolare <br></br>Senza interruzione      | ~ 12 min. |


<br></br>

**Se il dispositivo è in esecuzione versioni 0,2, 0,3, 1.0, 1.1 e 1.2**, è necessario installare gli aggiornamenti firmware disco nella parte superiore di tutti gli aggiornamenti visualizzati nelle tabelle precedenti. È possibile verificare se è necessario gli aggiornamenti di firmware disco eseguendo la `Get-HcsFirmwareVersion` cmdlet. Se si esegue queste versioni firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, quindi non è necessario installare questi aggiornamenti.


| Ordine  | KB        | Descrizione                    | Tipo di aggiornamento  | Fase di installazione |
|--------|-----------|-------------------------|------------- |-------------|
| 6.      | KB3121899 | Firmware disco              |  Manutenzione dei servizi <br></br>Dannosi      | ~ 30 minuti |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 3. È possibile usare il portale classico Azure per applicare gli aggiornamenti successivi.
> - Se l'aggiornamento da 2.2 aggiornamento, il tempo totale installa è vicino 1.1 ore.
> - Prima di utilizzare questa procedura per applicare l'aggiornamento, assicurarsi che sia controller dispositivo è online e tutti i componenti hardware siano corretti.

Per scaricare e installare gli aggiornamenti rapidi, procedere come segue.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [versione 3 di aggiornamento](storsimple-update3-release-notes.md).
