<properties
   pageTitle="Aggiornare il dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come utilizzare la funzionalità di aggiornamento StorSimple per installare correzioni e gli aggiornamenti in modalità normale e la manutenzione."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Aggiornare il dispositivo StorSimple 8000 serie

## <a name="overview"></a>Panoramica

La funzionalità di aggiornamenti StorSimple consentono di mantenere facilmente aggiornati dispositivo StorSimple. A seconda del tipo di aggiornamento, è possibile applicare gli aggiornamenti al dispositivo tramite il portale classico Azure oppure tramite l'interfaccia di Windows PowerShell. In questa esercitazione vengono illustrati i tipi di aggiornamento e su come installare ciascuno di essi.

È possibile applicare due tipi di aggiornamenti di dispositivo: 

- Regolare (o modalità normale) aggiornamenti
- Aggiornamenti in modalità di manutenzione

È possibile installare gli aggiornamenti regolarmente tramite il portale di classica Azure e Windows PowerShell; Tuttavia, è necessario utilizzare Windows PowerShell per installare gli aggiornamenti in modalità di manutenzione. 

Ogni tipo di aggiornamento è descritto separatamente, sotto.

### <a name="regular-updates"></a>Aggiornamenti regolari

Regolarmente sono aggiornamenti non dannosi che possono essere installati quando il dispositivo è in modalità normale. Gli aggiornamenti vengono applicati tramite il sito Web Microsoft Update a ogni controller di dispositivo. 

> [AZURE.IMPORTANT] Controller caso di errore può verificarsi durante il processo di aggiornamento. Tuttavia, questa operazione non modificherà la disponibilità del sistema o un'operazione.

- Per informazioni dettagliate su come installare gli aggiornamenti regolarmente tramite il portale classico Azure, vedere [Installa aggiornamenti regolari tramite il portal(#install-regular-updates-via-the-azure-classic-portal) classica Azure.

- È anche possibile installare regolarmente tramite Windows PowerShell per StorSimple. Per informazioni dettagliate, vedere [installare regolarmente tramite Windows PowerShell per StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aggiornamenti in modalità di manutenzione

Gli aggiornamenti in modalità di manutenzione sono aggiornamenti dannosi, ad esempio aggiornamento del firmware disco. Questi aggiornamenti richiedono il dispositivo da inserire nella modalità di manutenzione. Per informazioni dettagliate, vedere [passaggio 2: modalità di manutenzione immettere](#step2). È possibile usare il portale classico Azure per installare gli aggiornamenti in modalità di manutenzione. Se, tuttavia, è necessario utilizzare Windows PowerShell per StorSimple. 

Per informazioni dettagliate su come installare gli aggiornamenti in modalità di manutenzione, vedere [gli aggiornamenti in modalità di installazione manutenzione tramite Windows PowerShell per StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Gli aggiornamenti in modalità di manutenzione devono essere applicati singolarmente a ogni controller. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installare gli aggiornamenti regolari tramite il portale classico Azure

È possibile utilizzare il portale classico Azure per applicare gli aggiornamenti al dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installare gli aggiornamenti regolarmente tramite Windows PowerShell per StorSimple

In alternativa, è possibile utilizzare Windows PowerShell per StorSimple per applicare gli aggiornamenti normale (modalità normale).

> [AZURE.IMPORTANT] Sebbene sia possibile installare regolarmente usando Windows PowerShell per StorSimple, è consigliabile installare aggiornamenti periodici tramite il portale classico Azure. A partire da 1 aggiornamento, i pre-controlli vengono eseguiti prima di installare gli aggiornamenti dal portale. Questi pre-controlli verranno priorità errori e garantire un'esperienza più uniforme. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installare gli aggiornamenti in modalità di manutenzione tramite Windows PowerShell per StorSimple

Utilizzare Windows PowerShell per StorSimple per applicare gli aggiornamenti in modalità di manutenzione nel dispositivo StorSimple. Tutte le richieste dei / o vengono sospesi in questa modalità. Vengono interrotti anche i servizi, ad esempio non volatili RAM (NVRAM) o il servizio cluster. Entrambi i controller sono riavviati quando si entra o uscire da questa modalità. Quando si esce da questa modalità, tutti i servizi verranno ripresa e devono essere integri. (Questa operazione potrebbe richiedere alcuni minuti).

Se è necessario applicare gli aggiornamenti in modalità di manutenzione, viene visualizzato un avviso tramite il portale classico Azure sono gli aggiornamenti da installare. Questo avviso contengono le istruzioni per l'uso di Windows PowerShell per StorSimple per installare gli aggiornamenti. Dopo l'aggiornamento del dispositivo, utilizzare la stessa procedura per modificare il dispositivo in modalità normale. Per istruzioni dettagliate, vedere [passaggio 4: modalità di manutenzione di uscita](#step4).

> [AZURE.IMPORTANT] 
> 
> - Prima di immettere la modalità di manutenzione, verificare che entrambi i controller dispositivo siano integri selezionando lo **Stato di Hardware** nella pagina **manutenzione** nel portale di classica Azure. Se il controller non è integro, contattare il supporto Microsoft per la procedura successiva. Per ulteriori informazioni, vedere contattare il supporto tecnico Microsoft. 
> - Quando si è in modalità di manutenzione, è necessario applicare prima dell'aggiornamento in un controller e quindi su altri controller.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Passaggio 1: Connettersi a console seriale<a name="step1">

Usare un'applicazione, ad esempio PuTTY per accedere alla console seriale. La procedura seguente viene illustrato come usare PuTTY per connettersi alla console seriale.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Passaggio 2: Passare alla modalità di manutenzione<a name="step2">

Dopo la connessione alla console, determinare se sono presenti gli aggiornamenti a installare e passare alla modalità di manutenzione per installarli.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Passaggio 3: Installare gli aggiornamenti<a name="step3">

Successivamente, installare gli aggiornamenti.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Passaggio 4: Modalità di manutenzione di uscita<a name="step4">

Infine, uscire dalla modalità di manutenzione.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installare gli aggiornamenti rapidi tramite Windows PowerShell per StorSimple

A differenza di aggiornamenti di Microsoft Azure StorSimple, aggiornamenti rapidi sono installati da una cartella condivisa. Come per gli aggiornamenti, esistono due tipi di correzioni: 

- Regolare correzioni 
- Correzioni modalità di manutenzione  

Le procedure seguenti viene illustrato come utilizzare Windows PowerShell per StorSimple per installare normale e correzioni modalità manutenzione.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Cosa succede ai aggiornamenti se si esegue il ripristino del produttore del dispositivo?

Se un dispositivo è impostato su impostazioni del produttore, tutti gli aggiornamenti vengono persi. Dopo il dispositivo di reimpostazione factory è registrato e configurato, è necessario installare manualmente gli aggiornamenti tramite il portale classica Azure e/o Windows PowerShell per StorSimple. Per ulteriori informazioni su factory reimpostare, vedere [reimpostare il dispositivo per le impostazioni predefinite](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'uso di Windows PowerShell per StorSimple amministrare il dispositivo StorSimple](storsimple-windows-powershell-administration.md).
- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
