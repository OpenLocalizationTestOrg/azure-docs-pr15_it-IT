<properties
   pageTitle="Domande frequenti domande frequenti Azure Security Center | Microsoft Azure"
   description="In questo argomento fornisce le risposte alle domande su Azure Security Center."
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
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Domande frequenti su Azure Security Center

In questo argomento fornisce le risposte alle domande su Centro protezione di Azure, un servizio che consente di impedire, rilevare e rispondere alle minacce con maggiore visibilità e controllare la protezione delle risorse di Microsoft Azure.

## <a name="general-questions"></a>Domande di carattere generale

### <a name="what-is-azure-security-center"></a>Che cos'è Centro protezione di Azure?
Centro protezione di Azure consente di impedire, rilevare e rispondere alle minacce con maggiore visibilità e controllare la protezione delle risorse di Azure. Consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni.

### <a name="how-do-i-get-azure-security-center"></a>Come è possibile ottenere Azure Security Center?
Centro protezione di Azure è attivato con l'abbonamento a Microsoft Azure e accedervi dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). [Accedere al portale di](https://portal.azure.com), selezionare **Sfoglia**e scorrere fino al **Centro protezione**.  

## <a name="billing"></a>Fatturazione

### <a name="how-does-billing-work-for-azure-security-center"></a>Come funziona fatturazione per Azure Security Center?
Centro protezione è disponibile in due livelli: gratuita e Standard.

Il livello gratuito consente di impostare criteri di sicurezza e ricevere gli avvisi di sicurezza, problemi e i suggerimenti che guidano il processo di configurazione controlli necessari. Con il livello gratuito è inoltre possibile monitorare lo stato di sicurezza le risorse Azure e le soluzioni dei partner integrate con l'abbonamento Azure.

Il livello Standard fornisce rilevamenti di funzionalità più avanzate livello gratuito: business intelligence, analisi comportamento, un arresto anomalo analisi e il rilevamento anomalia delle minacce. Una versione di valutazione gratuita 90 giorni del livello Standard è disponibile. Per eseguire l'aggiornamento, selezionare livello prezzi nei [criteri di sicurezza](security-center-policies.md#setting-security-policies-for-subscriptions). Per altre informazioni, vedere [Security Center prezzi](security-center-pricing.md) .

## <a name="data-collection"></a>Raccolta di dati

Nel Centro sicurezza raccoglie i dati da macchine virtuali per valutare lo stato di protezione, fornire suggerimenti per la sicurezza e un avviso a rischi. Quando si accede a Centro protezione, tutte le macchine virtuali nell'abbonamento è attivata per la raccolta di dati. Raccolta di dati è consigliabile, ma è possibile escludere dalla [disattivazione della raccolta di dati](#how-do-i-disable-data-collection) nei criteri di Centro protezione.

### <a name="how-do-i-disable-data-collection"></a>Come è possibile disattivare la raccolta di dati?

È possibile disabilitare **la raccolta di dati** per una sottoscrizione nei criteri di sicurezza in qualsiasi momento. ([Accesso al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, selezionare **Centro protezione**e selezionare **criterio**).  Quando si seleziona una sottoscrizione, una nuova pala si apre e si consente di disattivare **la raccolta di dati** . Selezionare l'opzione **Elimina agenti** sulla barra multifunzione superiore per rimuovere gli agenti da macchine virtuali esistenti.

> [AZURE.NOTE] Criteri di sicurezza possono essere impostati al livello di abbonamento Azure e livello di gruppo di risorse, ma è necessario selezionare una sottoscrizione per disattivare la raccolta di dati.

### <a name="how-do-i-enable-data-collection"></a>Come è possibile abilitare la raccolta di dati?
È possibile abilitare la raccolta di dati per le sottoscrizioni Azure nei criteri di sicurezza. Per abilitare la raccolta di dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, selezionare **Nel Centro sicurezza**e selezionare **criterio**. **Raccolta di dati** è impostato su **On** e configurare gli account di archiviazione in cui si desidera dati da raccogliere per (vedere la domanda "[in cui sono archiviati dati?](#where-is-my-data-stored)"). Quando è abilitata la **raccolta di dati** , raccoglie automaticamente le informazioni di configurazione ed eventi di protezione da tutte le macchine virtuali supportate nella sottoscrizione.

> [AZURE.NOTE] Criteri di sicurezza è possibile impostare il livello di abbonamento Azure e delle risorse gruppo ma la configurazione di raccolta dati si verifica solo a livello di sottoscrizione.

### <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta di dati?
Raccolta di dati viene abilitata tramite l'agente di monitoraggio di Azure e l'estensione di monitoraggio di sicurezza di Azure. L'estensione di monitoraggio di sicurezza di Azure analizza per diversi protezione pertinenti e invia in tracce [Traccia eventi per Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Inoltre, il sistema operativo crea voci del registro eventi.  L'agente di monitoraggio di Azure legge voci del registro eventi ed ETW analizza e li copia al proprio account di archiviazione per l'analisi.  Questo è l'account di archiviazione che è configurata nei criteri di sicurezza. Per ulteriori informazioni sull'account di archiviazione, vedere la domanda "[in cui sono archiviati dati?](#where-is-my-data-stored)"

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>L'estensione agente di monitoraggio o il monitoraggio della protezione influisce le prestazioni del server?
L'agente e l'estensione utilizza un importo nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni.

### <a name="where-is-my-data-stored"></a>Dove vengono archiviati i dati?
Per ogni area geografica in cui si dispone di macchine virtuali in esecuzione, si scegliere l'account di archiviazione in cui sono archiviati dati raccolti dal tali macchine virtuali. Questo semplice mantenere i dati nella stessa area geografica per scopi di sovranità dei dati e informativa sulla privacy. Si scegliere l'account di archiviazione per una sottoscrizione nei criteri di sicurezza. ([Accesso al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, selezionare **Centro protezione**e selezionare **criterio**). Quando si fa clic su un abbonamento, viene aperta una nuova pala. Selezionare **gli account di archiviazione Scegli** per selezionare un'area.

> [AZURE.NOTE] Criteri di sicurezza è possibile impostare il livello di abbonamento Azure e delle risorse gruppo ma la selezione di un'area per l'account di archiviazione si verifica solo a livello di sottoscrizione.

Per ulteriori informazioni sull'archiviazione Azure e gli account di archiviazione, vedere [La documentazione di spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/) e [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Utilizzando il Centro protezione di Azure

### <a name="what-is-a-security-policy"></a>Che cos'è un criterio di protezione?
Criteri di sicurezza definisce il set di controlli consigliata per le risorse all'interno della sottoscrizione specificata o un gruppo di risorse. In Centro protezione di Azure, è possibile definire criteri per gli abbonamenti Azure e i gruppi di risorse in base ai requisiti di sicurezza della società e il tipo di applicazioni o riservatezza del contenuto dei dati di ogni abbonamento.

Ad esempio, le risorse utilizzate per lo sviluppo o test potrebbero essere requisiti di sicurezza diversi da quelli utilizzati per le applicazioni. Analogamente, applicazioni con dati regolamentati come personali (informazioni personali) possono richiedere un livello di sicurezza. I criteri di sicurezza abilitati in Centro protezione di Azure determinerà suggerimenti per la sicurezza e monitoraggio. Per ulteriori informazioni sui criteri di sicurezza, vedere [monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md).

> [AZURE.NOTE] In caso di un conflitto tra criteri a livello di abbonamento e criteri a livello di gruppo risorse, il criterio di livello di gruppo di risorse ha la precedenza.

### <a name="who-can-modify-a-security-policy"></a>Chi può modificare un criterio di protezione?
Criteri di sicurezza vengono configurati per ogni abbonamento o di un gruppo di risorse. Per modificare un criterio di protezione il livello di abbonamento o delle risorse gruppo, è necessario essere un proprietario o collaboratori di tale abbonamento.

Per informazioni su come configurare i criteri di sicurezza, vedere [impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Che cos'è un Consiglio di protezione?
Centro protezione di Azure analizza lo stato di protezione delle risorse di Azure. Quando vengono identificate i potenziali vulnerabilità, vengono creati consigli. I suggerimenti guidano il processo di configurazione del controllo base alle esigenze. Esempi sono:

- Il provisioning di antimalware per identificare e rimuovere software dannoso
- Configurazione di [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) e le regole per controllare il traffico in macchine virtuali
- Il provisioning di un firewall di applicazione web per difendere attacchi di applicazioni web di destinazione
- Distribuzione degli aggiornamenti di sistema mancanti
- Indirizzare le configurazioni del sistema operativo che non corrispondono le previsioni consigliate

Qui vengono visualizzati solo i consigli abilitati in Criteri di sicurezza.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Come è possibile visualizzare lo stato corrente di sicurezza delle risorse Azure?
Una sezione **integrità risorse** nel e **Centro sicurezza** Mostra generali di sicurezza dell'ambiente suddivisa macchine virtuali, applicazioni web e altre risorse. Ogni risorsa ha che mostra un indicatore se sono stati identificati eventuali potenziali problemi di protezione. Facendo clic sul riquadro dell'integrità di risorse consente di visualizzare le risorse e identifica in cui è richiesta attenzione o potrebbero esistere problemi.

### <a name="what-triggers-a-security-alert"></a>L'evento che genera un avviso di sicurezza?
Centro protezione di Azure automaticamente raccoglie, analizza e integra queste dati log le risorse Azure, della rete e le soluzioni partner come antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Esempi di rilevamento di:

- Compromesso macchine virtuali di comunicare con indirizzi IP dannosi conosciuti
- Avanzate malware rilevato tramite segnalazione errori di Windows
- Forzare attacchi macchine virtuali
- Avvisi di sicurezza da soluzioni di protezione partner integrate, ad esempio Anti-Malware o i firewall dell'applicazione Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Che cos'è la differenza tra minacce rilevata e l'avviso sulla tramite Microsoft Security Response Center e Azure Security Center?
Microsoft Security Response Center (MSRC) esegue il monitoraggio della protezione seleziona della rete Azure e infrastruttura e riceve reclami di business intelligence e abuse rischio da terze parti. Quando MSRC viene a conoscenza che si è avuti dati dei clienti da una parte atti illecita o non autorizzata o che l'utilizzo di Azure non è conforme con i termini per utilizzare accettabile, un responsabile ai problemi di sicurezza per segnalare il cliente. Notifica si verifica in genere inviando un messaggio di posta elettronica per i contatti di sicurezza specificati nel Centro protezione di Azure o il proprietario della sottoscrizione Azure se non è stato specificato un contatto di sicurezza.

Centro protezione è un servizio di Azure che esegue il monitoraggio dell'ambiente Azure del cliente e si applica analitica per rilevare automaticamente una vasta gamma di attività potenzialmente pericolose continuamente. Questi rilevamenti vengono rilevati come gli avvisi di sicurezza nel dashboard di Centro protezione.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>Come vengono gestite autorizzazioni in Centro protezione di Azure
Centro protezione di Azure supporta l'accesso basato sui ruoli. Per ulteriori informazioni su controllo dell'accesso basato sui ruoli (RBAC) in Azure, vedere [Controllo dell'accesso basato sui ruoli di Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Quando si apre Security Center solo consigli e verranno visualizzati avvisi relativi alle risorse a che l'utente ha accesso. Questo errore indica che gli utenti vedranno solo gli elementi correlati alle risorse in cui l'utente viene assegnato il ruolo di proprietario, collaboratori o Reader per l'abbonamento o il gruppo di risorse a cui appartiene una risorsa.

Se è necessario:

- **Modificare un criterio di protezione**, è necessario essere un proprietario o collaboratori della sottoscrizione.
- **Applica un suggerimento**, è necessario essere un proprietario o collaboratori della sottoscrizione.
- **Ha la visibilità sullo stato di sicurezza in tutte le sottoscrizioni**, è necessario essere un proprietario, un collaboratore o un visualizzatore (amministratore IT, del Team di protezione) di ogni abbonamento.
- **Ha la visibilità sullo stato di protezione delle risorse**, è necessario essere un gruppo di risorse proprietario, collaboratore o lettore (attrezzi).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Le risorse Azure vengono monitorate dal Centro protezione di Azure?
Centro protezione di Azure consente di controllare le risorse di Azure seguenti:

- Macchine () (inclusi [Servizi Cloud](../cloud-services/cloud-services-choose-me.md))
- Reti virtuali Azure
- Servizio SQL Azure
- Le soluzioni partner integrato con l'abbonamento Azure, ad esempio un firewall di applicazione web in macchine virtuali e [sull'Ambiente servizio App](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="what-types-of-virtual-machines-will-be-supported"></a>Tipi di macchine virtuali saranno supportati?
Il monitoraggio dell'integrità di sicurezza e suggerimenti sono disponibili per macchine () create con entrambi i [classica e modelli di distribuzione di Manager delle risorse](../azure-classic-rm.md).

Supportati macchine virtuali di Windows:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Macchine virtuali Linux supportate:

- Versioni Ubuntu 12.04, 14.04, 16.04
- Debian versioni 7, 8
- CentOS versioni 6. \*, 7.*
- Versioni Red Hat Enterprise Linux (RHEL) 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versioni 11. \*, 12.*
- Versioni di Linux Oracle 6. \*, 7.*

Sono supportate anche macchine virtuali in esecuzione in un servizio cloud. Cloud solo servizi web e lavoro ruoli in fase di produzione vengono monitorate bande orarie. Per ulteriori informazioni sul servizio cloud, vedere [Panoramica di servizi Cloud](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Perché nel Centro sicurezza Azure non riconosce la soluzione antimalware in esecuzione in macchine Virtuali personale Azure?

Centro protezione di Azure ha solo la visibilità sullo antimalware installato tramite le estensioni di Azure. Ad esempio Security Center non è in grado di rilevare antimalware che era già installato in un'immagine che è specificato o se è installato antimalware macchine virtuali con i processi (ad esempio sistemi di Gestione configurazione).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Perché viene visualizzato il messaggio "Mancanti analisi dati" per le macchine Virtuali?

È possibile richiedere del tempo (in genere minore di un'ora) per i dati di analisi popolare dopo la raccolta di dati è abilitata nel Centro protezione di Azure. Analisi non vengono popolata per macchine virtuali in stato di interruzione.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Perché viene visualizzato il messaggio "Agente macchine Virtuali è mancante?"

Per abilitare la raccolta di dati, è necessario installare l'agente di macchine Virtuali in macchine virtuali. Agente di macchine Virtuali è installato per impostazione predefinita per le macchine virtuali distribuiti da Azure Marketplace. Per informazioni su come installare l'agente di macchine Virtuali in altre macchine virtuali, vedere il blog [agente macchine Virtuali ed estensioni](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
