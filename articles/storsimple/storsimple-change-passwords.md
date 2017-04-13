<properties 
   pageTitle="Cambiare le password StorSimple | Microsoft Azure" 
   description="In questo articolo viene descritto come utilizzare il servizio di gestione di StorSimple per modificare le password di amministratore StorSimple Snapshot responsabile e dispositivo." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Utilizzare il servizio di gestione di StorSimple per modificare la password StorSimple

## <a name="overview"></a>Panoramica 

Il portale classico Azure pagina **Configura** contiene tutti i parametri di dispositivo che è possibile riconfigurare su un dispositivo StorSimple che viene gestito da un servizio di gestione StorSimple. In questa esercitazione viene illustrato come è possibile utilizzare la pagina di **configurazione** per modificare all'amministratore del dispositivo o la password StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Cambiare la password di amministratore del dispositivo

Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo StorSimple, è necessario immettere una password di amministratore del dispositivo. Quando il primo dispositivo StorSimple è registrato con un servizio, la password predefinita per questa interfaccia è *Password1*. Per la protezione dei dati, è necessario modificare la password al termine del processo di registrazione. È non è possibile uscire dal processo di registrazione senza modificare la password. Per ulteriori informazioni, vedere [passaggio 3: configurare e registrare il dispositivo di Windows PowerShell per StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La password è stata impostata prima di tutto tramite l'interfaccia di Windows PowerShell durante la registrazione può essere modificata quindi tramite il portale classico Azure. Per cambiare la password di amministratore del dispositivo, procedere come segue.

#### <a name="to-change-the-device-administrator-password"></a>Per modificare la password di amministratore del dispositivo

1. Nel portale classico, fare clic su **dispositivi** > **Configura** del dispositivo.

2. Scorrere fino alla sezione **Password dell'amministratore del dispositivo** . Fornire una password di amministratore che contiene da 8 a 15 caratteri. La password deve essere una combinazione di 3 o più caratteri maiuscoli, minuscoli, numerici e speciali.

3. Confermare la password.

4. Fare clic su **Salva** nella parte inferiore della pagina.

La password di amministratore del dispositivo deve essere aggiornata. È possibile usare questa password modificata per accedere all'interfaccia di Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Cambiare la password StorSimple Snapshot Manager

Software StorSimple Snapshot Manager risiede l'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di locali e cloud istantanee.

Quando si configura un dispositivo di StorSimple Snapshot Manager, verrà richiesto di specificare l'indirizzo IP del dispositivo e la password per l'autenticazione il dispositivo di archiviazione. La password prima di tutto è configurata tramite l'interfaccia di Windows PowerShell. Per ulteriori informazioni, vedere [passaggio 3: configurare e registrare il dispositivo di Windows PowerShell per StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

La password è stata impostata prima di tutto tramite l'interfaccia di Windows PowerShell durante la registrazione può essere modificata quindi tramite il portale classico. Per cambiare la password StorSimple Snapshot Manager, procedere come segue.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Per modificare la password StorSimple Snapshot Manager

1. Nel portale classico, fare clic su **dispositivi** > **Configura** del dispositivo.

2. Scorrere fino alla sezione **StorSimple Snapshot Manager** . Immettere una password con caratteri 14 o 15. Assicurarsi che la password contiene una combinazione di 3 o più caratteri maiuscoli, minuscoli, numerici e speciali.

3. Confermare la password.

4. Fare clic su **Salva** nella parte inferiore della pagina.

La password StorSimple Snapshot Manager deve essere aggiornata.
 

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulla [protezione StorSimple](storsimple-security.md).

- Ulteriori informazioni sulla [Modifica di configurazione del dispositivo](storsimple-modify-device-config.md).

- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
