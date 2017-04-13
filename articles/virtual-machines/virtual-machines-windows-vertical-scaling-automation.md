<properties
    pageTitle="Ridimensionare verticalmente Azure macchine virtuali con l'automazione di Azure | Microsoft Azure"
    description="Come ridurre verticalmente una macchina virtuale di Windows in risposta a monitoraggio degli avvisi con l'automazione di Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machines-with-azure-automation"></a>Ridimensionare verticalmente Azure macchine virtuali con l'automazione di Azure

Scalabilità verticale è il processo di aumentare o diminuire le risorse del computer in risposta al carico di lavoro. In Azure questo risultato può essere ottenuto modificando la dimensione della macchina virtuale. Questo è utile per gli scenari seguenti

- Se la macchina virtuale non viene utilizzato frequentemente, è possibile ridimensionare per difetto a una versione ridotta per ridurre i costi mensili
- Se la macchina virtuale viene visualizzato un carico di picco, possono essere ridimensionata di una dimensione maggiore per aumentare la capacità

La struttura per i passaggi necessari eseguire questa operazione è possibile eseguire sotto

1. Automazione di Azure il programma di installazione per accedere a macchine virtuali
2. Importare runbook scala verticale automazione Azure in abbonamento
3. Aggiungere un webhook il runbook
4. Aggiungere un avviso per la macchina virtuale

> [AZURE.NOTE] A causa delle dimensioni della macchina virtuale prima, le dimensioni possono essere ridimensionata, può essere limitata a causa la disponibilità di altre dimensioni del cluster in che la macchina virtuale corrente viene distribuito. In runbook automazione pubblicato utilizzati in questo articolo occuparsi di questo caso e ridimensionare solo all'interno di seguito coppie di dimensioni macchine Virtuali. Ciò significa che una macchina virtuale Standard_D1v2 verrà non improvvisamente ridimensionato Standard_G5 o ridotta a Basic_A0.

>| Macchine Virtuali dimensioni coppia di scala |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Automazione di Azure il programma di installazione per accedere a macchine virtuali

La prima cosa che occorre fare è creare un account Azure automazione contenenti runbook utilizzato per ridimensionare una macchina virtuale. Il servizio di automazione recente introdotta la funzionalità "Esegui come account" che semplifica l'impostazione di configurazione principale del servizio per l'esecuzione automatica di runbook per conto dell'utente molto semplice. Vengono fornite ulteriori informazioni vedere l'articolo seguente:

* [Eseguire l'autenticazione runbook con account Azure Esegui come](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importare runbook scala verticale automazione Azure in abbonamento

Runbook necessari per la scala verticalmente la macchina virtuale già pubblicati nella raccolta Runbook automazione Azure. È necessario importarli in abbonamento. Si imparerà a importare runbook, leggere l'articolo seguente.

* [Raccolte di runbook e modulo per l'automazione di Azure](../automation/automation-runbook-gallery.md)

Runbook che devono essere importati vengono visualizzati nell'immagine seguente

![Importare runbook](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Aggiungere un webhook il runbook

Dopo avere importato runbook che è necessario aggiungere un webhook dal runbook in modo che possono essere avviato da un avviso da una macchina virtuale. I dettagli della creazione di un webhook per il Runbook possono essere letti qui

* [Azure webhooks di automazione](../automation/automation-webhooks.md)

Assicurarsi di copiare il webhook prima di chiudere la finestra di dialogo webhook come sarà necessario nella sezione successiva.

## <a name="add-an-alert-to-your-virtual-machine"></a>Aggiungere un avviso per la macchina virtuale

1. Selezionare le impostazioni del computer virtuale
2. Selezionare "Regole di avviso"
3. Selezionare "Aggiungi avviso"
4. Selezionare una metrica da attivare l'avviso al
5. Selezionare una condizione che, quando soddisfatte verranno causano l'avviso verso fire
6. Selezionare un limite per la condizione nel passaggio 5. per essere soddisfatte
7. Selezionare un periodo in cui il servizio di monitoraggio controllerà per la soglia i passaggi da 5 e 6
8. Incollare in webhook che sono stati copiati dalla sezione precedente.

![Aggiungere un avviso per la macchina virtuale 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Aggiungere un avviso per macchina virtuale 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)
