<properties 
    pageTitle="Azure RemoteApp - come la larghezza di banda di rete e la qualità dell'esperienza utente lavoro insieme? | Microsoft Azure"
    description="Informazioni su come larghezza di banda di rete in Azure RemoteApp può influire sulla qualità dell'utente dell'esperienza."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp - come la larghezza di banda di rete e la qualità dell'esperienza utente lavoro insieme?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Quando è visualizzata la [larghezza di banda complessiva](remoteapp-bandwidth.md) necessari per Azure RemoteApp, prendere in considerazione i fattori seguenti: fanno tutti parte di un sistema dinamico che influisce sull'esperienza utente complessiva. 

- **La larghezza di banda di rete disponibili e condizioni correnti della rete** - un insieme di parametri (perdita, latenza, variazione) della stessa rete in un determinato momento può influire sull'applicazione dei flussi, ovvero un'esperienza utente complessiva pedice. La larghezza di banda della rete è una funzione di congestione, perdita casuale, latenza perché tutti questi parametri influiscono sulle meccanismo di controllo congestione, che a sua volta controlli la velocità di trasmissione per evitare conflitti.  Ad esempio una perdita di dati o rete ad alta latenza renderà l'utente esperienza errate anche in una rete con larghezza di banda 1000 MB. La perdita e la latenza variano in base al numero di utenti presenti nella stessa rete e quali utenti stanno facendo (ad esempio, riproduzione di video, scaricare o il caricamento di file di grandi dimensioni, stampa).
- **Scenari di utilizzo** - l'esperienza dipende da cosa stanno facendo gli utenti come singoli dipendenti e come un gruppo sulla stessa rete. Ad esempio, una diapositiva di lettura richiede un singolo frame può essere modificato; Se l'utente skims e scorre il contenuto di un documento di testo, è necessario un numero elevato di frame da aggiornare al secondo. La comunicazione e viceversa per il server in questo scenario utilizzerà infine maggiore larghezza di banda di rete. Anche prendere in considerazione un esempio estremo: più utenti sono riproduzione di video ad alta definizione (ad esempio risoluzione 4 KB), tenendo HD conferenze telefoniche, giochi video 3D o lavorando sistemi CAD. Tutti questi elementi può rendere anche una rete molto elevato della larghezza di banda praticamente inutilizzabile.
- **Risoluzione dello schermo e il numero di schermate** - maggiore larghezza di banda di rete è necessario schermi di dimensioni maggiori aggiornamento completo rispetto schermi di dimensioni inferiori. La tecnologia sottostante in modo molto efficace di codifica e trasmissione solo le aree di schermate che sono state aggiornate, ma a volte ci, è necessario aggiornare l'intero schermo. Quando l'utente ha uno schermo con risoluzione superiore (ad esempio risoluzione 4 KB), l'aggiornamento richiede maggiore larghezza di banda di rete rispetto a uno schermo con risoluzione più bassa (ad esempio 1024x768px). Questa stessa logica si applica se si usa più di una schermata per il reindirizzamento. Per aumentare il numero di schermate della larghezza di banda.
- **Reindirizzamento Appunti e dispositivo** - si tratta di un problema non molto evidente, ma in molti casi se un utente memorizza una grande quantità di dati negli Appunti, è necessario un po' di tempo per ulteriori informazioni per il trasferimento dal client Desktop remoto nel server. L'esperienza downstream può essere influenzato dall'esperienza di inviare il contenuto degli Appunti monte. Lo stesso vale per il reindirizzamento del dispositivo - se una stampante deve essere visualizzato un documento di grandi dimensioni, o uno scanner o una webcam viene prodotta una grande quantità di dati che deve essere inviato padre al server o archivio locale deve essere disponibile per un'app in esecuzione nel cloud per copiare un file di grandi dimensioni, è possibile notare ignorati fotogrammi o temporaneamente video "bloccata" perché i dati necessari per il reindirizzamento del dispositivo sono aumentare la larghezza di banda di rete specifiche esigenze. 

Quando si valuta le proprie esigenze della larghezza di banda di rete, assicurarsi di prendere in considerazione tutti questi fattori che come un sistema.

A questo punto, tornare all' [articolo di larghezza di banda di rete principale](remoteapp-bandwidth.md)o passare al test la [larghezza di banda di rete](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Ulteriori informazioni
- [Valutare l'utilizzo della larghezza di banda di rete RemoteApp di Azure](remoteapp-bandwidth.md)

- [Azure RemoteApp - verifica l'utilizzo della larghezza di banda di rete con alcuni scenari comuni](remoteapp-bandwidthtests.md)

- [Azure RemoteApp larghezza di banda - linee guida generali (se non è possibile verificare il proprio)](remoteapp-bandwidthguidelines.md)