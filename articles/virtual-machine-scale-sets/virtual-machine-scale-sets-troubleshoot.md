<properties
    pageTitle="Risolvere i problemi di autoscale macchina virtuale scala set | Microsoft Azure"
    description="Risoluzione dei problemi autoscale macchina virtuale scala set. Conoscere tipici problemi e risolverli."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Risoluzione dei problemi autoscale macchina virtuale scala set

**Problema** : è stata creata un'infrastruttura il ridimensionamento automatico in Gestione di risorse di Azure mediante macchine Virtuali scala set-ad esempio la distribuzione di un modello di questo: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale-si dispone delle regole di scala definite e tutto funziona bene, tranne per il fatto che indipendentemente dalla quantità carico vengono messi in macchine virtuali, non è possibile autoscale.

## <a name="troubleshooting-steps"></a>Risoluzione dei problemi

Di seguito sono riportati alcuni aspetti da considerare:

- Quanti core ogni macchina virtuale e vengono caricati ogni principali?
 Il modello di Guida introduttiva di Azure riportato sopra dispone di uno script do_work.php, che consente di caricare un singolo core. Se si sta utilizzando una macchina virtuale superiore a un singolo core dimensione memoria virtuale come Standard_A1 o D1 sarà necessario eseguire questo carico più volte. Controllare il numero di core nelle macchine virtuali consultando [macchine virtuali di dimensioni per Windows Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Quanti macchine virtuali in macchine Virtuali scala insieme, si eseguono operazioni su ciascuno di essi?

    Scalabilità evento avranno luogo solo quando la media CPU tra **tutte** le macchine virtuali in un set di scala supera il valore di soglia nel tempo interno definito nelle regole autoscale.

- È stato non ci si accorge tutti gli eventi scala?

    Controllare che i registri di controllo nel portale di Azure per gli eventi di scala. Forse durante una scala di backup e una scala verso il basso quale è stato perse. È possibile filtrare "Scala"..

    ![Log di controllo][audit]

- Le soglie scala e scala del disegno sono diversi?

    Si supponga di impostata una regola per scalabilità quando CPU Media è maggiore di 50% più 5 minuti e in quando medio CPU è inferiore al 50% di scala. Questo si verifichi un problema "Ali" quando l'utilizzo della CPU è vicino soglia, con azioni scala costantemente aumentare e ridurre le dimensioni del set di. Per questi motivi, il servizio autoscale tenta di impedire "battere", che apparire come non proporzioni dei caratteri. Pertanto assicurarsi che la scalabilità e soglie di scala sono diverse per consentire di spazio tra le proporzioni dei caratteri.

- È stato scritto JSON un modello personalizzato?

    È facile commettere, pertanto partire da un modello come quello precedente che hanno lavora e apportare le modifiche incrementali small. 

- Per garantire la manualmente scalabilità avanti o indietro?

    Provare a ridistribuire la risorsa macchine Virtuali scala impostare con un'impostazione di "capacità" diversi per modificare il numero di macchine virtuali manualmente. Un modello di esempio per eseguire questa operazione è tornato: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing-potrebbe essere necessario modificare il modello per assicurarsi che ha le stesse dimensioni di computer come impostare la scala Usa. Se è possibile modificare il numero di macchine virtuali manualmente, si conosce che il problema è limitato per scalare automaticamente.

- Controllare il Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights risorse in [Esplora risorse Azure](https://resources.azure.com/)

    Si tratta di uno strumento di risoluzione dei problemi indispensabile che mostra lo stato delle risorse a disposizione Manager delle risorse di Azure. Fare clic su abbonamento e osservare il gruppo di risorse per la risoluzione. Nel provider di risorse di elaborazione osservare la scala di macchine Virtuali impostata è stato creato e controllare la visualizzazione istanza, che mostra lo stato di una distribuzione. Controllare la visualizzazione di istanza di macchine virtuali nel Set di scala macchine Virtuali. Quindi passa il provider di risorse Microsoft.Insights e controllare che le regole autoscale perfetti.

- L'estensione diagnostica utilizzo e la creazione di dati sulle prestazioni?

    __Aggiornamento:__ Azure autoscale è stata migliorata per l'utilizzo di una pipeline di metriche basata su host che non richiede un'estensione diagnostica siano installati. Questo errore indica la successiva che alcuni paragrafi non sono più valide se si crea un'applicazione di adattamento automatico tramite la pipeline di nuova. Esempi di modelli di Azure che sono stati convertiti a utilizzare la pipeline di host: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    Utilizzando la metrica basata su host per autoscale è migliore per i motivi seguenti:

    - È necessario installare meno componenti mobili come senza estensioni diagnostica.
    - Modelli di più semplici. Aggiungere regole autoscale approfondimenti in un modello di set di scala esistente.
    - Creazione di relazioni più affidabile e avvio veloce di macchine virtuali di nuove.

    I motivi solo che è possibile continuare a usare un'estensione diagnostica sarebbe esigenza di memoria diagnostica reporting/proporzioni dei caratteri. Metrica basata su host non inviare una segnalazione della memoria.

    Tenendo presente, se si siano ancora utilizzando le estensioni di diagnostiche per il ridimensionamento solo seguire il resto di questo articolo.

    In Gestione risorse di Azure AutoScale consentono di gestire (ma non ha più di) mediante una macchina virtuale estensione chiamato l'estensione di diagnostica. Genera dati sulle prestazioni a un account di archiviazione che definire nel modello. Questi dati sono quindi aggregati dal servizio di Azure Monitor.

    Se il servizio che non è possibile leggere i dati da macchine virtuali, è previsto per inviare un messaggio di posta elettronica, ad esempio se sono state macchine virtuali verso il basso, controllare la posta elettronica (quello specificato quando si crea l'account Azure).

    È anche possibile passare ed esaminare i dati. Esaminare l'account di archiviazione Azure utilizzando una finestra di esplorazione cloud. Esempio di utilizzo di [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), accedere e selezionare l'abbonamento Azure si usa e il nome dell'account di archiviazione diagnostica viene fatto riferimento nella definizione di estensione diagnostica nel modello di distribuzione..

    ![Esplora cloud][explorer]

    Di seguito si vedrà un insieme di tabelle memorizzazione i dati di ogni macchina virtuale. Prendendo Linux e le unità di misura metriche CPU ad esempio, cercare righe le più recenti. Soluzioni cloud di Visual Studio supportano un linguaggio di query in modo che è possibile eseguire una query come "Timestamp gt datetime'2016-02-02T21:20:00Z'" per assicurarsi di ricevere gli eventi più recenti (presuppongono ora è in formato UTC). Indica i dati visualizzati in sono corrispondono alle regole scala è possibile impostare? Nell'esempio seguente, la CPU per computer 20 avviato ingrandimento al 100% su 5 minuti..

    ![Tabelle di archiviazione][tables]

    Se i dati non sono presente, è presente che il problema sia in esecuzione in macchine virtuali l'estensione diagnostica. Se i dati sono presente, significa che c'è un problema con le regole di scala o con il servizio di informazioni approfondite. Controllare [lo stato di Azure](https://azure.microsoft.com/status/).

    Una volta che si è stati dei passaggi descritti, se si verificano ancora problemi autoscale si può provare con i forum su [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)o [overflow dello Stack](http://stackoverflow.com/questions/tagged/azure), o una chiamata al supporto. Prepararsi a condividere il modello e una visualizzazione dei dati sulle prestazioni.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
