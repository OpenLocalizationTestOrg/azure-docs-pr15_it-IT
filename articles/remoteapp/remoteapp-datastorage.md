
<properties
    pageTitle="Non memorizzare mai dati riservati in immagini personalizzate per Azure RemoteApp | Microsoft Azure"
    description="Informazioni sulle linee guida per l'archiviazione dei dati in immagini personalizzate in RemoteApp di Azure"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>Non memorizzare mai dati riservati in immagini personalizzate

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Quando si ospita applicazione in Azure RemoteApp, il primo passaggio consiste nel creare un'immagine personalizzata. Abbiamo utilizzare tale immagine personalizzata per creare le istanze di macchine Virtuali che servono le App per gli utenti. Immagine personalizzata deve contenere solo le applicazioni e dati riservati mai che possono essere persi, ad esempio database SQL, file personale o i file di dati speciali come i file della società di QuickBooks. Tutti i dati riservati devono trovarsi esterni RemoteApp di Azure in un file server, un altro macchine Virtuali di Azure, o in SQL Azure. L'immagine dovrebbe contenere solo l'applicazione che si connette all'origine dati e presenta i dati. Verificare i [requisiti per le immagini di Azure RemoteApp](remoteapp-imagereqs.md) per altre informazioni. 

Per comprendere perché è consigliabile non memorizzare dati riservati, è necessario comprendere il funzionamento di Azure RemoteApp. Quando una raccolta viene creata o aggiornata, dietro le quinte duplicati più copie dell'immagine vengono creati. Tutte le istanze di macchine Virtuali sono replica esatta dell'immagine personalizzata. avvio di applicazioni gli utenti sono connessi a uno di questi casi macchine Virtuali. Ma la stessa istanza non è garantita e deve non ha importanza perché sono non permanente. Le istanze di macchine Virtuali che ospita le applicazioni non sono permanenti e può essere eliminato o eliminate in base a, ad esempio, durante l'aggiornamento di raccolta. 

Quando viene eseguito il provisioning della raccolta, gli utenti di avviare la connessione alle macchine virtuali dati utente sono persistenti protetto, in quanto questo viene salvato in archiviazione separata all'interno di un disco rigido virtuale si chiama un [disco profilo utente (UDP)](remoteapp-upd.md), ossia il profilo utente in c:\users\<profiloutente >. Quando si avvia un'applicazione, l'UDP è installato e considerato come un profilo utente locale dal sistema operativo. Altre informazioni sui come [RemoteApp di Azure consente di salvare le impostazioni e dati utente](remoteapp-upd.md).

Dati di esempio che non devono essere inclusi nell'immagine:

- Dati per gli utenti accedano condivisi
- SQL DB o DB QuickBooks
- Tutti i dati in D:\

Dati di esempio inclusi nel profilo predefinito da copiare in UDP ogni utenti:

- File di configurazione per ogni utente
- Script che gli utenti devono mantenuti nel loro UDP

Punti chiave:

- Non è mai archivio dati riservati che possono andare perduti sull'immagine durante la creazione di un'immagine personalizzata.
- Dati riservati sempre devono trovarsi in un server di file separato, separata macchine Virtuali di Azure, nel cloud e sempre esterni per le istanze di macchine Virtuali che ospita le applicazioni all'interno di Azure RemoteApp. 
- Dati utente sono stati salvati e viene mantenuto nel disco rigido di profilo utente (UDP)


