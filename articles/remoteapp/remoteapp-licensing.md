<properties
    pageTitle="Gestione delle licenze di Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come licenze funziona in Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Gestione delle licenze funzionamento in Azure RemoteApp?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

In modo che è stato configurato il servizio di Azure RemoteApp, creato i modelli e pronti a pubblicare App agli utenti. Ma c'è ancora una cosa ultima per stabilire: gestione delle licenze. Solo come funziona sulle licenze per RemoteApp e per le applicazioni che condivise tramite RemoteApp?

RemoteApp non richiede le licenze di Windows o CAL Desktop remoto. L'abbonamento si occupa del lato RemoteApp stesso. (Vedere i dettagli dei [prezzi piani](https://azure.microsoft.com/pricing/details/remoteapp)).

Se si utilizza una delle immagini incluse nell'abbonamento, è possibile condividere una delle App installate nell'immagine senza la necessità di una licenza distinta. Ad esempio, se si usa l'immagine del modello di Windows Server 2012 R2 per creare la raccolta, è possibile condividere System Center Endpoint Protection con altri utenti. Sola eccezioni a questa regola sono Office 365 ProPlus, che richiede una sottoscrizione separata, e Office 2013, non può essere condiviso in una raccolta di produzione.

Se si desidera utilizzare l'immagine di modello di Office 365 fornito con RemoteApp, è necessario disporre di un piano di Office 365 ProPlus *esistente* . Lo stesso avviene per qualsiasi applicazione di Office 365 che si pubblica tramite un modello personalizzato. È necessario attivare le app con un abbonamento. Questo è valido per le sottoscrizioni di valutazione e a pagamento. Se si desidera utilizzare l'immagine di modello di Office 365 durante il periodo di valutazione, *e non si dispone già di un abbonamento*, passare alla pagina di Office 365 per [iscriversi](https://go.microsoft.com/fwlink/p/?LinkID=403802) per una sottoscrizione di valutazione. Vedere [RemoteApp Office interazione e?](remoteapp-o365.md) per ulteriori informazioni.

Se durante il periodo di valutazione, non si vuole ottenere una sottoscrizione di valutazione di Office 365, utilizzare Office 2013 Professional Plus modello immagine che viene fornito con RemoteApp. Questa immagine di modello può essere utilizzata solo per 30 giorni e non può essere convertita in una raccolta a pagamento.

Per le altre applicazioni, è necessario verificare che si dispone della licenza per condividere l'app.

In secondo luogo, giusto? È possibile pubblicare app che si ha il diritto di condividere. Ed è necessario verificare che si realmente ha diritto a condividere i programmi.

Si noti che non è possibile utilizzare un contratto di licenza CAL o Volume License in una raccolta di cloud. È *possibile* utilizzare un contratto multilicenza per attivare le applicazioni nella raccolta ibrido (ad eccezione di Office). È sufficiente installarli nell'immagine del modello dal supporto di contratto multilicenza. Seguire le informazioni dal fornitore dell'applicazione di installazione di licenze in un ambiente con Desktop remoto.
