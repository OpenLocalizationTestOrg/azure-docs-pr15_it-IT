<properties 
   pageTitle="Impostare i proxy web per un dispositivo StorSimple | Microsoft Azure"
   description="Informazioni su come utilizzare Windows PowerShell per StorSimple per configurare le impostazioni proxy web per il dispositivo StorSimple."
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

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurare un proxy web per il dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare Windows PowerShell per StorSimple per configurare e visualizzare le impostazioni proxy web per il dispositivo StorSimple. Le impostazioni del proxy web vengono utilizzati dal dispositivo StorSimple durante la comunicazione con il cloud. Un server proxy web viene utilizzato per aggiungere un altro livello di sicurezza, filtro contenuto, cache a requisiti di larghezza di banda facilità o anche informazioni relative a analitica.

Proxy Web è una configurazione facoltativa per il dispositivo StorSimple. È possibile configurare il proxy web solo tramite Windows PowerShell per StorSimple. La configurazione è un processo in due passaggi come indicato di seguito:

1. È innanzitutto necessario configurare le impostazioni proxy web tramite la configurazione guidata o Windows PowerShell per i cmdlet StorSimple.

2. È quindi possibile abilitare le impostazioni del proxy web configurata tramite Windows PowerShell per i cmdlet StorSimple.

Una volta completata la configurazione del proxy web, è possibile visualizzare le impostazioni del proxy web configurato il servizio di Microsoft Azure StorSimple Manager e Windows PowerShell per StorSimple. 

Dopo aver letto questa esercitazione, sarà possibile per:

- Configurare un proxy web utilizzando i cmdlet e configurazione guidata
- Abilitare il proxy web utilizzando i cmdlet
- Visualizzare le impostazioni proxy web nel portale classico Azure
- Risolvere gli errori durante la configurazione del proxy web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurare un proxy web tramite Windows PowerShell per StorSimple

Utilizzare una delle operazioni seguenti per configurare le impostazioni proxy web:

- Configurazione guidata per completare la procedura di configurazione.

- Cmdlet di Windows PowerShell per StorSimple.

Ognuno di questi metodi viene descritto nelle sezioni seguenti.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurare proxy web tramite la configurazione guidata

È possibile utilizzare la configurazione guidata per completare la procedura per la configurazione del proxy web. Eseguire i passaggi seguenti per configurare proxy web nel dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Per configurare un proxy web tramite la configurazione guidata

1. Nel menu della console seriale, scegliere l'opzione 1, **accedere con l'accesso completo** e fornire la **password di amministratore di dispositivo**. Digitare il seguente comando per avviare una sessione di creazione guidata configurazione:

    `Invoke-HcsSetupWizard`

2. Se si tratta la prima volta che è stata utilizzata la configurazione guidata di registrazione di dispositivo, sarà necessario configurare tutte le impostazioni di rete necessarie fino a raggiungere la configurazione del proxy web. Se il dispositivo è già registrato, è possibile accettare tutte le impostazioni di rete configurate fino a raggiungere la configurazione del proxy web. Nella configurazione guidata, quando viene richiesto di configurare le impostazioni proxy web, digitare **Sì**.

3. Per l' **URL Web Proxy**, specificare l'indirizzo IP o il nome di dominio completo (FQDN) del server proxy web e il numero di porta TCP che si desidera il dispositivo da usare durante la comunicazione con il cloud. Utilizzare il formato seguente:

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    Per impostazione predefinita, il numero di porta TCP 8080 specificato.

4. Scegliere il tipo di autenticazione come **NTLM**, **Basic**o **Nessuno**. Base è l'autenticazione meno sicuro per la configurazione del server proxy. NT LAN Manager (NTLM) è un protocollo di autenticazione molto sicura e complesso che utilizza un sistema di messaggistica tre elementi (a volte quattro se è necessaria ulteriore integrità) per eseguire l'autenticazione utente. L'autenticazione predefinita è NTLM. Per ulteriori informazioni, vedere [base](http://hc.apache.org/httpclient-3.x/authentication.html) e [l'autenticazione NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **Nel servizio di gestione StorSimple, i grafici di monitoraggio di dispositivo non funzionano quando base o autenticazione NTLM è attivata nella configurazione del server proxy per il dispositivo. Per i grafici monitoraggio funzionino, è necessario assicurarsi che l'autenticazione è impostato su Nessuno.**

5. Se si utilizza l'autenticazione, fornire un **Nome utente Proxy Web** e la **Password del Proxy Web**. Sarà anche necessario confermare la password.

    ![Configurare il Proxy Web su periferica 1 StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Se si desidera registrare il dispositivo per la prima volta, continuare con la registrazione. Se il dispositivo è già stato registrato, la procedura guidata verrà chiuso. Verrà salvate la configurazione delle impostazioni.

Verrà abilitata anche proxy Web. È possibile ignorare il passaggio [abilitare proxy web](#enable-web-proxy) e passare direttamente alle [impostazioni di proxy di visualizzazione web nel portale di classica Azure](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurare un proxy web tramite Windows PowerShell per i cmdlet StorSimple

In alternativa per configurare le impostazioni proxy web consiste nell'utilizzare Windows PowerShell per i cmdlet StorSimple. Eseguire i passaggi seguenti per configurare proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Per configurare un proxy web tramite i cmdlet

1. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**. Quando richiesto, immettere la **password di amministratore di dispositivo**. La password predefinita è `Password1`.

2. Al prompt dei comandi digitare:

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Fornire e confermare la password quando richiesto, come illustrato di seguito.

    ![Configurare Proxy Web StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Il proxy web è ora configurato e deve essere abilitata.

## <a name="enable-web-proxy"></a>Abilitare proxy web

Proxy Web è disattivata per impostazione predefinita. Dopo aver configurato le impostazioni del proxy web nel dispositivo StorSimple, è necessario utilizzare Windows PowerShell per StorSimple per abilitare le impostazioni del proxy web.

> [AZURE.NOTE] **Questo passaggio non saranno necessario se è stata utilizzata la configurazione guidata per configurare un proxy web. Dopo una sessione di creazione guidata configurazione proxy Web viene attivata automaticamente per impostazione predefinita.**

In Windows PowerShell per StorSimple abilitare proxy web nel dispositivo, procedere come segue:

#### <a name="to-enable-web-proxy"></a>Per abilitare i proxy web

1. Nel menu console seriali, selezionare l'opzione 1, **accedere con l'accesso completo**. Quando richiesto, immettere la **password di amministratore di dispositivo**. La password predefinita è `Password1`.

2. Al prompt dei comandi digitare:

    `Enable-HcsWebProxy`

    A questo punto, è stata attivata la configurazione del proxy web nel dispositivo StorSimple.

    ![Configurare Proxy Web StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Visualizzare le impostazioni proxy web nel portale classico Azure

Le impostazioni del proxy web sono configurate tramite l'interfaccia di Windows PowerShell e non può essere modificati all'interno del portale classico. È tuttavia possibile, visualizzare queste impostazioni configurate nel portale di classica. Eseguire la procedura seguente per visualizzare proxy web.

#### <a name="to-view-web-proxy-settings"></a>Per visualizzare le impostazioni proxy web
1. Passare a **servizio StorSimple Manager > dispositivi**. Selezionare e fare clic su un dispositivo e quindi passare a **Configura**.
1. Scorrere verso il basso nella pagina **Configura** le **impostazioni del proxy Web** sezione. È possibile visualizzare le impostazioni del proxy web configurata nel dispositivo StorSimple come illustrato di seguito.

    ![Visualizzazione Web Proxy nel portale di gestione](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Errori durante la configurazione del proxy web

Se le impostazioni del proxy web sono stati configurati in modo non corretto, verranno visualizzati messaggi di errore all'utente di Windows PowerShell per StorSimple. Nella tabella seguente vengono illustrate alcune di questi messaggi di errore, probabili cause e le azioni consigliate.

|Sequenza n.|Codice di errore HRESULT|Possibili cause principali|Azione consigliata|
|:---|:---|:---|:---|
|1.|0x80070001|Esecuzione del comando da controller passivo e non è in grado di comunicare con controller attivo.|Eseguire il comando sul controller attivo. Per eseguire il comando da controller passivo, sarà necessario risolvere la connettività da passiva controller attivo. È necessario avviare Microsoft supporto se connettività viene interrotta.|
|2.|0x800710dd - l'identificatore di operazione non è valido|Impostazioni del proxy non sono supportate sul dispositivo virtuale StorSimple.|Impostazioni del proxy non sono supportate sul dispositivo virtuale StorSimple. Che possono essere configurati in un dispositivo fisico StorSimple.|
|3.|0x80070057 - parametro non valido|Uno dei parametri disponibili per le impostazioni del proxy non è valido.|URI non è disponibile nel formato corretto. Utilizzare il formato seguente:`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706ba - server RPC non disponibile|La causa è una delle operazioni seguenti:</br></br>Cluster non è attivo.</br></br>Servizio DataPath non è in esecuzione.</br></br>Il comando viene eseguito da controller passiva e non è in grado di comunicare con controller attivo.|Avviare il supporto Microsoft per assicurarsi che il cluster è attivo e servizio datapath sia in esecuzione.</br></br>Eseguire il comando da controller attivo. Se si desidera eseguire il comando da controller passivo, sarà necessario garantire che controller passivo possano comunicare con controller attivo. È necessario avviare Microsoft supporto se connettività viene interrotta.|
|5.|0X800706be - RPC non riuscita|Cluster è verso il basso.|Avviare il supporto Microsoft per assicurarsi che il cluster è attivo.|
|6.|0x8007138f - risorsa non trovata|Piattaforma servizio cluster non viene trovata. Questo problema può verificarsi durante l'installazione non è corretto.|Potrebbe essere necessario eseguire una factory Reimposta nel dispositivo. Potrebbe essere necessario creare una risorsa di piattaforma. Contattare il supporto Microsoft per la procedura successiva.|
|7.|0x8007138c - risorsa non in linea|Piattaforma o datapath risorse cluster non sono in linea.|Contattare il supporto Microsoft per contribuire a garantire che la risorsa servizio datapath e la piattaforma siano in linea.|

> [AZURE.NOTE] 
> 
> -  Elenco dei messaggi di errore non è esaustivo. 
> - Errori relativi alle impostazioni del proxy web non essere visualizzati nel portale di classica Azure nel servizio di gestione StorSimple. Se si verifica un problema con proxy web al termine della configurazione, lo stato dispositivo cambierà **offline** nel portale di classica. |

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi durante la distribuzione del dispositivo o configurare le impostazioni proxy web, vedere [risolvere i problemi relativi alla distribuzione di dispositivo StorSimple](storsimple-troubleshoot-deployment.md).

- Per informazioni su come utilizzare il servizio di gestione StorSimple, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
