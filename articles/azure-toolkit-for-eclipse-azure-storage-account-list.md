<properties
    pageTitle="Elenco di Account di archiviazione Azure"
    description="Gestire le impostazioni dell'account di archiviazione utilizzando il Toolkit di Azure per Eclisse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Elenco di Account di archiviazione Azure #

Gli account di archiviazione Azure abilitare percorsi di download da utilizzare per JDK, server applicazioni e componenti non autorizzati, oltre che per l'archiviazione di stato quando si utilizza la memorizzazione nella cache. Eclisse gestisce un elenco di account noti lo spazio di archiviazione disponibile per i progetti nell'area di lavoro Eclisse. Per aprire la finestra di dialogo **Account di archiviazione** che consente di gestire l'elenco all'interno di Eclisse, fare clic su **finestra**, fare clic su **Preferenze**, espandere **Azure**e quindi fare clic su **Account di archiviazione**.

Di seguito viene illustrata la finestra di dialogo **Account di archiviazione** .

![][ic719496]

Questa finestra di dialogo può essere aperti anche da un collegamento **account** nelle finestre di dialogo che utilizzano gli account di archiviazione, ad esempio le operazioni seguenti:

* Scheda **JDK** della finestra di dialogo **Configurazione del Server** .
* Scheda **Server** della finestra di dialogo **Configurazione del Server** .
* Finestra di dialogo **Aggiungi componente** .
* Finestra di dialogo proprietà **memorizzazione nella cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Per importare gli account di archiviazione tramite un file di impostazioni di pubblicazione ##

1. Nella finestra di dialogo **Account di archiviazione** , fare clic su **Importa da file di impostazioni di pubblicazione**.
2. (Ignorare questo passaggio se un file di impostazioni pubblica già stato salvato nel computer locale) Nella finestra di dialogo **Informazioni sull'abbonamento di importazione** , fare clic su **Scarica File di impostazioni di pubblicazione**. Se non si è ancora stato effettuato l'accesso all'account Azure, verrà richiesto agli utenti di accedere. Quindi verrà richiesto di salvare un Azure pubblicare file di impostazioni. (È possibile ignorare le risultante istruzioni visualizzate nelle pagine di accesso - vengono fornite dal portale di Azure e destinati agli utenti di Visual Studio) Salvarlo nel computer locale.
3. Ancora presente nella finestra di dialogo **Informazioni sull'abbonamento di importazione** , fare clic sul pulsante **Sfoglia** , selezionare il file di impostazioni di pubblicazione che salvato localmente in precedenza e quindi fare clic su **Apri**.
4. Fare clic su **OK** per chiudere la finestra di dialogo **Informazioni sull'abbonamento di importazione** .

## <a name="to-create-a-new-storage-account"></a>Per creare un nuovo account di archiviazione ##

1. Nella finestra di dialogo **Account di archiviazione** , fare clic su **Aggiungi**.
2. Nella finestra di dialogo **Aggiungi Account lo spazio di archiviazione** , fare clic su **Nuovo**.
3. Nella finestra di dialogo **Nuovo Account di archiviazione** , specificare i valori per le operazioni seguenti:
    * Nome dell'account di archiviazione.
    * Posizione dell'account di archiviazione.
    * Descrizione dell'account di archiviazione.
    * L'abbonamento a cui appartiene l'account di archiviazione.
4. Fare clic su **OK** per chiudere la finestra di dialogo **Nuovo Account di archiviazione** .

Possono richiedere alcuni minuti per l'account di archiviazione da creare. Una volta creato, fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi Account lo spazio di archiviazione** e il nuovo account di archiviazione verranno tutti aggiunti all'elenco degli account di archiviazione disponibile.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Per aggiungere un account di archiviazione esistente nell'elenco ##

1. Se si dispone già di un account di archiviazione Azure, crearne uno seguendo i passaggi elencati nella sezione **per creare una nuova sezione account archiviazione** sopra. (In alternativa, è possibile creare un nuovo account di archiviazione al [Portale di gestione Azure][].)
2. Nella finestra di dialogo **Account di archiviazione** , fare clic su **Aggiungi**.
3. Nella finestra di dialogo **Aggiungi Account lo spazio di archiviazione** immettere valori per **nome** e **Tasto di scelta rapida**. Per un account di archiviazione Azure esistente deve essere la chiave account nome e l'accesso. Usare la sezione **dello spazio di archiviazione** del [Portale di gestione Azure][] per visualizzare i nomi degli account di archiviazione e chiavi. La finestra di dialogo **Aggiungi Account lo spazio di archiviazione** sarà simile al seguente.

    ![][ic719497]

4. Fare clic su **OK** per chiudere la finestra di dialogo **Aggiungi Account lo spazio di archiviazione** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Per modificare un account di archiviazione per l'utilizzo di una nuova chiave di accesso ##

1. Nella finestra di dialogo **Account di archiviazione** , fare clic sull'account di archiviazione che si desidera modificare e quindi fare clic su **Modifica**.
2. Nella finestra di dialogo **Modifica dello spazio di archiviazione Account tasto di scelta** , modificare il valore di **Tasto di scelta rapida** .
3. Fare clic su **OK** per chiudere la finestra di dialogo **Modifica dello spazio di archiviazione Account tasto di scelta** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Per rimuovere un account di archiviazione dall'elenco gestito in Eclisse ##

1. Nella finestra di dialogo **Account di archiviazione** , fare clic sull'account di archiviazione che si desidera modificare e quindi fare clic su **Rimuovi**.
2. Fare clic su **OK** quando viene richiesto di rimuovere l'account di archiviazione.

>[AZURE.NOTE] Rimuovere l'account di archiviazione tramite la finestra di dialogo **Account di archiviazione** rimuove solo dall'elenco degli account di archiviazione visualizzabili all'interno di Eclisse. Non rimuove l'account di archiviazione dall'abbonamento Azure. Inoltre, l'account di archiviazione Impossibile visualizzati di nuovo nell'elenco dopo Eclisse ricarica i dettagli dell'abbonamento.

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
