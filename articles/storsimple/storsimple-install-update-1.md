<properties
   pageTitle="Installare Aggiornamento 1.2 sul dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come installare StorSimple 8000 serie aggiornamento 1.2 sul dispositivo serie 8000 StorSimple."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Installare l'aggiornamento 1.2 sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come installare Aggiornamento 1.2 in un dispositivo StorSimple che esegue una versione del software prima dell'aggiornamento 1. L'esercitazione copre anche i passaggi aggiuntivi necessari per l'aggiornamento quando è configurato un gateway nell'interfaccia di rete diversa da quella dati 0 del dispositivo StorSimple.

Aggiornamento 1.2 include aggiornamenti software dispositivo, gli aggiornamenti dei driver LSI e firmware disco. Il software e aggiornamenti dei driver LSI gli aggiornamenti non dannosi e possono essere applicati tramite il portale classico Azure. Gli aggiornamenti di firmware disco la disponibilità di aggiornamenti e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

A seconda della versione del dispositivo è in esecuzione, è possibile determinare se 1.2 aggiornamento verrà applicato. È possibile controllare la versione del software del dispositivo, passare alla sezione **occhio** del dispositivo **Dashboard**.

</br>

| Se in esecuzione la versione del software...   | Cosa accade nel portale di?                              |
|---------------------------------|--------------------------------------------------------------|
| Versione - GA                    | Se si esegue la versione definitiva (GA), non applicare questo aggiornamento. Informazioni [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per aggiornare il dispositivo.|
| Aggiornamento 0,1                      | Portale applica aggiornamento 1.2.                                |
| Aggiornamento 0,2                      | Portale applica aggiornamento 1.2.                                |
| Aggiornamento 0,3                      | Portale applica aggiornamento 1.2.                                |
| Aggiornamento 1                        | Questo aggiornamento non saranno disponibile.                           |
| Aggiornamento 1.1                      | Questo aggiornamento non saranno disponibile.                           |

</br>

> [AZURE.IMPORTANT]

> -  Potrebbe non essere visualizzato aggiornamento 1.2 immediatamente perché facciamo graduale degli aggiornamenti. Cercare gli aggiornamenti in pochi giorni nuovamente come questo aggiornamento sarà disponibile presto.
> - Questo aggiornamento include una serie di pre-controlli manuale e automatici per determinare lo stato di dispositivo in termini di connettività di rete e stato hardware. Questi controlli non vengono eseguiti solo se si applicano gli aggiornamenti dal portale di classica Azure.
> - È consigliabile installare gli aggiornamenti software e driver tramite il portale classico Azure. Devono essere inseriti solo all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti), se il controllo di pre-aggiornamento gateway non riesce nel portale. Gli aggiornamenti potrebbero richiedere ore 5 a 10 per installare (inclusi gli aggiornamenti di Windows). Tramite l'interfaccia di Windows PowerShell del dispositivo, è necessario installare gli aggiornamenti in modalità di manutenzione. Come gli aggiornamenti in modalità di manutenzione aggiornamenti dannosi, questi verrà generato un orario verso il basso per il dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Installare l'aggiornamento 1.2 tramite il portale classico Azure

Per aggiornare il dispositivo per [aggiornare 1.2](storsimple-update1-release-notes.md), procedere come segue. Utilizzare questa procedura solo se si dispone di un gateway configurato sull'interfaccia di rete 0 dati nel dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificare che il dispositivo sia in esecuzione **StorSimple 8000 serie aggiornamento 1.2 (6.3.9600.17584)**. **Ultimo aggiornamento data** devono essere modificati. Verrà visualizzata anche che sono disponibili aggiornamenti in modalità di manutenzione (questo messaggio potrebbe continuano a essere visualizzati fino a 24 ore dopo l'installazione degli aggiornamenti).

    Gli aggiornamenti in modalità di manutenzione sono dannosi aggiornamenti che risultato del tempo di inattività dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

    ![Pagina Manutenzione] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Pagina Manutenzione")

13. Scaricare gli aggiornamenti in modalità di manutenzione utilizzando la procedura descritta in [scaricare gli aggiornamenti rapidi]( #to-download-hotfixes) per cercare e scaricare KB3063416, gli aggiornamenti di firmware disco installazioni (altri aggiornamenti devono essere già installati a questo punto).

13. Seguire i passaggi elencati in [installare e verificare gli aggiornamenti rapidi modalità di manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità di manutenzione.

14. Nel portale di classica Azure, passare alla pagina **manutenzione** e nella parte inferiore della pagina, fare clic su **Analisi aggiornamenti** per controllare gli aggiornamenti di Windows e quindi fare clic su **Installa aggiornamenti**. Termine dopo tutti gli aggiornamenti vengono installati correttamente.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Installare l'aggiornamento 1.2 in un dispositivo che contiene un gateway configurato per l'interfaccia 0 rete dati non

Utilizzare questa procedura solo se non si riesce controllo gateway quando si tenta di installare gli aggiornamenti tramite il portale classico Azure. La verifica non riesce quando si dispone di un gateway assegnato a un'interfaccia di rete 0 non dati e il dispositivo è in esecuzione una versione del software prima dell'aggiornamento 1. Se il dispositivo non è disponibile un gateway su un'interfaccia di rete 0 non dati, è possibile aggiornare il dispositivo direttamente dal portale di classica Azure. Vedere [installare aggiornare 1.2 tramite il portale classico Azure](#install-update-1.2-via-the-azure-classic-portal).

Le versioni di software che possono essere aggiornate con questo metodo sono Update 0,1, aggiornamento 0,2 e Update 0,3.


> [AZURE.IMPORTANT]
>
> - Se il dispositivo è in esecuzione la versione definitiva (GA), contattare il [Supporto tecnico clienti Microsoft](storsimple-contact-microsoft-support.md) per facilitare l'aggiornamento.
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 1.2. È possibile usare il portale classico Azure per applicare gli aggiornamenti successivi.

Se il dispositivo è in esecuzione pre-aggiornamento 1 software e dispone di un gateway impostato per un'interfaccia di rete diversa da quella dati 0, è possibile applicare aggiornamento 1.2 in due modi seguenti:

- **Opzione 1**: scaricare l'aggiornamento e applicarlo utilizzando il `Start-HcsHotfix` cmdlet dall'interfaccia di Windows PowerShell del dispositivo. Questo è il metodo consigliato. **Non utilizzare questo metodo per applicare aggiornamento 1.2 se il dispositivo è in esecuzione Update 1.0 o 1.1 di aggiornamento.**

- **Opzione 2**: rimuovere la configurazione di gateway e installare l'aggiornamento direttamente dal portale di classica Azure.


Nelle sezioni seguenti vengono fornite istruzioni dettagliate per ciascuno di essi.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Opzione 1: Usare Windows PowerShell per StorSimple applicare aggiornamento 1.2 come aggiornamento rapido

Utilizzare questa procedura solo se si esegue l'aggiornamento 0,1, 0,2, 0,3 e se il controllo di gateway non riuscita quando si tenta di installare gli aggiornamenti dal portale di classica Azure. Se si esegue il software di rilascio (GA), informazioni di [Supporto tecnico clienti Microsoft](storsimple-contact-microsoft-support.md) per aggiornare il dispositivo.

Per installare l'aggiornamento 1.2 come aggiornamento rapido, è necessario scaricare e installare gli aggiornamenti rapidi seguenti:

| Ordine  | KB        | Descrizione             | Tipo di aggiornamento  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Aggiornamento del software         |  Regolare     |
| 2      | KB3043005 | Aggiornamento di controller LSI SA |  Regolare     |
| 3      | KB3063416 | Firmware disco           | Manutenzione dei servizi  |

Prima di utilizzare questa procedura per applicare l'aggiornamento, verificare quanto segue:

- Entrambi i controller di dispositivo sono online.

Per applicare l'aggiornamento 1.2, procedere come segue. **Gli aggiornamenti potrebbero richiedere circa 2 ore (entro 30 minuti per software, 30 minuti per driver, 45 minuti per firmware disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Opzione 2: Usare il portale classico Azure applicare aggiornamento 1.2 dopo la rimozione della configurazione di gateway

Questa procedura si applica solo ai dispositivi StorSimple che eseguono una versione del software prima dell'aggiornamento 1 e impostare un gateway nell'interfaccia di rete diversa da quella dati 0. È necessario deselezionare le impostazioni del gateway prima dell'applicazione dell'aggiornamento.

L'aggiornamento può richiedere alcune ore per completare. Se la si trovano in subnet diverse, rimozione della configurazione di gateway su interfacce iSCSI potrebbe causare il tempo di inattività. È consigliabile configurare dati 0 per il traffico iSCSI per ridurre i tempi di inattività.

Per disattivare l'interfaccia di rete con il gateway e quindi applicare l'aggiornamento, procedere come segue.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sulla versione 1.2 di aggiornamento](storsimple-update1-release-notes.md).
