<properties
   pageTitle="Comprendere la segnalazione di pagamento Azure Marketplace | Microsoft Azure"
   description="Informazioni su come verificare e caricare il report di pagamento di Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter="na"
   authors="v-jeana"
   manager="lakoch"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="v-jeana; hascipio; v-dabosl"/>

# <a name="understand-your-azure-marketplace-payout-reports"></a>Comprendere i report di pagamento di Azure Marketplace

## <a name="access-and-view-your-payout-reports"></a>Accedere e visualizzare i report di pagamento

Durante la transizione a Centro per sviluppatori alcuni dei report pagamento potrebbe essere disponibile al centro per sviluppatori https://dev.windows.com/en-us mentre gli altri utenti possono ancora visitare nel portale pubblicazione https://publish.windowsazure.com.

Pagamento reporting ora saranno disponibili in **Centro per sviluppatori** per le offerte Marketplace associate a una versione recente pagamenti; attualmente sono inclusi:
- Macchine virtuali
- Offre B + C
- Dati e Dev servizi offerti in EA

Creazione di report di pagamento sarà ancora presente nel **Portale di pubblicazione** per:
- Dati e Dev servizi offerti in Web diretta (che utilizza comunque il sistema di pagamento legacy).

Report sono disponibili 45 giorni dopo la chiusura del trimestre e vengono calcolati dopo qualsiasi rimborsi.

### <a name="access-payout-reports-in-dev-center"></a>Report di pagamento Access nel centro per sviluppatori

1. Passare al centro per sviluppatori in https://dev.windows.com/en-us.
2. Fare clic su **Dashboard**.

    ![LandingPageDashboardHighlight][1]

3. Fare clic su **Riepilogo pagamento**.

    ![DashboardPayoutSummary][2]


## <a name="view-your-payout-reports-in-dev-center"></a>Visualizzare i report di pagamento in Centro per sviluppatori

Il report di pagamento per ogni trimestre registra tutte le operazioni che si sono verificati all'interno di tale trimestre.

- L'importo riservato indica i pagamenti vengono attribuiti di fuori del ciclo di pagamento in scadenza (ad esempio questo importo consente di spostare pagamento imminente mese successivo).  Questo importo sarà generalmente il $0 (a meno che un cliente paga in anticipo).
- Fare clic sul pagamento in scadenza o **visualizzare i dettagli** di pagamento più recente collegamenti per visualizzare una nota sui pagamenti.
- Fare clic su **Istruzioni di pagamento** per visualizzare i dettagli in procede per app/prodotto.
- Fare clic sul collegamento della **visualizzazione** per vedere le singole istruzioni.

    ![PayoutSummaryUpcomingMostRecentLinksStatement][3]

- Usare il filtro di **Scomposizione procede** nella parte inferiore dell'istruzione singoli per visualizzare più App/prodotti se presenti.

    ![PayoutSummaryPaymentStatementsFilterControl][4]



## <a name="view-your-payout-reports-in-publishing-portal"></a>Visualizzare i report di pagamento nel portale di pubblicazione
Il report di pagamento per ogni trimestre registra tutte le operazioni che si sono verificati all'interno di tale trimestre.

1. Passare al portale di pubblicazione all'https://publish.windowsazure.com.
2. Nella sezione di **autori** , fare clic su **Report di pagamento**.
3. Fare clic su elenco a discesa per visualizzare i report trimestrali pagamento tutti disponibili.

    ![accessingpayoutreport][5]


### <a name="read-your-payout-reports"></a>Leggere i report di pagamento

Il report di pagamento per ogni trimestre registra tutte le operazioni che si sono verificati all'interno di tale trimestre.

- Se si sta cercando movimenti correlati a un trimestre specifico, selezionare il report di pagamento per tale trimestre dall'elenco a discesa. Ad esempio, se è interessati movimenti ad aprile a giugno 2015, selezionare l'intervallo di date dall'elenco a discesa.
- Se si sta cercando dettagli dei pagamenti che riguardano un trimestre specifico, selezionare il report di pagamento per trimestre successivo. Ad esempio, se è interessati i pagamenti ad aprile a giugno 2015, questi importi verranno visualizzati nel report pagamento successive di luglio a settembre 2015.
![readingpayoutreport][6]

- Riquadro riepilogo finanziario Mostra i saldi, crediti e debiti in base alla categoria.
- Movimenti mostrano le transazioni singole.

## <a name="definitions"></a>Definizioni

**Pannello riepilogo finanziario:**

![financialdefinitions][7]

**Movimenti:**

![ledgerdefinitions][8]

## <a name="payout-questions"></a>Domande di pagamento

Se si dispone di una domanda correlata ai pagamenti, contattare il team di supporto.

![payoutquestions][9]

1. Passare alla pagine del supporto.
2. Selezionare i **pagamenti**.
3. Selezionare **pagamento correlate richieste di informazioni**.
4. Fare clic su **Start richiesta**.

## <a name="next-steps"></a>Passaggi successivi

Per altre query di supporto, registrare un problema <all'https://portal.azure.com>.

[1]: ./media/marketplace-publishing-report-payout/LandingPage-DashboardHighlight.png
[2]: ./media/marketplace-publishing-report-payout/Dashboard-PayoutSummary.png
[3]: ./media/marketplace-publishing-report-payout/PayoutSummary-UpcomingOrMostRecentPaymentLinksSingleStatementLink.png
[4]: ./media/marketplace-publishing-report-payout/PayoutSummary-PaymentStatements-SingleStatement-FilterControl.png
[5]: ./media/marketplace-publishing-report-payout/accessingpayoutreport.png
[6]: ./media/marketplace-publishing-report-payout/readingpayoutreport.png
[7]: ./media/marketplace-publishing-report-payout/financialdefinitions.png
[8]: ./media/marketplace-publishing-report-payout/ledgerdefinitions.png
[9]: ./media/marketplace-publishing-report-payout/payoutquestions.png
