<properties
   pageTitle="Report sullo stato nel Centro sicurezza Azure rischio | Microsoft Azure"
   description="In questo documento consente di utilizzare report rischio di Centro protezione Azure intelligente durante un'analisi per trovare ulteriori informazioni relative a un avviso di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Report sullo stato rischio Centro protezione di Azure
In questo documento viene descritto come report rischio di Centro protezione Azure intelligente consente di acquisire familiarità con un rischio che ha generato un avviso di sicurezza.

## <a name="what-is-a-threat-intelligence-report"></a>Che cos'è un report di business intelligence rischio?
Individuazione Security Center funziona mediante il monitoraggio delle informazioni di protezione delle risorse di Azure, rete e le soluzioni partner connessi. In presenza di queste informazioni, spesso correlare le informazioni provenienti da più origini, per identificare i rischi. Questo processo fa parte di Centro protezione [funzionalità di rilevamento](security-center-detection-capabilities.md)di. 

Quando nel Centro sicurezza identifica un rischio, venga attivato un [avviso di sicurezza](security-center-managing-and-responding-alerts.md), che contiene informazioni dettagliate relative a un determinato evento, che include suggerimenti sulla risoluzione. Per semplificare l'intervento team esaminare e risolvere i rischi, Security Center include un report di business intelligence di rischio che contiene informazioni sul rischio rilevati, incluse informazioni quali il: 

- Identità non autorizzato o associazioni (se queste informazioni sono disponibili)
- Obiettivi utenti malintenzionati
- Campagne attacco corrente e nella cronologia (se queste informazioni sono disponibili)
- Utenti malintenzionati tattiche, strumenti e le procedure
- Associato gli indicatori di violazione (IoC), ad esempio gli URL e hash di file
- Victimology, ossia il settore e geografico prevalenza per risultare utili per determinare se le risorse Azure sono interessati
- Informazioni di risoluzione di problemi e riduzione dei rischi

>[AZURE.NOTE] Varia la quantità di informazioni in un report specifico. il livello di dettaglio è basato su attività e prevalenza del malware.

Centro protezione sono tre tipi di report di rischio, può variare a seconda dell'attacco. I report disponibili sono:

- **Attività gruppo Report**: fornisce approfondimenti nel utenti malintenzionati, obiettivi e tattiche.
- **Report campagne**: è incentrata su dettagli delle campagne di attacco specifico. 
- **Report riepilogo rischio**: coprire tutte le voci di due report precedente.

Questo tipo di informazioni è molto utile durante il processo di [risposta](security-center-incident-response.md) , in caso di una ricerca in corso per comprendere l'origine di attacco, motivazioni dannoso e cosa fare per evitare questo problema futuro. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Come è possibile accedere al report di business intelligence rischio?

È possibile esaminare gli avvisi correnti esaminando riquadro **gli avvisi di sicurezza** . Aprire il portale di Azure e seguire la procedura seguente per visualizzare ulteriori dettagli su ogni avviso:

1. Nel dashboard di Centro protezione, verrà visualizzato il riquadro **gli avvisi di sicurezza** .

2. Fare clic sul riquadro per aprire e il **avvisi di sicurezza** che contiene ulteriori dettagli sugli avvisi e fare clic su nell'avviso di sicurezza che si desidera ottenere ulteriori informazioni sulle.

    ![Avvisi di sicurezza](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. In questo caso e il **processo sospetto eseguito** Mostra i dettagli dell'avviso, come illustrato nella figura seguente:

    ![Detais degli avvisi di sicurezza](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  La quantità di informazioni disponibili per ogni avviso di sicurezza variano in base al tipo di avviso. Nel campo **report** è presente un collegamento al report di business intelligence rischio. Fare clic su di esso e un'altra finestra del browser verrà visualizzato con file PDF.

    ![Selezione di spazio di archiviazione](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Da qui è possibile scaricare il file PDF per il report e ottenere ulteriori informazioni sul problema di sicurezza che è stato rilevato e intraprendere azioni in base alle informazioni fornite.

## <a name="see-also"></a>Vedere anche

In questo documento è stato modo Azure Security Center rischio intelligente report consente di durante indagini relativi ad avvisi di sicurezza. Per ulteriori informazioni sul Centro protezione di Azure, vedere gli articoli seguenti:

- [Domande frequenti su Centro protezione di azure](security-center-faq.md). Trovare le domande più frequenti sull'utilizzo del servizio.
- [Utilizzo di Azure Security Center per risposta](security-center-incident-response.md)
- [Funzionalità di rilevamento di Centro protezione di Azure](security-center-detection-capabilities.md)
- [Centro protezione di azure pianificazione e le operazioni di Guida](security-center-planning-and-operations-guide.md). Informazioni su come pianificare e si conoscono le considerazioni di progettazione per l'adozione di Azure Security Center.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md). Informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Gestione problemi di protezione in Centro protezione di Azure](security-center-incident.md)
- [Blog di sicurezza di azure](http://blogs.msdn.com/b/azuresecurity/). Trovare post di blog sulla sicurezza di Azure e conformità.
