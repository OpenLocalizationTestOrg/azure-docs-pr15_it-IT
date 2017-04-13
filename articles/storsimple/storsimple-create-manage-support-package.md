<properties
   pageTitle="Creare un pacchetto di supporto StorSimple | Microsoft Azure"
   description="Informazioni su come creare, decrittografare e modificare un pacchetto di supporto per il dispositivo StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Creare e gestire un pacchetto di supporto StorSimple

## <a name="overview"></a>Panoramica

Un pacchetto di supporto StorSimple è un meccanismo di semplice utilizzo che raccoglie tutti i log pertinenti per facilitare il supporto Microsoft sulla risoluzione dei problemi di dispositivo StorSimple. I registri raccolti vengono crittografati e compressi.

In questa esercitazione include istruzioni dettagliate per creare e gestire il pacchetto di supporto.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Creare e caricare un pacchetto di supporto nel portale di classica Azure

È possibile creare e caricare un pacchetto di supporto nel sito di supporto Microsoft tramite la pagina **manutenzione** del servizio nel portale di classica Azure.

> [AZURE.NOTE] Il caricamento richiede una passkey di supporto. Il supporto tecnico dovrebbe fornire questo all'utente in un messaggio di posta elettronica.

Un pacchetto di supporto crittografato e compresso (file CAB) viene creato e caricato nel sito di supporto. Supporto tecnico quindi possibile recuperare il pacchetto dal sito di supporto per la risoluzione del problema.

Nel portale di classico per creare un pacchetto di supporto, procedere come segue.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Per creare un pacchetto di supporto nel portale di classica Azure

1. Selezionare i **dispositivi** > **manutenzione**.

2. Nella sezione **supporto pacchetto** selezionare **Carica e Crea pacchetto di supporto**.

3. Nella finestra di dialogo **Crea e Carica pacchetto di supporto** , eseguire le operazioni seguenti:

    ![Crea pacchetto di supporto](./media/storsimple-create-manage-support-package/IC740923.png)

    - Nella casella di testo **Supporto Passkey** immetterla. Il supporto tecnico Microsoft invierà questa passkey all'utente di posta elettronica.

    - Selezionare la casella di controllo per fornire il consenso per caricare automaticamente il pacchetto di supporto per il sito del supporto Microsoft.

    - Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Creare manualmente un pacchetto di supporto

In alcuni casi, è necessario creare manualmente il pacchetto di supporto tramite Windows PowerShell per StorSimple. Per esempio:

- Se è necessario rimuovere informazioni riservate dai file di log prima di condividere con supporto Microsoft.

- Se si verificano problemi durante il caricamento del pacchetto a causa di problemi di connettività.

È possibile condividere il pacchetto di supporto generato manualmente con supporto Microsoft tramite posta elettronica. Per creare un pacchetto di supporto tecnico in Windows PowerShell per StorSimple, procedere come segue.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Creare un pacchetto di supporto tecnico in Windows PowerShell per StorSimple

1. Per avviare una sessione di Windows PowerShell come un amministratore del computer remoto utilizzato per la connessione al dispositivo StorSimple, immettere il comando seguente:

    `Start PowerShell`

2. La sessione di Windows PowerShell, connettersi alla SSAdmin Console del dispositivo:

    - Al prompt dei comandi immettere:

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Nella finestra di dialogo visualizzata, immettere la password di amministratore del dispositivo. La password predefinita è:

        `Password1`

        ![Finestra di dialogo credenziali di PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Fare clic su **OK**.
    1. Al prompt dei comandi immettere:

        `Enter-PSSession $MS`

3. Nella sessione che si apre, immettere il comando appropriato.

    - Condivisioni di rete che sono protetti da password, immettere:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Verrà richiesto di una password, un percorso per la cartella di rete condivisa e una passphrase di crittografia (perché il pacchetto di supporto è crittografato). Un pacchetto di supporto viene quindi creato nella cartella specificata.

    - Per le azioni che non sono protetti da password, non è necessaria il `-Credential` parametro. Immettere quanto segue:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        Viene creato il pacchetto di supporto per entrambi i controller della cartella di rete specificato. È un file compresso e crittografato che può essere inviato al supporto Microsoft per la risoluzione dei problemi. Per ulteriori informazioni, vedere [Contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>I parametri di cmdlet esportazione HcsSupportPackage
È possibile utilizzare i parametri seguenti con i cmdlet di esportazione HcsSupportPackage.

| Parametro            | Obbligatorie/facoltative | Descrizione                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Obbligatorio          | Consente di specificare il percorso della cartella condivisa in cui è posizionato il pacchetto di supporto.                                                                 |
| `-EncryptionPassphrase` | Obbligatorio          | Utilizzare per fornire una frase per crittografare il pacchetto di supporto.                                                                                                        |
| `-Credential`           | Facoltativo          | Consente di fornire le credenziali di accesso per la cartella di rete condivisa.                                                                                        |
| `-Force`                | Facoltativo          | Consente di ignorare il passaggio di conferma passphrase la crittografia.                                                                                                                |
| `-PackageTag`           | Facoltativo          | Consente di specificare una directory nel *percorso* in cui è posizionato il pacchetto di supporto. Il valore predefinito è [nome del dispositivo]-[data corrente e time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Facoltativo          | Specificare come **Cluster** (impostazione predefinita) per creare un pacchetto di supporto per entrambi i controller. Se si desidera creare un pacchetto solo per il controller corrente, specificare **Controller**. |


## <a name="edit-a-support-package"></a>Modificare un pacchetto di supporto

Dopo aver creato un pacchetto di supporto, potrebbe essere necessario modificare il pacchetto per rimuovere informazioni riservate. Può trattarsi di volume nomi, indirizzi IP di dispositivo e backup nomi file di log.

> [AZURE.IMPORTANT] È possibile modificare solo un pacchetto di supporto generati tramite Windows PowerShell per StorSimple. È possibile modificare un pacchetto creato nel portale di classica Azure con il servizio di gestione StorSimple.

Per modificare un pacchetto di supporto prima di caricare nel sito del supporto Microsoft, prima di tutto decrittografare il pacchetto di supporto, modificare i file e quindi applicare nuovamente la. Eseguire la procedura seguente.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Per modificare un pacchetto di supporto tecnico in Windows PowerShell per StorSimple

1. Generare un pacchetto di supporto, come descritto in precedenza, in [per creare un pacchetto di supporto tecnico in Windows PowerShell per StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Scaricare lo script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) in locale nel client.

3. Importare il modulo Windows PowerShell. Specificare il percorso della cartella locale in cui è stato scaricato lo script. Per importare il modulo, immettere:

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Tutti i file sono file di *.aes* che compresso e crittografati. Per decomprimere e decrittografare i file, immettere:

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Nota le estensioni di file effettivo sono visualizzate per tutti i file.

    ![Modificare il pacchetto di supporto](./media/storsimple-create-manage-support-package/IC750706.png)

5. Quando viene richiesto per la crittografia passphrase, immettere la passphrase utilizzata per il supporto di installazione è stato creato.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Passare alla cartella contenente i file di log. Perché i file di log sono ora decompressi e decrittografare, avranno le estensioni di file originale. Modificare i file per rimuovere le informazioni specifiche del cliente, ad esempio volume nomi e indirizzi IP dispositivo e salvare i file.

7. Chiudere i file per comprimerli con gzip e crittografia AES 256. Si tratta di velocità e la sicurezza in trasferimento il pacchetto di supporto in rete. Per comprimere e crittografare i file, immettere quanto segue:

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Modificare il pacchetto di supporto](./media/storsimple-create-manage-support-package/IC750707.png)

8. Quando richiesto, fornire una passphrase di crittografia per il pacchetto di supporto modificate.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Prendere nota nuova passphrase, in modo da poterla condividere con Microsoft Support quando richiesto.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Esempio: Modifica dei file in un pacchetto di supporto in una condivisione protetto da password

Nell'esempio seguente viene illustrato come decrittografare, modificare e crittografare nuovamente un pacchetto di supporto.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare pacchetti di supporto e i registri di dispositivo per risolvere i problemi di distribuzione di dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
