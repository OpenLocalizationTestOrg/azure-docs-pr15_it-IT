<properties
    pageTitle="Rilevamento delle modifiche soluzione nel Log Analitica | Microsoft Azure"
    description="È possibile utilizzare la soluzione rilevamento delle modifiche di configurazione in Analitica Log per identificare più facilmente applicazioni e servizi di Windows le modifiche che si verificano nel proprio ambiente, ovvero identificativo queste modifiche alla configurazione consentono di individuare problemi operativi."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Modificare la soluzione verifica in Analitica Log


È possibile utilizzare la soluzione rilevamento delle modifiche di configurazione nel registro Analitica per identificare più facilmente software e servizi di Windows le modifiche e Linux daemon che si verificano nel proprio ambiente, ovvero identificativo queste modifiche alla configurazione consentono di individuare problemi operativi.

Installare la soluzione per aggiornare il tipo di agente che sono stati installati. Le modifiche apportate ai installato un software e servizi di Windows Server monitorate vengono letti e quindi i dati vengono inviati al servizio Log Analitica nel cloud per l'elaborazione. Logica viene applicata ai dati ricevuti e il servizio cloud registra i dati. Quando vengono rilevate modifiche, server con le modifiche vengono visualizzati nel dashboard il rilevamento delle modifiche. In base alle informazioni nel dashboard di rilevamento delle modifiche, è possibile vedere facilmente le modifiche apportate all'interno dell'infrastruttura di server.

## <a name="installing-and-configuring-the-solution"></a>Installare e configurare la soluzione
Utilizzare le informazioni seguenti per installare e configurare la soluzione.

- Operations Manager è necessaria per la soluzione il rilevamento delle modifiche.
- È un agente di Windows o Operations Manager in ogni computer in cui si desidera eseguire il monitoraggio delle modifiche.
- Aggiungere la soluzione il rilevamento delle modifiche nell'area di lavoro OMS usando la procedura descritta nella sezione [aggiungere Analitica Log soluzioni dalla raccolta soluzioni](log-analytics-add-solutions.md).  Non esiste alcun ulteriori operazioni di configurazione necessari.


## <a name="change-tracking-data-collection-details"></a>Modificare i dettagli di raccolta dati di verifica

Il rilevamento delle modifiche raccoglie inventario software e dei metadati del servizio di Windows con gli agenti che è stata attivata.

Nella tabella seguente mostra i metodi di raccolta dati e altri dettagli sulle modalità di raccolta dati per il rilevamento delle modifiche.

| piattaforma | Agente diretta | Agente SCOM | Spazio di archiviazione Azure | SCOM necessari? | Dati di agente SCOM inviati tramite il gruppo di gestione | frequenza di raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png)|![No](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![No](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Sì](./media/log-analytics-change-tracking/oms-bullet-green.png)| ogni ora|

## <a name="use-change-tracking"></a>Utilizzare il rilevamento delle modifiche

Dopo aver installata la soluzione, è possibile visualizzare il riepilogo delle modifiche per il server di monitoraggio utilizzando il riquadro **Il rilevamento delle modifiche** nella pagina **Panoramica** OMS.

![immagine del riquadro il rilevamento delle modifiche](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

È possibile visualizzare le modifiche apportate all'infrastruttura e drill-down in dettagli relative alle categorie seguenti:

- Modifiche in base al tipo di configurazione per software e servizi di Windows
- Software assume l'aspetto di applicazioni e aggiornamenti per i singoli server
- Numero totale di modifiche di software per ogni applicazione
- Modifiche del servizio di Windows per i singoli server

![immagine del dashboard il rilevamento delle modifiche](./media/log-analytics-change-tracking/oms-changetracking01.png)

![immagine del dashboard il rilevamento delle modifiche](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Per visualizzare le modifiche apportate tipo

1. Nella pagina **Panoramica** fare clic sul riquadro **Il rilevamento delle modifiche** .
2. Nel dashboard **Modificare verifica** rivedere le informazioni di riepilogo in una delle pale tipo Modifica e quindi fare clic su uno per visualizzare informazioni dettagliate nella pagina **log ricerca** .
3. In tutte le pagine di ricerca di log, è possibile visualizzare i risultati, ora, risultati dettagliati e la cronologia di ricerca di log. È inoltre possibile filtrare facet per limitare i risultati.

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare [le ricerche Log in Analitica Log](log-analytics-log-searches.md) per visualizzare dati di rilevamento dettagliata.
