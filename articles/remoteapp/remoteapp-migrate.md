
<properties
    pageTitle="Eseguire la migrazione dei dati utente da Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come eseguire la migrazione dei dati utente e disconnettersi RemoteApp di Azure."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Come eseguire la migrazione dei dati e in uscita RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

È possibile usare molti strumenti e i metodi per trasferire [dati utente](remoteapp-upd.md) e in uscita RemoteApp di Azure. Ecco alcuni metodi:

- Copiare e incollare tramite condivisione degli Appunti
- Copiare i file e dati in un file server
- Copiare i file in OneDrive for Business tramite un browser
- Copiare i file usando il reindirizzamento

>[AZURE.NOTE] 
> Non è possibile abilitare l'App OneDrive for Business o Consumer agenti di sincronizzazione - [non sono supportate](remoteapp-onedrive.md) in Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Utilizzo di copia e Incolla in Esplora File

Copiare e incollare tramite gli Appunti viene abilitata RemoteApp distribuzioni [per impostazione predefinita](remoteapp-redirection.md). Questo consente agli utenti di copiare i file tra le applicazioni di PC e RemoteApp locale. Spesso, tramite normali di usare le App in RemoteApp, salvate i file da utenti loro UPDs - spostare dati fuori RemoteApp sono semplice:

1. [Pubblicare Esplora File come un'app](remoteapp-publish.md) in una raccolta di RemoteApp. Si noti che si tratta di un'attività amministrativa.
2. Indirizzare gli utenti per avviare l'app di Esplora File che è stato pubblicato e da utilizzare per copiare e incollare i file nel loro UDP e usarlo.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Caricare file e i dati in un file server utilizzando copia di file di rete standard

Spesso le organizzazioni utilizzano file server per memorizzare dati generali. Se si conosce il nome del server o il percorso, gli utenti possano esplorare la rete locale per il server e quindi copiare i propri file, così come avveniva in precedenza. È consigliabile nuovamente pubblicare RemoteApp Esplora File e condividerlo con altri utenti.

>[AZURE.NOTE] 
> Il file server deve essere connesso alla rete routing RemoteApp è stata distribuita in.

## <a name="copy-files-to-onedrive-for-business"></a>Copiare i file in OneDrive for Business
Anche se non è possibile abilitare l'agente di sincronizzazione di Business in RemoteApp di OneDrive, è tuttavia possibile copiare file dall'UDP in OneDrive for Business tramite un browser. 

1. Pubblicare RemoteApp Esplora File e quindi indicare agli utenti di accedere ai file che app. 
2. Risulta più semplice di trasferire file in modalità compressa, in modo che gli utenti devono creare un file ZIP che contiene tutti i file da spostare in OneDrive for Business.
3. Chiedere agli utenti di accedere al portale di Office 365, quindi passare a OneDrive e caricare il file con estensione zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiare i file tramite il reindirizzamento dell'unità

Se è stato attivato [il reindirizzamento dell'unità](remoteapp-redirection.md), già stato creato un'unità mappata per gli utenti. In questo caso, possono zip i propri file nell'unità reindirizzata e quindi salvarli in PC locale.