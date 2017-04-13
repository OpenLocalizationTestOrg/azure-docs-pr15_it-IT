<properties
   pageTitle="Utilizzando il Centro protezione di Azure per un intervento | Microsoft Azure"
   description="In questo documento viene illustrato come utilizzare Azure Security Center per uno scenario di risposta."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>Utilizzo di Centro protezione di Azure per un intervento
Molte organizzazioni di informazioni su come rispondere ai problemi di protezione solo dopo aver subito un attacco. Per ridurre i costi e danni, è importante avere un intervento piano prima attacco entrerà in vigore. È possibile utilizzare Azure Security Center nelle diverse fasi di un intervento.

## <a name="incident-response-planning"></a>Risposta ai problemi di pianificazione

Un piano efficace dipende da tre funzionalità principali: la possibilità di proteggere, rilevare e rispondere a rischi. Protezione sia su come evitare problemi, il rilevamento è sull'identificazione dei rischi in anticipo e risposta riguarda la rimozione il pirata informatico e sul ripristino di sistemi per ridurre l'impatto violazioni della.

In questo articolo verrà utilizzato le fasi di risposta ai problemi di sicurezza dall'articolo [Risposta di protezione di Microsoft Azure nel Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , come illustrato nella figura seguente:

![Ciclo di vita di risposta](./media/security-center-incident-response/security-center-incident-response-fig1.png)

È possibile usare nel Centro sicurezza durante le fasi rileva, valutare e diagnosi. Ecco alcuni esempi di come Security Center può essere utile durante le fasi iniziali intervento tre:

- **Rileva**: rivedere l'indicazione prima di un'analisi evento.
    - Esempio: revisione verifica iniziale che è stato generato un avviso di sicurezza priorità alta nel dashboard di Centro protezione.
- **Valutazione**: eseguire la valutazione iniziale per ottenere ulteriori informazioni sull'attività sospetto.
    - Esempio: ottenere maggiori informazioni sull'avviso di sicurezza.
- **Diagnosi**: condurre un'analisi tecnica e identificare strategie di contenimento, attenuazione e soluzione.
    - Esempio: eseguire la procedura di monitoraggio e aggiornamento descritta dal Centro protezione l'avviso di sicurezza specifico.

Scenario che segue viene illustrato come sfruttare Security Center durante le fasi rileva, valutare e diagnosi/Rispondi di un incidente. In Centro protezione, un [incidente](security-center-incident.md) è un'aggregazione di tutti gli avvisi per una risorsa che si adattano al [interrompere catena](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) modelli. Interventi visualizzati nel riquadro [gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md) e blade. Un evento imprevisto rivela l'elenco dei relativi avvisi, che consente di ottenere ulteriori informazioni su ogni occorrenza. Nel Centro sicurezza presenta anche autonoma gli avvisi di sicurezza che possono essere utilizzati anche per tenere traccia di un'attività sospetta.

## <a name="scenario"></a>Scenario:

Contoso migrati recente alcune delle risorse locale Azure, tra cui alcuni basate su macchina virtuale line-of-business carichi di lavoro e database SQL. Attualmente, Contoso Core Computer protezione incidente risposta del Team (CSIRT) presenta un problema di analisi dei problemi di sicurezza a causa di business intelligence di sicurezza non integrati con gli strumenti di risposta corrente. Questa mancanza di integrazione introduce un problema durante la fase di rileva (troppi falsi positivi), oltre che durante le fasi di valutazione e diagnosi. Durante la migrazione, e che decida di acconsentire esplicitamente per il Centro protezione risolvere questo problema.

La prima fase della migrazione termine dopo il onboarded tutte le risorse e indirizzato tutti i suggerimenti sulla protezione dal Centro protezione. Contoso CSIRT è fulcro per la gestione dei problemi di sicurezza computer. Il team è costituito da un gruppo di persone con responsabilità di occuparsi di eventuali problemi di protezione. I membri del team sono definiti in modo chiaro compiti per assicurarsi che nessuna area della risposta rimanga scoperte.

Per questo scenario, verranno lo stato attivo sui ruoli degli utenti tipo seguenti che fanno parte di Contoso CSIRT:

![Ciclo di vita di risposta](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Alice è nelle operazioni di protezione. Le proprie responsabilità includono:

- Monitorare e rispondere a minacce nell'arco.
- Escalation al cloud carico di lavoro proprietario o analyst di protezione in base alle esigenze.

Giorgio è un esperto di sicurezza e la sua responsabilità includono:

- Esaminando attacchi.
- Avvisi giù.
- Utilizzo di proprietari di carico di lavoro per determinare e applicare riduzioni disponibili.

Come si può notare, Alice e Giorgio hanno responsabilità diverse e devono interagire tra loro per condividere le informazioni di Centro protezione.

## <a name="recommended-solution"></a>Soluzione consigliata

Poiché Alice e Giorgio hanno ruoli diversi, si inizierà a usare diverse aree di Centro protezione per ottenere informazioni rilevanti per le attività quotidiane. Alice utilizzeranno **gli avvisi di sicurezza** come parte del proprio monitoraggio giornaliero.

![Avvisi di sicurezza](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Alice utilizzeranno gli avvisi di sicurezza durante le fasi rileva e valutazione. Al termine del valutazione iniziale Alice, Anna può effettuare l'escalation il problema a Giorgio se è necessario analizzare ulteriormente. A questo punto, Giorgio le informazioni verrà utilizzate fornito dal Centro protezione, a volte in combinazione con altre origini dati, per passare alla finestra di condivisione della diagnosi.


## <a name="how-to-implement-this-solution"></a>Come implementare questa soluzione

Per vedere come si preferisce usare Azure Security Center in uno scenario di risposta, si verrà eseguire operazioni di Alice in fasi rileva e valutazione e quindi vedere che cosa significa Giorgio di diagnosticare il problema.

### <a name="detect-and-assess-incident-response-stages"></a>Rilevare e valutare le fasi di risposta

Alice effettuato l'accesso al portale di Azure e l'uso di console di Centro protezione. Come parte del proprio giornaliere di Monitoraggio attività, ha avviato rivedere gli avvisi di sicurezza ad alta priorità eseguendo la procedura seguente:

1. Fare clic sul riquadro **gli avvisi di sicurezza** e accedere a e il **avvisi di sicurezza** .
    ![Blade degli avvisi di sicurezza](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Allo scopo di questo scenario Alice verrà eseguita una valutazione nell'avviso di attività SQL dannoso, come illustrato nella figura precedente.
2. Fare clic sull'avviso **SQL dannoso attività** ed esaminare le risorse attaccate e **l'attività SQL dannoso** :  ![dettagli evento imprevisto](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    In questo blade Alice possibile scrivere note relative alle risorse attaccate, come è le più volte questo attacco e quando è stato rilevato.
3. Fare clic su di **attacchi delle risorse** per ottenere ulteriori informazioni su questo attacco.

Dopo aver letto la descrizione, Alice è sicuro che non si tratta di un falso positivo e che Anna deve effettuare l'escalation in questo caso a Giorgio.

### <a name="diagnose-incident-response-stage"></a>Diagnosticare fase di risposta

Giorgio riceve le maiuscole/minuscole da Alice e avvia rivisto la procedura per la risoluzione che Security Center suggeriti.

![Ciclo di vita di risposta](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Risorse aggiuntive

Il team di intervento può anche sfruttare le funzionalità di [Sicurezza di Power BI](security-center-powerbi.md) per visualizzare diversi tipi di report. Questi report possono aiutarli durante approfondire per visualizzare, analizzare e filtrare consigli e gli avvisi di sicurezza. Per le aziende che utilizzano le informazioni sulla protezione e soluzione di gestione (SIEM) evento durante il processo di analisi, è inoltre possibile [integrare Security Center con la soluzione](security-center-integrating-alerts-with-log-integration.md). Utilizzando lo [strumento di integrazione log Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/), è possibile integrare i log di controllo Azure e gli eventi di protezione macchine (). Per esaminare un attacco, è possibile utilizzare queste informazioni con le informazioni fornite dal Centro protezione.


## <a name="conclusion"></a>Conclusioni

Assemblare un team prima che si verifica un evento imprevisto è molto importante per l'organizzazione e positiva interferisce con la modalità di gestione problemi. Visto agli strumenti appropriati per monitorare le risorse consente di questo team accurate operazioni per risolvere un problema di sicurezza. Consentono di Centro protezione [funzionalità di rilevamento](security-center-detection-capabilities.md) IT rapidamente problemi di protezione e risolvere i problemi di sicurezza.
