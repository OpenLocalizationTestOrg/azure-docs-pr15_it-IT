
<properties
    pageTitle="Utilizzo di Office con RemoteApp Azure | Microsoft Azure" 
    description="Informazioni su interazione Office e RemoteApp di Azure"
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

# <a name="using-office-with-azure-remoteapp"></a>Utilizzo di Office con RemoteApp Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Sono disponibili due opzioni per l'hosting di applicazioni di Office in Azure RemoteApp: Office 365 ProPlus o versione di valutazione di Office 2013 Professional Plus.

**Buongiorno non tutti sanno che abbiamo un nuovo migliore che presto sostituito questo articolo. Estrarre [come usare l'abbonamento a Office 365 con RemoteApp di Azure](remoteapp-officesubscription.md). Coprire tutte le informazioni che necessarie per l'uso di Office 365 + RemoteApp Azure.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
È possibile creare un insieme di RemoteApp utilizzando l'immagine del modello di Office 365 ProPlus. Questa opzione consente di estendere il servizio di Office 365 a RemoteApp. È necessario disporre di un piano di abbonamento esistente e gli utenti devono essere concesso in licenza per il servizio Office 365 ProPlus, uno dei due autonoma o attraverso Office 365 piano di servizio.

RemoteApp supporta l'attivazione del Computer condivise di Office 365. Quando si abilita l'attivazione di Computer condiviso e usare lo [strumento di distribuzione di Office](http://www.microsoft.com/download/details.aspx?id=36778) per l'installazione, Office 365 ProPlus viene installata senza viene attivata. Quando un utente firma in una raccolta che contiene Office 365, Office consente di controllare se l'utente è stato eseguito il provisioning per Office 365 ProPlus. Se, Office temporaneamente consente di attivare Office 365 ProPlus - questa attivazione viene mantenuto fino a tale abbandona di utenti del servizio.

Per utilizzare l'attivazione di Office 365 condiviso Computer, è necessario creare un [modello personalizzato](remoteapp-create-custom-image.md) e installare Office 365 ProPlus, seguire [queste istruzioni](https://technet.microsoft.com/library/dn782858.aspx).

È possibile gestire le licenze di Office 365 degli utenti al [Portale di amministrazione di Office 365](https://portal.office365.com/). Leggere altre informazioni sui [piani di servizio di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Versione di valutazione di Office 2013 Professional Plus
Durante la valutazione di 30 giorni di RemoteApp, è possibile utilizzare l'immagine di modello Professional Plus (versione di valutazione) di Office 2013 per creare un insieme di RemoteApp. È possibile assegnare agli utenti di questa versione di valutazione insieme utilizzando gli account di lavoro di Azure Active Directory o un account di Microsoft. Non è necessario alcun abbonamento aggiuntiva.

Questa è un'ottima soluzione per avviare la pneumatici e avere una visione ottima per Office in RemoteApp. Tuttavia, questa opzione è previsto per la valutazione e verifica. Gli insiemi di RemoteApp creati con l'immagine di modello Professional Plus (versione di valutazione) di Office 2013 non è possibile passa alla modalità di produzione e verranno disabilitati alla fine del periodo di valutazione.

## <a name="switching-from-trial-to-production"></a>Passaggio dalla versione di valutazione a produzione
Quando si avvia la versione di valutazione gratuita di 30 giorni, una nota nella sezione RemoteApp del portale che indica quanto tempo hanno lasciato nella versione di valutazione prima che sia necessario transizione a un account a pagamento. È possibile attivare l'account e passare alla modalità di produzione attraverso il collegamento in questa nota.

Quando si attiva l'account, questo avrà effetto su tutte le raccolte di RemoteApp nell'account.

- Raccolte che eseguono Windows Server 2012 R2 o le immagini dei modelli di Office 365 ProPlus passerà alla produzione diretta. Tutti i dati utente e le impostazioni, comprese le sessioni in corso, rimarranno invariate.
- Se è stato caricato le immagini dei modelli personalizzati, raccolte con le immagini verranno inoltre facile transizione.
- L'immagine di modello Professional Plus (versione di valutazione) di Office 2013 è destinato solo di valutazione. Raccolte in esecuzione con l'immagine di modello non utilizzino produzione. Verranno messe nello stato "disabilitato".


Se non si passa alla modalità di produzione la scadenza della versione di valutazione, le raccolte RemoteApp saranno disabilitate. Niente paura: le impostazioni e i dati degli utenti vengono salvati per un'altra 90 giorni, pertanto è comunque possibile attivare il servizio e passare alla modalità di produzione senza alcuna perdita di dati.
