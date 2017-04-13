<properties
   pageTitle="Pubblicazione di un servizio Cloud usando gli strumenti di Azure | Microsoft Azure"
   description="Informazioni sulle procedure per la pubblicazione di progetti di servizi cloud Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Pubblicazione di un servizio Cloud usando gli strumenti di Azure

Con gli strumenti di Azure per Microsoft Visual Studio, è possibile pubblicare l'applicazione Azure direttamente da Visual Studio. Visual Studio supporta integrata pubblicazione per la gestione temporanea o l'ambiente di produzione di un servizio cloud.

Prima di poter pubblicare un'applicazione di Azure, è necessario disporre di un abbonamento a Azure. È anche necessario configurare un account di servizio e lo spazio di archiviazione cloud che verrà utilizzato da un'applicazione. È possibile impostare questi [Azure portale classica](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Quando si pubblica, è possibile selezionare l'ambiente di distribuzione per il servizio cloud. È anche necessario selezionare un account di archiviazione che consente di archiviare il pacchetto di applicazione per la distribuzione. Dopo la distribuzione, il pacchetto di applicazione viene rimosso dall'account di archiviazione.

Quando si sviluppa e testare un'applicazione Azure, è possibile utilizzare la distribuzione Web per pubblicare le modifiche in modo incrementale per i ruoli di web. Dopo la pubblicazione dell'applicazione in un ambiente di distribuzione, distribuzione Web consente di distribuire le modifiche direttamente al computer che esegue il ruolo web virtuale. Non è necessario creare un pacchetto e pubblicare l'intera applicazione Azure ogni volta che si desidera aggiornare il proprio ruolo web per verificare le modifiche. Con questo approccio è possibile tenere le modifiche di ruolo web disponibile nel cloud per il testing senza attendere che l'applicazione pubblicato in un ambiente di distribuzione.

Utilizzare le procedure seguenti per pubblicare l'applicazione Azure e aggiornare un ruolo web tramite la distribuzione Web:

- Pubblicare o pacchetto di un'applicazione Azure da Visual Studio

- Aggiornare un ruolo web come parte di sviluppo e di ciclo di test

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Pubblicare o pacchetto di un'applicazione Azure da Visual Studio

Quando si pubblica l'applicazione Azure, è possibile eseguire una delle operazioni seguenti:

- Creare un pacchetto di servizio: È possibile utilizzare il pacchetto e il file di configurazione del servizio per pubblicare l'applicazione in un ambiente di distribuzione dal [Portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

- Pubblicare il progetto Azure da Visual Studio: per pubblicare l'applicazione direttamente in Azure, utilizzare la pubblicazione guidata. Per informazioni, vedere [Pubblicazione guidata applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Per creare un pacchetto di servizio da Visual Studio

1. Quando si è pronti pubblicare l'applicazione, aprire Esplora soluzioni, aprire il menu di scelta rapida per il progetto Azure che contiene i ruoli e scegliere pubblica.

1. Per creare un pacchetto di servizio solo, procedere come segue:  

  1. Nel menu di scelta rapida per il progetto Azure scegliere **pacchetto**.

  1. Nella finestra di dialogo **Applicazione Azure pacchetto** scegliere la configurazione del servizio per il quale si desidera creare un pacchetto e quindi scegliere la configurazione della build.

  1. (facoltativo) Per attivare Desktop remoto per il servizio cloud dopo la pubblicazione, selezionare la casella di controllo **Attiva Desktop remoto per tutti i ruoli** e quindi selezionare **Impostazioni** di configurazione Desktop remoto. Se si desidera eseguire il debug il servizio cloud dopo la pubblicazione, attivare il debug remoto selezionando **Abilita Debugger remoto per tutti i ruoli**.

      Per ulteriori informazioni, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).

  1. Per creare il pacchetto, selezionare il collegamento di **pacchetto** .

      Esplora file Mostra il percorso del file del pacchetto appena creato. È possibile copiare il percorso in modo che è possibile utilizzare dal [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Per pubblicare il pacchetto in un ambiente di distribuzione, è necessario utilizzare questa posizione come percorso di pacchetto quando si crea un servizio cloud e distribuisce il pacchetto in un ambiente con il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facoltativo) Per annullare il processo di distribuzione, nel menu di scelta rapida per la voce nel log delle attività, scegliere **Annulla e rimuovere**. Questa interrompe il processo di distribuzione ed elimina l'ambiente di distribuzione comuni.

    >[AZURE.NOTE] Per rimuovere l'ambiente di distribuzione dopo che è stato distribuito, è necessario utilizzare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facoltativo) Dopo avere iniziato istanze del ruolo, Visual Studio viene automaticamente l'ambiente di distribuzione del nodo di **Servizi Cloud** in Esplora Server. Da qui è possibile visualizzare lo stato delle istanze dei singoli ruoli. Vedere [le risorse di gestione di Azure con Esplora risorse Cloud](vs-azure-tools-resources-managing-with-cloud-explorer.md). L'illustrazione seguente mostra le istanze di ruolo mentre sono ancora in stato inizializzazione:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Aggiornare un ruolo Web come parte di sviluppo e di ciclo di test

Se l'infrastruttura di back-end dell'app è stabile, ma i ruoli web necessitano più accurato, è possibile utilizzare distribuzione Web per aggiornare solo un ruolo web nel progetto. Questo è utile quando non si vuole ricostruire e ridistribuire i ruoli di lavoro back-end, o se si dispone di più ruoli web e si desidera aggiornare solo uno dei ruoli web.

### <a name="requirements"></a>Requisiti

Ecco i requisiti per l'utilizzo di distribuzione Web per aggiornare il proprio ruolo web:

- **Per sviluppo e la verifica solo a scopo:** Le modifiche apportate direttamente al computer virtuale in cui viene eseguito il ruolo web. Se questa macchina virtuale deve essere riutilizzata, le modifiche andranno perse poiché il pacchetto originale è stato pubblicato viene utilizzato per ricreare la macchina virtuale per il ruolo. È necessario ripubblicare l'applicazione per ottenere le modifiche più recenti per il ruolo web.

- **Solo i ruoli di web possono essere aggiornato ogni:** Ruoli di lavoro non possono essere aggiornati. Inoltre, è possibile aggiornare RoleEntryPoint in role.cs web.

- **Supportino solo una singola istanza di un ruolo web:** È possibile assegnare più istanze di qualsiasi ruolo web nel proprio ambiente di distribuzione. Tuttavia, sono supportati più ruoli web ogni una sola istanza.

- **è necessario attivare connessioni desktop remoto:** Questa operazione è necessaria che distribuzione Web può utilizzare l'utente e la password per connettere la macchina virtuale per distribuire le modifiche nel server che esegue Internet Information Services (IIS). Inoltre, potrebbe essere necessario connettere la macchina virtuale per aggiungere un certificato attendibile a IIS nella macchina virtuale. (In questo modo che la connessione remota per IIS che viene utilizzato da distribuzione Web è protetta).

La procedura seguente presuppone che si usa la creazione guidata **Pubblicazione applicazione Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Per abilitare distribuzione quando si pubblica l'applicazione Web

1. Per attivare l' **Abilita distribuzione Web** per tutte le casella di controllo ruoli web, configurare le connessioni a desktop remote. Selezionare **Attiva Desktop remoto** per tutti i ruoli e quindi immettere le credenziali che verranno utilizzate per la connessione remota nella finestra di **Configurazione Desktop remoto** che viene visualizzato. Per ulteriori informazioni, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md) .

1. Per abilitare la distribuzione Web per tutti i ruoli di web nell'applicazione, selezionare **Attiva distribuzione Web per tutti i ruoli di web**.

    Viene visualizzato un triangolo giallo. Distribuisci Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, non è consigliabile caricare dati riservati. Se è necessario proteggere il processo per i dati sensibili, è possibile aggiungere un certificato SSL da utilizzare per le connessioni di distribuzione Web. Il certificato deve essere un certificato attendibile. Per informazioni su come eseguire questa operazione, vedere la sezione **Per verificare la distribuzione sicura Web** più avanti in questo argomento.

1. Scegliere **Avanti** per visualizzare la schermata di **Riepilogo** e quindi scegliere **pubblica** per distribuire il servizio cloud.

    Il servizio cloud viene pubblicato. La macchina virtuale creata contiene connessioni remote abilitate per IIS in modo che la distribuzione Web può essere utilizzata per aggiornare i ruoli web senza ripubblicare loro.

    >[AZURE.NOTE] Se si dispone di più istanze configurata per un ruolo web, viene visualizzato un messaggio di avviso che indica che ogni ruolo web verranno limitato a un'istanza solo nel pacchetto creato per pubblicare l'applicazione. Fare clic su **OK** per continuare. Come indicato nella sezione requisiti, è possibile impostare più di un ruolo web ma solo un'istanza di ogni ruolo.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Per aggiornare il proprio ruolo Web utilizzando la distribuzione Web

1. Per utilizzare distribuzione Web, apportare modifiche al codice del progetto per uno dei ruoli web Visual Studio che si desidera pubblicare, quindi rapida il nodo del progetto nella soluzione e scegliere **pubblica**. Verrà visualizzata la finestra di dialogo **Pubblica sito Web** .

1. (Facoltativo) Se è stato aggiunto un certificato SSL attendibile da utilizzare per le connessioni remote per IIS, è possibile deselezionare la casella di controllo **Consenti certificato non attendibile** . Per informazioni su come aggiungere un certificato per rendere sicure distribuzione Web, vedere la sezione **Per rendere Web distribuire protetta** più avanti in questo argomento.

1. Per utilizzare distribuzione Web, il meccanismo pubblica richiede il nome utente e la password che è configurato per la connessione desktop remota quando è stato pubblicato prima di tutto il pacchetto.

  1. In **nome utente**immettere il nome utente.

  1. Nella casella **Password**immettere la password.

  1. (Facoltativo) Se si desidera salvare la password nel profilo, scegliere **Salva password**.

1. Per pubblicare le modifiche al proprio ruolo web, scegliere **pubblica**.

    Viene visualizzata la riga di stato **avviato pubblica**. Al termine di pubblicazione, verrà visualizzato **pubblicazione completata** . Le modifiche a questo punto sono state distribuite al ruolo web sul computer virtuale. A questo punto è possibile avviare un'applicazione Azure nell'ambiente di Azure per verificare le modifiche.

### <a name="to-make-web-deploy-secure"></a>Per rendere Web distribuire sicure

1. Distribuisci Web utilizza un certificato autofirmato non attendibile per impostazione predefinita, non è consigliabile caricare dati riservati. Se è necessario proteggere il processo per i dati sensibili, è possibile aggiungere un certificato SSL da utilizzare per le connessioni di distribuzione Web. Il certificato deve essere un certificato attendibile, si ottengono da un'autorità di certificazione (CA).

    Per una distribuzione Web protezione per ogni macchina virtuale per ognuno dei ruoli web, è necessario caricare il certificato attendibile che si desidera utilizzare per web distribuire al [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). In questo modo che il certificato viene aggiunto alla macchina virtuale creato per il ruolo web quando si pubblica l'applicazione.

1. Per aggiungere un certificato SSL attendibile a IIS da utilizzare per le connessioni remote, procedere come segue:

  1. Per connettere la macchina virtuale che esegue il ruolo web, selezionare l'istanza del ruolo web nel **Cloud Explorer** o **Esplora Server**e quindi scegliere il comando **Connetti utilizzando Desktop remoto** . Per istruzioni dettagliate su come connettere la macchina virtuale, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).

      Il browser verrà richiesto di scaricare un. File con estensione RDP.

  1. Per aggiungere un certificato SSL, aprire il servizio di gestione in Gestione IIS. In Gestione IIS, abilitare SSL aprendo il collegamento **associazioni** nel riquadro **Azioni** . Viene visualizzata la finestra di dialogo **Aggiungi associazione del sito** . Scegliere **Aggiungi**e quindi scegliere HTTPS nell'elenco a discesa **tipo** . Nell'elenco di **certificati SSL** , scegliere il certificato SSL che ha firmato da un'autorità di certificazione e caricare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Per ulteriori informazioni, vedere [Configurare le impostazioni di connessione per il servizio di gestione](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Se si aggiunge un certificato SSL attendibile, triangolo giallo di avviso non viene visualizzata la **Pubblicazione guidata**.

## <a name="include-files-in-the-service-package"></a>Includere i file nel pacchetto di servizio

Potrebbe essere necessario includere file specifici nel pacchetto di servizio in modo che siano disponibili nel computer virtuale creato per un ruolo. È consigliabile, ad esempio, aggiungere un .exe o un file con estensione msi che viene utilizzato da uno script di avvio per il pacchetto di servizio. O potrebbe essere necessario aggiungere un assembly che è necessario un progetto web ruolo o lavoro ruolo. Per includere i file che deve essere aggiunta alla soluzione per l'applicazione Azure.

### <a name="to-include-files-in-the-service-package"></a>Per includere i file del pacchetto di servizio

1. Per aggiungere un assembly a un pacchetto di servizio, seguire la procedura seguente:

  1. In **Esplora soluzioni** aprire il nodo del progetto per il progetto in cui Manca assembly di riferimento.

  1. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **dei riferimenti** e quindi scegliere **Aggiungi riferimento**. Verrà visualizzata la finestra di dialogo Aggiungi riferimento.

  1. Scegliere il riferimento che si desidera aggiungere e quindi scegliere il pulsante **OK** .

      Il riferimento viene aggiunto all'elenco nella cartella **riferimenti** .

  1. Aprire il menu di scelta rapida per l'assembly che è stato aggiunto e scegliere **proprietà**. Viene visualizzata la finestra **proprietà** .

      Per includere questo assembly nel pacchetto service, nell' **elenco copia locale** sceglie **True**.

1. In **Esplora soluzioni** aprire il nodo del progetto per il progetto in cui Manca assembly di riferimento.

1. Per aggiungere l'assembly al progetto, aprire il menu di scelta rapida per la cartella **dei riferimenti** e quindi scegliere **Aggiungi riferimento**. Verrà visualizzata la finestra di dialogo **Aggiungi riferimento** .

1. Scegliere il riferimento che si desidera aggiungere e quindi scegliere il pulsante **OK** .

    Il riferimento viene aggiunto all'elenco nella cartella **riferimenti** .

1. Aprire il menu di scelta rapida per l'assembly che è stato aggiunto e scegliere **proprietà**. Viene visualizzata la finestra Proprietà.

1. Per includere questo assembly nel pacchetto service, nell'elenco **Copia locale** , scegliere **True**.

1. Per includere i file nel pacchetto di servizio che sono state aggiunte al progetto ruolo web, aprire il menu di scelta rapida per il file e quindi scegliere **proprietà**. Nella finestra delle **proprietà** , scegliere **contenuto** dalla casella di riepilogo **Operazione di compilazione** .

1. Per includere i file nel pacchetto di servizio che sono state aggiunte al progetto di ruolo di lavoro, aprire il menu di scelta rapida per il file e quindi scegliere **proprietà**. Nella finestra delle **proprietà** , scegliere **Copia se più recente** dalla casella di riepilogo **Copia nella directory di output** .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla pubblicazione in Azure da Visual Studio, vedere [Pubblicazione guidata applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).
