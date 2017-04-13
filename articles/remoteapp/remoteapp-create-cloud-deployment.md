<properties 
    pageTitle="Come creare un insieme di cloud di Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come creare una distribuzione di Azure RemoteApp per salvare i dati nel cloud Azure." 
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

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Come creare un insieme di cloud di Azure RemoteApp

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Esistono due tipi di [raccolte di Azure RemoteApp](remoteapp-collections.md): 

- Cloud: risiede completamente Azure. È possibile scegliere di salvare tutti i dati nel cloud (in modo che un insieme di basata solo su cloud) o per connettere la raccolta a un VNET e salvare i dati sono.   
- Ibrido: include una rete virtuale per l'accesso locale - richiede l'uso di Azure Active Directory e un ambiente di Active Directory locale.

In questa esercitazione viene illustrato il processo di creazione di un insieme di cloud. Esistono quattro passaggi: 

1.  Creare un insieme di Azure RemoteApp.
2.  Se si desidera configurare la sincronizzazione della directory. Se si usa Active Directory Azure + Active Directory, è necessario sincronizzare gli utenti, contatti e le password da Active Directory locale al tenant di Azure Active Directory.
5.  Pubblicare app.
6.  Configurare l'accesso degli utenti.


**Prima di iniziare**

È necessario eseguire le operazioni seguenti prima di creare la raccolta:

- [Iscriversi](https://azure.microsoft.com/services/remoteapp/) per RemoteApp di Azure. 
- Raccogliere informazioni sugli utenti che si desidera concedere l'accesso al. Può trattarsi di informazioni relative all'account Microsoft o informazioni sull'account di lavoro di Active Directory per gli utenti.
- Si presuppone che si è uno dei due prevede di utilizzare una delle immagini modello fornite come parte dell'abbonamento o di avere già caricato l'immagine di modello da usare. Se è necessario caricare un'immagine di modello diverso, è possibile farlo dalla pagina le immagini dei modelli. È sufficiente fare clic su **Carica un'immagine di modello** e seguire i passaggi della procedura guidata. 
- Se si desidera utilizzare l'immagine di Office 365 ProPlus? Estrarre informazioni [qui](remoteapp-officesubscription.md).
- Si vuole fornire App personalizzate o LOB programmi? Creare una nuova [immagine](remoteapp-imageoptions.md) e usarla nella raccolta cloud.
- Determinare se è necessario connettersi a un VNET. Se si sceglie di connettersi a un VNET, assicurarsi che soddisfa le [istruzioni per il dimensionamento](remoteapp-vnetsizing.md) e che non [può connettersi a RemoteApp](remoteapp-vnet.md). Consultare l' [articolo pianificazione VNET ](remoteapp-planvnet.md)per ulteriori informazioni.
- Se si usa un VNET, decidere se si desidera partecipare al proprio dominio di Active Directory locale.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Passaggio 1: Creare un insieme di cloud - con o senza un VNET##


Per **creare una raccolta basata solo su cloud**, procedere come segue:

1. Nel portale di gestione, passare alla pagina RemoteApp.
2. Fare clic su **Nuovo > Creazione rapida**.
3. Immettere un nome per la raccolta e selezionare l'area geografica.
4. Scegliere il piano che si desidera utilizzare - standard o di base.
5. Scegliere il modello da usare per la raccolta. 

    **Suggerimento:** L'abbonamento per RemoteApp viene fornito con [le immagini dei modelli](remoteapp-images.md) che contengono pronta per la pubblicazione di Office 365 o Office 2013 (per l'utilizzo di valutazione) programmi, alcuni pubblicato (ad esempio Word) e gli altri utenti. È anche possibile creare una nuova [immagine](remoteapp-imageoptions.md) e utilizzarla nella raccolta cloud.


1. Fare clic su **Crea RemoteApp insieme**.
    
    **Importanti:** È possibile richiedere fino a 30 minuti per eseguire il provisioning della raccolta.

Dopo aver creata la raccolta di Azure RemoteApp, fare doppio clic sul nome della raccolta. Che verrà visualizzata la pagina **Avvio rapido** , in cui aver completato la configurazione della raccolta.

Per creare un **insieme di VNET + cloud**, procedere come segue:

1. Nel portale di gestione, passare alla pagina RemoteApp di Azure.
2. Fare clic su **Nuovo** > **creare con VNET**.
3. Immettere un nome per la raccolta.
4. Scegliere il piano che si desidera utilizzare - standard o di base.
5. Scegliere VNET è già stato creato. Non sa come farlo? Per ora, la procedura è nell'argomento [ibrida](remoteapp-create-hybrid-deployment.md) .
6. Decidere se si vuole partecipare l'insieme al proprio dominio. In caso affermativo, è necessario utilizzare AD Connect per integrare Azure Active Directory e l'ambiente di Active Directory. Che viene descritto in seguito nel **passaggio 2**.
6. Fare clic su **Crea RemoteApp insieme**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Passaggio 2: Configurare la sincronizzazione delle directory Active Directory (facoltativa) ##

Se si desidera utilizzare Active Directory, RemoteApp di Azure richiede la sincronizzazione delle directory tra Azure Active Directory e di Active Directory locale per sincronizzare gli utenti, contatti e le password per il tenant di Azure Active Directory. Per informazioni sulla pianificazione, vedere [Configurazione di Active Directory per RemoteApp di Azure](remoteapp-ad.md) . È anche possibile accedere direttamente al [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) per informazioni.

## <a name="step-3-publish-apps"></a>Passaggio 3: Pubblicare App ##

Un'app di Azure RemoteApp è l'app o un programma che offrono agli utenti. Si trova nell'immagine modello caricato per la raccolta. Quando un utente accede a un'app, viene visualizzata l'app per l'esecuzione nel proprio ambiente locale, ma viene effettivamente eseguita in una macchina virtuale in Azure. 

Prima che gli utenti possono accedere App, è necessario pubblicarli: pubblicazione App consente agli utenti accedono le app tramite il client Desktop remoto.
 
È possibile pubblicare app più alla raccolta RemoteApp di Azure. Dalla pagina di pubblicazione, fare clic su **pubblica** per aggiungere un programma. È possibile pubblicare dal menu di **avvio** dell'immagine di modello o specificando il percorso sull'immagine del modello per l'app. Se si sceglie di aggiungere dal menu **Start** , scegliere l'app per la pubblicazione. Se si sceglie di specificare il percorso per l'app, specificare un nome per l'app e il percorso in cui è installato sull'immagine del modello.

## <a name="step-4-configure-user-access"></a>Passaggio 4: Configurare l'accesso degli utenti ##

Dopo avere creato la raccolta, è necessario aggiungere gli utenti che si desidera essere in grado di utilizzare le risorse remote. Se si usa Active Directory, gli utenti di fornire l'accesso è necessario essere in esiste nel tenant di Active Directory associato all'abbonamento usato per creare la raccolta.

1.  Nella pagina Guida introduttiva, fare clic su **Configura l'accesso degli utenti**. 
2.  Immettere l'account di lavoro (da Active Directory) o un account Microsoft che si desidera concedere l'accesso.

    **Note:** 

    Assicurarsi di utilizzare il “user@domain.com” formato.

    Se si utilizza Office 365 ProPlus nella raccolta, è necessario utilizzare le identità di Active Directory per gli utenti. In questo modo convalidare licenze. 

3.  Dopo che gli utenti vengono convalidati, fare clic su **Salva**.


## <a name="next-steps"></a>Passaggi successivi ##

Ecco fatto: aver creato e distribuito la raccolta di cloud RemoteApp di Azure. Il passaggio successivo consiste nel dispone agli utenti di scaricare e installare il client Desktop remoto. È possibile trovare l'URL per il client nella pagina Azure RemoteApp Quick Start. Fare in modo che gli utenti nel client, quindi accedere App che è stato pubblicato.

### <a name="help-us-help-you"></a>Aiutaci a consentono di 
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic su **Modifica nella GitHub** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.