<properties
    pageTitle="Abilitare affinità sessione utilizzando il Toolkit di Azure per Eclisse"
    description="Informazioni su come abilitare affinità sessione utilizzando il Toolkit di Azure per Eclisse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Abilitare affinità sessione #

All'interno del Toolkit di Azure per Eclisse, è possibile abilitare affinità sessione HTTP o "sessioni permanenti", per i ruoli. Nella figura seguente mostra le proprietà di **Bilanciamento del carico** finestra di dialogo usato per abilitare la funzionalità di affinità sessione:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Per abilitare affinità sessione per il proprio ruolo ##

1. Pulsante destro del mouse il ruolo di gestione di progetti di Eclisse e quindi scegliere **Il bilanciamento del carico** **Azure**.
1. Nella finestra di dialogo **proprietà per il bilanciamento del carico di WorkerRole1** :
    1. Controllare **affinità sessione Enable HTTP (sessioni permanenti) per questo ruolo.**
    1. Per l' **endpoint di Input da utilizzare**, selezionare un endpoint input da utilizzare, ad esempio, **http (pubblico: 80, privato: 8080)**. L'applicazione deve utilizzare questo endpoint come endpoint HTTP. È possibile attivare più endpoint per il proprio ruolo, ma è possibile selezionare solo uno di essi per supportare sessioni permanenti.
    1. Rigenerare l'applicazione.

Dopo l'abilitazione, se si dispone di più istanze di ruolo, le richieste HTTP provenienti da un determinato client continuerà gestito dalla stessa istanza di ruolo.

Per l'installazione di un modulo IIS speciale denominato applicazione richiedere Routing (ARR) in ognuna delle istanze del ruolo, il Toolkit di Eclisse abilitato. ARR Reindirizza richieste per l'istanza di ruolo appropriato. Il toolkit consente di riconfigurare automaticamente l'endpoint selezionato il traffico HTTP in arrivo viene inviato al software ARR. Il toolkit anche crea un nuovo endpoint interno che il server di linguaggio è configurato per ascoltare. Questo è l'endpoint utilizzato da ARR per reindirizzare il traffico HTTP all'istanza del ruolo appropriato. In questo modo, ogni istanza del ruolo nella distribuzione di più istanze funge da proxy inverso per tutte le altre varianti attivazione sessioni permanenti.

## <a name="notes-about-session-affinity"></a>Note sulle affinità sessione ##

* Affinità sessione non funziona nell'emulatore di calcolo. Le impostazioni possono essere applicate nell'emulatore di calcolo senza interferire con il processo di compilazione o calcolano esecuzione emulatore, ma la caratteristica non funziona all'interno dell'emulatore di calcolo.
* Attivazione affinità sessione verrà generato un aumento della quantità di spazio su disco occupato da alla distribuzione di Azure, come software aggiuntivo verrà scaricato e installato in istanze del ruolo quando viene avviato il servizio cloud Azure.
* Il tempo necessario per inizializzare ogni ruolo richiederà più tempo.
* Un endpoint interno, in genere come un rerouter il traffico come descritto in precedenza, sarà added.ss

Per un esempio di come gestire i dati della sessione quando viene attivata l'affinità sessione, vedere [come gestire i dati della sessione con affinità sessione][].

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

[Come gestire i dati della sessione con affinità sessione][]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Come gestire i dati della sessione con affinità sessione]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
