<properties
    pageTitle="Configurare una macchina virtuale di SQL Server come server blocco appunti IPython | Microsoft Azure"
    description="Impostare la macchina virtuale un dati scienza con SQL Server e IPython Server."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurare una macchina virtuale Azure SQL Server come server di blocco appunti IPython per analitica avanzate

In questo argomento viene illustrato come preparare e configurare una macchina virtuale di SQL Server da utilizzare come parte di un ambiente di scienze dati basate su cloud. La macchina virtuale di Windows è configurata con supporto di strumenti, ad esempio blocco appunti IPython, Esplora archivi Azure e AzCopy, nonché altre utilità sono utili per i progetti di ricerca di scienze dati. Esplora archivi Azure e AzCopy, ad esempio, fornire agevolmente per caricare dati in archiviazione blob Azure dal computer locale o per scaricare nel computer locale dallo spazio di archiviazione blob.

La raccolta di Azure macchina virtuale include diverse immagini che contengono Microsoft SQL Server. Selezionare un'immagine di macchine Virtuali di SQL Server è adatta alle proprie esigenze di dati. Sono consigliate immagini:

- SQL Server 2012 SP2 Enterprise per piccole e dimensioni dei dati di medie dimensioni
- SQL Server 2012 SP2 Enterprise ottimizzato per DataWarehousing carichi di lavoro per le dimensioni di dati di grandi dimensioni di dimensioni molto grandi

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise immagine **non include un disco dati**. È necessario aggiungere e/o allegare uno o più dischi rigidi virtuali per memorizzare i dati. Quando si crea una macchina virtuale Azure, ha un disco per il sistema operativo associato all'unità C e un temporaneo associato all'unità D. Non utilizzare l'unità D per archiviare i dati. Come indica il nome, fornisce solo lo spazio di archiviazione temporaneo. E non offre ridondanza o copia di backup in quanto che non si trova in archiviazione Azure.


##<a name="Provision"></a>Connettersi al portale classica di Azure ed effettuare il provisioning di una macchina virtuale di SQL Server

1.  Accedere al [Portale classica Azure](http://manage.windowsazure.com/) usando l'account.
    Se non si dispone di un account Azure, visitare il [periodo di prova gratuito di Azure](https://azure.microsoft.com/pricing/free-trial/).

2.  Nel portale classica di Azure, in basso a sinistra della pagina web, fare clic su **+ Nuovo**, fare clic su **calcolare**e quindi fare clic su **Raccolta da** **macchina virtuale**.

3.  Nella pagina **Crea una macchina virtuale** selezionare un'immagine di macchina virtuale contenente SQL Server in base alle esigenze dei dati e quindi fare clic sulla freccia nell'angolo inferiore destro della pagina successiva. Per informazioni più aggiornate su immagini di SQL Server supportate in Azure, vedere [Introduzione a SQL Server in macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) argomento nella documentazione di [SQL Server in macchine virtuali di Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) impostare.

    ![Selezionare SQL Server macchine Virtuali][1]

4.  Nella prima pagina di **Configurazione della macchina virtuale** , specificare le informazioni seguenti:

    -   Specificare un **nome macchina virtuale**.
    -   Nella casella **Nuovo nome utente** digitare il nome utente univoco per l'account di amministratore locale macchine Virtuali.
    -   Nella casella **Nuova PASSWORD** digitare una password complessa. Per ulteriori informazioni, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Nella casella **Conferma PASSWORD** digitare nuovamente la password.
    -   Selezionare le **dimensioni** appropriate dall'elenco a discesa.

     > [AZURE.NOTE] Le dimensioni della macchina virtuale specificata durante il provisioning: A2 contiene la dimensione minima consigliata per carichi di lavoro di produzione. La dimensione minima consigliata per una macchina virtuale: A3 quando si utilizza SQL Server Enterprise Edition. Selezionare A3 o versione successiva quando si utilizza SQL Server Enterprise Edition. Quando si utilizza SQL Server 2012 o 2014 Enterprise ottimizzata per le immagini transazione carichi di lavoro, selezionare A4.
Selezionare A7 quando si utilizza SQL Server 2012 o 2014 Enterprise ottimizzata per le immagini di dati deposito carichi di lavoro. Le dimensioni selezionate limitano il numero di dischi di dati che è possibile configurare. Per informazioni più aggiornate su macchina virtuale a disponibilità dimensioni e il numero di dischi dati che è possibile associare a una macchina virtuale, vedere [Le dimensioni di macchina virtuale per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Per informazioni sui prezzi, vedere [Prezzi Macines virtuale](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Fare clic sulla freccia successiva in basso a destra per continuare.

    ![Configurazione di macchine Virtuali][2]

5.  Nella seconda pagina di **configurazione della macchina virtuale** , configurare le risorse per la rete, lo spazio di archiviazione e la disponibilità:

    -   Nella finestra di **Servizio Cloud** , scegliere **Crea un nuovo servizio cloud**.
    -   Nella casella **Nome DNS del servizio Cloud** consentono la prima parte di un nome DNS di propria scelta, in modo che venga completato un nome in formato **TESTNAME.cloudapp.net**
    -   Nella casella **Area/AFFINITÀ gruppo/virtuale rete** selezionare un'area in cui verrà ospitata in questa figura virtuale.
    -   Nella finestra **Account di archiviazione**, selezionare un account di archiviazione esistente o selezionarne uno generato automaticamente.
    -   Nella casella **SET di disponibilità** , selezionare **(nessuno)**.
    -   Leggere e accettare le informazioni sui prezzi.

6.  Nella sezione **endpoint** nell'elenco a discesa vuota sotto il **nome**, selezionare **MSSQL** fare digitare il numero di porta dell'istanza del motore di Database (**1433** per l'istanza predefinita).

7.  Di una macchina SQL Server virtuale può anche fungere da un Server di blocco appunti IPython, verrà configurato in un passaggio successivo.
    Aggiungere un nuovo endpoint per specificare la porta da utilizzare per il server IPython blocco appunti. Immettere un nome nella colonna **nome** , selezionare un numero di porta di desiderato per la porta pubblica e 9999 per la porta privata.

    Fare clic sulla freccia successiva in basso a destra per continuare.

    ![Selezionare le porte MSSQL e IPython][3]

8.  Accettare l'opzione di **installazione macchine Virtuali agente** predefinita selezionata e fare clic sul segno di spunta nell'angolo inferiore destro della procedura guidata per completare il processo di provisioning macchine Virtuali.

    `![Opzioni finale macchine Virtuali][4]

9.  Attendere Azure consente di preparare la macchina virtuale. Verificare lo stato di macchina virtuale per eseguire i passaggi:

    -   Avvio (Provisioning)
    -   Arrestato
    -   Avvio (Provisioning)
    -   In esecuzione (Provisioning)
    -   In esecuzione


##<a name="RemoteDesktop"></a>Aprire la macchina virtuale con Desktop remoto e completare la configurazione

1.  Al termine di provisioning, fare clic sul nome del computer virtuale per passare alla pagina del DASHBOARD. Nella parte inferiore della pagina, fare clic su **Connetti**.

2.  Scegliere per aprire il file rpd con il programma Desktop remoto di Windows (`%windir%\system32\mstsc.exe`).

3.  Nella finestra di dialogo **Protezione di Windows** , specificare la password per l'account di amministratore locale specificata in un passaggio precedente.
    (Potrebbe essere richiesto per verificare le credenziali della macchina virtuale.)

4.  La prima volta che si accede a questa macchina virtuale numerosi processi potrebbero essere necessario completare, inclusi il programma di installazione del desktop, gli aggiornamenti di Windows e completamento delle attività di configurazione iniziale di Windows (sysprep). Al termine di sysprep di Windows, le attività di configurazione completata l'installazione di SQL Server. Queste attività possono causare un ritardo di alcuni minuti mentre completati. `SELECT @@SERVERNAME`potrebbero non restituire il nome corretto finché non viene completata l'installazione di SQL Server e SQL Server Management Studio potrebbe non essere visibile della pagina iniziale.

Una volta che si è connessi alla macchina virtuale con Desktop remoto di Windows, la macchina virtuale funziona come qualsiasi altro computer. Connettersi all'istanza predefinita di SQL Server con SQL Server Management Studio (in esecuzione nel computer virtuale) nel modo consueto.


##<a name="InstallIPython"></a>Installare blocco appunti IPython e altri strumenti di supporto

Per configurare la nuova macchina SQL Server virtuale per fungere da un server di blocco appunti IPython e installare altri strumenti di supporto, ad esempio AzCopy, Esplora archivi Azure, utili pacchetti dati scienza Python e ad altri utenti, uno script di personalizzazione speciale è disponibile. Per installare:

- Pulsante destro del mouse sull'icona di **Avvio di Windows** e fare clic su **prompt dei comandi di (amministratori)**
- Copiare i comandi seguenti e incollare al prompt dei comandi.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Quando richiesto, immettere una password di propria scelta per il server IPython blocco appunti.
- Lo script di personalizzazione consente di automatizzare diverse procedure dopo l'installazione, che includono:
    + Installazione e configurazione del server di blocco appunti IPython
    + Apertura di porte TCP in Windows firewall per gli endpoint creata in precedenza:
    + Per la connettività remota di SQL Server
    + Per connettività remota server IPython blocco appunti
    + Recupero dei blocchi appunti IPython di esempio e script SQL
    + Scaricare e installare pacchetti Python scienza dati utili
    + Scaricare e installare gli strumenti di Azure, ad esempio AzCopy ed Esplora archivi Azure  
<br>
- Si può accedere ed eseguire IPython blocco appunti da un browser qualsiasi locale o remoto tramite un URL del modulo `https://<virtual_machine_DNS_name>:<port>`, dove porta è la porta pubblica IPython selezionata durante il provisioning la macchina virtuale.
- Blocco appunti IPython server è in esecuzione come servizio in background e verrà riavviato automaticamente quando si riavvia il computer virtuale.

##<a name="Optional"></a>Collegare il disco di dati in base alle esigenze

Se l'immagine di macchine Virtuali non include dischi di dati, ad esempio dischi diversa da quella unità C (disco OS) e unità D (disco temporaneo), è necessario aggiungere uno o più dischi di dati per l'archiviazione dei dati. L'immagine di macchina virtuale per SQL Server 2012 SP2 Enterprise ottimizzati per i carichi di lavoro di DataWarehousing viene fornito preconfigurato con dischi aggiuntivi per i file di dati e log di SQL Server.

 > [AZURE.NOTE] Non utilizzare l'unità D per archiviare i dati. Come indica il nome, fornisce solo lo spazio di archiviazione temporaneo. E non offre ridondanza o copia di backup in quanto che non si trova in archiviazione Azure.

Per allegare dischi dati aggiuntivi, seguire la procedura descritta in [come allegare un disco di dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md), che guidano l'utente tramite:

1. Associare dischi vuoti alla macchina virtuale viene completato il provisioning nei passaggi precedenti
2. Inizializzare nuovi dischi nella macchina virtuale


##<a name="SSMS"></a>Connettersi a SQL Server Management Studio e abilitare l'autenticazione in modalità misti

Motore di Database di SQL Server non è possibile utilizzare l'autenticazione di Windows senza ambiente di dominio. Per connettersi al motore di Database da un altro computer, configurare SQL Server per l'autenticazione mista. Modalità di autenticazione mista consente l'autenticazione di SQL Server e l'autenticazione di Windows. Modalità di autenticazione SQL è richiesta per acquisire i dati direttamente dal database macchine Virtuali di SQL Server [Azure Machine Learning Studio](https://studio.azureml.net) utilizzando il modulo Importa dati.

1.  Mentre si è connessi alla macchina virtuale mediante Desktop remoto, utilizzare il riquadro di **ricerca** di Windows e digitare **SQL Server Management Studio** (SMS). Fare clic per avviare il SQL Server Management Studio (SQL Server Management Studio). Può essere necessario aggiungere un collegamento a SQL Server Management Studio sul Desktop per utilizzi futuri.

    ![Avviare SQL Server Management Studio][5]

    Alla prima apertura di Management Studio deve creare l'ambiente di Studio Gestione utenti. Alcuni secondi.

2.  Quando si apre, Management Studio viene visualizzata la finestra di dialogo **connettersi al Server** . Nella casella **nome Server** digitare il nome del computer virtuale per la connessione al motore di Database con Esplora aree di oggetto.
    (Anziché il nome del computer virtuale è possibile anche utilizzare **(locale)** o un singolo periodo come **nome del Server**. Selezionare **L'autenticazione di Windows**e lasciare ** *il\_macchine Virtuali\_nome*\\il\_locale\_amministratore** nella casella **nome utente** . Fare clic su **Connetti**.

    ![Connessione al Server][6]

    <br>

     > [AZURE.TIP] È possibile modificare la modalità di autenticazione di SQL Server mediante una modifica della chiave del Registro di sistema Windows o SQL Server Management Studio. Per modificare la modalità di autenticazione utilizzando la chiave del Registro di sistema, avviare una **Nuova Query** ed eseguire il seguente script:

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Per modificare la modalità di autenticazione con SQL Server management Studio:

3.  In **Esplora oggetti di SQL Server Management Studio**, pulsante destro del mouse sul nome dell'istanza di SQL Server (il nome del computer virtuale) e quindi fare clic su **proprietà**.

    ![Proprietà del server][7]

4.  Nella pagina **sicurezza** , in **autenticazione Server**, selezionare **SQL Server e la modalità di autenticazione di Windows**e quindi fare clic su **OK**.

    ![Selezionare la modalità di autenticazione][8]

5.  Nella finestra di dialogo **SQL Server Management Studio** fare clic su **OK** per confermare la necessità di riavviare SQL Server.

6.  In **Esplora oggetti**mouse sul server e quindi scegliere **Riavvia**. (Se Agente SQL Server è in esecuzione, è necessario anche riavviarlo.)

    ![Riavviare][9]

7.  Nella finestra di dialogo **SQL Server Management Studio** fare clic su **Sì** per accettare che si desidera riavviare SQL Server.

##<a name="Logins"></a>Creare gli accessi l'autenticazione di SQL Server

Per connettersi al motore di Database da un altro computer, è necessario creare almeno un accesso di autenticazione di SQL Server.  

È possibile creare nuovi account di accesso di SQL Server a livello di programmazione o con SQL Server Management Studio. Per creare un nuovo utenti con autenticazione di SQL Server a livello di programmazione, avviare una **Nuova Query** ed eseguire il seguente script. Sostituire < nuovo nome utente\> e < nuova password\> con la scelta del *nome utente* e *password*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Modificare il criterio di password in base alle esigenze (l'esempio codice Disattiva scadenza dei criteri di controllo e la password). Per ulteriori informazioni sull'account di accesso di SQL Server, vedere [creare un account di accesso](http://msdn.microsoft.com/library/aa337562.aspx).  

Per creare nuovi account di accesso di SQL Server con SQL Server Management Studio:

1.  In **Esplora oggetti di SQL Server Management Studio**espandere la cartella dell'istanza del server in cui si desidera creare il nuovo account di accesso.

2.  Pulsante destro del mouse sulla cartella **protezione** , scegliere **Nuovo**e selezionare **Accedi...**.

    ![Nuovo account di accesso][10]

3.  Nella pagina **Generale** della finestra di dialogo **account di accesso - nuovo** immettere il nome del nuovo utente nella casella **nome di accesso** .

4.  Selezionare **l'autenticazione di SQL Server**.

5.  Nella casella **Password** immettere una password per il nuovo utente. Immettere nuovamente la password nella casella **Conferma Password** .

6.  Per applicare le opzioni di criteri password per complessità e l'applicazione, selezionare **Applica criteri di password** (scelta consigliata). Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

7.  Per applicare opzioni dei criteri password per la scadenza, selezionare **Applica la scadenza delle password** (scelta consigliata). Applicare criteri password devono essere selezionato per abilitare questa casella di controllo. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

8.  Per forzare l'utente per creare una nuova password dopo la prima volta che viene usato l'account di accesso, selezionare l'opzione **Cambiamento obbligatorio password all'accesso successivo** (scelta consigliata se l'account di accesso per conto di qualcun altro da utilizzare. Se l'account è per uso personale, non selezionare questa opzione.) Applicare la scadenza delle password deve essere selezionata per abilitare questa casella di controllo. Si tratta di un'opzione predefinita quando si seleziona l'autenticazione di SQL Server.

9.  Nell'elenco di **database predefinito** , selezionare un database predefinito per l'accesso. **master** è il valore predefinito per questa opzione. Se non è ancora stato creato un database utente, lasciare il valore impostato su **master**.

10. Nell'elenco **lingua predefinita** , lasciare **predefinito** come valore.

    ![Proprietà account di accesso][11]

11. Se questo è il primo accesso che si sta creando, si desidera impostare l'accesso come amministratore di SQL Server. In caso affermativo, nella pagina **Ruoli Server** selezionare **sysadmin**.

    > [AZURE.IMPORTANT] I membri del ruolo di server sysadmin hanno il controllo completo del motore di Database. Per motivi di sicurezza, è consigliabile limitare con attenzione appartenenza a questo ruolo.

    ![sysadmin][12]

12. Fare clic su OK.

##<a name="DNS"></a>Determinare il nome DNS della macchina virtuale

Per connettersi al motore di Database SQL Server da un altro computer, è necessario conoscere il nome del sistema DNS (Domain Name) della macchina virtuale.

(È il nome che Internet utilizzato per identificare la macchina virtuale. È possibile utilizzare l'indirizzo IP, ma l'indirizzo IP potrebbero essere modificati quando Azure Riposiziona risorse per la ridondanza o la manutenzione. Il nome DNS sarà stabile perché possono essere reindirizzato a un nuovo indirizzo IP.)

1.  Nel portale classica di Azure (del passaggio precedente), selezionare o **macchine VIRTUALI**.

2.  Nella pagina **Le istanze di macchina virtuale** , nella colonna **Nome DNS** individuare e copiare il nome del DNS per la macchina virtuale visualizzata preceduto da **http://**. (Interfaccia utente potrebbe non essere visualizzato l'intero nome, ma è possibile pulsante destro del mouse su di esso e scegliere Copia.)

##<a name="cde"></a>Connettersi al motore di Database da un altro computer

1.  In un computer connesso a internet, aprire SQL Server Management Studio.

2.  Nella casella **nome del Server** della finestra di dialogo **connessione al Server** o **connessione al motore di Database** immettere il nome DNS del computer virtuale (determinati nell'attività precedente) e un endpoint pubblico numero di porta nel formato di *DNSName, numero di porta* , ad esempio **tutorialtestVM.cloudapp.net,57500**.

3.  Nella casella **autenticazione** selezionare **Autenticazione di SQL Server**.

4.  Nella finestra di **accesso** , digitare il nome di un account di accesso è stato creato in un'attività precedente.

5.  Nella casella **Password** digitare la password dell'account di accesso che si crea in un'attività precedente.

6.  Fare clic su **Connetti**.

##<a name="amlconnect"></a>Connettersi al motore di Database da Azure apprendimento

Nelle versioni successive fasi del processo di scienze di dati del Team, si utilizzerà [Azure Machine formazione Studio](https://studio.azureml.net) per creare e distribuire modelli di risorse computer. Per acquisire dati da database macchine Virtuali di SQL Server direttamente in Azure apprendimento per la formazione o il punteggio, utilizzare il modulo di **Importare dati** in una nuova prova di [Azure Machine Learning Studio](https://studio.azureml.net) . In questo argomento viene descritto in altri dettagli sui collegamenti Guida processo scienza dei dati del Team. Per un'introduzione, vedere [Novità Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md).

2.  Nel riquadro **delle proprietà** del [modulo Importa dati](https://msdn.microsoft.com/library/azure/dn905997.aspx)selezionare **Database SQL Azure** nell'elenco a discesa **Origine dati** .

3.  Immettere nella casella di testo **nome del server di Database**`tcp:<DNS name of your virtual machine>,1433`

4.  Immettere il nome utente SQL nella casella di testo **nome account utente del Server** .

5.  Immettere la password dell'utente sql nella casella di testo **password dell'account utente Server** .

    ![Azure ML Importa dati][13]

##<a name="shutdown"></a>Arresto e rilasciare macchina virtuale quando non è in uso

Azure macchine virtuali di prezzo come **pagare solo per i quali si utilizza**. Per garantire che non sta fatturato quando non si utilizza la macchina virtuale, deve essere impostato su **arrestato (Deallocated)** .

> [AZURE.NOTE] Arrestare la macchina virtuale all'interno (con opzioni risparmio energia), viene interrotta la macchina virtuale ma rimane assegnata. Per essere certi che non viene addebitata, interrompere sempre macchine virtuali dal [Portale classica Azure](http://manage.windowsazure.com/). È anche possibile interrompere la macchina virtuale tramite Powershell chiamando ShutdownRoleOperation con "PostShutdownAction" è uguale a "StoppedDeallocated".

Per arrestare e rilasciare la macchina virtuale:

1. Accedere al [Portale classica Azure](http://manage.windowsazure.com/) usando l'account.  

2. Selezionare **macchine VIRTUALI** dalla barra di spostamento sinistro.

3. Nell'elenco di macchine virtuali, fare clic sul nome del computer virtuale, quindi passare alla pagina del **DASHBOARD** .

4. Nella parte inferiore della pagina, scegliere **ARRESTA**.

![Arresto macchine Virtuali][15]

La macchina virtuale verranno rilasciata ma non eliminata. È possibile riavviare il computer virtuale in qualsiasi momento dal portale classica di Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>È possibile utilizzare le macchine Virtuali Server di SQL Azure: che cos'è successiva?

La macchina virtuale è ora possibile utilizzare gli esercizi di scienze di dati. La macchina virtuale è pronto per essere utilizzato come server IPython blocco appunti per l'esplorazione e l'elaborazione dei dati e altre attività in combinazione con Azure apprendimento e Team dati scienza processo (TDSP).

Passaggi successivi del processo di scienze dei dati sono mappati del [Processo di scienze dei dati del Team](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e possono includere procedure dettagliate che spostare i dati nel HDInsight, elaborano e in tale posizione di esempio in preparazione per l'apprendimento dai dati di apprendimento di Azure computer.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
