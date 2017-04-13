<properties
    pageTitle="Creazione di un'immagine di modello personalizzato per Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come creare un'immagine di modello personalizzato per RemoteApp di Azure. È possibile utilizzare questo modello di raccolta ibrido o cloud."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Creazione di un'immagine di modello personalizzato per RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp Usa un'immagine del modello di Windows Server 2012 R2 per ospitare tutti i programmi che si desidera condividere con altri utenti. Per creare un'immagine di modello RemoteApp personalizzata, è possibile iniziare con un'immagine esistente o crearne uno nuovo. 


> [AZURE.TIP] Non tutti sanno che è possibile creare un'immagine da una macchina virtuale Azure Brano true e i dati tagliati verso il basso sulla quantità di tempo si impiega per importare l'immagine. Seguire i passaggi descritti [di seguito](remoteapp-image-on-azurevm.md).

I requisiti per l'immagine che può essere caricato per l'utilizzo con Azure RemoteApp sono:


- Le dimensioni dell'immagine devono essere un multiplo di MB. Se si tenta di caricare un'immagine che non è un multiplo esatto, non viene eseguito il caricamento.
- Le dimensioni dell'immagine devono essere 127 GB o più piccole.
- Deve trattarsi di un file disco rigido virtuale (VHDX file [Hyper-V dischi rigidi virtuali] non sono attualmente supportati).
- Il disco rigido virtuale non deve essere una macchina virtuale di generazione 2.
- Il disco rigido virtuale può essere dimensioni fisse o espansione dinamica. Un disco rigido virtuale espansione dinamica è consigliabile perché meno tempo per caricare in Azure rispetto a un file di dimensioni fisse disco rigido virtuale.
- Il disco deve essere inizializzato utilizzando il Record (avvio principale) stile di partizione. Il GUID stile tabella di partizione (GPT) non è supportato.
- Il disco rigido virtuale deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo che contiene un'installazione di Windows.
- È necessario installare il ruolo di Remote Desktop sessione Host (RDSH) e la funzionalità esperienza Desktop.
- È necessario il ruolo di Gestore connessione Desktop remoto *non* è stato possibile installare.
- File di sistema (crittografia) deve essere disattivato.
- L'immagine deve essere preparata con Sysprep utilizzando i parametri **/oobe /generalize /shutdown** (non si utilizza il parametro **/mode:vm** ).
- Non è possibile caricare il disco rigido virtuale da una catena di snapshot.


**Prima di iniziare**

È necessario eseguire le operazioni seguenti prima di creare il servizio:

- [Iscriversi](https://azure.microsoft.com/services/remoteapp/) per RemoteApp.
- Creare un account utente in Active Directory da utilizzare come l'account di servizio RemoteApp. Limitare le autorizzazioni per l'account in modo che solo partecipare computer al dominio. Per ulteriori informazioni, vedere [Configurare Azure Active Directory per RemoteApp](remoteapp-ad.md) .
- Raccogliere informazioni sulla rete locale: l'indirizzo IP di informazioni e dettagli dispositivo VPN.
- Installare il modulo di [Azure PowerShell](../powershell-install-configure.md) .
- Raccogliere informazioni sugli utenti che si desidera concedere l'accesso al. Può trattarsi di informazioni relative all'account Microsoft o informazioni sull'account di lavoro di Active Directory per gli utenti.



## <a name="create-a-template-image"></a>Creare un'immagine di modello ##

Questi sono i passaggi di alto livelli per creare una nuova immagine di modello da zero:

1.  Individuare un'immagine DVD di aggiornamento di Windows Server 2012 R2 o ISO.
2.  Creare un file disco rigido virtuale.
4.  Installare Windows Server 2012 R2.
5.  Installare il ruolo di Remote Desktop sessione Host (RDSH) e la funzionalità esperienza Desktop.
6.  Installare caratteristiche aggiuntive di richiesta dalle applicazioni.
7.  Installare e configurare le applicazioni. Per semplificare l'app di condivisione, aggiungere qualsiasi App o i programmi che si desidera condividere nel menu **Start** dell'immagine, in particolare in **%systemdrive%\ProgramData\Microsoft\Windows\Start Start\Programmi.
8.  Eseguire tutte le altre configurazioni di Windows richieste dalle applicazioni.
9.  Disabilitare la crittografia del File System (crittografia file System).
10. **REQUIRED:** Accedere a Windows Update e installare tutti gli aggiornamenti importanti.
9.  SYSPREP l'immagine.

La procedura dettagliata per creare una nuova immagine è:

1.  Individuare un'immagine DVD di aggiornamento di Windows Server 2012 R2 o ISO.
2.  Creare un file disco rigido virtuale tramite Gestione disco.
    1.  Avviare Gestione disco (diskmgmt).
    2.  Creare un disco rigido virtuale espansione dinamica di 40 GB o più dimensioni. (Stimare la quantità di spazio necessario per Windows, le applicazioni e le personalizzazioni. Windows Server con il ruolo RDSH e funzionalità esperienza Desktop richiederà circa 10 GB di spazio).
        1.  Fare clic su **Azioni > Crea disco rigido virtuale**.
        2.  Specificare la posizione, le dimensioni e formato disco rigido virtuale. Selezionare **espansione dinamica**e quindi fare clic su **OK**.

            Verrà eseguito per alcuni secondi. Una volta completata la creazione del disco rigido virtuale, verrà visualizzato un nuovo disco senza qualsiasi lettera e nello stato "Non inizializzato" nella console di Gestione disco.

        - Pulsante destro del mouse sul disco (non lo spazio non allocato) e quindi fare clic su **Disco inizializzare**. Selezionare i **record di avvio principale** (Record di avvio principale) come lo stile di partizione e quindi fare clic su **OK**.
        - Creare un nuovo volume: pulsante destro del mouse sullo spazio non allocato e quindi fare clic su **Nuovo Volume semplice**. Accettare le impostazioni predefinite della procedura guidata, è possibile verificare che si assegna una lettera di unità per evitare potenziali problemi quando si carica immagine del modello.
        - Pulsante destro del mouse sul disco e quindi fare clic su **Disconnetti disco rigido virtuale**.





1. Installare Windows Server 2012 R2:
    1. Creare una nuova macchina virtuale. Utilizzare la creazione guidata macchina virtuale Hyper-V Manager o Client Hyper-v.
        1. Nella pagina specificare generazione scegliere **generazione 1**.
        2. Nella pagina Connessione disco rigido virtuale selezionare **usare un disco rigido virtuale esistente**e individuare il disco rigido virtuale creato nel passaggio precedente.
        2. Nella pagina delle opzioni di installazione, selezionare **Installa un sistema operativo da un CD/DVD_ROM di avvio**e quindi selezionare il percorso del supporto di installazione di Windows Server 2012 R2.
        3. Scegliere altre opzioni della procedura guidata necessari per installare Windows e le applicazioni. Completare la procedura guidata.
    2.  Al termine della procedura guidata, modificare le impostazioni della macchina virtuale e apportare le modifiche necessarie per l'installazione di Windows e i programmi, ad esempio il numero di processori virtuali e quindi fare clic su **OK**.
    4.  Connettere la macchina virtuale e installare Windows Server 2012 R2.
1. Installare il ruolo di Remote Desktop sessione Host (RDSH) e la funzionalità esperienza Desktop:
    1. Avviare Server Manager.
    2. Nella schermata iniziale o il menu **Gestisci** , fare clic su **Aggiungi ruoli e funzionalità** .
    3. Fare clic su **Avanti** nella pagina prima di iniziare.
    4. Selezionare **installazione basato sui ruoli o funzionalità di base**e quindi fare clic su **Avanti**.
    5. Selezionare il computer locale dall'elenco e quindi fare clic su **Avanti**.
    6. Selezionare **Servizi Desktop remoto**e quindi fare clic su **Avanti**.
    7. Espandere **le interfacce utente e infrastruttura** e selezionare **Esperienza Desktop**.
    8. Fare clic su **Aggiungi funzionalità**e quindi fare clic su **Avanti**.
    9. Nella pagina Servizi Desktop remoto, fare clic su **Avanti**.
    10. Fare clic su **Host sessione Desktop remoto**.
    11. Fare clic su **Aggiungi funzionalità**e quindi fare clic su **Avanti**.
    12. Nella pagina Conferma selezioni di installazione, selezionare **automaticamente se richiesto, riavviare il server di destinazione**e quindi fare clic su **Sì** nella finestra del messaggio riavvia.
    13. Fare clic su **Installa**. Riavvio del computer.
1.  Installare caratteristiche aggiuntive di richiesta dalle applicazioni, ad esempio .NET Framework 3.5. Per installare la funzionalità, eseguire l'aggiunta di ruoli e guidata funzionalità.
7.  Installare e configurare i programmi e applicazioni che si desidera pubblicare tramite RemoteApp.

>[AZURE.IMPORTANT]
>
>Installare il ruolo RDSH prima di installare le applicazioni per garantire che vengono rilevati problemi di compatibilità delle applicazioni prima l'immagine viene caricato RemoteApp.
>
>Assicurarsi che verrà visualizzato un collegamento all'applicazione (file con**estensione lnk** ) nel menu di **avvio** per tutti gli utenti (%systemdrive%\ProgramData\Microsoft\Windows\Start Start\Programmi). Anche assicurarsi che l'icona di che viene visualizzato nel menu **Start** cosa si desidera venga visualizzato dagli utenti. In caso contrario, modificarlo. (Non *ha* per aggiungere l'applicazione all'inizio menu, ma risulta molto più semplice pubblicare l'applicazione in RemoteApp. In caso contrario, è necessario fornire il percorso di installazione dell'applicazione quando si pubblica l'app.)


8.  Eseguire tutte le altre configurazioni di Windows richieste dalle applicazioni.
9.  Disabilitare la crittografia del File System (crittografia file System). Eseguire il comando seguente in una finestra di comando con privilegi elevati:

        Fsutil behavior set disableencryption 1

    In alternativa, è possibile impostare o aggiungere il seguente valore DWORD del Registro di sistema:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Se si creano l'immagine all'interno di una macchina virtuale Azure, rinominare il ** \%windir%\Panther\Unattend.xml** mentre i file, ciò blocca lo script di caricamento utilizzato in un secondo momento da usare. Modificare il nome del file in Unattend.old in modo che il file sarà comunque possibile nel caso in cui è necessario ripristinare la distribuzione.
10. Accedere a Windows Update e installare tutti gli aggiornamenti importanti. Potrebbe essere necessario eseguire Windows Update più volte per ottenere tutti gli aggiornamenti. (A volte si installa un aggiornamento e l'aggiornamento stesso richiede un aggiornamento)
10. SYSPREP l'immagine. Al prompt dei comandi con privilegi elevati eseguire il comando seguente:

    **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown.**

    **Nota:** Non utilizzare l'opzione **/mode:vm** del comando SYSPREP anche se si tratta di una macchina virtuale.


## <a name="next-steps"></a>Passaggi successivi ##
Dopo aver creato l'immagine di modello personalizzato, è necessario caricare l'immagine alla raccolta RemoteApp. Per creare la raccolta, usare le informazioni presenti negli articoli seguenti:


- [Come creare un insieme di ibrido di RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Come creare un insieme di cloud di RemoteApp](remoteapp-create-cloud-deployment.md)
 