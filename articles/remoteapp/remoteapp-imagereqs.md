
<properties
    pageTitle="Requisiti di immagine RemoteApp Azure | Microsoft Azure"
    description="Informazioni sui requisiti per la creazione di immagini da utilizzare con RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="requirements-for-azure-remoteapp-images"></a>Requisiti per le immagini RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp Usa un'immagine di Windows Server 2012 R2 per ospitare tutti i programmi che si desidera condividere con altri utenti. Per creare un'immagine personalizzata, è possibile iniziare con un'immagine esistente o [crearne uno nuovo](remoteapp-create-custom-image.md).

> [AZURE.TIP] Non tutti sanno che l'abbonamento RemoteApp di Azure consente di accedere a un'immagine di Windows Server 2012 R2 nella raccolta macchine Virtuali di Azure che è possibile utilizzare per creare la propria immagine di modello. [Verificarlo](remoteapp-image-on-azurevm.md).  


I requisiti per l'immagine che può essere caricato per l'utilizzo con Azure RemoteApp sono:


- Applicazioni personalizzate non archiviano dati in locale sull'immagine. Queste immagini sono prive di stato e può contenere solo le applicazioni.
- L'immagine non contiene dati che possono essere persi.
- Le dimensioni dell'immagine devono essere un multiplo di MB. Se si tenta di caricare un'immagine che non è un multiplo esatto, non viene eseguito il caricamento.
- Le dimensioni dell'immagine devono essere 127 GB o più piccole.
- Deve trattarsi di un file disco rigido virtuale (VHDX file non sono attualmente supportati).
- Il disco rigido virtuale non deve essere una macchina virtuale di generazione 2.
- Il disco rigido virtuale può essere dimensioni fisse o espansione dinamica. Un disco rigido virtuale espansione dinamica è consigliabile perché meno tempo per caricare in Azure rispetto a un file di dimensioni fisse disco rigido virtuale.
- Il disco deve essere inizializzato utilizzando il Record (avvio principale) stile di partizione. Il GUID stile tabella di partizione (GPT) non è supportato.
- Il disco rigido virtuale deve contenere una singola installazione di Windows Server 2012 R2. Può contenere più volumi, ma solo che contiene un'installazione di Windows.
- È necessario installare il ruolo di Remote Desktop sessione Host (RDSH) e la funzionalità esperienza Desktop.
- È necessario il ruolo di Gestore connessione Desktop remoto *non* è stato possibile installare.
- File di sistema (crittografia) deve essere disattivato.
- L'immagine deve essere preparata con Sysprep utilizzando i parametri **/oobe /generalize /shutdown** (non si utilizza il parametro **/mode:vm** ).
- Non è possibile caricare il disco rigido virtuale da una catena di snapshot.

Per ulteriori informazioni sulla creazione di immagini per RemoteApp di Azure, vedere [creare un'immagine di Azure RemoteApp](remoteapp-imageoptions.md) .
