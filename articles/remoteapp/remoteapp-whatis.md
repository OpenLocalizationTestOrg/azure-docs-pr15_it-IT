<properties 
    pageTitle="Che cos'è RemoteApp di Azure? | Microsoft Azure" 
    description="Informazioni sulla condivisione di applicazioni e risorse da qualsiasi dispositivo mediante RemoteApp di Azure." 
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
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>Che cos'è RemoteApp di Azure?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp introduce funzionalità del programma Microsoft RemoteApp locale, supportato da Servizi Desktop remoto, in Azure. RemoteApp Azure consente di specificare l'accesso remoto sicuro alle applicazioni da molti dispositivi diversi utente. Azure RemoteApp sostanzialmente ospita le sessioni di Terminal Server non permanenti nel cloud e si torna a utilizzarli e condividerli con altri utenti.

Azure RemoteApp è possibile condividere App e le risorse con gli utenti in qualsiasi dispositivo. Abbiamo ospitare le app nel cloud, ovvero che è occuparsi dell'hardware e proporzioni dei caratteri per soddisfare le esigenze utente. È necessario effettuare è caricare le app che si desidera condividere e quindi visualizzato agli utenti di utilizzare queste applicazioni. [Gli utenti ottenere mantenere i propri dispositivi](remoteapp-clients.md), mentre si gestiscono tutti gli elementi tramite il portale di Azure. È anche possibile di usando le proprie credenziali aziendale, che consente di garantire la sicurezza dei dati e le applicazioni.

Continuare a leggere per ulteriori informazioni su Azure RemoteApp, o, se è già stata sicuro l'utente, [eseguire una prova ora](https://azure.microsoft.com/services/remoteapp/).

Eventuali domande in merito RemoteApp di Azure? Consultare le [domande frequenti](remoteapp-faq.md).

Azure RemoteApp fa parte di [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nuovo!** Per ulteriori informazioni su Azure RemoteApp necessaria O pronto per la convalida RemoteApp di Azure in scala? Partecipare a nostro settimanale [gli esperti webinar](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Raccolte RemoteApp Azure
Esistono due tipi di [raccolte di Azure RemoteApp](remoteapp-collections.md):


- Un **insieme di cloud** è ospitato e archivia dati per le applicazioni nel cloud. Gli utenti possono accedere App effettuando l'accesso con loro account Microsoft o credenziali aziendale sincronizzati o federato con Azure Active Directory.

    Scegliere un insieme di cloud quando l'applicazione che si desidera condividere non richiede una connessione a tutte le risorse private network, rete della società (ad esempio tramite un dispositivo VPN). Se l'applicazione utilizza risorse su Internet, OneDrive o Azure, una raccolta di cloud funzionerà dell'utente. È inoltre più veloce creare.

- Un **insieme di ibrida** è ospitato e archivia i dati del cloud Azure, ma anche dati di access consente agli utenti le risorse archiviate nella rete locale. Gli utenti possono accedere App effettuando l'accesso con le proprie credenziali aziendale sincronizzati o federato con Azure Active Directory.

    Scegliere un insieme di ibrido se si richiede una connessione alle risorse sulla rete privata della società. Ad esempio, se l'applicazione richiede l'accesso a una delle operazioni seguenti:

    - Server di file che si trovano nella rete intranet
    - Quicken
    - Database dietro un firewall

    Si tratta in genere più utile per grandi aziende con molte risorse nelle rispettive reti private non possono essere spostate nel cloud.

Gli insiemi di file diversi hanno diverse opzioni, incluse le reti, in modo determinare [quale insieme](remoteapp-collections.md) funziona meglio dell'utente. 


### <a name="updating-your-collection"></a>Aggiornamento della raccolta
Uno dei principali differenze tra le raccolte ibrida e cloud è la modalità di gestione degli aggiornamenti software. Con una raccolta di cloud che utilizza l'immagine preinstallato in Office 365 ProPlus o Office 2013, non è necessario preoccuparsi di eventuali aggiornamenti. Il servizio gestisce stesso che transita gli aggiornamenti in modo continuativo, App e il sistema operativo.

Per gli insiemi di distribuzione ibrida, nonché gli insiemi di cloud che utilizzano un'immagine di modello personalizzato, gli utenti responsabili della gestione di App. Per le immagini di dominio, è possibile controllare gli aggiornamenti mediante strumenti, ad esempio Windows Update, criteri di gruppo o System Center.

Dopo aver aggiornato l'immagine di modello personalizzato, si caricarla la nuova immagine nel cloud Azure e quindi aggiorna la raccolta per utilizzare la nuova immagine. (È possibile farlo dalla pagina Azure RemoteApp **Quick Start** o il Dashboard.)

Per ulteriori informazioni, vedere [aggiornare la raccolta](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Client supportati RemoteApp
Azure RemoteApp è supportato in App RemoteApp client per Windows e Windows RT, nonché le applicazioni di Microsoft Desktop remoto per Mac, iOS e Android. Utilizzare queste App in un dispositivo mobile loro gli utenti o calcolare dispositivi per accedere ai programmi RemoteApp di Azure nuovi.

Per ulteriori informazioni sui client, vedere [accesso le app di Azure RemoteApp](remoteapp-clients.md) .

## <a name="next-steps"></a>Passaggi successivi
Go! Provalo! Questi articoli consentono di iniziare a usare RemoteApp di Azure:

- [Quali tipi di raccolta necessari per RemoteApp di Azure](remoteapp-collections.md)
- [Creare un'immagine RemoteApp di Azure](remoteapp-imageoptions.md)
- [Come creare un insieme di cloud di Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Come creare un insieme di ibrido di Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Gestione delle licenze funzionamento in Azure RemoteApp?](remoteapp-licensing.md)
- [Procedure consigliate per l'uso di Azure RemoteApp](remoteapp-bestpractices.md)
- [Domande frequenti su RemoteApp Azure](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Aiutaci a consentono di 
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic **su GitHub** o **Modifica** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.