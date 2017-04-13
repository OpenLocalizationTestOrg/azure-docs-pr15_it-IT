<properties 
    pageTitle="Verificare una runbook in Azure automazione | Microsoft Azure"
    description="Prima di pubblicare una runbook in Azure automazione, è possibile verificarla per assicurarsi che funziona come previsto.  In questo articolo viene descritto come verificare un runbook e visualizzarne l'output."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Test un runbook automazione Azure
Quando si verifica un runbook, viene eseguita la [versione bozza](automation-creating-importing-runbook.md#publishing-a-runbook) e le azioni eseguite vengono completate. Non verrà creata alcuna cronologia dei processi, ma vengono visualizzati i flussi di [Output](automation-runbook-output-and-messages.md#output-stream) e [avvisi ed errori](automation-runbook-output-and-messages.md#message-streams) del test output riquadro. I messaggi in [flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro di Output solo se la [variabile $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) è impostata su Continua.

Anche se si esegue la versione bozza, dal runbook ancora viene eseguito il flusso di lavoro normalmente ed esegue operazioni con le risorse nell'ambiente. Per questo motivo, è necessario verificare solo runbook risorse non di produzione.

La procedura per ogni [tipo di runbook](automation-runbook-types.md) di test è lo stesso e non vi è alcuna differenza test tra l'editor di testo e grafica editor nel portale di Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Per provare una runbook nel portale di Azure

È possibile lavorare con qualsiasi [tipo di runbook](automation-runbook-types.md) nel portale di Azure.

1. Aprire la versione bozza dal runbook [nell'editor di testo](automation-editing-a-runbook.md#Portal) o [grafica editor](automation-graphical-authoring-intro.md).
2. Fare clic sul pulsante **Test** per aprire e il Test.
3. Se dal runbook include parametri, saranno elencati nel riquadro sinistro nel punto in cui è possibile fornire i valori da utilizzare per il test.
4. Se si desidera eseguire il test un [Lavoro Runbook ibrida](automation-hybrid-runbook-worker.md), modificare le **Impostazioni di esecuzione** per **Ibrido lavoro** e selezionare il nome del gruppo di destinatari.  In caso contrario, mantenere il valore predefinito **Azure** per eseguire il test nel cloud.
5. Fare clic sul pulsante **Avvia** per avviare il test.
6. Se dal runbook [PowerShell flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks) o [grafiche](automation-runbook-types.md#graphical-runbooks), è possibile interrompere o sospendere mentre si sta testando con i pulsanti sotto il riquadro di Output. Quando si sospende dal runbook, completa l'attività corrente prima di sospensione. Una volta dal runbook è sospesa, è possibile interromperlo o riavviarla.
7. Esaminare l'output da runbook nel riquadro di output.


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare o importare una runbook, vedere [creazione o importazione runbook di automazione di Azure](automation-creating-importing-runbook.md)
- Per ulteriori informazioni sulla creazione di grafici, vedere [grafiche creazione condivisa in automazione Azure](automation-graphical-authoring-intro.md)
- Per iniziare a utilizzare runbook del flusso di lavoro di PowerShell, vedere [il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
- Per ulteriori informazioni sulla configurazione runboks per restituire i messaggi di stato ed errori, incluse le procedure consigliate, vedere [Runbook output e i messaggi di automazione di Azure](automation-runbook-output-and-messages.md)