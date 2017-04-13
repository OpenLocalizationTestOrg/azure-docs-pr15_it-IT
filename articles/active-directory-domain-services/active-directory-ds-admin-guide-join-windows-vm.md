<properties
    pageTitle="Azure Active Directory servizi di dominio: Partecipare a una macchina virtuale Server di Windows a un dominio gestito | Microsoft Azure"
    description="Partecipare a una macchina virtuale di Windows Server in servizi di dominio Active Directory di Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Partecipare a una macchina virtuale di Windows Server per un dominio gestito

> [AZURE.SELECTOR]
- [Azure portale classico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

In questo articolo viene illustrato come partecipare a una macchina virtuale che eseguono Windows Server 2012 R2 a un dominio gestito di servizi di dominio Active Directory di Azure, tramite il portale classico Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Passaggio 1: Creare la macchina virtuale di Windows Server
Seguire le istruzioni riportate in questa esercitazione [Crea una macchina virtuale che esegue Windows nel portale di classica Azure](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . È importante per assicurarsi che il nuovo creato macchina virtuale appartiene alla stessa rete virtuale in cui è abilitato Servizi di dominio Active Directory Azure. L'opzione 'Crea rapido' non consente di aggiungere la macchina virtuale a una rete. È necessario usare l'opzione 'Dalla raccolta' per creare la macchina virtuale.

Per creare una macchina virtuale di Windows unita virtuali in cui è stata abilitata servizi di dominio Active Directory di Azure, procedere come segue.

1. Nel portale di classica Azure, sulla barra dei comandi nella parte inferiore della finestra fare clic su **Nuovo**.

2. In **calcolare**, fare clic su **macchina virtuale**e quindi fare clic su **Dalla raccolta**.

3. Nella prima schermata consente di **scegliere un'immagine** per la macchina virtuale dall'elenco di immagini disponibili. Selezionare l'immagine appropriata.

    ![Selezionare immagine](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Seconda schermata consente di selezionare un nome del computer, dimensioni e il nome utente con privilegi amministrativi e una password. Utilizzare il livello e le dimensioni necessarie per eseguire l'app o un carico di lavoro. Il nome utente che selezionare qui è un utente di amministratore locale del computer. Non immettere qui le credenziali dell'account utente di dominio.

    ![Configurare macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. La terza schermata consente di configurare le risorse per la rete, lo spazio di archiviazione e la disponibilità. Assicurarsi di selezionare la rete virtuale in cui è abilitato Servizi di dominio Active Directory di Azure nell'elenco a discesa **Regione/affinità gruppo/virtuale rete** . Specificare un **Nome DNS del servizio Cloud** come appropriata per la macchina virtuale.

    ![Selezionare la rete virtuale per la macchina virtuale](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Assicurarsi di aggiungere la macchina virtuale alla stessa rete virtuale in cui è stata abilitata servizi di dominio Active Directory Azure. Di conseguenza, la macchina virtuale possono vedere il dominio ed eseguire attività come si accede al dominio. Se si sceglie di creare la macchina virtuale in una rete virtuale diversa, connettersi tale virtuali virtuali in cui è stata abilitata servizi di dominio Active Directory Azure.

6. La quarta schermata consente di installare l'agente di macchine Virtuali e configurare alcune estensioni disponibili.

    ![Completato](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Dopo aver creata la macchina virtuale, il portale classico Elenca la nuova macchina virtuale nodo **macchine virtuali** . Sia la macchina virtuale e il servizio cloud avviati automaticamente e il relativo stato è elencato come **in esecuzione**.

    ![Macchina virtuale sia in esecuzione](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Passaggio 2: Connettere la macchina virtuale di Windows Server utilizzando l'account di amministratore locale
A questo punto, connettersi al computer appena creato Windows Server virtuale, per aggiungere il dominio. Utilizzare le credenziali di amministratore locale specificato quando si crea la macchina virtuale, per la connessione.

Eseguire la procedura seguente per connettere la macchina virtuale.

1. Passare al livello di nodo **macchine virtuali** nel portale di classica. Selezionare la macchina virtuale creato nel passaggio 1 e fare clic su **Connetti** nella barra dei comandi nella parte inferiore della finestra.

    ![Connettersi a macchina virtuale di Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Il portale classico viene richiesto di aprire o salvare un file con estensione 'RDP', che consente di connettere la macchina virtuale. Fare clic per aprire il file dopo aver terminato il download.

3. Al prompt dei comandi di accesso, immettere le **credenziali di amministratore locale**, specificata durante la creazione di macchina virtuale. Ad esempio è stato utilizzato 'localhost\mahesh' in questo esempio.

A questo punto devono essere registrati per la macchina virtuale di Windows appena creata utilizzando le credenziali di amministratore locale. Il passaggio successivo consiste nel aggiungere la macchina virtuale al dominio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Passaggio 3: Join la macchina virtuale Windows Server Active Directory AAD del dominio gestito
Eseguire la procedura seguente per unire la macchina virtuale di Windows Server Active Directory AAD del dominio gestito.

1. Connettersi a Windows Server come illustrato nel passaggio 2. Dalla schermata Start aprire **Server Manager**.

2. Nel riquadro sinistro della finestra Server Manager, fare clic su **Server locale** .

    ![Avviare Server Manager sul computer virtuale](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Fare clic su **gruppo di lavoro** nella sezione **proprietà** . Nella pagina delle proprietà **Proprietà-sistema** fare clic su **Modifica** per aggiungere il dominio.

    ![Pagina delle proprietà del sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Specificare il nome di dominio dei servizi di dominio Active Directory Azure gestite dominio nella casella di testo **dominio** e fare clic su **OK**.

    ![Specificare il dominio da unire](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Viene richiesto di immettere le credenziali per il dominio. Verificare che si **specificare le credenziali per un utente che appartengono agli amministratori di controller di dominio AAD** gruppo. Solo i membri di questo gruppo dispongono di privilegi per aggiungere computer al dominio gestito.

    ![Specificare le credenziali per l'aggiunta al dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. È possibile specificare le credenziali in uno dei modi seguenti:

    - Formato UPN: specificare il suffisso UPN per l'account utente in base alla configurazione di Azure Active Directory. In questo esempio, il suffisso UPN dell'utente "Roberto" è 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato SAMAccountName: È possibile specificare il nome dell'account nel formato SAMAccountName. In questo esempio, l'utente "Roberto" necessario immettere 'CONTOSO100\bob'.

        > [AZURE.NOTE] **È consigliabile utilizzare il formato UPN per specificare le credenziali.** SAMAccountName potrebbe essere generato automaticamente se prefisso UPN dell'utente è troppo lungo (ad esempio, 'joereallylongnameuser'). Se più utenti hanno lo stesso prefisso UPN (ad esempio, "Roberto") nel tenant di Azure Active Directory, può essere formato SAMAccountName generato automaticamente dal servizio. In questi casi, il formato UPN può essere utilizzato in modo affidabile agli utenti di accedere al dominio.

7. Una volta completato l'aggiunta al dominio, è visualizzato il seguente messaggio di benvenuto del dominio. Riavviare il computer virtuale per il completamento dell'operazione join dominio.

    ![Introduzione al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi aggiunta a un dominio
### <a name="connectivity-issues"></a>Problemi di connettività
Se la macchina virtuale non riesce a trovare il dominio, vedere le procedure di risoluzione dei problemi seguenti:

- Assicurarsi che la macchina virtuale sia connesso alla stessa rete virtuale come che è stata abilitata in servizi di dominio. In caso contrario, la macchina virtuale non riesce a connettersi al dominio e pertanto non è possibile aggiungere il dominio.

- Se la macchina virtuale è connesso a un altro virtuali, assicurarsi che la rete virtuale sia connesso alla rete virtuale in cui è stata abilitata servizi di dominio.

- Provare a effettuare il ping del dominio con il nome di dominio del dominio gestito (ad esempio, ' ping contoso100.com'). Se non è possibile effettuare questa operazione, provare a effettuare il ping gli indirizzi IP per il dominio visualizzato nella pagina in cui è abilitato Servizi di dominio Active Directory Azure (ad esempio, ' ping 10.0.0.4'). Se è in grado di effettuare il ping l'indirizzo IP ma non il dominio, DNS può essere configurato in modo non corretto. Si potrebbero non ha configurato gli indirizzi IP del dominio come server DNS per la rete virtuale.

- Provare a svuotamento della cache del Risolutore DNS sul computer virtuale ('ipconfig /flushdns).

Se viene visualizzato nella finestra di dialogo che richiede le credenziali per il dominio, non si dispone di problemi di connettività.


### <a name="credentials-related-issues"></a>Problemi relativi a credenziali
Se si verificano problemi con le credenziali e non è possibile aggiungere il dominio, consultare la procedura seguente.

- Provare a utilizzare il formato UPN per specificare le credenziali. SAMAccountName per il proprio account potrebbe essere generato automaticamente se esistono più utenti con lo stesso prefisso UPN nel tenant o il prefisso UPN è eccessiva. Di conseguenza, il formato SAMAccountName per il proprio account potrebbe essere diverso da cosa aspettarsi o usare il dominio locale.

- Provare a utilizzare le credenziali di un account utente che appartiene al gruppo 'AAD controller di dominio Administrators' per aggiungere computer al dominio gestito.

- Assicurarsi di avere [attivato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo la procedura descritta in Guida introduttiva.

- Assicurarsi di utilizzare l'UPN dell'utente in base alla configurazione di Azure Active Directory (ad esempio 'bob@domainservicespreview.onmicrosoft.com') effettuare l'accesso.

- Assicurarsi che dispone di attesa per il tempo necessario per la sincronizzazione delle password completare come specificato nella Guida introduttiva.


## <a name="related-content"></a>Contenuti correlati

- [Servizi di dominio Active Directory Azure - Guida introduttiva](./active-directory-ds-getting-started.md)

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](./active-directory-ds-admin-guide-administer-domain.md)
