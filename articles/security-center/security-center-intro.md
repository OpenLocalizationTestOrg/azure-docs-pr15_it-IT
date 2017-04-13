<properties
   pageTitle="Introduzione al Centro protezione di Azure | Microsoft Azure"
   description="Le informazioni nel Centro sicurezza Azure, le relative funzionalità chiave e sul suo funzionamento."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introduzione al Centro protezione di Azure

Le informazioni nel Centro sicurezza Azure, le relative funzionalità chiave e sul suo funzionamento.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.

## <a name="what-is-azure-security-center"></a>Che cos'è Centro protezione di Azure?
 Centro protezione consente di impedire, rilevare e rispondere alle minacce con maggiore visibilità e controllare la protezione delle risorse di Azure. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

##  <a name="key-capabilities"></a>Funzionalità principali
 Nel Centro sicurezza offre rischio di semplice utilizzo ed efficace funzionalità di sono progettate programmi, il rilevamento e risposta in Azure. Funzionalità chiave sono:

| | |
|----- |-----|
| Impedire | Controlla lo stato di protezione delle risorse di Azure |
| | Consente di definire i criteri per gli abbonamenti Azure e i gruppi di risorse in base alle esigenze di sicurezza della società, i tipi di applicazioni in uso e la riservatezza dei dati |
| | Suggerimenti relativi alla protezione basato su criteri viene utilizzato per i proprietari di servizi tramite il processo di implementazione necessari controlli |
| | Distribuisce rapidamente servizi di sicurezza e accessori da Microsoft e partner |
| Rilevare |Raccoglie e analizza i dati di sicurezza di risorse di Azure, rete e le soluzioni partner come programmi antimalware e firewall automaticamente |
| | Utilizza globale delle minacce intelligence dai prodotti Microsoft e servizi, Microsoft digitale crimini unità (DCU), Microsoft Security Response Center (MSRC) e feed esterni |
| | Si applica analitica avanzate, tra cui comportamento analisi e apprendimento |
| Rispondi | Fornisce operazioni non consentite/gli avvisi di sicurezza priorità |
| | Sono disponibili informazioni dettagliate nell'origine dell'attacco e risorse interessate |
| | Suggerimenti per interrompere l'attacco corrente ed evitare attacchi futuri |

## <a name="introductory-walkthrough"></a>Procedura dettagliata introduttiva
 Dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/), accedere Security Center. [Accedere al portale di](https://portal.azure.com), selezionare **Sfoglia**e quindi scorrere all'opzione **Security Center** o selezionare il riquadro di **Centro protezione** che precedentemente aggiunte al portale dashboard.

![Riquadro di protezione nel portale di Azure][1]

Dal Centro protezione, è possibile impostare i criteri di sicurezza, eseguire il monitoraggio delle configurazioni di sicurezza e visualizzare gli avvisi di sicurezza.

### <a name="security-policies"></a>Criteri di sicurezza

È possibile definire i criteri per gli abbonamenti Azure e i gruppi di risorse in base ai requisiti di sicurezza della società. È possibile personalizzare i tipi di applicazioni in uso o per la riservatezza dei dati in ciascuna sottoscrizione. Ad esempio, le risorse utilizzate per lo sviluppo o test potrebbero essere requisiti di sicurezza diversi da quelli utilizzati per le applicazioni. Analogamente, applicazioni con regolamentati dati personali possono richiedere un livello di sicurezza.

> [AZURE.NOTE] Per modificare un criterio di protezione livello di abbonamento o il livello di gruppo di risorse, è necessario essere il proprietario della sottoscrizione o un collaboratore.

Scegliere il riquadro **dei criteri** per un elenco delle sottoscrizioni e gruppi di risorse e il **Centro protezione** .   

![Blade Security Center][2]

Scegliere un abbonamento a visualizzare i dettagli dei criteri e il **criterio di protezione** .

![Abbonamento blade criteri di sicurezza][3]

**Raccolta di dati** (vedere sopra) consente di raccogliere dati per un criterio di protezione. Attivazione sono disponibili:

- Analisi giornaliera di tutte supportate macchine virtuali per il monitoraggio di sicurezza e suggerimenti.
- Insieme di eventi di protezione per il rilevamento di analisi e il rischio.

**Scegliere un account di archiviazione per l'area geografica** (vedere sopra) consente di scegliere, per ogni area geografica in cui si dispone di macchine virtuali in esecuzione, l'account di archiviazione in cui sono archiviati dati raccolti dal tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni regione, verrà creato automaticamente. I dati raccolti sono logicamente isolati dai dati di altri clienti per motivi di sicurezza.

> [AZURE.NOTE] Raccolta di dati e scegliendo un account di archiviazione per paese è configurato a livello di sottoscrizione.

Selezionare i **criteri di prevenzione della** (vedere sopra) per aprire e il **criteri di prevenzione della** . **Mostra i consigli per** consente di scegliere i controlli di protezione che si desidera eseguire il monitoraggio e consigliabile in base alle esigenze di sicurezza delle risorse all'interno della sottoscrizione.

Selezionare un gruppo di risorse per visualizzare i dettagli dei criteri.

![Gruppo di risorse blade criteri di sicurezza][4]

**Ereditarietà** (vedere sopra) consente di definire il gruppo di risorse come:

- Ereditato (impostazione predefinita) che indica che tutti i criteri di sicurezza per il gruppo di risorse vengono ereditate dal livello di sottoscrizione.
- Univoco che indica che il gruppo di risorse avrà un criterio di sicurezza personalizzata. È necessario apportare modifiche in **Mostra suggerimenti per**.

> [AZURE.NOTE] Se esiste un conflitto tra criteri a livello di abbonamento e criteri a livello di gruppo risorse, il criterio di livello di gruppo di risorse avrà la precedenza.

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

 Nel Centro sicurezza analizza lo stato di protezione delle risorse di Azure per identificare i potenziali vulnerabilità. Un elenco di suggerimenti consente di eseguire in modo semplificato il processo di configurazione controlli necessari. Alcuni esempi:

- Provisioning antimalware per identificare e rimuovere software dannoso
- Configurazione di gruppi di sicurezza di rete e le regole per controllare il traffico in macchine virtuali
- Il provisioning di firewall di applicazione web per difendere attacchi che hanno come destinazione delle applicazioni web
- Distribuzione degli aggiornamenti di sistema mancanti
- Indirizzare le configurazioni del sistema operativo che non corrispondono le previsioni consigliate

Fare clic sul riquadro di **suggerimenti** per un elenco di suggerimenti. Fare clic su ogni suggerimento per visualizzare informazioni aggiuntive o per eseguire azioni per risolvere il problema.

![Suggerimenti relativi alla protezione in Centro protezione di Azure][5]

### <a name="resource-health"></a>Integrità delle risorse

Riquadro **dell'integrità di protezione delle risorse** Mostra generali di sicurezza dell'ambiente in base al tipo di risorsa, inclusi macchine virtuali, applicazioni web e altre risorse.   

Selezionare un tipo di risorsa sul riquadro **dell'integrità di protezione delle risorse** per visualizzare ulteriori informazioni, incluso un elenco di qualsiasi potenziali vulnerabilità che sono stati identificati. (**Macchine virtuali** sia selezionata nell'esempio seguente).

![Riquadro dell'integrità di risorse][6]

### <a name="security-alerts"></a>Avvisi di sicurezza

 Nel Centro sicurezza automaticamente raccoglie, analizza e si integra dati log le risorse Azure, della rete e le soluzioni partner come programmi antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Esempi di rilevamento di:

- Compromesso macchine virtuali di comunicare con indirizzi IP dannosi conosciuti
- Avanzate malware rilevato tramite segnalazione errori di Windows
- Forzare attacchi macchine virtuali
- Avvisi di sicurezza da programmi antimalware integrata e firewall

Fare clic sul riquadro **gli avvisi di sicurezza** , viene visualizzato un elenco di avvisi priorità.

![Avvisi di sicurezza][7]

Selezione di un avviso Mostra altre informazioni sui attacco e suggerimenti su come eseguire il.

![Dettagli degli avvisi di sicurezza][8]

### <a name="partner-solutions"></a>Soluzioni partner

Il riquadro di **soluzioni dei Partner** consente di monitor a colpo d'occhio lo stato di integrità delle soluzioni partner integrato con l'abbonamento Azure. Centro protezione consente di visualizzare gli avvisi provenienti dalle soluzioni.

Selezionare il riquadro di **soluzioni dei Partner** . Viene visualizzata una pala un elenco di tutte le soluzioni partner connessi.

![Soluzioni partner][9]

## <a name="get-started"></a>Guida introduttiva
Per iniziare a utilizzare Security Center, è necessario un abbonamento a Microsoft Azure. Centro protezione è compatibile con l'abbonamento Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/), accedere Security Center. Vedere la [documentazione del portale](https://azure.microsoft.com/documentation/services/azure-portal/) per altre informazioni.

[Guida introduttiva di Azure Security Center](security-center-get-started.md) consente di eseguire il monitoraggio della protezione e componenti di gestione dei criteri di Centro protezione.

## <a name="see-also"></a>Vedere anche
In questo documento sono stati introdotti Security Center, le relative funzionalità chiave e come iniziare. Per ulteriori informazioni, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) , informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) , informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) -informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Blog di sicurezza di Windows Azure](http://blogs.msdn.com/b/azuresecurity/) , ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
