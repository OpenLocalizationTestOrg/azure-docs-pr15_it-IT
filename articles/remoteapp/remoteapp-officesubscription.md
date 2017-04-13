
<properties 
    pageTitle="Come usare l'abbonamento a Office 365 con Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come è possibile usare l'abbonamento a Office 365 in Azure RemoteApp per condividere le applicazioni di Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Come usare l'abbonamento a Office 365 con RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Non tutti sanno che è possibile utilizzare l'abbonamento a Office 365 esistente in Azure RemoteApp per condividere le applicazioni di Office dal cloud. Continuare a leggere per informazioni su degli Office 365 + opzioni RemoteApp Azure, inclusi i collegamenti ad articoli relativi a Office 365 che consentono di sfruttare al meglio l'abbonamento.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Utilizzo di account Office 365 per RemoteApp di Azure
Articolo di nuovo Giovanni per tutte le informazioni: [come usare RemoteApp di Azure con gli account utente di Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>È possibile usare l'abbonamento a Office 365 per l'esecuzione di applicazioni di Office in Azure RemoteApp?

Sì! Con l'abbonamento a Office 365 è infatti l'unico modo per visualizzare le applicazioni di Office a RemoteApp di Azure.

(Nota: se la distribuzione di Azure RemoteApp viene fornita un partner di hosting, potrebbe essere possibile fornirgli licenze di Office in base a un [Contratto di licenza Provider del servizio](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


Il vantaggio di abbonamento a Office 365 è che consente di utilizzare la stessa licenza utente molti piattaforme diverse e ambienti, tra cui cloud Azure. Quando si usa le applicazioni di Office in Azure RemoteApp non è necessario acquistare altre licenze o configurare delle licenze esistenti in qualsiasi modo speciale. È sufficiente un abbonamento a Office 365 che include [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus consente [l'attivazione di computer condiviso](https://technet.microsoft.com/library/Dn782860.aspx) : questa funzionalità consente attivazione temporanea basate sull'utente per Office in virtuale e ambienti cloud come RemoteApp di Azure (e Servizi Desktop remoto).

Quali piani di Office 365 includono Office 365 ProPlus? Consultare la tabella [disponibilità servizio all'interno di ogni piano](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Si noti che non tutti i piani includono Office 365 ProPlus (ad esempio, il piano di Office 365 Business). Se il piano, non è consigliabile eseguire l'aggiornamento a un piano che esegue (ad esempio Office 365 Education E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>OK, così come sono le di Office 365 ProPlus licenze utilizzate con Azure RemoteApp?

Ogni licenza utente per Office 365 ProPlus consente a un singolo utente di attivare le applicazioni Office in un massimo di 5 computer plus Tablet e telefoni. Ogni attivazione registrato con l'utente fino a quando non vengono disattivare Office nel dispositivo. (Gli utenti possono gestire i dispositivi nel [portale di Office 365](https://portal.office365.com/)).

Con RemoteApp di Azure un singolo utente può accedere in più computer nello stesso giorno inavvertitamente. Ciò avviene perché il servizio gestisce automaticamente e scale di risorse nel cloud, mentre l'utente può vedere solo le applicazioni e i programmi condivisi. Per questo scenario Office 365 ProPlus offre una modalità di attivazione di computer condiviso: questo errore indica che l'utente non è necessario eseguire qualsiasi gestione delle licenze per accedere a queste risorse e che i singoli computer non contano rispetto al limite di attivazione di 5 computer.

Come si (amministratori) assegnare licenze di Office 365 ProPlus per gli utenti, consente Office nei propri dispositivi personali, nonché attraverso la raccolta di Azure RemoteApp.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Le applicazioni di Office è possibile usare con Office 365 e Azure RemoteApp?

Per attivare e condividere Office 2013 nelle distribuzioni RemoteApp di Azure, è possibile utilizzare l'abbonamento a Office 365. Attualmente non è supportata l'uso di altre versioni di Office con RemoteApp di Azure. Sono inclusi Office 2003, Office 2007, Office 2010 e Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Informazioni su Visio Pro o Project Pro?

L'immagine di Office 365 ProPlus incluso nell'abbonamento RemoteApp include Visio Pro e Project Pro. Ma non è possibile utilizzare l'abbonamento a Office 365 ProPlus per attivare tali programmi: hanno la propria licenza. È possibile attivarli nel [portale di Office 365](https://portal.office365.com/). 

Non è necessario questi programmi di licenza se non si desidera usare quel gruppo. Attivare solo le applicazioni di cui che si desidera utilizzare - e ignorare gli altri. Verrà comunque visualizzato nell'immagine, ma non è possibile utilizzare. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Come introduzione a Office 365 e Azure RemoteApp

Dopo aver scoperto i dettagli di gestione delle licenze di Office 365, iniziamo iniziare a usarlo in Azure RemoteApp - è molto semplice:

Quando si crea la raccolta di Azure RemoteApp, utilizzare l'immagine di **Office 365 ProPlus (è necessaria la sottoscrizione)** .

![Immagine di RemoteApp Azure con Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Questa immagine contiene la versione più recente di Windows Server e Office 365 ProPlus. Dopo aver configurato la raccolta (inclusi pubblicazione App), gli utenti semplicemente accedere a RemoteApp di Azure (utilizzando il client RemoteApp) e fornire le proprie credenziali di Office 365 per le applicazioni di Office. Licenze vengono automaticamente inviate senza tutti i set di o gestione obbligatorio.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>È possibile creare un'immagine personalizzata con Office 365 ProPlus?

È possibile creare un'immagine personalizzata per la raccolta contenente Office 365 ProPlus. Esistono 2 scelte - usare la foto Azure che sono disponibili o è possibile creare un'immagine personalizzata e installare Office 365 ProPlus sono.

### <a name="use-the-azure-gallery-image"></a>Usare l'immagine della raccolta Azure

Il modo più semplice per distribuire Office 365 ProPlus a una raccolta è a [partire da una delle immagini raccolta Azure](remoteapp-image-on-azurevm.md) inclusi nell'abbonamento RemoteApp di Azure. Verificare che si sceglie l'immagine di **Windows Server Host sessione Desktop remoto con Office 365 ProPlus preinstallato** . Quindi, installare le applicazioni desiderate nell'immagine e si è possibile continuare.

### <a name="use-a-custom-image"></a>Utilizzare un'immagine personalizzata

È anche possibile creare un'immagine personalizzata, è possibile creare una [Macchina virtuale Azure](remoteapp-image-on-azurevm.md) o [creare l'immagine in locale](remoteapp-create-custom-image.md) e caricarlo in Azure. In entrambi i casi, verificare che l'installazione di Office 365 ProPlus utilizzando il nodo di attivazione di computer condiviso. Usare lo [Strumento di distribuzione di Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) e seguire le [istruzioni](https://technet.microsoft.com/library/Dn782858.aspx) per l'installazione.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Disabilitare gli aggiornamenti automatici per Office 365 ProPlus nell'immagine personalizzata - importante

Immagine personalizzata viene utilizzato da Azure RemoteApp come modello per l'aggiunta di risorse aggiuntive della richiesta dall'aumento di utenti. Per evitare ritardi e problemi di connessione, è possibile disattivare gli aggiornamenti automatici per Office nell'immagine. In caso contrario, tutte le risorse creata con il modello vengono aggiornato automaticamente quando viene avviato. Utilizzare invece il processo di Azure RemoteApp standard per aggiornare l'immagine personalizzata. In questo modo aggiornare le applicazioni di Office una sola volta sull'immagine del modello e quindi consentire Azure RemoteApp occuparsi di ottenere gli aggiornamenti agli utenti.

Per disattivare gli aggiornamenti automatici, aggiungere quanto segue nel file di configurazione dello strumento di distribuzione di Office:

        <Updates Enabled="FALSE" />

A questo punto il file di configurazione deve contenere queste righe:
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>In che modo è possibile aggiornare un'immagine con Office 365 ProPlus?

Esistono diversi motivi per aggiornare l'immagine nella raccolta. Di seguito sono sufficienti pochi:

- Ottenere gli aggiornamenti più recenti di Windows 
- Ottenere gli ultimi aggiornamenti di applicazione di Office 365 ProPlus
- Aggiornare l'app personalizzata
- Modificare altre impostazioni di configurazione per l'immagine

Per i passaggi necessari-to-end per l'aggiornamento della raccolta per utilizzare l'immagine è aggiornato, vedere [di seguito](remoteapp-update.md). Ma per informazioni su come aggiornare l'immagine e Office 365 ProPlus, vedere le informazioni seguenti.

Si sono disponibili due opzioni per l'aggiornamento dell'immagine: sostituire l'immagine con una completamente nuova o manualmente aggiornare l'immagine esistente.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Sostituire l'immagine con la più recente nella galleria Azure + Aggiungi personalizzazioni
Con questa opzione fare in modo che Microsoft degli aggiornamenti del Server di Windows e Office 365 ProPlus. Invece di aggiornare l'immagine esistente, sarà necessario creare un'immagine completamente nuova in base all'immagine di raccolta più recente. Ripetere i passaggi in precedenza per personalizzare l'immagine, installare le applicazioni personalizzate, modificare la configurazione di immagine e così via.

Queste immagini vengono aggiornate regolarmente, in modo che è possibile posizionare facile e sapere che le app di Windows Server e Office 365 ProPlus siano aggiornate. Naturalmente, il compromesso è che è necessario applicare le personalizzazioni ogni volta che si riceve una nuova immagine. È possibile creare script per automatizzare l'impostazione le personalizzazioni.

### <a name="manually-update-your-existing-image"></a>Aggiornare manualmente l'immagine esistente

Con questa opzione, utilizzare gli strumenti di Windows standard per applicare gli aggiornamenti all'immagine. Per Office 365 ProPlus, usare lo strumento di distribuzione di Office per scaricare e installare le versioni di Office 365 ProPlus più recente.

> [AZURE.IMPORTANT] Nota: disattivare gli aggiornamenti automatici di Office 365 ProPlus.

Servono altre informazioni sull'uso lo strumento di distribuzione di Office per gli aggiornamenti?

- [Distribuire a portata di clic di prodotti Office 365 tramite lo strumento di distribuzione di Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [La distribuzione e aggiornamento di Office 365 ProPlus tramite lo strumento di distribuzione di Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (video)
- [Configurare le impostazioni di aggiornamento per Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)
