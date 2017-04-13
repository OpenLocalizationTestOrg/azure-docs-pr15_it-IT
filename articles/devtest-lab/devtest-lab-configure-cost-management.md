<properties
    pageTitle="Visualizzare la tendenza costo previsto laboratorio mensile in Azure DevTest esercitazioni | Microsoft Azure"
    description="Le informazioni del grafico di tendenza Azure DevTest esercitazioni mensile costo previsto."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="tarcher"/>

# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visualizzare la tendenza costo previsto laboratorio mensile in Azure DevTest esercitazioni

La funzionalità di gestione dei costi di esercitazioni DevTest consente di controllare il costo di ambiente. In questo articolo viene illustrato come utilizzare il grafico **Mensile tendenza costo previsto** per la visualizzazione stimata costo date del mese del calendario corrente e il costo previsto di fine del mese per mese del calendario corrente. In questo articolo si imparerà a visualizzare il grafico di tendenza costo stimato mensile nel portale di Azure.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizzazione del grafico mensile tendenza costo previsto

Per visualizzare il grafico mensile tendenza costo stimata, procedere come segue: 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi**e quindi selezionare **Esercitazioni DevTest** dall'elenco.

1. Selezionare il laboratorio desiderato dall'elenco di esercitazioni.   

1. In blade del laboratorio, selezionare **impostazioni di costo**.

1. Blade **costo impostazioni** dell'ambiente di test, selezionare **laboratorio costo tendenza**.

1. Lo screenshot seguente è illustrato un esempio di un grafico di costo. 

    ![Grafico dei costi](./media/devtest-lab-configure-cost-management/graph.png)

Il valore di **costo stimato** è stimata costo-fino a oggi del mese del calendario corrente. Il **costo previsto** è il costo previsto per l'intero mese del calendario corrente, calcolata utilizzando il costo di laboratorio per cinque giorni precedenti.
 
Gli importi dei costi sono arrotondati per eccesso al numero intero successivo. Per esempio: 

- 5.01 viene arrotondato per eccesso 6 
- 5,50 viene arrotondato per eccesso 6
- 5.99 viene arrotondato per eccesso 6

Come in cui viene indicato sopra il grafico, i costi che viene visualizzato nel grafico sono costi *stimati* utilizzando [Uso prepagato](https://azure.microsoft.com/offers/ms-azr-0003p/) offrono tariffe.
Inoltre, si verificano le seguenti *non* inclusi nel calcolo del costo:

- Sottoscrizioni CSP e Dreamspark non sono attualmente supportate in Azure DevTest esercitazioni utilizza [Azure API di fatturazione](../billing-usage-rate-card-overview.md) per calcolare il costo laboratorio che non supporta le sottoscrizioni CSP o Dreamspark.
- Le tariffe offerta. Al momento non è in grado di utilizzare le tariffe offerta (come illustrate in abbonamento) che si dispongano di negoziare con Microsoft o Microsoft partner. Si sono utilizzando le tariffe uso prepagato.
- Le imposte
- Gli sconti
- La valuta di fatturazione. Attualmente, il costo di laboratorio viene visualizzato solo nella valuta dollari.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati

- [Altri due suggerimenti per mantenere il costo puntuale esercitazioni DevTest](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
- [Perché costo soglie?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportate alcune operazioni da provare Avanti:

- [Definire i criteri di laboratorio](./devtest-lab-set-lab-policy.md) - informazioni su come impostare vari criteri usati per controllare le modalità di utilizzo di ambiente e il relativo macchine virtuali. 
- [Immagine personalizzata crea](./devtest-lab-create-template.md) - quando si crea una macchina virtuale, specificare una base, che può essere un'immagine personalizzata o un'immagine Marketplace. In questo articolo viene illustrato come creare un'immagine personalizzata da un file disco rigido virtuale.
- [Configurare Marketplace immagini](./devtest-lab-configure-marketplace-images.md) - esercitazioni DevTest supporta la creazione di macchine virtuali basate su immagini di Azure Marketplace. In questo articolo viene illustrato come specificare che, se presenti, le immagini di Azure Marketplace possono essere utilizzato per la creazione di macchine virtuali in un ambiente di lavoro.
- [Creare una macchina virtuale in un ambiente di lavoro](./devtest-lab-add-vm-with-artifacts.md) - viene illustrato come creare una macchina virtuale da un'immagine di base (uno personalizzato o il Marketplace) e su come lavorare con elementi di una macchina virtuale.