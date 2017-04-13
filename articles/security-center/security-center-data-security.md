<properties
   pageTitle="Protezione dei dati di Centro protezione di Azure | Microsoft Azure"
   description="In questo documento spiega come dati sono gestiti e garantiti in Centro protezione di Azure."
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
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Protezione dei dati di Centro protezione di Azure
Per aiutare i clienti impedire, rilevare e rispondere a rischi, Azure Security Center raccoglie ed elabora i dati delle risorse Azure, incluse le informazioni di configurazione, metadati, i registri eventi, un arresto anomalo file ridotta e altro ancora. Abbiamo rendere sicuri impegni per proteggere la privacy e sicurezza dei dati. Microsoft aderisce alle linee guida di sicurezza e conformità restrittive, ovvero dalla codifica alla operativo un servizio. 

Questo articolo spiega come dati viene gestiti e garantiti in Centro protezione di Azure.

## <a name="data-sources"></a>Origini dati

Centro protezione di Azure analizzati i dati dalle origini seguenti:

- Servizi di Azure: Legge le informazioni sulla configurazione dei servizi Azure è stata distribuita per la comunicazione con provider di risorse del servizio.
- Il traffico di rete: Legge campionati rete il traffico i metadati dall'infrastruttura di Microsoft, ad esempio origine/destinazione IP/porta, dimensioni pacchetto e protocollo.
- Soluzioni dei partner: Consente di raccogliere gli avvisi di sicurezza da soluzioni partner integrate, ad esempio firewall e le soluzioni antimalware. Questi dati vengono archiviati in Centro protezione di Azure lo spazio di archiviazione, al momento disponibile negli Stati Uniti.
- Macchine virtuali: Azure Security Center è possibile raccogliere le informazioni sulla configurazione e informazioni sugli eventi di sicurezza, ad esempio eventi di Windows e controllo registri, i registri IIS, i messaggi di registro di sistema e file di dettagli arresto anomalo da macchine virtuali utilizzando agenti di raccolta dati. Vedere la sezione "Gestione raccolta di dati" sotto per informazioni dettagliate.  

Inoltre, informazioni sugli avvisi di sicurezza, suggerimenti e lo stato di integrità di sicurezza vengono archiviate in Centro protezione di Azure lo spazio di archiviazione, al momento disponibile negli Stati Uniti. Queste informazioni possono includere le informazioni di configurazione correlati e gli eventi di protezione raccolti da macchine virtuali in base alle esigenze necessario fornirgli l'avviso di sicurezza, recommendation o lo stato di integrità di sicurezza.

## <a name="data-protection"></a>Protezione dei dati

**Separazione di dati**: dati vengono mantenuti in modo logico distinto per ogni componente in tutto il servizio. Tutti i dati è contrassegnato per l'organizzazione. In questo tag persiste nel ciclo di vita dei dati e viene applicata ogni livello del servizio. Inoltre, i dati raccolti dal macchine virtuali vengono archiviati negli account dell'utente dello spazio di archiviazione.

**Accesso ai dati**: per fornire suggerimenti per la sicurezza e analizzare i rischi di sicurezza, personale Microsoft possono accedere a informazioni raccolte o analizzata da Azure servizi, ivi compresi arresto anomalo. Arresto anomalo e gli eventi di creazione del processo accidentalmente possono includere dati dei clienti o dati personali da macchine virtuali. Abbiamo rispettare il [Legali Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e l' [Informativa sulla Privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), quale stato che Microsoft non usare il dati dei clienti o estrarre informazioni da essa per scopi commerciali pubblicitari o simili. Abbiamo usare solo dati dei clienti in base alle esigenze per offrire Azure servizi, ivi compresi a fini compatibili con i servizi. Mantenere tutti i diritti ai dati personali.

**Usare dati**: Microsoft Usa motivi e intelligence rischio visualizzato tra più tenant per migliorare la funzionalità di prevenzione; avviene secondo gli impegni di privacy descritti l' [Informativa sulla Privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Percorso di dati**: stato specificato un account di archiviazione per ogni area geografica in cui sono in esecuzione macchine virtuali. In questo modo è possibile archiviare i dati nella stessa regione macchina virtuale da cui vengono raccolti i dati. Questi dati, inclusi file di dettagli arresto anomalo, verranno archiviati in modo permanente nell'account di archiviazione. Il servizio memorizza anche informazioni sugli avvisi di sicurezza, inclusi gli avvisi di soluzioni dei partner integrate, suggerimenti e lo stato di integrità di protezione in Centro protezione di Azure lo spazio di archiviazione, al momento disponibile negli Stati Uniti.

## <a name="managing-data-collection-from-virtual-machines"></a>Gestire la raccolta di dati da macchine virtuali

Quando si sceglie di attivare il Centro protezione di Azure, la raccolta di dati è attivata per ognuna delle sottoscrizioni. È possibile disattivare la raccolta di dati nella sezione "Criteri di sicurezza" del Dashboard di Centro protezione di Azure. Quando la raccolta di dati è attivata, Centro protezione di Azure disposizioni Azure Monitoring l'agente di tutte le supportate macchine virtuali e quelli nuovi vengono creati. L'estensione di monitoraggio di sicurezza di Azure Cerca relative alla protezione varie configurazioni ed eventi in [Traccia eventi per Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) consente di tenere traccia. Inoltre, il sistema operativo genererà eventi registro eventi nel corso dell'esecuzione nel computer. Esempi di tali dati sono: tipo di sistema operativo e versione, operativo registri eventi di sistema (registri eventi di Windows), processi in esecuzione, il nome del computer, gli indirizzi IP, eseguito l'accesso utente e ID tenant. L'agente di monitoraggio di Azure legge voci del registro eventi ed ETW analizza e li copia al proprio account di archiviazione per l'analisi. 

Stato specificato un account di archiviazione per ogni area geografica in cui si dispone di macchine virtuali in esecuzione, in cui i dati raccolti da macchine virtuali in cui è archiviato stessa regione. Questo semplice mantenere i dati nella stessa area geografica per scopi di sovranità dei dati e informativa sulla privacy. Nella sezione "Criteri di sicurezza" del Dashboard di Centro protezione di Azure, è possibile configurare gli account di archiviazione per ogni area.

Agente di monitoraggio di Azure vengono copiate anche arresto anomalo al proprio account di archiviazione.  Azure Security Center raccoglie copie temporanee dei file di immagine di un arresto anomalo e consente di analizzare la presenza di tentativi di sfruttamento e compromessi esito negativo.  Centro protezione di Azure esegue l'analisi all'interno dell'area geografica stesso come account di archiviazione ed Elimina le copie temporanee al termine analisi.

È possibile disabilitare la raccolta di dati da macchine virtuali in qualsiasi momento, che consente di rimuovere qualsiasi agente monitoraggio installato dal Centro protezione di Azure.


## <a name="see-also"></a>Vedere anche

In questo documento è stato come dati viene gestiti e garantiti in Centro protezione di Azure. Per ulteriori informazioni sul Centro protezione di Azure, vedere:

- [Guida e pianificazione della Centro protezione di Azure](security-center-planning-and-operations-guide.md) , informazioni su come pianificare e si conoscono le considerazioni di progettazione per l'adozione di Azure Security Center.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza
- [Monitoraggio delle soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) -informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) , ovvero trova domande frequenti sull'utilizzo del servizio
- [Blog sulla protezione di Azure](http://blogs.msdn.com/b/azuresecurity/) , trovare post di blog sulla protezione di Azure e conformità
