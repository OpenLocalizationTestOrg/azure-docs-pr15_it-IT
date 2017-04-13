<properties
   pageTitle="Guida introduttiva di Azure Security Center | Microsoft Azure"
   description="In questo articolo contribuisce di iniziare rapidamente Azure Security Center che guidano l'utente tramite i componenti di gestione dei criteri e il monitoraggio di protezione e collegamenti ai passaggi successivi."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guida introduttiva di Azure Security Center

In questo articolo contribuisce di iniziare rapidamente con Azure Security Center mostrando i componenti di gestione di monitoraggio e criteri di sicurezza del Centro protezione.

> [AZURE.NOTE] In questo articolo vengono presentati il servizio usando una distribuzione di esempio. In questo articolo non è una Guida dettagliata.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Security Center, è necessario disporre di un abbonamento a Microsoft Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi a un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Il livello gratuito di Centro protezione viene attivato automaticamente con l'abbonamento e consente di visualizzare lo stato di protezione delle risorse di Azure. Fornisce la gestione dei criteri di sicurezza di base, suggerimenti per la sicurezza e l'integrazione con sicurezza prodotti e servizi dei partner Azure.

Dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/), accedere Security Center. Per ulteriori informazioni sul portale Azure, vedere la [documentazione del portale](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Raccolta di dati

Nel Centro sicurezza raccoglie dati dalle macchine () per valutare lo stato di protezione, fornire suggerimenti per la sicurezza e un avviso a rischi. Quando si accede a Centro protezione, tutte le macchine virtuali nell'abbonamento è attivata per la raccolta di dati. Raccolta di dati è consigliabile, ma è possibile rifiutare esplicitamente di partecipare disattivando la raccolta di dati nei criteri di Centro protezione.

La procedura seguente viene descritto come accedere e usare i componenti del Centro protezione. In questa procedura è viene illustrato come disattivare la raccolta di dati se si sceglie di rifiutare esplicitamente di partecipare.

## <a name="access-security-center"></a>Centro protezione di Access

Nel portale, procedere come segue per accedere a Centro protezione:

1. Nel menu **Microsoft Azure** selezionare **Centro protezione**.
![Menu di Azure][1]

2. Se si accede a Centro protezione per la prima volta, verrà visualizzata e il **completamento dell'installazione** . Selezionare Sì **! Desidero avvio Azure Security Center** per aprire e il **Centro sicurezza** e per consentire la raccolta di dati.
![Schermata iniziale][10]

3. Dopo l'avvio di Centro protezione da e il completamento dell'installazione o seleziona Security Center dal menu di Microsoft Azure, verrà visualizzata e il **Centro protezione** . Per facilitare l'accesso a e il **Centro protezione** in futuro, selezionare l'opzione **blade Pin al dashboard** (alto a destra).
![Blade PIN all'opzione di dashboard][2]

## <a name="use-security-center"></a>Utilizzare il Centro protezione

È possibile configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse. Di seguito configurare un criterio di protezione per l'abbonamento:

1. Scegliere il riquadro **criteri** e il **Centro protezione** .
![Criteri di sicurezza][3]

2. Scegliere un abbonamento e il **criterio di sicurezza - definire i criteri per abbonamento o delle risorse gruppo** .
3. In e il **criterio di protezione** , **la raccolta di dati** è attivata per raccogliere automaticamente log. L'estensione monitoraggio viene eseguito il provisioning in tutte le macchine virtuali corrente e nuove nella sottoscrizione. (È possibile rifiutare esplicitamente la raccolta di dati mediante l'impostazione **raccolta di dati** per **disattivare**, ma non nel Centro sicurezza fornendo consigli e gli avvisi di sicurezza)
4. Selezionare **Scegli un account di archiviazione per regione**e il **criterio di protezione** . Per ogni area geografica in cui si devono di macchine virtuali in esecuzione, si scegliere l'account di archiviazione in cui sono archiviati dati raccolti dal tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni regione, viene creato automaticamente. I dati raccolti sono logicamente isolati dai dati di altri clienti per motivi di sicurezza.

     > [AZURE.NOTE] È consigliabile attivare la raccolta di dati e scegliere nome e un account di archiviazione a livello di sottoscrizione. Criteri di sicurezza possono essere impostati al livello di abbonamento Azure e livello di gruppo di risorse, ma la configurazione di account di archiviazione e la raccolta di dati si verifica solo a livello di sottoscrizione.

5. Scegliere **i criteri di prevenzione**e il **criterio di protezione** . Verrà aperta e il **criteri di prevenzione della** .
![Criteri di prevenzione della][4]

6. Nella e **criterio di programmi** , attivare i suggerimenti che si desidera visualizzare come parte dei criteri di sicurezza. Esempi:

 - Impostare **gli aggiornamenti di sistema** su **On** analizza tutte le macchine virtuali supportate gli aggiornamenti del sistema operativo mancanti.
 - L'impostazione **vulnerabilità OS** su **On** analizza tutte le macchine virtuali supportate per identificare le configurazioni del sistema operativo che potrebbero rendere maggiormente esposto agli attacchi la macchina virtuale.

### <a name="view-recommendations"></a>Suggerimenti di visualizzazione

1. Tornare a e il **Centro sicurezza** e selezionare il riquadro **suggerimenti** . Nel Centro sicurezza analizza periodicamente lo stato di protezione delle risorse di Azure. Quando Security Center identifica potenziali vulnerabilità, vengono visualizzati suggerimenti su e il **consigli** .
![Suggerimenti in Centro protezione di Azure][5]

2.  Selezionare un suggerimento su e il **consigli** per visualizzare ulteriori informazioni e/o eseguire l'azione per risolvere il problema.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Visualizzare lo stato di salute e sicurezza delle risorse

1.  Tornare a e il **Centro protezione** . Riquadro **dell'integrità della protezione di risorse** contiene indicatori di stato protezione per macchine virtuali, rete, dati e applicazioni.
2.  Selezionare **macchine virtuali di** per visualizzare ulteriori informazioni. E il **macchine virtuali** viene visualizzato un riepilogo di stato di programmi antimalware, gli aggiornamenti del sistema, riavvio e vulnerabilità OS delle proprie macchine virtuali.
![Il riquadro di integrità risorse nel Centro protezione di Azure][6]

3.  Selezionare un suggerimento in **Macchina virtuale raccomandazioni** per visualizzare ulteriori informazioni e/o azioni per configurare controlli necessari.
4.  Selezionare una macchina virtuale in **macchine virtuali di** per visualizzare ulteriori dettagli.

### <a name="view-security-alerts"></a>Visualizza gli avvisi di sicurezza

1.  Tornare a e il **Centro sicurezza** e selezionare il riquadro **gli avvisi di sicurezza** . E il **avvisi di sicurezza** verrà visualizzato un elenco di avvisi. L'analisi di Centro protezione dei registri di protezione e attività di rete genera gli avvisi. Sono inclusi gli avvisi di soluzioni dei partner integrate.
![Avvisi di sicurezza in Centro protezione di Azure][7]

    > [AZURE.NOTE] Gli avvisi di sicurezza sono disponibili solo se è attivato il livello Standard di Centro protezione. Una versione di valutazione gratuita di 90 giorni del livello Standard è disponibile. Per informazioni su come ottenere il livello Standard, vedere [passaggi successivi](#next-steps) .

2.  Selezionare un avviso per visualizzare informazioni aggiuntive. In questo esempio, se si seleziona **modificato binario è stato rilevato**. Verrà aperta pale che forniscono informazioni dettagliate sull'avviso.
![Dettagli degli avvisi di protezione in Centro protezione di Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Visualizzare lo stato delle soluzioni partner

1. Tornare a e il **Centro protezione** . Il riquadro di **soluzioni dei Partner** consente di controllare a colpo d'occhio lo stato di integrità delle soluzioni partner integrato con l'abbonamento Azure.
2. Selezionare il riquadro di **soluzioni dei Partner** . Blade verrà visualizzato un elenco delle soluzioni partner connessi al Centro protezione.
![Soluzioni partner][9]

3. Selezionare una soluzione partner. In questo esempio, se si seleziona la soluzione **WAF F5** .  Blade si apre e Mostra lo stato della soluzione partner e le risorse associate della soluzione. Selezionare **console di soluzioni** per aprire l'ambiente di gestione dei partner per questa soluzione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato introdotto dei componenti di protezione di monitoraggio e criteri di gestione del Centro protezione. Ora che si ha familiarità con Security Center, provare a eseguire la procedura seguente:

- Configurare un criterio di sicurezza per l'abbonamento Azure. Per ulteriori informazioni, vedere [impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md).
- Utilizzare i suggerimenti nel Centro protezione per proteggere le risorse Azure. Per ulteriori informazioni, vedere [gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md).
- Esaminare e gestire gli avvisi di sicurezza corrente. Per ulteriori informazioni, vedere [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md).
- Ulteriori informazioni su come il [rischio rilevamento caratteristiche avanzate](security-center-detection-capabilities.md) in dotazione con il [livello Standard](security-center-pricing.md) di Centro protezione. Una versione di valutazione gratuita di 90 giorni del livello Standard è disponibile.
- Se hai domande sull'utilizzo di Centro protezione, vedere [Domande frequenti su Centro protezione di Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
