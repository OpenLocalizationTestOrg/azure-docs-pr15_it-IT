
<properties
    pageTitle="Novità di Azure RemoteApp? | Microsoft Azure"
    description="Informazioni sulle modifiche e i miglioramenti apportati a RemoteApp di Azure"
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



# <a name="whats-new-in-azure-remoteapp"></a>Novità di Azure RemoteApp?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Uno dei vantaggi di Azure RemoteApp è che stiamo lavorando sempre per migliorare la qualità. Ogni volta che in tal caso è necessario annunciare le modifiche apportate.

## <a name="future-updates"></a>Aggiornamenti futuri
Buongiorno - non tutti sanno che il team di Azure RemoteApp pubblica aggiornamenti mensili per il blog di licenze È possibile trovare non solo che cos'è cambia in Azure RemoteApp, ma anche altre informazioni sull'utilizzo di RDS. Consultare il blog di [Blog di Servizi Desktop remoto](https://blogs.msdn.microsoft.com/rds/), per informazioni. Ad esempio, un paio di settimane, inseriti una voce su [sollevare e spostamento carichi di lavoro con RemoteApp di Azure e Azure Active Directory](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>Settembre 2015
- Infopath aggiunto l'immagine di modello e alla raccolta di Microsoft Office 365. Se si desidera condividere Infopath, assicurarsi di aggiornare le raccolte con l'immagine più recente.
- Aggiornamenti client:
    - Client Windows aggiornato in modo da consentire agli utenti di condividere commenti e suggerimenti, soprattutto per problemi di connessione.
    - client di iOS aggiornato per la risoluzione dei messaggi di errore e per risolvere un problema nel punto in cui le credenziali scadute versioni precedenti del previsto.
- Stiamo lavorando su come ottenere il supporto di Office 2016 testato. Al termine, cercare le immagini aggiornate.
- Pubblicare un nuovo articolo sulle [differenze tra le raccolte cloud e ibridi](remoteapp-collections.md) , in modo da poter scegliere il tipo di insieme più adatta per le app - basata solo su cloud, cloud + VNET o ibrida.
- Per condividere QuickBooks utilizzando RemoteApp di Azure, ma non si è sicuri dei passaggi? Consultare l' [articolo nuovo Aldo](remoteapp-quickbooks.md) avvisare esattamente operazioni da eseguire.

## <a name="august-2015"></a>Agosto 2015
Modifiche apportate in agosto - Ecco le evidenziazioni:

- È ora possibile utilizzare un VNET Azure con un insieme di cloud! Vedere le [istruzioni di creazione cloud](remoteapp-create-cloud-deployment.md) per la nuova procedura.
- È stato possibile aggiungere App al menu di **avvio **per il client RemoteApp di Windows. App verranno visualizzato nell'elenco di applicazioni ed è possibile aggiungerli al menu **Start **di Windows.
- Aggiungere una nuova immagine nella raccolta di macchine Virtuali di Azure - Windows Server Host sessione Desktop remoto con Microsoft Office 365 ProPlus.
- Fisso client Mac in modo che le applicazioni di windows a scelta obbligatoria verranno interrotto il blocco.
- Descritte come è possibile utilizzare l' [abbonamento a Office 365 ProPlus](remoteapp-officesubscription.md) con RemoteApp di Azure.
- Informazioni dettagliate su come [proteggere l'App e i dati](remoteapp-secure.md) nella raccolta RemoteApp di Azure.

## <a name="july-2015"></a>Luglio 2015

Luglio preparare le modifiche apportate a essere recapitata in agosto e pertanto non è molto parlare ora, per la maggior parte degli aggiornamenti di documento. Ecco le modifiche più recenti:

- Aggiunta una scheda del **supporto tecnico** al portale, è possibile accedere più facilmente risorse di supporto, ad esempio i forum.
- Rielaborare le informazioni sulla risoluzione dei problemi per la creazione di un insieme di caratteristiche ibride. Estrazione obbligatoria [più recenti e ottenere il massimo](remoteapp-hybridtrouble.md) per la risoluzione dei problemi di suggerimenti, ad esempio, come identificare le porte corrette per configurare per le VNET.
- Descritte come [dati utente](remoteapp-upd.md) viene creato e salvato in Azure RemoteApp.
- Descritte come [bloccare App](remoteapp-secure.md).
- Pubblicare i [cmdlet di Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- E infine abbiamo iniziato una conversazione con alcuni utenti di Azure RemoteApp sulla terminologia. Cercare le modifiche al modo in cui che si parla le opzioni di raccolta diverso.

## <a name="june-2015"></a>Giugno 2015

Un numero di modifiche! Il team è stato molto occupato giugno:

- Riprogettato delle Azure RemoteApp [pagina introduttiva](https://www.remoteapp.windowsazure.com/) - estrarlo!
- Aggiornare il software in tutte le immagini disponibili come parte dell'abbonamento.
- Una chiamata effettuata miglioramenti alle raccolte ibrida, inclusi forzata tunneling supporto e la verifica delle dimensioni di subnet IP prima di provare a creare la raccolta.
- Scoperto che la * con caratteri jolly non funziona per webcam. Se, tuttavia, è necessario specificare l'ID dell'istanza o GUID. Si verrà aggiornare le informazioni sul reindirizzamento per riflettere.
- È stata in modo che è possibile aggiungere il software antivirus personalizzato all'immagine quando si crea un'immagine di modello dalla raccolta di Azure.

Abbiamo ulteriori modifiche attuare luglio, in modo che si farà nuovamente con un altro aggiornamento breve.

## <a name="may-2015"></a>Maggio 2015

Sono state alcune aggiunte (e di mesi) dal è appena creato in questo argomento, in modo che questo elenco cheats un po' ed è tra l'inizio di marzo fino a maggio. Estrarre le nuove caratteristiche:

- Automatizzare tutto: RemoteApp di Azure è ora disponibile [cmdlet nel modulo di Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
- [Creare un'immagine di Azure RemoteApp da una macchina virtuale Azure](remoteapp-image-on-azurevm.md). Consente di caricare l'immagine personalizzata Azure molto più veloce.
- Utilizzare un VNET Azure anziché un VNET RemoteApp per connettere le risorse della rete aziendale in Azure. Abbiamo aggiornato le [istruzioni insieme ibrido](remoteapp-create-hybrid-deployment.md) per guidano l'utente nella creazione di un VNET Azure (è il passaggio 1).
- A proposito di VNETs, vedere [linee guida per il nuovo](remoteapp-vnetsizing.md) attorno limiti di dimensioni VNET e limitazioni.
- E a proposito di limiti - solo cosa sono [le impostazioni predefinite e limiti di servizio](../azure-subscription-service-limits.md)?

Per ulteriori informazioni su Azure RemoteApp necessaria Il team RemoteApp si è fuori in vigore alla Ignite ultime settimane. Guardare video Aldo, [The nozioni fondamentali di Microsoft Azure RemoteApp Management e amministrazione](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

È necessario vedere RemoteApp di Azure reali? Estrarre l'esercitazione [eseguire qualsiasi app da qualsiasi dispositivo in un punto qualsiasi](remoteapp-anyapp.md) - viene illustrato come condividere l'accesso con altri utenti, tra cui la condivisione dei file di database. Esiste anche un'esercitazione sulla [creazione di Office 365](remoteapp-tutorial-o365anywhere.md) eseguire la stessa in qualsiasi dispositivo.

Grazie per conservata con noi: eseguire il mese successivo con necessari più aggiornamenti.


### <a name="help-us-help-you"></a>Aiutaci a consentono di
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic su **Modifica nella GitHub** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.
