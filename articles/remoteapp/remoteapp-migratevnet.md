<properties
    pageTitle="Come eseguire la migrazione da un VNET RemoteApp a un VNET Azure | Microsoft Azure"
    description="Informazioni su come eseguire la migrazione da un VNET RemoteApp a un VNET Azure"
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



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Come eseguire la migrazione di un insieme di ibrido da un VNET RemoteApp a un VNET Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Fortuna! Abbiamo attivato per distribuire insiemi di RemoteApp ibrido direttamente nelle reti Azure esistente virtuale (VNETs) invece di creare VNETs RemoteApp specifiche. Consente di sfruttare le caratteristiche più recenti di VNET (ad esempio ExpressRoute) e assegnare le raccolte ibrido accesso diretto alla rete ad altri servizi di Azure e macchine virtuali distribuiti a tale VNET.  (In questo modo viene prestazioni migliori e più facile configurazione più configurazioni VNET-VNET).


Supponiamo che si è già creato un insieme di RemoteApp denominato *OriginalCollection* con un RemoteApp VNET chiamato *RemoteAppVNET*ibrido. Ecco i passaggi per la migrazione a un nuovo VNET Azure chiamato *AzureVNET*.

1.  Nella scheda **reti** nel [portale di gestione](http://manage.windowsazure.com/), creare una VNET denominata *AzureVNET*, utilizzando la stessa posizione, configurazione del DNS e spazio di indirizzi (per almeno uno dei subnet *AzureVNET* ) come è stato utilizzato per *RemoteAppVNET*.
2.  Configurare *AzureVNET* a uno dei due host o disponibile la connettività di rete per la distribuzione di Active Directory che *OriginalCollection* è dominio aggiunto a.
3.  Nella scheda **su macchine virtuali** , creare una nuova raccolta RemoteApp denominata *Nuova raccolta*. Utilizzare l'opzione **Crea con VNET** **Creazione rapida**.
3.  Configurare *NewCollection* per la distribuzione a una subnet *AzureVNET*.
4.  Configurare *NewCollection* per utilizzare la stessa immagine e informazioni di partecipazione dominio come è stato utilizzato per *OriginalCollection*.
5.  Dopo alcune ore, *NewCollection* verrà visualizzato nell'elenco insieme con lo stato attivo.

A questo punto, se non è necessario eseguire la migrazione di tutte le informazioni utente dalla raccolta originale per la nuova raccolta, eseguire le operazioni seguenti:

6.  Eliminare *OriginalCollection*.
7.  Eliminare *RemoteAppVNET*.

E fatto!

In alternativa, se è necessario eseguire la migrazione di informazioni utente dalla raccolta originale per la nuova raccolta, eseguire le operazioni seguenti:

6.  Inviare un messaggio di posta elettronica a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) con l'ID di abbonamento Azure, il nome della raccolta originale e il nome della nuova raccolta e chiedere loro di eseguire la migrazione di informazioni utente.
7.  All'interno di 2 giorni lavorativi team RemoteApp sposterà l'elenco di accesso utente e tutti i documenti dell'utente e le impostazioni utente dalla raccolta originale per la nuova raccolta.
8.  Eliminare *OriginalCollection*.
9.  Eliminare *RemoteAppVNET*.

E a questo punto, fatto!

Se si dispone di eventuali domande o assistenza speciale, e-mail [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
