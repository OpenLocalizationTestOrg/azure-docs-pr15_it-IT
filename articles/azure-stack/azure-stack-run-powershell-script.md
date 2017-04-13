<properties
    pageTitle="Distribuire Stack Azure prova | Microsoft Azure"
    description="Informazioni su come preparare la prova pratica dello Stack di Azure ed eseguire lo script di PowerShell per distribuire Azure Stack prova."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Distribuire Stack Azure prova
Per distribuire la prova pratica dello Stack di Azure, è innanzitutto necessario [preparare il computer di distribuzione](#prepare-the-deployment-machine) , quindi scegliere [Esegui script di PowerShell distribuzione](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Scaricare ed estrarre TP2 prova Stack di Microsoft Azure

Prima di iniziare, verificare che si almeno 85 GB di spazio.

1. Download di Azure Stack prova TP2 è costituito da un file zip contenente i seguenti 12 file, per un totale ~ 20 GB:
    - 1 MicrosoftAzureStackPOC.EXE
2. Esaminare le informazioni della procedura guidata per l'estrazione e schermata Contratto di licenza e quindi fare clic su **Avanti**.
3. Esaminare le informazioni della procedura guidata per l'estrazione e schermata informativa sulla Privacy e quindi fare clic su **Avanti**.
4. Selezionare la destinazione per i file estratti, fare clic su **Avanti**.
    - Il valore predefinito è: <drive letter>:\<cartella corrente > \Microsoft Azure Stack prova
5. Esaminare la schermata di percorso di destinazione e le informazioni della procedura guidata per l'estrazione e quindi fare clic su **Estrai** per estrarre i file di ThirdPartyLicenses.rtf e CloudBuilder.vhdx (~44.5 GB).

> [AZURE.NOTE] Dopo avere estratto i file, è possibile eliminare il file zip per recuperare spazio nel computer. In alternativa, è possibile spostare il file zip in un'altra posizione, in modo che, se è necessario ridistribuire è non è necessario scaricare di nuovo i file zip.

## <a name="prepare-the-deployment-machine"></a>Preparare il computer di distribuzione

1. Assicurarsi che è possibile fisica connettersi al computer di distribuzione, o avere accesso console fisica (ad esempio KVM). È necessario l'accesso dopo il riavvio del computer di distribuzione nel passaggio 9.

2. Verificare che il computer di distribuzione soddisfi i [requisiti minimi](azure-stack-deploy.md). È possibile utilizzare il [Controllo di distribuzione per Azure Stack Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) per confermare la tua richiesta.

3. Effettuare l'accesso come amministratore locale nel computer di prova.

4. Copiare il file CloudBuilder.vhdx C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Se si sceglie di non utilizzare lo script consigliato per preparare il computer host di prova (i passaggi da 5-passaggio 7), non immettere un codice di licenza nella pagina di attivazione. Una versione di valutazione di Windows Server 2016 immagine è inclusa e immettendo un codice di licenza genera messaggi di avviso di scadenza.

5. Nel computer di prova, eseguire il seguente script di PowerShell per scaricare i file di supporto di Azure Stack TP2:

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Questo script Scarica i file di supporto di Azure Stack TP2 nella cartella specificata dal parametro $LocalPath.
    
6. Aprire una console di PowerShell con privilegi elevata e passare alla directory in cui sono stati copiati i file.
    - 11 N.BIN MicrosoftAzureStackPOC (dove N è 1-11)
7. Fare clic sul MicrosoftAzureStackPOC.EXE > Esegui come amministratore.

8. Eseguire lo script PrepareBootFromVHD.ps1. Questo script e i file di installazione automatica sono disponibili con altri script di supporto fornito insieme a questa compilazione.
    Sono disponibili cinque parametri per questo script di PowerShell:
    - CloudBuilderDiskPath (obbligatorio): il percorso CloudBuilder.vhdx nell'HOST.
    - DriverPath (facoltativo): consente di aggiungere driver aggiuntivi per l'host in HD virtuale.
    - ApplyUnattend (facoltativo): specificare questo parametro per automatizzare la configurazione del sistema operativo. Se specificato, l'utente deve fornire AdminPassword per configurare il sistema operativo all'avvio (necessari purché accompagnamento file unattend_NoKVM.xml).
    Se non si utilizza questo parametro, viene utilizzato il file Unattend generico senza ulteriore personalizzazione. Sarà necessario KVM personalizzazione completa dopo il riavvio.
    - (Facoltativo): AdminPassword utilizzato solo quando il parametro ApplyUnattend è impostato, è necessario un minimo di otto caratteri.
    - VHDLanguage (facoltativo)-specifica la lingua del disco rigido virtuale, impostata su "en-US".
    Lo script descritte e contiene l'utilizzo di esempio, se l'utilizzo più comune:
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Se si esegue il comando esatto, è necessario immettere AdminPassword quando viene richiesto.

9. Una volta completato lo script, è necessario confermare il riavvio del computer. Se sono presenti altri utenti connessi, questo comando non viene eseguito. Se il comando non riesce, eseguire il comando seguente:`Restart-Computer -force` 

10. L'HOST riavvia nel sistema operativo del CloudBuilder.vhdx, in cui la distribuzione continua.

> [AZURE.IMPORTANT] Pila di Azure richiede l'accesso a Internet, direttamente o tramite un proxy trasparente. La distribuzione di prova TP2 supporta esattamente una scheda di rete per la rete. Se si dispone di più schede di rete, assicurarsi che che è attivato solo (e tutti gli altri disattivati) prima di eseguire lo script di distribuzione nella sezione successiva.

## <a name="run-the-powershell-deployment-script"></a>Eseguire lo script di distribuzione di PowerShell

1. Effettuare l'accesso come amministratore locale nel computer di prova. Usare le credenziali specificate nella procedura precedente.

2. Aprire una console di PowerShell con privilegi elevata.

3. In PowerShell, eseguire il comando:`cd C:\CloudDeployment\Configuration`

4. Eseguire il comando di distribuzione:`.\InstallAzureStackPOC.ps1`

5. Al prompt dei comandi **Immettere la password** , immettere una password e quindi confermarla. Si tratta della password per tutte le macchine virtuali. Assicurarsi di essere registrate.

6. Immettere le credenziali dell'account Azure Active Directory. L'utente deve essere l'amministratore globale nel tenant di directory.

7. Il processo di distribuzione può richiedere alcune ore, in cui il sistema si riavvia automaticamente una sola volta.

    > [AZURE.IMPORTANT] Se si desidera controllare lo stato di distribuzione, accedere come azurestack\AzureStackAdmin. Se non si accedere come amministratore locale dopo che il computer viene aggiunto al dominio, è non è possibile visualizzare lo stato di avanzamento di distribuzione. Non eseguire nuovamente la distribuzione, invece accedere come azurestack\AzureStackAdmin convalidare che sia in esecuzione.

    Quando la distribuzione ha avuto esito positivo, viene visualizzata la console di PowerShell: **completamento: azione "Distribuzione"**.

    Se la distribuzione non riesce, provare a [ripetere l'esecuzione](azure-stack-rerun-deploy.md). In alternativa, è possibile [ridistribuire](azure-stack-redeploy.md) da zero.

### <a name="deployment-script-examples"></a>Esempi di script di distribuzione

Se la propria identità AAD è associato a una Directory AAD:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Se la propria identità AAD è associata a maggiore rispetto a una AAD Directory:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Se l'ambiente non è abilitato DHCP, è necessario includere i parametri aggiuntivi seguenti a uno o più opzioni tra (utilizzo di esempio forniti):

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Parametri facoltativi InstallAzureStackPOC.ps1

| Parametro | Obbligatorie/facoltative | Descrizione |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Facoltativo | Imposta il nome utente di Azure Active Directory e la password. Queste credenziali Azure possono essere un ID organizzazione o un Account Microsoft. Per usare le credenziali dell'Account Microsoft, omettere questo parametro nel cmdlet. Se si omette questo parametro viene visualizzato un prompt popup Azure autenticazione durante la distribuzione. Consente di creare i token di autenticazione e aggiornamento utilizzati durante la distribuzione. |
| AADDirectoryTenantName | Obbligatorio | Imposta la directory tenant. Utilizzare questo parametro per specificare una directory specifica in cui l'account AAD disponga delle autorizzazioni necessarie per gestire più directory. Nome di un Tenant di Directory AAD nel formato di completo <directoryName>. onmicrosoft.com. |
| AdminPassword | Obbligatorio | Imposta l'account di amministratore locale e tutti gli altri account utente in macchine virtuali create come parte della distribuzione di prova. Questa password deve corrispondere la password di amministratore locale corrente nell'host. |
| AzureEnvironment | Facoltativo | Selezionare l'ambiente di Azure con cui si desidera registrare la distribuzione di Azure Stack. Opzioni includono *Azure pubblico*, *Azure - Cina* *Azure - governo degli Stati Uniti*. |
| EnvironmentDNS | Facoltativo | Viene creato un server DNS come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per risolvere i nomi all'esterno dell'indicatore, fornire i server DNS dell'infrastruttura esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server. |
| NatIPv4Address | Richiesta per il supporto DHCP NAT | Configura un indirizzo IP statico MAS BGPNAT01. Utilizzare questo parametro solo se il protocollo DHCP non è possibile assegnare un indirizzo IP di accedere a Internet. |
| NatIPv4DefaultGateway | Richiesta per il supporto DHCP NAT | Imposta il gateway predefinito utilizzato con l'indirizzo IP statico per MAS BGPNAT01. Utilizzare questo parametro solo se il protocollo DHCP non è possibile assegnare un indirizzo IP di accedere a Internet.  |
| NatIPv4Subnet | Richiesta per il supporto DHCP NAT | Prefisso Subnet IP utilizzato per DHCP su supporto NAT. Utilizzare questo parametro solo se il protocollo DHCP non è possibile assegnare un indirizzo IP di accedere a Internet.  |
| PublicVLan | Facoltativo | Imposta l'ID VLAN. Utilizzare questo parametro solo se l'host e MAS BGPNAT01 necessario configurare ID VLAN per accedere a network fisica (e Internet). Per esempio`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Rieseguire | Facoltativo | Utilizzare questo flag per eseguire di nuovo la distribuzione.  Tutti gli input precedenti viene utilizzato. Nuovamente l'immissione dei dati precedentemente fornite non è supportata in quanto più valori univoci vengono generati e utilizzati per la distribuzione. |
| Acquisisca | Facoltativo | Utilizzare questo parametro se è necessario specificare un server di tempo specifico. |

## <a name="next-steps"></a>Passaggi successivi

[Connettersi a Stack Azure](azure-stack-connect-azure-stack.md)
