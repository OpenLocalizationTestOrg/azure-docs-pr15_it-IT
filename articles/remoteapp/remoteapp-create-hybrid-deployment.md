<properties
    pageTitle="Creazione di un insieme di ibrido per Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come creare una distribuzione di RemoteApp che si connette alla rete interna."
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

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Creazione di un insieme di ibrido per RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Esistono due tipi di raccolte di Azure RemoteApp:

- Cloud: risiede completamente Azure. È possibile scegliere di salvare tutti i dati nel cloud (in modo che un insieme di basata solo su cloud) o per connettere la raccolta a un VNET e salvare i dati sono.   
- Ibrido: include una rete virtuale per l'accesso locale - richiede l'uso di Azure Active Directory e un ambiente di Active Directory locale.

Non sapere quale è necessario? Estrarre [il tipo di raccolta necessari per RemoteApp di Azure](remoteapp-collections.md).

In questa esercitazione viene illustrato il processo di creazione di un insieme di ibrido. Sono disponibili otto passaggi:

1.  Decidere quali [immagine](remoteapp-imageoptions.md) da utilizzare per la raccolta. Creare un'immagine personalizzata o usare una delle immagini Microsoft incluse con l'abbonamento.
2. Configurare la rete virtuale. Verificare le informazioni [VNET pianificazione](remoteapp-planvnet.md) e [il ridimensionamento](remoteapp-vnetsizing.md) .
2.  Creare una raccolta.
2.  Aggiungere l'insieme al proprio dominio locale.
3.  Aggiungere un'immagine di modello per la raccolta.
4.  Configurare la sincronizzazione della directory. Azure RemoteApp richiede integrare Azure Active Directory da uno dei due 1) configurazione Azure Active Directory Sync con l'opzione di sincronizzazione delle Password o 2) configurazione Azure Active Directory Sync senza l'opzione di sincronizzazione delle Password, ma con un dominio federato per ADFS. Verificare le [informazioni di configurazione per Active Directory con RemoteApp](remoteapp-ad.md).
5.  Pubblicare App RemoteApp.
6.  Configurare l'accesso degli utenti.

**Prima di iniziare**

È necessario eseguire le operazioni seguenti prima di creare la raccolta:

- [Iscriversi](https://azure.microsoft.com/services/remoteapp/) per RemoteApp di Azure.
- Creare un account utente in Active Directory da utilizzare come l'account di servizio RemoteApp di Azure. Limitare le autorizzazioni per l'account in modo che solo partecipare computer al dominio.
- Raccogliere informazioni sulla rete locale: l'indirizzo IP di informazioni e dettagli dispositivo VPN.
- Installare il modulo di [Azure PowerShell](../powershell-install-configure.md) .
- Raccogliere informazioni sugli utenti che si desidera concedere l'accesso al. È necessario il nome dell'entità utente di Azure Active Directory (ad esempio name@contoso.com) per ogni utente. Assicurarsi che l'UPN corrisponda tra Azure Active Directory e Active Directory.
- Scegliere l'immagine del modello. Un'immagine di modello Azure RemoteApp contiene le App e i programmi che si desidera pubblicare per gli utenti. Per ulteriori informazioni, vedere [Opzioni di immagine RemoteApp di Azure](remoteapp-imageoptions.md) .
- Se si desidera utilizzare l'immagine di Office 365 ProPlus? Estrarre informazioni [qui](remoteapp-officesubscription.md).
- [Configurare Active Directory per RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Passaggio 1: Configurare la rete virtuale
È possibile distribuire un insieme di ibrida che utilizza una rete virtuale Azure esistente oppure è possibile creare una nuova rete virtuale. Una rete virtuale consente di dati di access gli utenti della rete locale tramite risorse remote RemoteApp. Tramite una rete virtuale Azure consente l'accesso alla rete diretto insieme ad altri servizi di Azure e macchine virtuali distribuiti a tale rete virtuale.

Verificare le informazioni [VNET pianificazione](remoteapp-planvnet.md) e le [dimensioni VNET](remoteapp-vnetsizing.md) prima di creare il VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Creare un VNET Azure e partecipare alla distribuzione di Active Directory

Prima di tutto creare una [rete virtuale](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). In tal caso la scheda di **rete** nel portale di Azure. È necessario connettersi alla rete aziendale virtuale per la distribuzione di Active Directory che viene sincronizzata al tenant di Azure Active Directory.

Per ulteriori informazioni, vedere [creare una rete virtuale tramite il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Assicurarsi che la rete virtuale è pronta per RemoteApp di Azure
Prima di creare la raccolta, assicurarsi che la nuova rete virtuale sia pronta. È possibile convalidare eseguendo le operazioni seguenti:

1. Creare una macchina virtuale Azure all'interno della subnet della rete virtuale che appena creato per RemoteApp.
2. Utilizzare Desktop remoto per connettere la macchina virtuale. (Fare clic su **Connetti**.)
3. Parteciparvi nella stessa implementazione di Active Directory che si desidera utilizzare per RemoteApp.

Che ha operato? La rete virtuale e subnet sono pronti per Azure RemoteApp!

È possibile trovare ulteriori informazioni sulla creazione di Azure macchine virtuali e connettersi con Desktop remoto [](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Passaggio 2: Creare un insieme di Azure RemoteApp ##



1. Nel [portale di Azure](http://manage.windowsazure.com), passare alla pagina RemoteApp di Azure.
2. Fare clic su **Nuovo > Crea con VNET**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che si desidera utilizzare - standard o di base.
5. Scegliere il VNET dall'elenco a discesa elenco e quindi subnet.
6. Scegliere di partecipare al proprio dominio.
5. Fare clic su **Crea RemoteApp insieme**.

Dopo aver creata la raccolta di Azure RemoteApp, fare doppio clic sul nome della raccolta. Che verrà visualizzata la pagina **Avvio rapido** , in cui aver completato la configurazione della raccolta.

È stato un problema? Verificare le [informazioni sulla risoluzione dei problemi di ibrido insieme](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Passaggio 3: Collegare la raccolta al dominio locale ##


1. Nella pagina **Guida introduttiva** , fare clic su **partecipa un dominio locale**.
2. Aggiungere l'account di servizio Azure RemoteApp al proprio dominio di Active Directory locale. È necessario il nome di dominio, unità organizzativa, nome utente dell'account di servizio e la password.

    Si tratta le informazioni raccolte se è stata seguita la procedura descritta in [Configura Active Directory per RemoteApp di Azure](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Passaggio 4: Collegamento a un'immagine di Azure RemoteApp ##

Un'immagine di modello Azure RemoteApp contiene i programmi che si desidera condividere con gli utenti. È possibile creare una nuova [immagine del modello](remoteapp-imageoptions.md) o creare un collegamento a un'immagine esistente (uno già importati o caricamento di Azure RemoteApp). È anche possibile collegare a una delle Azure RemoteApp [le immagini dei modelli](remoteapp-images.md) che contengono Office 365 o applicazioni di Office 2013 (per l'utilizzo di valutazione).

Se si sta caricando la nuova immagine, è necessario immettere il nome e scegliere la posizione dell'immagine. Nella pagina successiva della procedura guidata, si verrà visualizzato un set di cmdlet di PowerShell - copia ed eseguire i cmdlet da un prompt di Windows PowerShell con privilegi elevato per caricare l'immagine specificata.

Se si sta collegando a un'immagine di modello esistente, è sufficiente specificare il nome dell'immagine, luogo e sottoscrizioni Azure associato.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Passaggio 5: Configurare la sincronizzazione delle directory Active Directory ##

Azure RemoteApp richiede integrare Azure Active Directory da uno dei due 1) configurazione Azure Active Directory Sync con l'opzione di sincronizzazione delle Password o 2) configurazione Azure Active Directory Sync senza l'opzione di sincronizzazione delle Password, ma con un dominio federato per ADFS.

Estrarre [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) : questo articolo consente di impostare l'integrazione di directory in 4 passaggi.

Per la pianificazione di informazioni e istruzioni dettagliate, vedere [roadmap sulla sincronizzazione della Directory](http://msdn.microsoft.com//library/azure/hh967642.aspx) .

## <a name="step-6-publish-apps"></a>Passaggio 6: Pubblicare App ##

Un'app di Azure RemoteApp è l'app o un programma che offrono agli utenti. Si trova nell'immagine modello caricato per la raccolta. Quando un utente accede a un'app, viene visualizzato per l'esecuzione nel proprio ambiente locale, ma è davvero in esecuzione in Azure.

Prima che gli utenti possono accedere App, è necessario per la pubblicazione: consente agli utenti accedere le app tramite il client Desktop remoto.

È possibile pubblicare app più alla raccolta. Dalla pagina di pubblicazione, fare clic su **pubblica** per aggiungere un'app. È possibile pubblicare dal menu di **avvio** dell'immagine di modello o specificando il percorso sull'immagine del modello per l'app. Se si sceglie di aggiungere dal menu **Start** , scegliere il programma da aggiungere. Se si sceglie di specificare il percorso per l'app, specificare un nome per l'app e il percorso in cui è installato sull'immagine del modello.

## <a name="step-7-configure-user-access"></a>Passaggio 7: Configurare l'accesso degli utenti ##

Dopo avere creato la raccolta, è necessario aggiungere gli utenti che si desidera essere in grado di utilizzare le risorse remote. Gli utenti di fornire l'accesso è necessario essere in esiste nel tenant di Active Directory associato all'abbonamento utilizzato per creare la raccolta di Azure RemoteApp.

1.  Nella pagina Guida introduttiva, fare clic su **Configura l'accesso degli utenti**.
2.  Immettere l'account di lavoro (da Active Directory) o un account Microsoft che si desidera concedere l'accesso.

    **Note:**

    Assicurarsi di utilizzare il “user@domain.com” formato.

    Se si utilizza Office 365 ProPlus nella raccolta, è necessario utilizzare le identità di Active Directory per gli utenti. In questo modo convalidare licenze.


3.  Dopo che gli utenti vengono convalidati, fare clic su **Salva**.


## <a name="next-steps"></a>Passaggi successivi ##
Ecco fatto: aver creato e distribuito la raccolta di ibrido RemoteApp di Azure. Il passaggio successivo consiste nel dispone agli utenti di scaricare e installare il client Desktop remoto. È possibile trovare l'URL per il client nella pagina Azure RemoteApp Quick Start. Fare in modo che gli utenti nel client, quindi accedere App che è stato pubblicato.



### <a name="help-us-help-you"></a>Aiutaci a consentono di
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic su **Modifica nella GitHub** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.
