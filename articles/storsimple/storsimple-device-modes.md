<properties 
   pageTitle="Modificare la modalità del dispositivo StorSimple | Microsoft Azure"
   description="Descrive le modalità di dispositivo StorSimple e viene spiegato come utilizzare Windows PowerShell per StorSimple per modificare la modalità del dispositivo."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Modificare la modalità del dispositivo in un dispositivo StorSimple

Questo articolo fornisce una breve descrizione i diversi modi in cui è possibile operare dispositivo StorSimple. Il dispositivo StorSimple può funzionare in tre modi: normale, manutenzione e ripristino. 

Dopo aver letto in questo articolo, si saprà:

- Quali le modalità di dispositivo StorSimple sono
- Come scoprire le modalità dispositivo StorSimple è in
- Come modificare da normale alla modalità di manutenzione e *viceversa*


Le attività di gestione sopra possono essere eseguite solo tramite l'interfaccia di Windows PowerShell del dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Informazioni sulle modalità di dispositivo StorSimple

Il dispositivo StorSimple può funzionare in modalità normale, manutenzione o ripristino. Ognuna delle tre modalità seguenti brevemente descritto di seguito.

### <a name="normal-mode"></a>Modalità normale

Questo è definito come la modalità operativa normale per un dispositivo StorSimple completamente configurato. Per impostazione predefinita, il dispositivo deve essere in modalità normale.

### <a name="maintenance-mode"></a>Modalità di manutenzione

Può succedere che il dispositivo StorSimple potrebbe essere necessario è stato eseguito in modalità di manutenzione. Questa modalità consente di eseguire la manutenzione sul dispositivo e installare gli aggiornamenti dannosi, ad esempio quelli relativi alla firmware disco.

È possibile inserire il sistema in modalità di manutenzione solo tramite Windows PowerShell per StorSimple. Tutte le richieste dei / o vengono sospesi in questa modalità. Vengono interrotti anche i servizi, ad esempio non volatili RAM (NVRAM) o il servizio cluster. Entrambi i controller sono riavviati quando si entra o uscire da questa modalità. Quando si esce dalla modalità di manutenzione, tutti i servizi verranno ripresa e devono essere integri. Alcuni minuti.

>[AZURE.NOTE]Modalità di manutenzione **è supportata solo in un dispositivo che funzionano correttamente. Non è supportata in un dispositivo in cui uno o entrambi i controller non funzionano.**
</br>

### <a name="recovery-mode"></a>Modalità di ripristino

Modalità di recupero può essere descritto come "Windows modalità provvisoria con supporto di rete". Modalità di ripristino svolge il team di supporto Microsoft e consente di eseguire la diagnostica del sistema. Lo scopo principale della modalità di recupero è per recuperare i registri di sistema.

Se il sistema entra in modalità di ripristino, è necessario contattare il supporto Microsoft per la procedura successiva. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **È possibile inserire il dispositivo in modalità di ripristino. Se il dispositivo è in stato non valido, la modalità di ripristino tenta di ottenere il dispositivo in uno stato in cui possono esaminarlo agli addetti al supporto Microsoft.**

## <a name="determine-storsimple-device-mode"></a>Determinare la modalità di dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Per determinare la modalità del dispositivo corrente

1. Accedere alla console seriale dispositivo eseguendo la procedura descritta in [PuTTY utilizzare per connettersi alla console seriale dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Esaminare il messaggio di intestazione nel menu console seriale del dispositivo. Questo messaggio indica in modo esplicito se il dispositivo è in modalità di manutenzione o ripristino. Se il messaggio non contiene informazioni specifiche relative alla modalità di sistema, il dispositivo è in modalità normale.

## <a name="change-the-storsimple-device-mode"></a>Modificare la modalità del dispositivo StorSimple 

Per eseguire la manutenzione o installare gli aggiornamenti in modalità di manutenzione, è possibile posizionare il dispositivo StorSimple in modalità di manutenzione (dalla modalità normale). Per attivare o disattivare la modalità di manutenzione, procedere come segue.

> [AZURE.IMPORTANT] Prima di immettere la modalità di manutenzione, verificare che entrambi i controller dispositivo siano integri accedendo lo **Stato di Hardware** nella pagina **manutenzione** nel portale di classica Azure. Se uno o entrambi i controller non sono corretti, contattare il supporto Microsoft per la procedura successiva. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Per passare alla modalità di manutenzione

1. Accedere alla console seriale dispositivo eseguendo la procedura descritta in [PuTTY utilizzare per connettersi alla console seriale dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**. Quando richiesto, immettere la **password di amministratore di dispositivo**. La password predefinita è: `Password1`.

3. Al prompt dei comandi digitare 

    `Enter-HcsMaintenanceMode`

4. Verrà visualizzato un messaggio di avviso che indica che la modalità di manutenzione verrà interrompere tutte le richieste dei / o server la connessione al portale di classica Azure e verrà richiesto di confermare l'operazione. Digitare **Y** per passare alla modalità di manutenzione.

5. Entrambi i controller verranno riavviato. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità di manutenzione.


#### <a name="to-exit-maintenance-mode"></a>Per uscire dalla modalità di manutenzione

1. Accedere alla console seriale dispositivo. Verificare dal messaggio di intestazione in modalità di manutenzione di dispositivo.

2. Al prompt dei comandi digitare:

    `Exit-HcsMaintenanceMode`

3. Verrà visualizzato un messaggio di avviso e un messaggio di conferma. Digitare **Y** per uscire dalla modalità di manutenzione.

4. Entrambi i controller verranno riavviato. Una volta completato il riavvio, verrà visualizzato un altro messaggio che indica che il dispositivo è in modalità normale.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [applicare gli aggiornamenti in modalità normale e la manutenzione](storsimple-update-device.md) nel dispositivo StorSimple.

