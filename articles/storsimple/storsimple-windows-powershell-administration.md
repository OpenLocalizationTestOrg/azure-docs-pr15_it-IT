<properties 
   pageTitle="PowerShell per la gestione dei dispositivi StorSimple | Microsoft Azure"
   description="Informazioni su come utilizzare Windows PowerShell per StorSimple per gestire il dispositivo StorSimple."
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
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizzare Windows PowerShell per StorSimple per amministrare il dispositivo

## <a name="overview"></a>Panoramica

Windows PowerShell per StorSimple offre un'interfaccia della riga di comando che è possibile utilizzare per gestire il dispositivo di Microsoft Azure StorSimple. Come indicato il nome, è un'interfaccia della riga di comando basato su Windows PowerShell integrato in uno spazio di esecuzione limitata. Dal punto di vista dell'utente nella riga di comando, uno spazio di esecuzione limitata verrà visualizzato in una versione con restrizioni di Windows PowerShell. Mantenendo alcune delle funzionalità di base di Windows PowerShell, l'interfaccia dispone aggiuntive cmdlet dedicato rivolto gestire il dispositivo in uso Microsoft Azure StorSimple. 

In questo articolo descrive le caratteristiche di StorSimple, tra cui come è possibile connettersi a questa interfaccia di Windows PowerShell e vengono forniti collegamenti alle procedure dettagliate o flussi di lavoro che è possibile eseguire utilizzando l'interfaccia. I flussi di lavoro includono come eseguire la registrazione del dispositivo, configurare l'interfaccia di rete nel dispositivo, installare gli aggiornamenti che richiedono il dispositivo per essere in modalità di manutenzione, modificare lo stato dispositivo e risolvere eventuali problemi che possono verificarsi.

Dopo aver letto in questo articolo, sarà possibile per:

- Connettere il dispositivo di StorSimple con Windows PowerShell per StorSimple.

- Amministrare il dispositivo di StorSimple con Windows PowerShell per StorSimple.

- È possibile ottenere assistenza in Windows PowerShell per StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell per i cmdlet StorSimple consentono di gestire il dispositivo StorSimple da una console seriale o in modalità remota tramite Windows PowerShell remota. Per ulteriori informazioni su ognuna di esse dei cmdlet di singoli che può essere utilizzato in questa interfaccia, passare a [riferimento sui cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- I cmdlet di Azure PowerShell StorSimple sono un insieme diverso di cmdlet che consentono di automatizzare StorSimple livello di servizio e attività di migrazione dalla riga di comando. Per ulteriori informazioni sui cmdlet di PowerShell Azure per StorSimple, visitare il [riferimento sui cmdlet di Azure StorSimple](https://msdn.microsoft.com/library/azure/dn920427.aspx).

È possibile accedere a Windows PowerShell per StorSimple utilizzando uno dei metodi seguenti:

- [Connettersi a console seriale dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Quando in modalità remota connettersi StorSimple usando Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Connettersi a Windows PowerShell per StorSimple tramite la console seriale dispositivo

È possibile [scaricare PuTTY](http://www.putty.org/) o terminal simili software di emulazione per connettersi a Windows PowerShell per StorSimple. È necessario configurare PuTTY specificamente per accedere al dispositivo di Microsoft Azure StorSimple. Gli argomenti seguenti contengono istruzioni dettagliate su come configurare PuTTy e connettersi al dispositivo. Varie opzioni del menu console seriale anche sono state spiegate in.

### <a name="putty-settings"></a>Impostazioni puTTY

Assicurarsi di usare le seguenti impostazioni PuTTY per connettersi all'interfaccia di Windows PowerShell dalla console di sequenza.

#### <a name="to-configure-putty"></a>Per configurare PuTTY

1. Nel riquadro **categorie** della finestra di dialogo PuTTY **riconfigurazione** selezionare **tastiera**.

2. Assicurarsi che siano selezionate le opzioni seguenti (si tratta le impostazioni predefinite quando si inizia una nuova sessione). 

  	|Elemento di tastiera|Selezionare|
  	|---|---|
  	|Tasto BACKSPACE|Controllo-? (127)|
  	|Home o fine|Standard|
  	|Tastiera e i tasti funzione|ESC [n ~|
  	|Stato iniziale dei tasti di direzione|Visualizzazione normale|
  	|Stato iniziale del tastierino numerico|Visualizzazione normale|
  	|Abilitare le caratteristiche di tastiera|Alt di controllo è la differenza tra AltGr|

    ![Impostazioni Putty supportate](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Fare clic su **Applica**.

4. Nel riquadro **categoria** selezionare **traduzione**.

5. Nella casella di riepilogo **set di caratteri Remote** selezionare **UTF-8**.

6. In **gestione dei caratteri di disegno di linee**, selezionare **punti di codice lineette utilizza Unicode**. L'illustrazione seguente mostra le selezioni PuTTY corrette.

    ![UTF Putty impostazioni](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Fare clic su **Applica**.


È ora possibile utilizzare PuTTY per connettersi alla console seriale dispositivo eseguendo la procedura seguente.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Informazioni sulla console di sequenza

Quando si accede a Windows PowerShell viene visualizzata un'interfaccia del dispositivo StorSimple tramite la console seriale, un messaggio di intestazione, seguito dalle opzioni di menu. 

Il messaggio di intestazione contiene informazioni di dispositivo StorSimple base come modello, nome, versione del software installato e lo stato del controller di cui che si accede. Nella figura seguente mostra un esempio di un messaggio di intestazione.

![Messaggio di intestazione seriale](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] È possibile utilizzare il messaggio di intestazione per determinare se il controller a che si è connessi tratti attivo o passivo.

L'immagine seguente illustra le varie opzioni di spazio che sono disponibili nel menu console seriale.

![Registrare il dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

È possibile scegliere tra le seguenti impostazioni:

1. **Accedere con l'accesso completo** Questa opzione consente di connettersi (con le credenziali appropriate) di spazio **SSAdminConsole** sul controller locale. (La locale è il controller che si accede al momento tramite la console seriale del dispositivo StorSimple.) Questa opzione è utilizzabile anche per consentire il supporto Microsoft per accedere senza restrizioni spazio (una sessione di supporto) per risolvere eventuali problemi di dispositivi possibili. Dopo l'opzione 1 per l'accesso, è possibile consentire il tecnico di supporto Microsoft accedere a spazio senza restrizioni eseguendo uno specifico cmdlet. Per informazioni dettagliate, vedere [avviare una sessione di supporto](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Accedere a controller peer con accesso completo** Questa opzione è diverso da quello di opzione 1, tranne per il fatto che sia possibile connettersi (con le credenziali appropriate) per lo spazio di esecuzione **SSAdminConsole** sul controller di peer. Poiché il dispositivo StorSimple è un dispositivo di disponibilità con due controller di una configurazione attiva passiva, peer fa riferimento a in altro controller il dispositivo che si accede a tramite la console seriale).
Simile all'opzione 1, questa opzione può anche essere utilizzata per consentire il supporto Microsoft per accedere senza restrizioni spazio in un controller di peer.

3. **Connettersi con accesso limitato** Questa opzione viene utilizzata per accedere all'interfaccia di Windows PowerShell in modalità limitata. Non viene chiesto credenziali di accesso. Questa opzione si connette a uno spazio di esecuzione più limitato rispetto alle opzioni di 1 e 2.  Alcune operazioni che sono disponibili tramite l'opzione 1 che **non possono* essere eseguite in questo spazio sono:

    - Ripristinare le impostazioni del produttore
    - Cambiare la password
    - Attivare o disattivare l'accesso di supporto
    - Applicare gli aggiornamenti
    - Installare gli aggiornamenti rapidi 
                                                

    >[AZURE.NOTE] **Se si dimentica la password di amministratore del dispositivo e non riesce a connettersi tramite opzione 1 o 2, si tratta l'opzione desiderata.**

4. **Lingua di modifica** Questa opzione consente di modificare la lingua di visualizzazione nell'interfaccia di Windows PowerShell. Lingue supportate sono inglese, giapponese, russo, francese, coreano sud, spagnolo, italiano, tedesco, cinese e portoghese (Brasile).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Quando in modalità remota connettersi StorSimple usando Windows PowerShell per StorSimple

È possibile utilizzare Windows PowerShell remota per connettersi al dispositivo StorSimple. Quando ci si connette in questo modo, non verrà visualizzato un menu. (È visualizzato un menu solo se si utilizza la console seriale sul dispositivo per la connessione. Connessione remota consente di accedere direttamente all'equivalente di "opzione 1 – accesso completo" nella console seriale.) Con Windows PowerShell remota, connettersi a uno specifico spazio di esecuzione. È inoltre possibile specificare la lingua di visualizzazione. 

La lingua di visualizzazione è indipendente dalla lingua impostata utilizzando l'opzione di **Lingua di modifica** nel menu console seriale. Sessione remota di PowerShell acquisirà automaticamente le impostazioni locali del dispositivo che effettua la connessione se non è specificato.

>[AZURE.NOTE] Se si lavora con host virtuali di Microsoft Azure e periferiche virtuali StorSimple, è possibile utilizzare Windows PowerShell remota e host virtuale per la connessione al dispositivo virtuale. Se è stata configurata una posizione di condivisione nell'host in cui salvare informazioni della sessione di Windows PowerShell, tenere presente che il principale Everyone include solo gli utenti autenticati. Pertanto, se impostati Condividi per consentire l'accesso a tutti gli utenti e ci si connette senza specificare le credenziali, verrà utilizzato il capitale anonimo non autenticato e verrà visualizzato un errore. Per risolvere il problema, presenti nella condivisione host è necessario attivare l'account Guest e assegnare Guest account accesso completo alla condivisione o è necessario specificare le credenziali valide insieme cmdlet di Windows PowerShell.

È possibile utilizzare HTTP o HTTPS per connettersi tramite Windows PowerShell remota. Usare le istruzioni visualizzate nelle esercitazioni seguenti:

- [Connessione remota tramite HTTP](storsimple-remote-connect.md#connect-through-http)
- [Connessione remota tramite HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerazioni sulla protezione di connessione

Prima di decidere come connettersi a Windows PowerShell per StorSimple, tenere presente quanto segue:

- La connessione direttamente alla console seriale dispositivo è sicura, ma che si connettono a console seriale switch di rete non è. Prestare attenzione dei rischi per la sicurezza quando ci si connette al dispositivo seriale su parametri di rete.

- Connessione tramite una sessione HTTP potrebbe offrire maggiore sicurezza rispetto alla connessione tramite la console seriale in rete. Anche se non è il metodo più sicuro, è accettabile su reti attendibili.

- La connessione tramite una sessione HTTPS è più sicura e l'opzione consigliata.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Amministrare il dispositivo di StorSimple con Windows PowerShell per StorSimple
Nella tabella seguente mostra un riepilogo di tutte le attività comuni di gestione e flussi di lavoro complessi che possono essere eseguite all'interno dell'interfaccia di Windows PowerShell del dispositivo StorSimple. Per ulteriori informazioni su ogni flusso di lavoro, fare clic sulla voce appropriata nella tabella.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell per flussi di lavoro StorSimple

|Se si desidera procedere come segue...|Utilizzare questa procedura.|
|---|---|
|Eseguire la registrazione del dispositivo|[Configurare e registrare il dispositivo con Windows PowerShell per StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurare proxy web</br>Impostazioni del proxy di visualizzazione web|[Configurare un proxy web per il dispositivo StorSimple](storsimple-configure-web-proxy.md)|
|Modificare le impostazioni dell'interfaccia di rete 0 di dati nel dispositivo|[Modificare l'interfaccia 0 rete dati per il dispositivo StorSimple](storsimple-modify-data-0.md)|
|Interrompere un controller </br> Riavviare o arrestare un controller </br> Chiudere un dispositivo</br>Reimpostare il dispositivo per le impostazioni predefinite|[Gestire i controller di dispositivo](storsimple-manage-device-controller.md)|
|Installare gli aggiornamenti in modalità di manutenzione e aggiornamenti rapidi|[Aggiornare il dispositivo](storsimple-update-device.md)|
|Passare alla modalità di manutenzione </br>Uscire dalla modalità di manutenzione|[Modalità di dispositivo StorSimple](storsimple-device-modes.md)|
|Creare un pacchetto di supporto</br>Decrittografare e modificare un pacchetto di supporto|[Creare e gestire un pacchetto di supporto](storsimple-create-manage-support-package.md)|
|Avviare una sessione di supporto</br>|[Avviare una sessione di supporto tecnico in Windows PowerShell per StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>È possibile ottenere assistenza in Windows PowerShell per StorSimple

In Windows PowerShell per StorSimple, cmdlet Guida è disponibile. Una versione online, aggiornata di questa guida è disponibile anche, che consente di aggiornare la Guida del sistema.

Visualizzazione della Guida in questa interfaccia è simile a quella di Windows PowerShell e la maggior parte dei cmdlet di relative alla Guida funzionerà. È possibile trovare Guida per Windows PowerShell online nella libreria TechNet: [scrittura di script con Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Di seguito è una breve descrizione dei tipi di Guida per l'interfaccia di Windows PowerShell, inclusa la modalità di aggiornare la Guida.

#### <a name="to-get-help-for-a-cmdlet"></a>Per ottenere assistenza per un cmdlet

- Per ottenere assistenza per qualsiasi cmdlet o una funzione, utilizzare il comando seguente:`Get-Help <cmdlet-name>`

- Per visualizzare la Guida in linea per tutti i cmdlet, utilizzare il cmdlet precedente con il `-Online` parametro:`Get-Help <cmdlet-name> -Online`

- Per informazioni su come completo, è possibile utilizzare il `–Full` parametro e per esempi, utilizzare la `–Examples` parametro.

#### <a name="to-update-help"></a>Per aggiornare la Guida

È possibile aggiornare facilmente la Guida dell'interfaccia di Windows PowerShell. Per aggiornare la Guida del sistema, procedere come segue.

#### <a name="to-update-cmdlet-help"></a>Per aggiornare cmdlet della Guida

1. Avviare Windows PowerShell con l'opzione **Esegui come amministratore** .

1. Al prompt dei comandi digitare: `Update-Help`

1. Verranno installati i file della Guida aggiornati.

1. Dopo l'installazione di file della Guida, digitare: `Get-Help Get-Command`. Questa operazione verrà visualizzato un elenco di cmdlet per la quale è disponibile la Guida.


>[AZURE.NOTE] Per ottenere un elenco di tutti i cmdlet disponibili in uno spazio di esecuzione, accedere all'opzione di menu corrispondente ed eseguire il `Get-Command` cmdlet.

## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi con il dispositivo StorSimple durante l'esecuzione di uno dei flussi di lavoro precedente, fare riferimento agli [Strumenti per la risoluzione dei problemi StorSimple distribuzioni](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

