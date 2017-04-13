<properties
   pageTitle="Eseguire qualsiasi app di Windows in qualsiasi dispositivo con Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come condividere qualsiasi app di Windows con altri utenti tramite RemoteApp di Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Eseguire qualsiasi app di Windows in qualsiasi dispositivo con RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

È possibile eseguire un'applicazione di Windows in un punto qualsiasi in qualsiasi dispositivo immediatamente, gravemente - semplicemente utilizzando RemoteApp di Azure. Che si tratti di un'applicazione personalizzata programmata 10 anni o un'app di Office, gli utenti non sono necessario essere collegati a un sistema operativo specifico (ad esempio Windows XP) per le applicazioni alcuni.

Con RemoteApp di Azure, gli utenti possono anche utilizzare i propri dispositivi Android o Apple e ottenere la stessa esperienza arrivato in Windows (o in Windows Phone). A questo scopo che ospita l'applicazione di Windows in una raccolta di macchine virtuali Windows Azure - gli utenti possano accedervi da qualsiasi postazione hanno una connessione a internet. 

Sono disponibili informazioni su un esempio di esattamente come eseguire questa operazione.

In questo articolo verranno condividano tutti gli utenti di Access. Tuttavia, è possibile utilizzare qualsiasi app. Come è possibile installare l'app in un computer Windows Server 2012 R2, è possibile condividerla con la procedura seguente. È possibile verificare i [requisiti dell'app](remoteapp-appreqs.md) per assicurarsi che l'app funzionano.

Si noti che poiché l'accesso è un database e si vuole che il database sia utile, è necessario effettuare alcuni passaggi aggiuntivi per consentire l'accesso agli utenti la condivisione di dati di Access. Se l'app non è un database o non è necessario agli utenti di essere in grado di accedere a una condivisione file, è possibile ignorare la procedura descritta in questa esercitazione

> [AZURE.NOTE] <a name="note"></a>È necessario un account Azure per completare l'esercitazione:
> - È possibile [aprire un account Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): viene visualizzato crediti è possibile utilizzare per provare a pagamento servizi Azure e anche se vengono utilizzati massimo è possibile mantenere l'account e usare gratuiti servizi Azure, ad esempio siti Web. Carta di credito non verranno mai addebitata, a meno che non in modo esplicito modificare le impostazioni e chiedere di essere addebitati.
> - È possibile [attivare vantaggi sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): abbonamento MSDN Your offre crediti ogni mese che è possibile utilizzare per i servizi di Azure a pagamento.


## <a name="create-a-collection-in-remoteapp"></a>Creare una raccolta in RemoteApp

Avviare la creazione di una raccolta. La raccolta viene utilizzato come contenitore per le applicazioni e gli utenti. Ogni insieme è basato su un'immagine, è possibile crearne uno nuovo o utilizzare uno fornito con l'abbonamento. Per questa esercitazione verrà utilizzata l'immagine di valutazione di Office 2013 - contenga l'app che si desidera condividere.

1. Nel portale di Azure, scorrere verso il basso nella struttura di spostamento a sinistra fino a visualizzare RemoteApp. Aprire la pagina.
2. Fare clic su **Crea un insieme di RemoteApp**.
3. Fare clic su **Creazione rapida** e immettere un nome per la raccolta.
4. Selezionare l'area che si desidera utilizzare per creare la raccolta. Per risultati ottimali, selezionare l'area che vicino limitazioni geografiche alla posizione in cui gli utenti accederà l'app. Ad esempio, in questa esercitazione, gli utenti si troverà a Redmond. L'area di Azure più vicina è **US ovest**.
5. Selezionare il piano di fatturazione che si desidera utilizzare. Il piano di fatturazione base inserisce 16 utenti in una macchina virtuale Azure grande, mentre il piano di fatturazione standard con 10 utenti su una macchina virtuale Azure grandi dimensioni. Come esempio generale, il piano di base funziona perfettamente per flusso di lavoro tipo di voce dati. Per un'app di produttività, come Office, è possibile piano standard.
6. Infine, selezionare l'immagine di Office 2013 Professional. Questa immagine contiene app di Office 2013. Promemoria - questa immagine è valida solo per gli insiemi di valutazione e POCs. Si ' non supportati in questa immagine in una raccolta di produzione.
7. A questo punto, fare clic su **Crea RemoteApp insieme**.

![Creare un insieme di cloud in RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Verrà avviata la creazione della raccolta, ma può essere necessaria fino a un'ora.

Ora si è pronti ad aggiungere gli utenti.

## <a name="share-the-app-with-users"></a>Condividere l'app con gli utenti

Dopo la raccolta è stata creata correttamente, è possibile pubblicare l'accesso agli utenti e aggiungere gli utenti che devono avere accesso a tale.

Se si è spostati all'esterno del nodo di Azure RemoteApp durante la creazione della raccolta, iniziare eseguendo familiarità tornare alla home page della Azure.

2. Fare clic sulla raccolta precedentemente create per accedere a opzioni aggiuntive e configurare la raccolta.
![Una nuova raccolta cloud RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Nella scheda **pubblicazione** , fare clic su **pubblica** nella parte inferiore dello schermo e quindi fare clic su **pubblica avviare applicazioni del menu**.
![Pubblicare un programma RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selezionare le app che si desidera pubblicare dall'elenco. Per il nostro obiettivo è stato scelto di Access. Fare clic su **completa**. Attendere che le app completare la pubblicazione.
![Accesso per la pubblicazione in RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Al termine dell'app pubblicazione, testa sopra la scheda **L'accesso degli utenti** per aggiungere tutti gli utenti che devono accedere alle app. Immettere i nomi utente (indirizzo di posta elettronica) per gli utenti e quindi fare clic su **Salva**.

![Aggiungere utenti a RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. A questo punto, è necessario informare gli utenti queste nuove app e su come accedervi. A tale scopo, inviare agli utenti tramite posta elettronica che punta l'URL di download del client Desktop remoto.
![Client di scaricare URL per RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurare l'accesso per l'accesso

Alcune App necessario configurazione aggiuntivi dopo distribuirle mediante RemoteApp. In particolare, per l'accesso, verranno per creare una condivisione file su Azure che tutti gli utenti possono accedere. (Se non si desidera eseguire tale operazione, è possibile creare un [insieme di ibrido](remoteapp-create-hybrid-deployment.md) [anziché l'insieme di cloud] che consente agli utenti accedere a file e informazioni sulla rete locale.) Quindi, sarà necessario informare gli utenti per eseguire il mapping di un'unità locale nel proprio computer al Azure file system.

La prima parte che è l'amministratore di eseguire. Quindi, sono disponibili alcuni passaggi per gli utenti.

1. Avviare la pubblicazione interfaccia della riga di comando (cmd.exe). Nella scheda **pubblica** , selezionare **cmd**e quindi fare clic su **pubblica > pubblica programma usando percorso**.
2. Immettere il nome dell'app e il percorso. Ai fini, usare "Esplora File" come nome e "% SYSTEMDRIVE%\windows\explorer.exe" come percorso.
![Pubblicare il file cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. A questo punto è necessario creare un [account di archiviazione](../storage/storage-create-storage-account.md)di Azure. Denominata nostra "accessstorage", quindi selezionare un nome significativo. (Per misquote Highlander, possono essere presenti sola "accessstorage.") ![Account di archiviazione dei Azure](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Tornare al dashboard in modo che è possibile ottenere il percorso allo spazio di archiviazione (posizione endpoint). Si verrà: utilizzare questo argomento, dunque, verificare di copiarle in un punto.
![Il percorso di archiviazione di account](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Successivamente, dopo aver creato l'account di archiviazione, è necessario il tasto principale. Fare clic su **Gestisci i tasti di scelta**e quindi copiare il tasto principale.
6. A questo punto, impostare il contesto dell'account di archiviazione e creare una nuova condivisione di file per l'accesso. In una finestra di Windows PowerShell privilegi elevati, eseguire i cmdlet seguenti:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Per la condivisione, pertanto sono i cmdlet eseguiamo:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


A questo punto, è attiva dell'utente. Prima di tutto necessario gli utenti installare un [client RemoteApp](remoteapp-clients.md). Successivamente, gli utenti è necessario eseguire il mapping di un'unità dai loro account per la condivisione di file Azure che è stato creato e aggiungere i propri file di Access. Ecco come quello che:

1. Nel client RemoteApp access App pubblicato. Avviare il programma cmd.exe.
2. Eseguire il seguente comando per eseguire il mapping di un'unità dal computer alla condivisione di file:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Se si imposta il parametro **/ persistente** su Sì, l'unità viene mantenuto tra le sessioni.
1. A questo punto, avviare l'app di Esplora File da RemoteApp. Copiare i file di Access che si desidera utilizzare nell'app condivisa alla condivisione di file.
![Applicazione pratica di accedere ai file in una condivisione di Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Infine, aprire Access e quindi aprire il database condiviso. È necessario visualizzare i dati in Access in esecuzione dal cloud.
![Un database di Access reale in esecuzione dal cloud](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

È ora possibile utilizzare l'accesso in uno qualsiasi dei dispositivi - assicurarsi che l'installazione di un client RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Ora che è stata acquisito la creazione di una raccolta, provare a creare una [raccolta che utilizza Office 365](remoteapp-tutorial-o365anywhere.md). Oppure è possibile creare un [insieme di ibrido ](remoteapp-create-hybrid-deployment.md)che possono accedere alla rete locale.

<!--Image references-->
 
